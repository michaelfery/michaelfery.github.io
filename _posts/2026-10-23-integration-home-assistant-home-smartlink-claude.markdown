---
layout: post
title:  "Mes radiateurs Home-SmartLink dans Home Assistant, avec Claude comme binôme"
date:   2026-10-23 09:00:00.000 +02:00
img: posts/home-smartlink-ha-banner.png
tags: [Home Assistant, Claude, IA, Python, API]
---

Chez moi, les radiateurs électriques sont pilotés par des thermostats **Carrera Home-SmartLink** : de petits boîtiers branchés sur le fil pilote, reliés entre eux en Bluetooth mesh, puis à une box Wi-Fi, puis à un cloud, puis… à une application mobile. Et c'est tout. Pas d'API documentée, pas d'intégration [Home Assistant](https://www.home-assistant.io/).

Je voulais pouvoir lire la température de chaque pièce et piloter les radiateurs depuis Home Assistant, comme le reste de la maison. J'ai donc décidé d'écrire l'intégration moi-même… avec un binôme un peu particulier : **Claude**, l'assistant d'Anthropic, utilisé via [Claude Code](https://www.anthropic.com/claude-code) dans mon terminal.

Le résultat est en ligne : [michaelfery/home-smartlink-ha](https://github.com/michaelfery/home-smartlink-ha), installable via HACS, avec un client Python publié sur [PyPI](https://pypi.org/project/homesmartlink/). Cet article raconte comment on y est arrivés : la méthode de travail, l'étude des échanges entre l'app et son cloud, et ce que l'IA a bien (et moins bien) fait.

> **Avertissement** : l'intégration est non officielle, sans lien avec Carrera ni Texas de France. L'API utilisée n'est pas documentée et peut changer à tout moment.

## Le point de départ

L'architecture du système est la suivante :

```text
thermostats ──(BLE mesh)── box Home-SmartLink ──(Wi-Fi)── cloud ── app mobile
```

Aucun accès local à la box : tout passe par le cloud. La seule façon de savoir comment parler à ce cloud, c'est de regarder ce que fait l'application.

## Travailler avec Claude : un cadre avant tout

Avant d'ouvrir le terminal, j'avais fait ma part : capturer le trafic de l'application avec mon propre compte, sous forme de fichiers HAR, **une action par fichier** (connexion, ouverture d'une pièce, passage en mode éco, changement de consigne…). Le nom du fichier décrit l'action. C'est simple, mais c'est ce qui rend l'analyse possible : on compare deux captures qui ne diffèrent que par un geste.

Ensuite, je n'ai pas demandé « fais-moi une intégration Home Assistant ». J'ai écrit un prompt découpé en trois phases, avec des règles explicites. En voici l'essentiel :

```markdown
# Phase 1 : analyse (aucun code d'intégration)
1. Écris un script Python qui parse les HAR et extrait : endpoints, méthodes,
   headers utiles, payloads. Masque systématiquement tokens, mots de passe,
   emails, IDs de compte.
2. Produis docs/API.md : base URL, authentification, modèle de données,
   endpoints de lecture et d'écriture, mapping des modes.
3. Liste explicitement ce qui est incertain ou manquant, et dis-moi quelles
   actions je dois capturer en plus. Ne devine pas un endpoint non observé.

# Phase 2 : plan
Propose un plan détaillé et attends ma validation avant d'écrire l'intégration.
(client API async séparé de l'intégration, config flow, reauth, options,
DataUpdateCoordinator, tests, CI hassfest et HACS…)

# Phase 3 : développement
Implémente par étapes en commitant à chaque étape.

# Règles
- Ne commite jamais les HAR bruts ni aucun secret.
- Si un comportement de l'API est ambigu, arrête-toi et pose la question.
- Code et commentaires en anglais, échanges avec moi en français.
```

Trois idées comptent dans ce prompt :

- **Analyse, plan, code, dans cet ordre.** Claude a passé la première phase sans écrire une ligne d'intégration, et la deuxième à attendre mon feu vert.
- **« Ne devine pas. »** Sur une API non documentée, la pire chose qu'un assistant puisse faire, c'est inventer un endpoint plausible. Chaque affirmation de `docs/API.md` cite la capture dont elle vient, et ce qui n'a pas été observé est marqué comme tel.
- **Les secrets d'abord.** Les captures contiennent mon mot de passe et un token valide. Elles ne quittent jamais ma machine.

