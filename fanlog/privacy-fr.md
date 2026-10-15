---
layout: default
title: "Politique de confidentialité de FanLog"
description: "Ce que les applications FanLog font de vos données. Réponse courte : il n'y a pas de serveur FanLog, rien de ce que vous notez ne quitte votre téléphone, et les seules requêtes faites portent sur des fichiers publics."
permalink: /fanlog/privacy-fr/
---

# Politique de confidentialité de FanLog

*Dernière mise à jour : 15 octobre 2026*

Read this page in [English](/fanlog/privacy/).

FanLog est une famille d'applications pour suivre un sport à son rythme : **FanLog MMA**, **FanLog
Rugby**, et toute application publiée plus tard sous le nom FanLog. Chacune garde la liste des
combats ou des matchs que tu comptes voir, te laisse marquer ce que tu as vu et le noter, et cache
chaque résultat jusqu'à ce que tu en décides autrement. Cette page les couvre toutes : elles sont
construites à partir du même code et traitent tes données de la même façon. Elle est courte parce
qu'elles en font très peu.

---

## En une phrase

**Il n'y a pas de serveur FanLog, pas de compte et pas d'inscription.** Ta liste, ce que tu as
marqué comme vu, tes notes et les athlètes ou équipes que tu suis sont stockés sur ton téléphone et
n'en partent jamais. Les applications font deux sortes de requêtes réseau, toutes deux vers des
fichiers publics, et aucune ne transmet quoi que ce soit qui te concerne.

---

## Ce que l'application garde, et où

Tout est stocké **sur ton appareil**, dans des fichiers que seule l'application peut lire :

| Donnée | Où |
|---|---|
| Les combats ou matchs ajoutés à ta liste, marqués vus ou notés | une base SQLite locale (`fanlog.db`) |
| Les athlètes ou équipes suivis, et la date du suivi | la même base |
| Les annonces déjà notifiées | la même base |
| Le catalogue : événements, combats ou matchs, athlètes ou équipes, résultats | la même base, remplacée une saison à la fois à chaque mise à jour |
| Tes réglages : thème, taille du texte, langue, masquage des résultats, notifications, compétitions mises de côté | des préférences locales |

Rien n'est transmis à l'auteur des applications, ni visible par lui. Il n'y a aucun identifiant
d'aucune sorte — ni compte, ni identifiant d'appareil, ni identifiant publicitaire — donc rien qui
puisse relier cet appareil à une personne, même si ces données en sortaient.

**C'est plus important ici que dans la plupart des applications.** Ce que tu as regardé est tout
l'objet de FanLog, et c'est aussi le genre de chose que personne ne devrait avoir à partager. C'est
une colonne d'une base de données sur ton téléphone, et aucun code dans les applications ne
l'envoie nulle part.

---

## Ce qui quitte l'appareil, et quand

Quatre cas, et ce sont les seuls.

**Le catalogue.** Chaque application contient un instantané de son catalogue et le rafraîchit
depuis des fichiers publics hébergés sur GitHub, à `raw.githubusercontent.com/michaelfery/fanlog-catalog`.
La requête demande un fichier et ne dit rien d'autre ; l'application s'y nomme (`FanLog` et sa
version), comme un navigateur se nomme. GitHub voit cette requête comme n'importe quel site voit un
visiteur — c'est-à-dire qu'il voit l'adresse IP d'où elle vient, comme tout serveur web. Rien de ta
liste, de tes notes ou de tes suivis n'y figure, puisque l'application n'a aucun moyen de les
envoyer.

**Les images.** Les photographies des athlètes et, dans les applications de sports d'équipe, les
écussons des clubs sont chargés au fil du défilement depuis le CDN d'images d'ESPN, à
`a.espncdn.com`. Elles sont liées et non embarquées, donc les serveurs d'ESPN voient ces requêtes
comme GitHub voit la précédente : un fichier demandé, depuis une adresse IP. L'application n'envoie
aucun identifiant avec elles. Ce que cela implique, dit clairement parce que la plupart des
politiques l'omettent : **les athlètes et les clubs dont ton téléphone charge l'image sont visibles
par ESPN sous forme de requêtes d'images**, comme tout site qui charge des images chez un tiers rend
ces requêtes visibles à ce tiers.

Sans réseau, ces deux requêtes échouent simplement et l'application continue avec ce qu'elle a déjà.
Les applications sont conçues pour fonctionner hors ligne ; le réseau ne sert qu'à rafraîchir le
catalogue.

