---
layout: post
topics: [Cloud & DevOps]
title:  "Azure Exams : Comment se préparer ?"
date:   2020-02-24 07:00:00.000 +01:00
# categories: blog
# description: 
img: posts/azure-badges.png
tags: [Azure, Pluralsight, Microsoft, AZ-203, AZ-204, Developer Associate, DevOps Expert]
---

Hello,

Pour 2020, je me suis fixé quelques objectifs. On ne peut pas vraiment parler de bonnes résolutions mais il s'agit plutôt de validation des acquis et de mise en ordre de mes compétences.

Pour commencer, j'ai donc décidé de passer quelques certifications Microsoft.
Je n'en avais pas passé depuis des années et, on ne va pas se mentir, la valeur des certifications de l'époque n'était pas reconnue. Je pense d'ailleurs à juste titre.

En effet, il s'agissait principalement d'enchaîner des dizaines de questions aussi absurdes les unes que les autres et qui montraient rarement autre chose que le bachotage bête et méchant des mêmes questions trouvées sur internet.
Avoir ces certifications ne nous différenciait donc pas des tricheurs et ne validait même pas une réelle compétence globale.

Alors pourquoi me relancer dans les certifications me direz-vous ?

## L'approche basée sur des rôles

J'ai été séduit par la nouvelle approche de Microsoft sur les certifications et les rôles associés.
Cette nouvelle stratégie annoncée à Ignite 2018 est intitulée **Microsoft role-based certifications**.

Quelques exemples :

