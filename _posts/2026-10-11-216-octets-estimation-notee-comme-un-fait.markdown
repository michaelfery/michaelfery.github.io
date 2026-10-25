---
layout: post
topics: [Android]
app: almanac
title:  "216 octets : l'estimation notée comme un fait"
description: "J'ai écarté une bibliothèque parce qu'elle était trop lourde. Une semaine plus tard, je l'ai mesurée : 216 octets. Sur l'erreur de noter une estimation comme un fait."
date:   2026-10-11 09:00:00.000 +02:00
tags: [Android, Méthode, Almanac, IA, Claude]
---

Cet été, en construisant [Almanac](/almanac/), une app Android d'entretien du logement, j'ai écarté une bibliothèque parce qu'elle était trop lourde. Une semaine plus tard, je l'ai mesurée. Elle coûtait **216 octets**.

C'est une petite histoire, mais elle illustre une erreur que je fais plus souvent que je ne voudrais l'admettre : **noter une estimation comme si c'était un fait**.

## Le 3 août : une phrase dans un ADR

Almanac affiche des listes de tâches d'entretien : purger les radiateurs, vérifier le détecteur de fumée, nettoyer les gouttières… La maquette posait une icône sur chaque ligne. Problème : les glyphes voulus, comme une vanne ou un thermostat, ne sont pas dans le jeu d'icônes de base de Jetpack Compose. Ils sont dans `material-icons-extended`, la bibliothèque complète.

L'ADR du système de design, écrit le 3 août, réglait la question en une phrase :

> `material-icons-extended` coûte une dizaine de mégaoctets pour une poignée. Reporté : soit on embarque les quelques vecteurs nécessaires, soit on s'en passe.

La phrase est plausible. La bibliothèque est réputée énorme : elle contient des milliers d'icônes, et quiconque l'a ajoutée à un projet a vu son build de debug enfler. Les icônes sont passées à la trappe, et la décision s'est rangée parmi les choses réglées.

Le problème, c'est le verbe : **« coûte »**. Pas « coûterait sans doute », pas « d'après ce qu'on lit ». La phrase a la forme d'une mesure, alors que c'était une estimation.

## Le 10 août : la mesure

Une semaine plus tard, la question des icônes est revenue : une icône par famille d'équipement (chauffage, eau, gaz, ventilation…) aiderait l'œil à parcourir une liste de trente-quatre lignes. Avant de rouvrir le débat, j'ai fait ce qui aurait dû être fait le 3 août : construire l'APK avec et sans la bibliothèque, et comparer.

| | Sans | Avec | Écart |
|---|---|---|---|
| APK release, R8 activé | 1 907 515 o | 1 907 731 o | **+216 o** |
| APK debug, sans réduction | 13 212 239 o | 19 948 380 o | +6,7 Mo |

En release, **216 octets**. La raison est connue, simplement je ne l'avais pas appliquée : R8, l'outil de réduction d'Android, retire tout le code qui n'est jamais appelé. Sur des milliers d'icônes, il ne garde que les sept qu'on utilise. Le coût pour l'utilisateur est de quelques centaines d'octets.

Le chiffre de « dix mégaoctets » n'était pas sorti de nulle part : c'est à peu près ce que prend le **build de debug**, qui n'est pas réduit (+6,7 Mo ici). Ce coût-là ne concerne que l'installation sur l'émulateur, et la compilation ne le paie qu'une fois. L'estimation mélangeait le coût pour le développeur et le coût pour l'utilisateur.

Les icônes sont arrivées le jour même : sept, une par famille, plus une générique.

## La petite ironie

En faisant la mesure, j'ai découvert autre chose : sur ce projet, **R8 était désactivé en release**. `isMinifyEnabled = false`, la valeur par défaut d'un projet neuf, jamais remise en question.

Autrement dit, le chiffre de 216 octets n'était vrai qu'à une condition que le projet ne remplissait pas encore. Sans R8, la bibliothèque aurait bien pesé ses mégaoctets dans l'APK de release. La mesure a donc tranché une question, et en a ouvert une meilleure : pourquoi l'APK de release n'était-il pas réduit ? C'est le genre de question qu'une estimation ne pose jamais.

## Ce que j'ai changé dans ma façon de noter

L'ADR n'a pas été réécrit en silence. La section d'origine a été remplacée par une révision qui dit ce qui s'est passé :

> La première version de cet ADR écartait la bibliothèque étendue au motif qu'elle « coûte une dizaine de mégaoctets pour une poignée » d'icônes. **C'était une estimation, pas une mesure, et elle était fausse.**

Et la leçon a rejoint une autre, apprise quelques jours plus tôt sur les mesures de marché. En comparant les apps concurrentes sur le Play Store, j'avais mesuré un mauvais paquet, au nom deviné, puis conclu qu'une application avait disparu, alors qu'elle était en ligne sous un autre identifiant. Le journal de décisions d'Almanac garde depuis une section « Erreurs à ne pas refaire », où l'on trouve notamment :

> Un paquet deviné n'est pas une mesure : sans identifiant confirmé, une absence de résultat ne dit rien.

Les deux erreurs ont la même forme : **une supposition écrite au présent de l'indicatif**. Une fois écrite ainsi, elle cesse d'être une hypothèse qu'on vérifiera, et devient un fait qu'on cite. Elle bloque des décisions qui n'auraient demandé qu'une mesure de cinq minutes.

## Quand c'est une IA qui relit vos ADR

Il y a une raison de plus d'être strict, et elle est récente. Je construis mes apps avec Claude Code, et un agent relit les ADR et le journal de décisions **à chaque session**. C'est ce qui lui donne le contexte du projet, et c'est exactement pour ça qu'ils doivent être encore plus vrais.

Un humain se souvient vaguement que « dix mégaoctets » était une estimation lancée en passant. Un agent qui ouvre l'ADR trois semaines plus tard n'a pas ce souvenir : il lit « coûte une dizaine de mégaoctets », il le tient pour acquis, et il construit dessus avec la même assurance que sur tout le reste. Une estimation mal notée ne s'éteint plus avec le temps. Elle se propage, de session en session, dans des décisions qui la citent sans le savoir.

Et l'IA est elle-même une source de ces phrases. Elle produit volontiers des estimations plausibles, au présent de l'indicatif, qu'il est tentant de recopier telles quelles dans un document. C'est pour ça que le journal de décisions d'Almanac a une colonne « Hypothèse » définie sans ambiguïté : *proposé par Claude, jamais validé. Ne pas traiter comme acquis.* Une proposition de l'assistant n'entre dans « Tranché » que quand je l'ai décidée explicitement.

Écrire pour un lecteur qui croit tout ce qu'il lit, c'est une bonne discipline. Elle sert aussi les lecteurs humains, à commencer par soi-même dans six mois.

## En pratique

J'en tire trois règles simples :

- **Un chiffre a une source.** Soit une mesure datée (« mesuré le 10 août sur ce dépôt »), soit le mot « estimation », écrit en toutes lettres.
- **Une décision fondée sur une estimation est marquée comme provisoire.** Dans le journal de décisions d'Almanac, ce qui n'a pas été vérifié vit dans une colonne à part, « Hypothèse », jamais dans « Tranché ».
- **Quand la mesure coûte moins cher que le débat, on mesure.** Deux builds et une soustraction, c'est moins long que de rediscuter une décision.

Et quand on mesure, on regarde aussi ce que la mesure révèle autour. Ici, la vraie trouvaille n'était pas les 216 octets : c'était le R8 désactivé.