**La sauvegarde automatique d'Android.** Android peut copier la base de données et les réglages de
l'application dans la sauvegarde liée à ton compte Google, comme il le fait pour d'autres
applications de ton téléphone. C'est ce qui te rend ta liste et tes notes quand tu réinstalles ou
changes de téléphone.

- **C'est Android qui la fait, pas l'application.** L'application déclare seulement lesquels de ses
  fichiers méritent d'être sauvegardés : la base de données et les réglages. Le catalogue n'en fait
  pas partie — il est retéléchargé — ni un rapport de plantage.
- **Elle est chiffrée avec le code de verrouillage de ton appareil**, à partir d'Android 9. Google
  conserve la copie sans pouvoir la lire, et l'auteur des applications n'y a aucun accès.
- **Tu peux la couper**, dans les réglages d'Android, sous Google puis Sauvegarde, pour tout
  l'appareil ou pour une seule application.
- **C'est voulu.** Sans serveur, la base de données de ton téléphone *est* ton carnet — tout ce que
  tu as coché, depuis que tu utilises l'application. Sans cette sauvegarde, un téléphone perdu
  emporterait tout avec lui.

**Le pourboire.** Les réglages proposent un achat facultatif, « Soutenir FanLog », qui ne débloque
rien. Il passe entièrement par Google Play Billing, sur ton appareil et ton compte Google : Google
traite le paiement selon ses propres conditions, et l'application apprend seulement que l'achat
existe, qu'elle relit depuis Play pour te remercier. L'auteur ne voit ni ton nom, ni ton moyen de
paiement, et aucun reçu n'est envoyé nulle part, puisqu'il n'existe nulle part où l'envoyer.

---

## Si l'application plante

Elle écrit un rapport sur le téléphone — la version de l'application, la version d'Android, le
modèle de l'appareil et l'endroit du code où ça a cassé ; rien sur toi. Les réglages te proposent
ensuite de l'envoyer, par l'application de ton choix, après l'avoir lu en entier. Rien n'est envoyé
si tu ne l'envoies pas, et le rapport disparaît quand tu l'ignores.

---

## Autorisations, et pourquoi

Celles que tu verrais en inspectant une application :

| Autorisation | Pourquoi |
|---|---|
| Internet | Récupérer les fichiers du catalogue et les images décrites plus haut. |
| Notifications | Te prévenir qu'une personne ou une équipe que tu suis est programmée, ou qu'un événement suivi commence bientôt. Refuse-la et l'application fonctionne, sans notifications. |
| État du réseau, réveil, service au premier plan, démarrage terminé | **Apportées par la bibliothèque Android de travail en arrière-plan**, qui rafraîchit le catalogue et remet les rappels en place après un redémarrage. Utilisées pour rien d'autre. |
| Facturation | Celle de Google Play, pour le pourboire facultatif ci-dessus. |

Les notifications sont construites et affichées **sur l'appareil**, à partir de données qui s'y
trouvent déjà. Rien n'est envoyé pour les produire, et aucun service de push n'est utilisé.

Il n'y a **aucun outil d'analyse d'audience, aucun traqueur publicitaire et aucun outil de rapport
de plantage** dans ces applications. Pas désactivés — absents de la compilation.

---

## Ce que FanLog ne fait pas

- Pas de publicité, pas de régie, pas d'identifiant publicitaire.
- Pas de vente ni de partage de données avec des tiers.
- Pas de compte, pas d'adresse e-mail demandée, aucune connexion.
- Pas d'accès à la position, aux contacts, aux photos ni au micro.
- Rien de verrouillé derrière un paiement, et pas d'abonnement.

---

## Enfants

Les applications FanLog ne s'adressent pas aux enfants et ne collectent sciemment aucune donnée à
leur sujet : elles n'en collectent aucune, de personne.

---

## Tes droits

Tes données n'existent que sur ton appareil, donc c'est toi qui les contrôles, directement.
Désinstaller une application supprime tout ce qu'elle contient ; effacer son stockage dans les
réglages d'Android fait la même chose sans la désinstaller. Il n'y a rien à nous demander, parce
que nous ne détenons rien.

---

## Modifications

Cette politique change si les applications changent. La date en tête fait foi. Une application
ajoutée à la famille n'est couverte ici qu'une fois que cette page la nomme.

## Contact

Écris à [mferyapps@gmail.com](mailto:mferyapps@gmail.com).
