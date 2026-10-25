---
layout: post
topics: [IA]
app: luciole
title:  "Relire une histoire qui n'existe pas en un seul exemplaire"
description: "Comment relire une histoire qui existe en 48 versions : des relecteurs IA, des scripts, une jurisprudence et un petit studio."
date:   2026-11-22 09:00:00.000 +01:00
img: posts/luciole-studio-comparaison.png
tags: [Luciole, Écriture, IA, Claude, Android]
published: false
---

[Luciole](/luciole/) est une application d'histoires du soir : un parent lit l'histoire à voix haute, et c'est l'enfant qui décide de la suite. Chaque histoire se personnalise : le prénom de l'enfant, l'accord (il ou elle), le caractère du héros, le compagnon, le décor, la langue.

J'écris ces histoires moi-même. Mais très vite, je me suis heurté à un problème que je n'avais pas vu venir : **je ne pouvais pas les relire**. Pas faute de temps ou d'envie, mais parce qu'une histoire Luciole n'existe pas en un seul exemplaire.

Cet article raconte l'atelier de relecture que j'ai construit autour de cette écriture : des relecteurs IA, des scripts, une jurisprudence et un petit studio. Je le dis franchement : **sans ces relecteurs, Luciole n'aurait pas pu exister**. L'écriture, les choix et les corrections restent les miens. L'IA m'apporte un harnais supplémentaire, qui attrape les incohérences, les ruptures de continuité et les faux pas de ton à côté desquels on passe forcément quand on relit seul une histoire à choix.

## Une histoire, des centaines de textes

Voici la même histoire, « La carte dans la bouteille », lue avec deux configurations différentes : à gauche, un héros courageux, un perroquet et une crique ; à droite, un héros curieux, un crabe et une épave. Le prénom est le même, et les choix aussi.