## Phase 1 : comprendre l'API

Le premier livrable a été un script d'extraction. Il ne garde que les appels au cloud Home-SmartLink, remplace chaque identifiant par un pseudonyme stable (`<ID_1>`, `<DEVICE_2>`…) pour que les relations restent lisibles, et masque tokens et mots de passe. Surtout, il **se vérifie lui-même** : il retient chaque valeur brute remplacée et échoue si l'une d'elles réapparaît dans les fichiers produits. Sur mes captures, 67 valeurs masquées, aucune fuite.

À partir de là, le tableau s'est dessiné assez vite :

- **Authentification** : un `POST signin` avec e-mail et mot de passe renvoie un token opaque de 24 caractères. Pas de JWT, pas de durée de vie connue, **pas de refresh token**.
- **Lecture** : un document « maison » contient les pièces, et pour chacune ses réglages de chauffage (`currentMode`, consigne manuelle, températures éco et confort) et sa programmation hebdomadaire.
- **Modes** : `currentMode` vaut 1 pour Arrêt, 2 pour Hors-gel, 3 pour Manuel, 4 pour Programmation. Détail amusant : les boutons lune et soleil de l'app ne changent pas le mode. Ils copient la température éco ou confort dans la consigne manuelle, et la pièce reste en Manuel.
- **Pas de temps réel** : l'app ne garde aucune connexion ouverte et ne fait aucun polling. Elle lit tout à l'ouverture d'un écran, et c'est tout.

Et puis deux mauvaises surprises, qui ont fait l'objet de la première vraie question de Claude :

1. **Aucune température mesurée dans les réponses.** L'app affiche la consigne, pas la température de la pièce.
2. **Piloter un radiateur, c'est envoyer une trame binaire** de 64 octets, encodée en base64, suivie de l'enregistrement du document complet de la maison.

Claude s'est arrêté là, comme demandé, et m'a exposé les deux problèmes avant de proposer le moindre plan.

### La température cachée

La température mesurée existait bien, mais ailleurs. L'app lance un **diagnostic** à chaque ouverture : elle demande à la box d'interroger les thermostats. Le résultat se lit sur un endpoint `deviceStatus`, sous forme d'une chaîne par pièce :

```text
R0D0M3S0C38A42J0W0M0Y0
```

`R0` est le numéro de pièce, `M3` le mode, `C38` la consigne ×2 (19 °C), et `A42` la **température ambiante ×2**, soit 21 °C. Les autres champs restent mystérieux, et `docs/API.md` le dit.

Le diagnostic a un coût : la réponse arrive **environ 80 secondes** après la demande. L'intégration en lance donc un au démarrage puis toutes les 15 minutes (c'est réglable), et la température affichée dans Home Assistant peut avoir quelques minutes de retard. C'est une limite du système, pas de l'intégration, et le README l'explique.

### La trame binaire

Pour l'écriture, Claude a reconstitué l'encodage de la trame à partir des captures : la position du numéro de pièce, du mode, des consignes. Le critère de réussite était simple et vérifiable : **reproduire octet pour octet chacune des trames capturées**. Les 38 trames de la première série y sont passées, et ce sont devenues des tests de non-régression.

Plus tard, en travaillant sur le mode Programmation, Claude a pu confirmer l'encodage exact à partir de l'application elle-même. Il a alors trouvé des champs que je n'avais jamais fait varier dans mes captures : la détection de fenêtre ouverte, la durée des vacances… Ils étaient constants chez moi, donc invisibles. C'est la limite d'une analyse fondée uniquement sur l'observation.

Par prudence, l'encodeur **refuse tout état jamais observé** : une consigne hors de la plage de l'app (13,5 à 30 °C, par pas de 0,5), un mode inconnu… Mieux vaut une erreur claire qu'une trame bizarre envoyée à un radiateur.

## Phase 2 : le plan

Le plan proposé par Claude séparait deux couches :

- **`homesmartlink`** : un client Python asynchrone, typé, **sans dépendance à Home Assistant**, publié sur PyPI ;
- **`custom_components/home_smartlink`** : l'intégration elle-même, qui utilise ce client.

Il listait aussi les décisions qui me revenaient, et je trouve que c'est le bon réflexe. Un épisode le montre bien : j'ai répondu « Validé avec ajustements » sans préciser lesquels. Claude ne s'est pas lancé. Il m'a demandé quels ajustements je voulais, avec des exemples. Une demande ambiguë, une question : exactement la règle du prompt.

