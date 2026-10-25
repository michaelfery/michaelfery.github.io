---
layout: post
topics: [IA, Android]
app: almanac
title:  "Refondre une app sur une maquette IA : trois revirements et quatre refus"
description: "Refaire toute l'interface d'Almanac à partir d'une maquette Claude Design, sans renier trois décisions prises deux jours plus tôt."
date:   2026-11-01 09:00:00.000 +01:00
tags: [Android, Design, Claude, IA, Almanac]
---

[Almanac](/almanac/) est une application Android qui rappelle ce qu'il faut entretenir chez soi, quand et comment : la chaudière, la VMC, les gouttières, les détecteurs de fumée… Je l'ai construite cet été, et j'en suis le premier utilisateur.

Le 11 août, j'ai fait quelque chose qu'on déconseille en général à ce stade : j'ai refait toute l'interface d'un coup, à partir d'une maquette produite avec **Claude Design**. Une refonte de 37 fichiers, 3 266 lignes ajoutées, 1 307 retirées. Et surtout, une refonte qui contredisait trois décisions que j'avais prises… deux jours plus tôt.

Cet article raconte comment on adopte une maquette sans renier ce qu'on avait décidé, et pourquoi le plus important n'a pas été ce que j'ai pris, mais ce que j'ai refusé et la façon dont je l'ai écrit.

## Avant la maquette : un système de design très argumenté

Almanac a été cadré comme un vrai produit, même s'il reste un projet personnel. Toutes les décisions vivent dans le dépôt : un **journal de décisions** produit, et des **ADR** (*Architecture Decision Records*) pour ce qui est structurant. L'un d'eux, l'ADR-0006, fixe le système de design.

Sa direction tenait en trois principes :

1. **L'accueil ouvre sur le peu, jamais sur le total.** « Trois choses en octobre », pas « 23 tâches ».
2. **Ce qui se fait une seule fois n'a pas la même forme que ce qui revient.** Une découverte est une carte, une tâche récurrente est une ligne.
3. **La tâche est nommée par le geste, pas par l'objet.** « Trouvez le bouton test » plutôt que « Tester le disjoncteur ».

Et un pari : **la couleur est une ressource rare**. Une tâche ordinaire n'en reçoit aucune.

Pour affiner l'accueil, j'avais étudié les captures d'une app concurrente, NestIQ. J'y avais pris de bonnes idées (une seule carte mise en avant, des coins plus arrondis) et j'en avais refusé d'autres, par écrit :

- **la barre d'onglets** en bas de l'écran : je préférais un menu à trois barres, plus discret ;
- **le score de santé du logement** : un cadran « 77 / Home Health Score » sans explication, avec un montant de « réparations évitées » sorti de nulle part. Une métrique de vanité ;
- **un personnage qui félicite** : le mien, un artisan en salopette, avait pour règle « il n'encourage pas, il sait ». Jamais de félicitations, de badge ni de point d'exclamation.

C'était le 9 août.

## La maquette

Entre-temps, j'avais travaillé l'interface dans Claude Design, l'outil de maquettage d'Anthropic, en partant de mon brief. La maquette produite, `Almanac.dc.html`, était nettement plus aboutie que mon écran du moment : une typographie ronde et très grasse (Nunito), une palette par saison, une navigation à cinq onglets, un questionnaire d'installation ramené de huit écrans à cinq, une fiche de tâche avec étapes numérotées et outils nécessaires.

Elle contenait aussi, en bonne place, exactement ce que j'avais refusé quarante-huit heures plus tôt : la barre d'onglets, un score de santé et un personnage qui félicite.

Deux options se présentaient : rejeter la maquette au nom de mes décisions, ou l'importer et faire comme si mes décisions n'avaient jamais existé. J'ai choisi une troisième voie : **l'adopter, et écrire chaque revirement comme tel, avec la condition qui le rend acceptable**.

## Trois revirements, chacun avec sa condition

### 1. La barre d'onglets

Cinq onglets en bas - Accueil, Tâches, Matériel, Historique, Profil - remplacent le menu à trois barres.

Ce que ça coûte : tout ce que l'application sait faire devient visible en permanence. Ce que ça rapporte : l'historique et la liste du matériel sortent du fond d'un tiroir, alors que ce sont précisément eux qui font revenir dans l'app. La phrase qui a emporté la décision :

> Un menu caché est une fonctionnalité qu'on paie sans qu'elle serve.

### 2. Le score de santé

C'était le refus le plus net, en tête des alternatives écartées. Je l'ai adopté, mais sous une condition explicite : **ce score-ci mesure quelque chose, et il le dit**. « 81 » doit pouvoir se traduire en une phrase, du type « 5 échéances dépassées sur 27 suivies ». Le score de NestIQ ne disait pas d'où il sortait, et c'est ce qui en faisait une vanité.

> Un score qu'on ne peut pas expliquer en une phrase est une vanité.

Même logique pour le **niveau** et les **distinctions** que proposait la maquette. Le niveau vient du nombre d'interventions consignées, rien d'autre : pas de points, pas de multiplicateur, pas de série de jours à ne pas rompre, et il ne redescend jamais. Chaque distinction nomme un fait vérifiable : « Rien en retard » se vérifie dans la liste, « Sécurité à jour » dans l'historique.

> Une distinction qu'on ne peut pas rattacher à quelque chose de vrai est une flatterie, et une flatterie use la confiance.

### 3. Le personnage qui félicite

Il félicite désormais, mais dans son registre : **il constate une conséquence, il ne décerne pas un compliment**. « Bien joué » serait de la flatterie. « Ça ne vous coûtera rien cet hiver » est un fait de métier.

