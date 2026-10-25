---
layout: post
title:  "Tranché, Hypothèse, Ouvert : tenir un journal de décisions sur un projet perso"
date:   2026-11-15 09:00:00.000 +01:00
tags: [Méthode, Almanac, IA, Claude]
published: false
---

Sur un projet personnel, on ne tient pas de réunion, on n'écrit pas de compte rendu, et personne ne vous demandera pourquoi vous avez fait tel choix il y a trois semaines. Personne, sauf vous… et, de plus en plus, l'assistant IA avec qui vous codez.

Pour [Almanac](/almanac/), mon app d'entretien du logement, j'ai tenu dès le premier jour un **journal de décisions**. Pas un document de spécification : un simple fichier Markdown, `docs/product/decisions.md`, qui dit ce qui est décidé, ce qui ne l'est pas, et pourquoi. Voici comment il est construit.

## Commencer par le but, et par le vrai risque

Le journal s'ouvre sur une section « But du projet », et elle compte plus que tout le reste :

> Faire exister une chose bien faite, utile à Michaël et utile à d'autres.

Suivent trois listes courtes. **Ce qui tombe** : la pression de croissance et d'échéance, pas de modèle payant au lancement, pas de calendrier subi. **Ce qui reste entier** : la barre d'utilité pour les autres, donc la justesse de ce que l'app affirme. **Un avantage de méthode** : être le premier utilisateur, ce qui permet de vérifier sur place tout ce qui est physique (la date est-elle lisible sur la chaudière ? l'organe est-il là où c'est écrit ?).

Et une phrase qui résume tout :

> Le risque principal est l'abandon. Pas le marché, pas la concurrence, pas la technique.

Sur un projet perso, c'est presque toujours vrai. Cette phrase a justifié de **couper court au cadrage** et de faire tourner quelque chose vite, plutôt que de peaufiner l'analyse jusqu'à l'essoufflement.

## Deux documents, deux natures de décision

Le dépôt sépare deux choses :

- **Le journal de décisions**, pour les décisions **produit** : le périmètre, le modèle économique, ce qu'on affiche ou pas.
- **Les ADR** (*Architecture Decision Records*), pour les décisions **techniques coûteuses à défaire**. Le critère d'admission est explicite : le choix d'une bibliothèque de dates ne mérite pas d'ADR, le choix d'un modèle de persistance, oui.

Les ADR ont quatre statuts (Proposé, Accepté, Remplacé, Abandonné) et une règle : **un ADR n'est jamais supprimé ni réécrit**. Un ADR abandonné reste là, « pour ne pas y revenir sans élément neuf ».

## Le cœur : trois statuts

Le journal classe chaque sujet dans l'une de trois sections, et l'en-tête dit sans détour que **la distinction est le but du document** :

- **Tranché** : décidé explicitement par moi.
- **Hypothèse** : proposé, jamais validé. *Ne pas traiter comme acquis.*
- **Ouvert** : identifié, pas encore traité.

Cette séparation n'était pas là par hasard. Elle est née d'une erreur, notée plus bas dans le même document : une recommandation de l'assistant, « France seule au lancement », avait été reprise d'échange en échange comme un acquis, alors que je ne l'avais jamais validée. Une proposition répétée finit par ressembler à une décision. La colonne *Hypothèse* existe pour que ça ne se reproduise pas.

### Une décision tranchée a une raison

Chaque ligne « Tranché » est un tableau à trois colonnes : le sujet, la décision, la raison. Quelques exemples :

| Sujet | Décision | Raison |
|---|---|---|
| Export des données | **Jamais payant** | Sans compte ni synchronisation, l'export est le filet de sécurité de l'utilisateur. Faire payer le confort, jamais l'échappatoire. |
| Affiliation | **Écartée** | Une commission introduirait un intérêt à la recommandation, dans un produit dont le seul actif est de dire vrai. |
| Palier payant | **Purement additif, jamais soustractif** | Ajouter un paywall sur une base gratuite provoque une réaction ; ajouter une fonctionnalité, non. |

La raison est ce qui permet de **ne pas rouvrir la question** à chaque fois qu'elle se représente. Quand une maquette a proposé plus tard un bouton « trouver un pro près de chez moi », je n'ai pas eu à en débattre : il tombait sous la décision sur l'affiliation, déjà prise et déjà justifiée.

### Une décision tranchée peut porter sa propre condition de retour

C'est sans doute la pratique la plus utile du journal. Certaines décisions ne valent que sous une condition, et le journal l'écrit.

L'écran « Mon logement » applique les changements immédiatement, sans bouton « Enregistrer ». Le journal précise :

> Condition explicite : cela ne vaut que parce que retirer un équipement ne touche pas à l'archive. Si un jour retirer un équipement supprime, masque ou rend inaccessible une intervention enregistrée, il faut revenir à une validation explicite.

Le jour où quelqu'un, moi ou un assistant, voudra faire en sorte que retirer un équipement efface son historique, le journal dira de lui-même qu'il faut rouvrir la décision. Une décision sans sa condition se défend mal ; une décision avec sa condition se défend seule.

### Une décision tranchée peut aussi porter sa réserve