![Le studio de Luciole compare deux configurations d'une même histoire : 66 % du récit diffère](/assets/img/posts/luciole-studio-comparaison.png)

**66 % du récit diffère d'un côté à l'autre.** Les paragraphes grisés sont communs, tout le reste change. Multipliez maintenant les héros par les compagnons, les décors, les accords, les langues, puis par les chemins de choix : une seule histoire produit des centaines de textes. Et l'accord ne change pas qu'un pronom. Selon que le prénom commence par une voyelle ou non, c'est l'élision qui change, et une phrase parfaite pour « Naïa » peut devenir bancale pour « Alix ».

Un linter vérifie des règles : longueur des phrases, mots interdits, structure des choix. Mais il ne lit pas. Les défauts qui restent ne se voient qu'en **lisant une version entière, dans l'ordre** : une couture maladroite entre deux fragments, une voix qui faiblit sur un seul caractère de héros, une peur qui n'est pas résolue sur un chemin. Et personne ne relit des centaines de versions.

## Première version : un relecteur par chemin

Ma première réponse a été frontale : **24 lectures par histoire**, une par chemin de choix, chacune avec une configuration différente. Les axes tournent avec un décalage, pour que chaque valeur (chaque héros, chaque compagnon, chaque décor) soit lue au moins deux fois, et jamais deux fois dans la même configuration. Quatre prénoms alternent, dont deux commencent par une voyelle.

Chaque lecture est confiée à un relecteur IA qui reçoit le brief de l'histoire, une grille d'âge et le texte tel que l'enfant l'entendra. Il ne voit **jamais** le code source de l'histoire, ni ce que les autres relecteurs ont dit. Les 24 retours sont ensuite consolidés par récurrence : un défaut vu par plusieurs relecteurs, sur des configurations différentes, est rarement un hasard.

Ça marchait. Et c'était intenable.

## Ce que ça coûte, mesuré

Trois passes de relecture sur « La nuit qui appelle » ont consommé l'essentiel d'un quota hebdomadaire. Chaque relecteur coûtait entre 50 et 90 000 tokens, dont une bonne part passée à ouvrir des fichiers : la consigne, trois références, le brief, trois fiches de personnages, puis enfin le texte.

Les corrections ont été très terre à terre :

- **Un dossier par lecture**, assemblé par un script : la consigne, le brief, les extraits utiles, le texte. Le relecteur fait deux opérations, lire son dossier et écrire son retour.
- **Un modèle plus léger** (Sonnet) pour les relecteurs, le tri restant à l'orchestrateur.
- **Des passes ciblées** entre deux passes complètes : on ne relit que les chemins qui traversent ce qui a été retouché.
- **Un seul réveil** : un workflow lance tous les relecteurs et rend un résultat unique, au lieu de réveiller l'orchestrateur 24 fois.

Ordre de grandeur : d'environ 1,5 million de tokens par passe complète à environ 300 000.

Puis une autre mesure m'a surpris. Un relecteur lisait son dossier en une seconde… et réfléchissait ensuite plus de cinq minutes, pour un retour d'une page. La consigne demandait deux lectures, dont une « phrase par phrase », et l'agent recopiait le texte dans sa réflexion. Une seule lecture, l'effort de réflexion réglé au minimum, une consigne qui borne la délibération : **de 22 à 31 000 tokens et de 5 à 16 minutes par relecteur, on est passé à environ 9 000 tokens et 100 secondes**, avec pour l'essentiel les mêmes défauts trouvés.

La leçon : **le temps d'un relecteur, c'est sa réflexion**. Tout ce qui se compte doit sortir de sa consigne et aller dans un script : les phrases de plus de 25 mots, les mots répétés à courte distance, les mots interdits, les tics. Un relecteur n'est utile que pour ce qu'un script ne sait pas voir.

## Quand les corrections créent les défauts

Sur « Le dragon qui hoquette », j'ai suivi le nombre de défauts bloquants passe après passe :

```text
41 → 20 → 8 → 2 → 4 → 1 → 0 → 3
```

La courbe descend, puis se met à osciller. En regardant de près, la cause était claire : après la cinquième passe, presque tous les nouveaux défauts portaient sur **les phrases que j'avais écrites pour corriger les précédents**. Le texte d'origine était assaini ; c'étaient mes rustines qui fuyaient.

Une correction écrite dans un fichier source, vérifiée par une recherche de texte, n'est pas une correction relue : le parent, lui, lit un écran, avec ce qui précède et ce qui suit, pour un prénom et un héros donnés. D'où trois règles :

- **Une correction se relit dans son écran**, sur huit configurations qui couvrent chaque valeur de chaque axe. C'est là qu'on voit le pronom qui change de référent ou la redite avec le fragment voisin. L'outil signale aussi une correction qui n'atterrit dans aucun écran, ce qui arrive plus souvent qu'on ne croit.
- **Un lot de corrections est relu avant d'être commité**, par un relecteur dédié qui voit chaque phrase avant et après, dans son écran. Il rend un verdict par correction : bonne, à revoir, dangereuse. Coût : environ 30 000 tokens, contre plus d'un million pour une passe complète.
- **On retire avant d'ajouter.** Quand un défaut révèle un trou (un objet jamais nommé, une cause manquante), la meilleure correction supprime souvent la phrase qui creuse le trou. Une phrase inventée pour le combler est la première source de régression.

Et un nouvel indicateur : **le nombre de défauts trouvés dans du texte non touché depuis la passe précédente**. Quand il tend vers zéro et que les nouveaux défauts ne portent plus que sur les corrections, le texte est stable : c'est la main qui corrige qu'il faut surveiller, pas le texte.

## La jurisprudence : ne pas trancher deux fois

Un autre phénomène usait la boucle : **les mêmes remarques revenaient à chaque passe**. Un mot comme « cime », le prénom de l'enfant répété au début : quatre fois chacun, relevés par des relecteurs qui ne pouvaient pas savoir que j'avais déjà répondu.

Tout ce que j'écarte entre désormais dans un fichier, `deja-tranche.md`, une **jurisprudence**. Elle est relue avant chaque tri, et un relecteur qui pense qu'un cas mérite d'être rouvert peut le dire en une ligne, sans en faire un constat. Elle peut aussi être propre à une histoire : la « blague du hérisson » du dragon, injectée par erreur partout, avait fait chercher à quatre relecteurs d'une autre histoire une blague qui n'y avait jamais existé.

La jurisprudence grossit à chaque boucle et sert toutes les histoires. C'est elle qui empêche la relecture de tourner à vide.

## La version allégée : des lecteurs naïfs et une oreille

Même optimisée, la grande relecture restait lourde, mécanique et parfois trop exigeante. Une passe complète en anglais prenait encore 35 minutes et 1,6 million de tokens, et la plupart des remarques « importantes » comparaient le texte à une fiche, pas à ce qu'on entend.

La relecture d'aujourd'hui tient en **six relecteurs au lieu de 24**, aux rôles bien séparés :

- **Quatre relecteurs de continuité** lisent chacun six chemins à la suite. Lire plusieurs chemins d'affilée aide justement à voir ce qui change de l'un à l'autre. Ils ne reçoivent que ce que l'histoire promet : les choix, les fins, le moment de peur, l'état du monde.
- **Un lecteur-enfant** lit trois versions, le texte seul, et dit ce qu'il retient et où il décroche.
- **Une oreille** lit trois autres versions et n'écoute que la voix : ce qui explique, ce qui fait notice, ce qui sonne fabriqué.

L'oreille est là pour une raison précise : **les lecteurs seuls poussent le texte vers l'explicite**. Chaque fois qu'un lecteur ne comprend pas, la tentation est d'ajouter une phrase qui explique. Répétée sur des dizaines de remarques, elle use la voix d'une histoire entière. L'oreille fait contrepoids, et ses remarques se corrigent toujours en retirant, jamais en ajoutant.

Quelques règles complètent le dispositif :

- **Un défaut est retenu s'il est vu par au moins deux relecteurs**, ou s'il s'agit d'une faute de sens : une contradiction, un choix trahi, une peur non résolue. Un défaut vu par un seul lecteur désigne souvent un public, pas un défaut.
- **Une confusion ne se corrige jamais par une phrase qui explique.**
- **Candide** lit l'histoire résumée en dix lignes, avec ses choix et ses fins, et pose les questions d'un enfant avant que je valide le brief. C'est moins cher de casser un brief que de réécrire une histoire.
- **Des outils sans histoire.** Les consignes des relecteurs ne citent aucun personnage ni aucune réplique d'une histoire réelle. Sinon, l'exemple d'une histoire devient la norme de la suivante.

Et surtout : **ma lecture clôt la boucle**. Une fois que je relis l'histoire et qu'elle me plaît, on ne corrige plus que de vrais bugs de continuité, jamais le style. Sans cette règle, une relecture automatique trouve toujours quelque chose.

## Le studio : trancher sans jongler

Tout cela produit des rapports, et il faut les trancher. Pour ne pas jongler entre un fichier, un terminal et un téléphone, j'ai construit **Luciole Studio**, une petite application macOS qui fait tourner les histoires sur le vrai moteur de l'app.

- On y écrit les briefs, les fiches et l'histoire elle-même, compilée à chaud.
- On lit l'histoire dans n'importe quelle configuration : langue, accord, prénom, héros, compagnon, décor, avec les choix.
- **⌘K** affiche deux lectures côte à côte, comme dans la capture plus haut. On peut lier les choix des deux côtés pour isoler l'effet d'un seul axe.
- Les **rapports de relecture se tranchent dans le studio** : pour chaque proposition, faire, ne pas faire ou défaire, avec une note. Mes décisions s'écrivent à côté du rapport et passent avant tout le reste au tour suivant.

## Le parcours au sort

Les premiers retours de parents ont fait remonter des défauts qu'aucune passe n'avait vraiment ciblés : des tournures correctes que personne ne dit à voix haute, de petits mots faux, des avalanches de phrases courtes hors des moments où la cadence le justifie. Relancer une passe complète pour ça aurait été disproportionné.

D'où le **parcours au sort**. Un script tire une histoire publiée, une configuration, un prénom et un chemin, avec une graine pour pouvoir le rejouer. Un relecteur le lit d'une traite, « comme un parent qui lit beaucoup d'albums et qui sent tout de suite quand une phrase n'est pas de sa bouche ». La consigne liste ce qu'il vérifie en premier :

- l'avalanche de phrases courtes hors des moments de cadence ;
- la formulation étrange, correcte mais que personne ne dirait ;
- le mauvais petit mot : un article défini là où on attend un possessif, un pronom dont on ne sait pas à qui il renvoie ;
- la phrase d'inventaire, qui pose le décor sans qu'un geste le regarde ;
- le tic, le mot qui revient d'un écran à l'autre ;
- l'explication, la phrase qui résume ce qu'on vient de lire ;
- la redite de continuité, comme un objet cherché alors qu'on vient de le voir.

Il rend **huit propositions au plus**, chacune citée mot pour mot et localisée dans chaque langue de l'histoire, plus trois moments « à ne pas toucher ». Il ne corrige rien : je tranche dans le studio, et une correction faite en français seulement désynchroniserait la version anglaise, d'où l'exigence des deux langues.

C'est devenu le rythme de croisière : **42 parcours au sort** relus à ce jour sur les neuf histoires des trois univers (la forêt, les pirates, l'hiver), et leurs corrections retenues livrées au fil des versions.