## Quatre refus

Adopter une maquette ne veut pas dire tout prendre. Quatre éléments ont été refusés, chacun parce qu'il tombait sous une décision déjà prise :

- **« Bonjour Camille. »** La maquette saluait l'utilisateur par son prénom. Mais le questionnaire ne le demande jamais, et l'inventer serait une familiarité que l'application n'a pas gagnée. La salutation reste sans nom.
- **Trois niveaux de gravité colorés**, avec un délai annoncé et un fond rouge. Le guide éditorial d'Almanac demande de décrire la conséquence réaliste, jamais le pire cas. Encadrer une conséquence de rouge fait passer du savoir à l'alarme, et le nom même du produit promet l'inverse.
- **Le bouton « Trouver un pro près de chez moi ».** L'affiliation avait été écartée dès le cadrage : une commission introduirait un intérêt à la recommandation, dans un produit dont le seul actif est de dire vrai. L'information « demande un professionnel certifié » reste ; l'orientation commerciale, non. Ce bouton-là, je ne l'ai même pas soumis au débat : il a été signalé plutôt que construit.
- **Les photos et numéros de série sur les fiches de matériel.** C'est le début d'un inventaire à saisir, exactement ce que l'écran de première ouverture promet de ne pas imposer.

## Écrire un revirement pour qu'il reste lisible

La partie la plus utile de cet épisode n'est pas visuelle, elle est documentaire.

La tentation, après un revirement, c'est de **réécrire l'ADR** pour qu'il dise la nouvelle décision, comme si elle avait toujours été là. Je ne l'ai pas fait. L'ADR-0006 a gardé ses sections d'origine, et il a reçu à la fin une **révision datée du 11 août** qui prime sur elles en cas de contradiction. Le message de commit le dit ainsi :

> Un revirement sans sa raison d'origine n'est pas relisible : il faut pouvoir lire pourquoi la barre d'onglets et le score avaient été refusés, puis pourquoi ils sont adoptés.

Concrètement, la révision contient trois parties :

- **Ce qui change** : chaque revirement est nommé comme un revirement, avec sa condition ;
- **Ce qu'on refuse à la maquette** : les quatre refus, chacun rattaché à la décision qu'il respecte ;
- **Ce qui n'a pas bougé** : les marqueurs gardent leurs couleurs propres hors du thème saisonnier, les listes se groupent par *quand* et jamais par domaine, aucun état ne s'appelle « en retard ».

Le journal de décisions produit, lui, distingue trois statuts : **Tranché** (décidé par moi), **Hypothèse** (proposé par Claude, jamais validé) et **Ouvert**. Il garde aussi une section « Erreurs à ne pas refaire », dont la première est : *consigner une recommandation comme une décision*. C'est exactement le piège qu'une belle maquette tend : elle a l'air décidée parce qu'elle est finie.

## Le revirement en petit : un écran de lancement qui mentait

Le même jour, un dernier commit a corrigé un détail qui résume l'esprit de la refonte : **l'écran de lancement ne ment plus sur la saison**.

L'app change d'accent de couleur selon la saison. Mais l'écran de lancement système est affiché par Android *avant* que la moindre ligne de code de l'application ne tourne, donc avant qu'elle sache quelle saison est enregistrée. Il restait donc vert, la couleur de l'ancien thème unique, même en automne ou en hiver. Le rendre saisonnier demandait une machinerie lourde (un alias d'activité par saison, activé au fil de l'année) qui risque de faire disparaître le raccourci de l'écran d'accueil, tout ça pour quatre cents millisecondes.

La solution a été de lui faire porter le crème neutre de la page, identique en toutes saisons : un écran qui n'affirme rien, plutôt qu'un écran qui affirme faux. La formule de l'ADR :

> Un mensonge de quatre cents millisecondes reste un mensonge.

## Et aujourd'hui ?

Ce travail a eu lieu sur le prototype. Trois jours plus tard, j'ai reconstruit l'application proprement dans un nouveau dépôt, et c'est cette version qui est sur le Play Store. Les revirements y sont restés : les onglets en bas (Profil est depuis devenu Réglages) et le score de santé, dont un appui ouvre le détail du calcul.

![L'accueil d'Almanac : le score de santé, les tâches du mois et les dates à relever](/almanac/assets/shots/fr/01-home.webp){: width="320" }

Le système de design a continué d'évoluer, avec la même méthode. Les points colorés de gravité, refusés le 11 août, sont revenus sous une forme strictement encadrée : un point, réservé à noter une seule chose et à la feuille de calcul du score, et explicitement **pas** une palette d'état générale. Le document de design note qu'on a essayé de colorer toute une liste avec, et que la liste « s'est mise à se disputer avec le thème ». Un refus peut lui aussi être révisé, à condition d'écrire pourquoi.

## Ce que j'en retiens

- **Une maquette finie a l'air décidée. Elle ne l'est pas.** Elle reste une proposition, et c'est à vous de la trier.
- **Un revirement n'est pas un échec**, à condition de lui donner une condition. « Oui au score, *parce qu'il* dit ce qu'il compte » est une décision. « Finalement, oui au score » est une capitulation.
- **Écrivez ce que vous refusez, pas seulement ce que vous prenez.** Les refus sont la partie la plus utile de la révision : ce sont eux qui disent ce que le produit est.
- **Ne réécrivez pas l'histoire de vos décisions.** Ajoutez une révision datée. Dans six mois, c'est la seule façon de comprendre pourquoi l'app est comme elle est.

[Almanac](/almanac/) est gratuit, sans publicité et sans compte. Si vous l'essayez, je suis preneur de vos retours.