Autre exemple : l'ajout d'une tâche à l'agenda du téléphone. Décision : disponible sur toute tâche, sans aucune permission. Réserve, écrite juste en dessous : *deux systèmes suivent alors la même chose*, et Almanac ne saura pas si la tâche a été faite. Mitigation, pas résolution.

Écrire la réserve, c'est admettre qu'on a choisi en connaissance de cause. Ça évite de redécouvrir le problème dans trois mois en croyant tomber sur un bug.

### Ce qui est ouvert le reste honnêtement

La section « Ouvert » est courte. Un exemple :

> Q5 — Ce qui fait ouvrir l'application. Se tranchera par l'usage réel plutôt que par l'analyse.

Certaines questions ne se règlent pas en réfléchissant plus. Les noter comme ouvertes, avec la façon dont elles se trancheront, évite de leur inventer une réponse.

## Ce qui a été mesuré, daté

Le journal a aussi une section « Mesuré » : les installations des apps concurrentes sur le Play Store, **relevées le 1er août 2026, avec le même protocole**. Et juste en dessous, ce qui n'a **pas** pu être mesuré : les volumes de recherche, faute d'accès aux outils. L'indice qu'on a quand même est noté comme « directionnel seulement ».

Un chiffre sans date ni protocole devient vite un fait qu'on cite sans savoir d'où il sort. J'ai raconté dans [« 216 octets »](/2026/10/216-octets-estimation-notee-comme-un-fait) ce que coûte une estimation notée comme une mesure.

## Erreurs à ne pas refaire

C'est la section la plus utile, et la plus inconfortable à écrire. En voici trois :

- **Consigner une recommandation comme une décision.** C'est l'histoire de « France seule », et la raison d'être de la colonne Hypothèse.
- **Demander un type plutôt qu'une présence.** Le questionnaire demandait le « type de chauffage », ce qui supposait une exclusivité que la réalité n'a pas : on peut avoir des radiateurs *et* un poêle. Des tâches disparaissaient en silence.
- **Sur-extrapoler à partir d'une seule réponse.** « Je serai le premier utilisateur » avait été lu comme « le produit n'est que pour lui », d'où un repli excessif sur toutes les questions d'audience. La leçon notée : *une réponse recalibre un paramètre, elle ne redéfinit pas le projet.*

Ces erreurs ne sont pas des fautes de code. Ce sont des erreurs de raisonnement, et c'est précisément pour ça qu'elles méritent d'être écrites : aucun test ne les attrapera.

## Quand on change d'avis

Un journal de décisions n'interdit pas de changer d'avis. Il interdit de le faire en silence.

Le 11 août, j'ai adopté une maquette qui contredisait trois décisions prises deux jours plus tôt. L'ADR du design a gardé ses sections d'origine et reçu une révision datée ; le journal, lui, a reçu une ligne qui nomme les revirements comme tels, avec la condition qui les rend acceptables. Je l'ai raconté en détail dans [« Refondre une app sur une maquette IA »](/2026/11/almanac-refonte-maquette-trois-revirements). La règle tient en une phrase du dépôt : un revirement sans sa raison d'origine n'est pas relisible.

Les décisions tranchées sont aussi **datées et signées** quand elles tombent en cours de route : « écartée par Michaël le 10 août 2026 », « tranché par Michaël le 11 août 2026 ». Ça paraît cérémonieux pour un projet perso. C'est surtout ce qui permet, plus tard, de savoir si une ligne est une décision ou un souvenir.

## Et l'IA dans tout ça

J'ai construit Almanac avec Claude Code, et ça change le statut du journal. L'assistant le relit à chaque session : c'est sa mémoire du projet. Il lit « Tranché » comme une contrainte et « Hypothèse » comme une piste. Dans mon journal, la définition de la colonne Hypothèse est d'ailleurs explicite : **proposé par Claude, jamais validé**.

Les trois statuts prennent alors un sens très concret. Un assistant propose beaucoup, et bien. Mais ce qu'il propose n'est pas décidé tant que je ne l'ai pas tranché, et le document doit le dire, pour lui comme pour moi. Le journal est le seul endroit où la frontière entre ce que l'IA a suggéré et ce que j'ai choisi reste visible.

## Pour démarrer le vôtre

Le gabarit minimal tient en quelques lignes :

```markdown
# Journal de décisions

## But du projet
Ce qu'on veut, ce qui tombe, ce qui reste entier, le vrai risque.

## Tranché
| Sujet | Décision | Raison |

## Ouvert
| Sujet | État, et comment ça se tranchera |

## Hypothèses — proposées, non validées
| Sujet | Ce qui a été supposé | À trancher |

## Mesuré
Ce qui a été relevé, quand, comment. Et ce qui n'a pas pu l'être.

## Erreurs à ne pas refaire
```

Trois règles pour le tenir :

- **Une décision a une raison**, et si elle ne vaut que sous une condition, la condition est écrite.
- **Ce qui n'est pas décidé ne va jamais dans « Tranché »**, même si tout le monde fait comme si.
- **On ne réécrit pas le passé** : on ajoute, on date, on dit pourquoi on change d'avis.

C'est un petit effort à chaque décision. En échange, on ne retranche jamais deux fois la même question, et on sait toujours pourquoi l'app est comme elle est.