## Phase 3 : le développement

Ensuite, tout s'est enchaîné étape par étape, un commit par étape : le client et ses tests, le config flow, le coordinator, les entités, la réauthentification et les options, la CI, puis le README.

Quelques choix techniques valent d'être notés :

- **Un faux cloud local pour les tests.** Le client tourne contre un vrai petit serveur HTTP qui rejoue des réponses anonymisées. La bibliothèque de mock prévue au départ n'était pas compatible avec la dernière version d'aiohttp ; Claude l'a remplacée plutôt que d'épingler une vieille version.
- **Des fixtures surveillées.** Les réponses de test sont générées depuis mes captures par un script qui anonymise tout et refuse d'écrire si quelque chose fuit. En plus, **un test échoue** si une fixture contient une donnée personnelle.
- **Une seule écriture à la fois.** Chaque commande refait une lecture fraîche de la maison, envoie la trame, puis enregistre le document complet, sous verrou. C'est ce que fait l'app, avec la même limite : une modification faite dans l'app au même instant peut être écrasée.
- **Pas de refresh token** : le client se reconnecte avec les identifiants enregistrés quand la session expire, une seule fois et sous verrou.
- **Un cloud ménagé** : polling toutes les 60 secondes par défaut, backoff exponentiel plafonné à 10 minutes en cas d'erreur, et aucun secret dans les logs ni dans le `repr` du client.

### Des tests sur de vrais radiateurs, avec accord

Les tests automatisés ne suffisent pas : à un moment, il faut envoyer une vraie commande à un vrai radiateur. Pour chacune, Claude m'a demandé mon accord, a utilisé la Cuisine comme pièce de test, puis a remis l'état initial et vérifié qu'il était bien revenu.

Ça n'a pas été parfait : une capture mal enchaînée a laissé le Salon **environ trois minutes en Arrêt** avant d'être restauré. Rien de grave pour un radiateur, mais un bon rappel : quand l'IA agit sur le monde réel, chaque action doit être réversible et vérifiée.

### Publier : PyPI, GitHub et HACS

La publication sur PyPI passe par **Trusted Publishing** : aucun token n'est stocké dans le dépôt. Un tag `vX.Y.Z` déclenche un workflow qui vérifie que les versions du tag, du package et du manifest concordent, puis publie.

Ma part a été de créer le dépôt GitHub, de déclarer le « pending publisher » sur PyPI et de choisir la licence (MIT). Une fois prévenu, Claude a poussé le tag, vérifié l'installation du package depuis PyPI dans un environnement vierge, puis créé la release GitHub dont HACS a besoin.

Côté Home Assistant : HACS → dépôt personnalisé → installation → redémarrage → ajout de l'intégration avec l'e-mail et le mot de passe de l'app. Les trois thermostats sont apparus, chacun rangé dans sa pièce.

## Itérer : de la 0.1 à la 0.4 en une journée

La première version lisait les températures et pilotait les modes et les consignes. Le reste est venu par petites itérations, toutes le même jour :

| Version | Contenu |
|---|---|
| 0.1.0 | Lecture, modes et consignes |
| 0.2.0 | Mode Programmation |
| 0.3.0 | Éditeur de programme dans les options |
| 0.4.0 | Vacances, mode de toute la maison, fenêtre ouverte, connectivité de la box, diagnostics |
| 0.4.1 | Icône et préparation à la liste HACS par défaut |

### Le mode Programmation

Ma salle de bain est en mode programmé. J'avais l'intuition qu'en dehors des plages horaires elle visait 15,5 °C, et la température confort pendant les plages. Je l'ai décrite à Claude avec des captures d'écran de l'app. L'intuition était juste : **confort pendant une plage active, éco en dehors**. Il existe deux types de programme : *Avancé*, avec six plages ayant chacune ses jours, et *Simplifié*, avec Matin, Journée et Soir.

Dans Home Assistant, le thermostat en `auto` affiche maintenant la consigne effective, calculée comme le fait l'app, et ses attributs donnent le programme complet et la plage en cours. Le soir même, la salle de bain affichait bien 15,5 °C.

### Choisir l'interface

Pour modifier les plages depuis Home Assistant, Claude a proposé deux pistes :

