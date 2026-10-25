---
layout: post
topics: [Android]
app: luciole
title:  "Lancer une app Android en solo : ce que la Play Console ne vous dit pas"
description: "Publier une app Android seul, avec un compte personnel : les formulaires dans l'ordre, les attentes, et les pièges que la Play Console ne documente pas."
date:   2026-11-15 09:00:00.000 +01:00
tags: [Android, Google Play, Luciole, Almanac]
published: false
---

[Luciole](/luciole/), mon application d'histoires du soir, arrive sur le Play Store. C'est ma troisième application en production, après [Bookfolio](/bookfolio/) puis [Almanac](/almanac/), et je croyais connaître le chemin. Je le connaissais à moitié.

Publier une app Android quand on est seul, avec un compte développeur personnel, ce n'est pas un bouton « Publier ». C'est une suite de formulaires dans un ordre que rien n'indique, deux ou trois attentes de plusieurs jours, et quelques pièges qui ne sont documentés nulle part. Voici ce que j'aurais aimé lire avant de commencer.

<!-- TODO au lancement : date de l'accès à la production et de la sortie, et ajuster l'introduction. -->

## Avant d'ouvrir la console

Trois choses doivent exister avant même de créer l'application.

**Une politique de confidentialité en ligne.** Dès qu'une application vise des enfants, Google exige l'URL d'une politique, même si l'application ne collecte rien. Et le questionnaire sur le public cible la réclame **avant** de vous laisser cocher une tranche d'âge enfant. Les miennes vivent sur ce site, une par application.

**Une clé d'envoi, sauvegardée ailleurs.** Avec *Play App Signing*, Google garde la clé de signature finale ; vous ne gardez que la clé d'envoi. Elle vit hors du dépôt (`~/.config/luciole/`, droits 600, et un `.gitignore` qui refuse `*.jks`). Une clé d'envoi perdue se remplace, mais par une demande dans la console : c'est une semaine de perdue. Elle doit donc être sauvegardée quelque part où vous serez encore dans cinq ans.

**Un identifiant définitif.** L'`applicationId` (`fr.luciole.app`) est le nom de l'application pour Google. Il ne changera plus jamais. Il mérite cinq minutes de réflexion.

## Le premier soir : la console, à la main

L'API de Google Play sait publier des versions et des fiches, mais **elle ne sait pas créer une application**. La première fois, tout se fait à la main.

1. Créer l'application : nom, langue par défaut, gratuite.
2. Créer une version sur la piste de **test interne**, et envoyer le premier bundle.
3. Ajouter des testeurs.
4. Inviter un **compte de service**, qui servira ensuite à tout automatiser depuis le dépôt, avec les droits de publier sur les pistes de test et de modifier la fiche.

Bonne nouvelle : le test interne ne demande ni fiche complète ni classification. On peut installer l'application sur ses propres téléphones dès le premier soir.

Mauvaise nouvelle : tout le reste sera exigé pour aller plus loin. Je recommande de préparer les réponses aux questionnaires dans un fichier, à l'avance, parce que certaines engagent l'application.