## Et un relecteur humain

Pour finir, un aveu utile. La version 1.0.3 intègre les remarques d'un **relecteur externe, humain**, sur « La nuit la plus longue ». Ce qu'il a apporté ne relevait pas de l'incohérence : une mélodie fredonnée par le morse, un oiseau qui tourne la tête comme pour montrer le chemin, une réplique comme « Alors on ne l'a jamais invité. Et lui, il attendait. » Des choses qui manquaient, plutôt que des choses fausses.

C'est la limite du harnais, et je la trouve rassurante. Les relecteurs IA trouvent les incohérences, les ruptures, les faux pas. Ils ne trouvent pas ce qui manque au cœur d'une histoire. Ça, c'est le travail de l'auteur et des lecteurs.

## Ce que j'en retiens

- **Écrire une histoire à choix seul, c'est accepter de ne jamais lire la plupart de ses versions.** Un harnais de relecture n'est pas un luxe, c'est ce qui rend le format praticable.
- **Mesurer avant d'empiler des agents.** Tokens, minutes, nombre de défauts par passe : sans ces chiffres, j'aurais conclu que la relecture était trop chère, alors qu'elle était mal réglée.
- **Un relecteur trouve, il ne corrige pas.** La décision et la correction restent à l'auteur.
- **Tout ce qui se compte va dans un script.** Le relecteur garde ce que seul un lecteur peut voir.
- **Garder une jurisprudence.** Ce qui a été tranché ne doit pas être tranché deux fois.
- **Faire contrepoids à l'explicite.** Un lecteur qui ne comprend pas pousse toujours vers une phrase de plus ; il faut une oreille pour en retirer.
- **Le dernier mot reste humain** : celui de l'auteur, et celui des parents qui lisent les histoires à voix haute, le soir.

[Luciole](/luciole/) est disponible sur Android, sans compte, sans publicité et sans connexion.