* Décrocher la certification AZ-203 vous octroira un badge "Azure Developer Associate"
* Les certifications AZ-300 et AZ-301 cumulés vous permettront d'accéder au titre de ["Azure Solutions Architect Expert"](https://docs.microsoft.com/fr-fr/learn/certifications/azure-solutions-architect)
* La certification AZ-400, associée au badge Developer Associate ou Administrator Associate, débloquera le titre de ["Azure DevOps Engineer Expert"](https://docs.microsoft.com/fr-fr/learn/certifications/azure-devops)

![Azure DevOps Expert Path](/assets/img/posts/az-devops-expert-path.png)

Cette approche me paraît saine puisque les examens ne valident plus la connaissance d'un outil ou d'un service Azure mais bien d'un rôle global.

## La préparation

Comme je vous l'ai dit, 2020 est pour moi l'année de la validation des acquis.
Il ne s'agissait pas pour moi de développer de nouvelles compétences mais de valider celles que j'avais déjà, ou tout du moins que je pensais avoir.
La sélection des examens que j'ai choisi de passer s'est faite dans ce sens.

### AZ-400

J'ai passé le plus clair de mon temps en 2019 à la définition des stratégies de migration DevOps, à la mise en place d'usine logicielle et à l'accompagnement des équipes de développeurs dans l'autonomisation sur les pipelines de CI/CD. Il me semblait donc évident de commencer par l'examen [AZ-400 : Microsoft Azure DevOps Solutions](https://docs.microsoft.com/fr-fr/learn/certifications/exams/az-400).

Cet examen a l'avantage de traiter du DevOps sans toutefois se cantonner à Azure DevOps. **Jenkins, SonarQube et Terraform, par exemple, seront de la partie**. Il faut d'ailleur lire le titre de cette manière **"Azure - DevOps Solutions"** et non "Azure DevOps - Solutions". Subtil hein ?!

Pour préparer cette certification, je ne savais pas trop par où commencer.
Il existe énormément de sites vous expliquant quelles pages de la **doc Microsoft** il faut lire, quels path suivre sur le site **MS Learn** ou encore quels livres acheter.

J'ai donc lu les compétences mesurées et vu que mes compétences étaient assez transverses. Pas vraiment de trou flagrant dans la raquette. Il n'empêche que j'avais besoin de me rassurer et de parcourir à nouveau la globalité de ces compétences pour vérifier.

J'ai donc choisi de me tourner vers la plateforme **Pluralsight** qui possède un [Learning Path dédié à l'examen AZ-400](https://app.pluralsight.com/paths/certificate/microsoft-azure-devops-engineer-az-400).

J'ai choisi ce cours car il reflête bien les compétences demandées par Microsoft dans l'examen, module par module :

![AZ-400 Pluralsight Path details](/assets/img/posts/az-400-pluralsight-path-details.png)

Ne restait alors qu'à regarder ces **34h de vidéos**...😓
Ok j'avoue, j'ai fait du x2 en vitesse de lecture et ralenti sur les éléments importants !

Un autre point important des cours sur Pluralsight est la possibilité de se tester avec les **Skill IQ et Role IQ**. Je pourrais écrire un post entier sur ce que je pense de cette fonctionnalité mais on digresse.

Bref, après cette lecture intensive, quelques recherches sur la doc officielle en parrallèle, j'ai passé et obtenu l'examen sans problème 👨‍🎓.

De plus, lors de l'examen il y a un module composé de tâches non-guidées de créations de ressources et d'administration à réaliser sur Azure, via le portail. J'ai particulièrement apprécié ce module puisqu'on s'éloigne de la théorie et que les connaissances réelles d'utilisation du portail sont nécessaires. A quand un module où l'on testera la création et le déploiement, voire l'impact de correctifs en temps réel via cli ou des outils tiers ?!

📍 La certification AZ-400 est donc assez large mais elle teste un rôle de conseil et des compétences sur les outils autant que sur les stratégies DevOps dans des contextes bien différents. Je la valide à 100%.

## AZ-203

Pour l'examen AZ-203 en revanche, l'expérience était vraiment différente.
Déjà parce que j'étais un peu rouillé en dev. Dans cette industrie tout va tellement vite que **si l'on se focus sur un sujet 6 mois, on se sent largué sur le reste très vite**.
Heureusement tout est revenu assez vite.

L'examen AZ-203 est un peu particulier. Historiquement il s'agissait des certifications AZ-200 + AZ-201, aujourd'hui fusionnée en une AZ-203. Le contenu est très large tout en restant assez dense en termes de skills mesurés.

Il s'agissait de revoir **Azure App Service, Azure Function, Azure Search, SQL, Storage, Cosmos DB, API Management, VM Provisioning, Containers, Service Fabric, AKS, App & VM Security, Monitoring, Azure Active Directory, Messaging, Eventing, Logic Apps, etc.**

Là aussi j'ai regardé l'intégralité des vidéos en vitesse x2 (et parfois en passant certains modules, déjà abordés dans d'autres vidéos) puis j'ai passé l'examen.
Le résultat était positif mais ouch j'ai eu le cerveau en compote et besoin d'un week-end de 5 jours.

📍 L'examen AZ-203 me paraît du coup également ultra pertinent sur le scope et l'intensité des compétences mesurées mais bien trop dense. Il est d'ailleurs prévu de migrer la AZ-203 en une AZ-204 dont le périmètre sera revu, et pas mal de modules déplacés dans d'autres certifications d'administration ou d'IA.

## Le résultat

![Azure Badges](/assets/img/posts/azure-badges.png)

## Conclusion

Concernant le programme de certifications basé sur les rôles, je le trouve judicieux et les compétences associées reflêtent bien les compétences que je pense nécessaires pour ces rôles dans les équipes.
On pourrait débattre du terme "associate" ou "expert" mais la classification de niveau me paraît pertinente.

Globalement, je dirais que je suis content de l'avoir fait mais que je suis rincé. Que si c'était à refaire je prendrais un peu (beaucoup) plus de temps pour les préparer, et surtout je n'attendrais pas aussi longtemps avant de me remettre à valider mes acquis.

Cela me conforte dans l'idée que **(re-)valider ses compétences de manière régulière est primordial**. On devient vite has-been.

💡 Un dernier conseil pour la route. **Ne passez pas de certifications pour de mauvaises raisons**. Veillez à bien acquérir un background réel appris sur le terrain. Découvrez des organisations d'équipes et des architectures variés. Et surtout, **ne passez les examens que pour valider et non découvrir des compétences !**