1. des entités natives : jusqu'à **54 entités par pièce** (heures de début et de fin, interrupteurs par jour) ;
2. un **formulaire dans les options** de l'intégration, proche de l'écran de l'app.

Il recommandait le formulaire. J'ai demandé d'abord où se trouveraient concrètement les réglages dans l'interface, puis j'ai validé. C'est un moment où le binôme fonctionne bien : l'IA explore les options et donne une recommandation argumentée, je tranche.

Le formulaire se trouve dans **Configurer → Programmation hebdomadaire** : on choisit la pièce, le type de programme, les températures, puis on édite les plages pré-remplies. Seules les plages modifiées sont envoyées, et les règles de l'app s'appliquent (heures par pas de 10 minutes, fin après le début…). Pour les automatisations, deux actions font la même chose en YAML :

```yaml
action: home_smartlink.set_program_slot
target:
  entity_id: climate.bathroom_thermostat
data:
  slot: 2
  start: "17:00"
  end: "21:30"
  days: [monday, tuesday, wednesday, thursday, friday]
```

Petit moment d'humilité : je n'ai d'abord pas trouvé le bouton **Configurer**. Le formulaire était bien là, c'est moi qui cherchais au mauvais endroit.

### Un bug attrapé par la CI

Avant la 0.4.0, la CI a refusé une modification. Pour désactiver le mode vacances, l'app envoie une date « vide » au format `01/01/0001`. Le code utilisait `strftime('%Y')`, qui ne complète pas l'année 1 avec des zéros sous Linux (glibc). Sur mon Mac, tout passait. Sur un serveur Home Assistant, l'intégration aurait envoyé `01/01/1`. La CI tourne sous Linux, et c'est elle qui l'a vu. Correction : formater la date sans `strftime`.

## Ce que Claude a bien fait… et moins bien

**Bien :**

- **Respecter le cadre.** Analyse, plan, puis code. Des arrêts francs quand l'API réservait une surprise ou quand ma réponse était ambiguë.
- **Prouver plutôt qu'affirmer.** Les trames reproduites octet pour octet, l'installation depuis PyPI vérifiée dans un environnement vierge, l'état des radiateurs contrôlé après chaque test.
- **Documenter l'incertitude.** `docs/API.md` dit clairement ce qui est certain, probable ou inconnu.
- **La sécurité par défaut.** Anonymisation vérifiée par script et par test, aucun secret loggué, publication sans token.
- **Le volume et la constance.** 21 commits, 214 tests pour le client et 68 pour l'intégration, ruff et mypy strict, validations hassfest et HACS : le genre de rigueur qu'on a du mal à tenir seul un soir de semaine.

**Moins bien :**

- **L'observation a ses angles morts.** Les champs constants dans mes captures étaient invisibles, et l'encodeur a dû être corrigé en cours de route. Les cas d'erreur du cloud (mauvais mot de passe, session expirée, box débranchée) n'ont jamais été observés : ils sont traités de façon générique.
- **Les actions réelles restent risquées.** Les trois minutes du Salon en Arrêt en sont la preuve.
- **Certaines déductions sont à vérifier.** Claude avait déduit mon compte GitHub d'un trafic vu pendant les captures. C'était le bon, mais il l'a signalé comme une hypothèse à confirmer, et il a eu raison.
- **Le dernier kilomètre reste humain.** Créer le dépôt, choisir la licence, configurer PyPI, cliquer dans l'interface de Home Assistant : c'était ma part.

## Et maintenant ?

L'intégration tourne chez moi. Elle est installable comme dépôt personnalisé HACS, et une demande d'inclusion dans la liste HACS par défaut est en cours d'examen.

Quelques limites demeurent : tout passe par le cloud du fabricant, les températures ne sont pas en temps réel, et le mot de passe de l'app est stocké dans Home Assistant, faute de refresh token.

Un dernier conseil si vous tentez l'expérience sur vos propres appareils : **vos captures contiennent votre mot de passe en clair**. Gardez-les hors de git, supprimez-les à la fin, et changez votre mot de passe par précaution.

Si vous avez des thermostats Home-SmartLink, le code est sur [GitHub](https://github.com/michaelfery/home-smartlink-ha) et les retours sont les bienvenus. Et si vous hésitez à travailler avec une IA sur ce genre de projet, mon conseil tient en une phrase : **donnez-lui un cadre, des règles, et le droit de vous dire « je ne sais pas »**.