- **Public cible.** Luciole déclare deux tranches : « 6 à 8 ans » (l'enfant écoute et choisit) et « 18 ans et plus » (le parent lit). Cocher une tranche enfant fait entrer l'application dans le **programme Familles** : aucune publicité non certifiée, politique de confidentialité obligatoire, contrôles plus stricts.
- **Classification du contenu (IARC).** Un questionnaire, toutes les réponses à non pour Luciole, sauf une : les achats de biens numériques. Résultat : PEGI 3.
- **Annonces, accès à l'application, applications gouvernementales, santé, finance…** Non, non, non. Mais il faut les remplir.
- **Sécurité des données.** Celle-là mérite un chapitre à elle seule.

## La sécurité des données : honnête, même quand ça coûte

Jusqu'à sa version 0.14, Luciole ne demandait aucune permission et ne pouvait techniquement rien envoyer. La fiche affichait fièrement « Aucune donnée collectée ».

Puis sont arrivés les univers payants, et avec eux la bibliothèque de facturation de Google Play. Cette bibliothèque embarque sa propre télémétrie : depuis sa version 6, elle envoie à Google des informations sur l'utilisation de l'API et les problèmes de connexion. Google ne publie pas la liste exacte des champs.

Les règles de Google sont précises sur un point : **collecter, c'est transmettre des données hors de l'appareil, y compris par une bibliothèque**. Ce qui reste sur le téléphone (le prénom de l'enfant, ses choix, l'historique) n'est pas collecté. Le paiement lui-même est exempté, puisque l'application ne voit jamais la carte. Mais la télémétrie de la bibliothèque n'est pas clairement couverte par l'exemption.

J'ai donc choisi la réponse prudente : déclarer **une seule donnée, « Diagnostics »**, collectée par la bibliothèque de facturation, non partagée. Une déclaration inexacte peut faire retirer une application, et le programme Familles est examiné de près. Le badge « Aucune donnée collectée » a disparu ; il reste « Aucune donnée partagée avec des tiers ». L'alternative, retirer la télémétrie pour garder le badge, aurait risqué des plantages au moment de l'achat. Je l'ai écartée.

Au passage, il a fallu reprendre tous les textes qui affirmaient ce qui n'était plus vrai : la politique, la fiche, une capture d'écran. « Rien ne sort du téléphone » est devenu « Le prénom reste dans le téléphone ». C'est moins net, et c'est exact.

## Ensuite, tout depuis le dépôt

Une fois l'application créée, je ne retourne presque plus dans la console. La fiche vit dans le dépôt, sous `android/app/src/main/play/` : titres, descriptions, captures, notes de version, en français et en anglais. Le plugin [Gradle Play Publisher](https://github.com/Triple-T/gradle-play-publisher) l'envoie, et un petit script enchaîne les étapes : partage interne, piste interne, test fermé, production.

Trois pièges m'ont coûté du temps :

- **Un envoi qui échoue à la validation perd tout ce qu'il a envoyé**, et le suivant croit ses fichiers déjà à jour. Après un échec, il faut tout relancer de force.
- **Une nouvelle langue de fiche** : le plugin envoie les images en parallèle des textes, avant que la fiche anglaise existe. Google répond « Listing for language 'en-US' not found », et tout l'envoi est perdu. Il faut une première passe sans les images de la nouvelle langue, puis une passe complète.
- **Une version envoyée à la main reste en brouillon** tant qu'on n'a pas cliqué sur « Déployer ». Les testeurs ne la voient pas, et la console le montre à peine.

## Le test fermé : douze personnes, quatorze jours

C'est l'étape qui surprend le plus. Pour un **compte personnel**, Google impose un test fermé avec **au moins douze testeurs inscrits pendant quatorze jours** avant de pouvoir demander la production.

Deux détails ne sont dits nulle part clairement :

- Tant que l'application n'a jamais été examinée, l'API refuse de créer autre chose qu'un brouillon (« Only releases with status draft may be created on draft app »). Le premier envoi à l'examen se fait à la main, dans la console.
- **Les quatorze jours ne suffisent pas.** L'accès à la production se **demande**, par un questionnaire sur le déroulement du test, et Google l'examine à son tour. Avant cela, la promotion en production échoue avec un laconique `400 Precondition check failed`, sans dire pourquoi.

Pour une application d'histoires du soir, trouver douze testeurs, c'est trouver douze parents prêts à installer une version de test et à la garder deux semaines. C'est la partie la moins technique du parcours.

## La fiche : se faire trouver, puis se faire choisir

Sur le Play Store, « luciole » renvoie déjà à une autre application. Le nom seul ne se trouve pas. Le titre porte donc le mot que tape un parent : **« Luciole : histoires du soir »**, 27 caractères sur les 30 permis. La description courte porte ce qui distingue l'application : *« Histoire du soir à lire à voix haute : ton enfant choisit comment elle finit. »*

Les règles de Google sont strictes, et il vaut mieux les connaître avant d'écrire : pas d'emoji ni de capitales dans le titre, pas de « gratuit », pas de « n° 1 », pas d'appel à l'action ni de prix sur les captures, et du texte sur 20 % de l'image au plus.

Il y a aussi des pièges de marques. « Dont vous êtes le héros » est une marque des livres Gallimard ; j'écris « dont ton enfant est le héros », ce qui est d'ailleurs littéralement vrai, puisque c'est son prénom. En anglais, « Choose Your Own Adventure » appartient à Chooseco et ne s'écrit pas.

Les captures forment une série de sept panneaux, reliés par la traînée pointillée de la luciole, qui court d'un panneau à l'autre et se divise en trois fins au troisième. Chaque panneau porte une seule accroche : « Quatre fois, l'histoire s'arrête. Tu poses la question, ton enfant choisit. »

## Les achats intégrés

Luciole est gratuite, avec un univers complet offert. Les autres univers sont des achats uniques, sans abonnement. Le modèle vient d'une conviction simple : un parent accepte de payer trois euros pour un livre, il résiste à un abonnement de plus. Et un univers acheté fonctionnera encore dans deux ans, hors ligne, sans compte.

Côté technique, deux bonnes surprises et un piège :

- **Les prix vivent dans Google Play.** Changer un prix ou ouvrir un nouvel univers ne demande aucune nouvelle version de l'application.
- **Les comptes de test de licence** achètent avec une carte de test, sans être débités.
- **Le piège :** Google refuse de créer le moindre produit tant qu'aucun bundle déclarant la permission de facturation n'a été **diffusé** sur une piste. Un bundle en brouillon ne suffit pas. Et un téléphone qui a demandé un produit avant sa mise en vente le garde en cache, même après une réinstallation : il faut vider le cache du Play Store.

## Mettre en production sans dormir mal

Le document de publication d'Almanac le pose dès le départ : **la production commence à 10 % des utilisateurs**, pas à 100 %. Ce n'est pas de la prudence pour la forme. Un développeur seul ne peut pas publier un correctif à trois heures du matin, et un déploiement partiel, c'est ce qui achète une nuit de sommeil. On surveille les plantages et les blocages dans la console pendant 48 heures, puis on élargit.

## Ce que j'en retiens

- **Écrivez la procédure la première fois.** Le chemin ne se fait qu'une fois par application, et il s'oublie d'une fois sur l'autre. Bookfolio, Almanac et Luciole ont chacun leur document de publication ; ceux d'Almanac et de Luciole gardent aussi les réponses aux questionnaires.
- **Préparez les questionnaires à l'avance.** Public cible, sécurité des données, classification : certaines réponses engagent l'application, et ce n'est pas le moment de les improviser.
- **Soyez exact dans la sécurité des données**, même quand ça vous coûte un badge.
- **Comptez en semaines, pas en jours.** Entre le premier examen, le test fermé de quatorze jours et l'examen de l'accès à la production, le calendrier n'est pas le vôtre.
- **Automatisez tout ce qui peut l'être**, après être passé une fois à la main par la console : c'est elle qui vous apprend ce que l'API ne sait pas faire.
