---
layout: default
title: "Politique de confidentialité de Bookfolio"
description: "Ce que l'application Bookfolio fait de vos données. Réponse courte : pas de serveur Bookfolio ; votre bibliothèque est sur votre téléphone, plus la sauvegarde Android, Google Drive si vous activez la synchronisation, et la télémétrie seulement si vous l'activez."
permalink: /bookfolio/privacy-fr/
---

# Politique de confidentialité de Bookfolio

*Dernière mise à jour : 16 septembre 2026*

Read this page in [English](/bookfolio/privacy/).

Bookfolio est une application de suivi de lecture. Cette page décrit ce
qu'elle fait de vos données. Elle est plus longue que nous ne le voudrions,
parce que l'application parle à Google de plusieurs façons, et que chacune
mérite une phrase claire.

---

## En une phrase

**Il n'existe aucun serveur Bookfolio et aucun compte Bookfolio.** Votre
bibliothèque vit sur votre téléphone. Des copies en sortent dans trois cas :
la sauvegarde d'Android, active par défaut ; la synchronisation Google Drive,
si vous l'activez ; la télémétrie, si vous l'activez. Chercher un livre envoie
vos termes de recherche à Google Books, ou à Open Library quand Google Books
ne trouve rien.

---

## Ce que l'application enregistre, et où

Tout est stocké **localement sur votre appareil**, dans des fichiers que seule
Bookfolio peut lire :

| Donnée | Où |
|---|---|
| Les livres que vous ajoutez : titre, auteurs, éditeur, année, ISBN, catégories, résumé, adresse de la couverture | base de données locale (`bookfolio.db`) |
| Vos lectures : statut (À lire, En cours, Lu, DNF), dates de début et de fin, pages lues, note, notes, wishlist, possédé | même base |
| Vos collections | même base |
| Votre journal d'activité | même base |
| Vos recherches récentes | même base |
| Vos réglages : thème, couleur d'accent, rappels, objectif annuel, choix de télémétrie | préférences locales (`bookfolio_prefs.xml`) |
| Le jeton d'autorisation Google, si vous avez connecté Google | un espace chiffré, séparé des fichiers ci-dessus |
| Les couvertures, une fois téléchargées | le cache d'images de l'application |

Rien de tout cela n'est transmis à l'auteur de l'application, ni consultable
par lui.

---

## Ce qui sort de l'appareil, et quand

Six cas. Le premier n'est pas déclenché par vous, c'est pour cette raison
qu'il est en tête.

**La sauvegarde automatique d'Android.** Android copie `bookfolio.db` et
`bookfolio_prefs.xml` sur l'espace de sauvegarde associé à votre compte
Google, comme il le fait pour les autres applications de votre téléphone, et
les emporte lors d'un transfert vers un nouvel appareil. C'est ce qui vous rend
votre bibliothèque quand vous réinstallez l'application ou changez de
téléphone.

- **C'est Android qui la réalise, pas Bookfolio.** L'application déclare
  seulement quels fichiers méritent d'être sauvegardés. Le jeton
  d'autorisation Google n'en fait pas partie.
- **Elle est chiffrée avec le code de verrouillage de votre appareil**,
  depuis Android 9. Google conserve la copie sans pouvoir la lire, et l'auteur
  de Bookfolio n'y a aucun accès.
- **Elle est activée par défaut, et vous pouvez la couper**, dans les réglages
  d'Android, à la rubrique Google puis Sauvegarde, pour tout l'appareil ou
  pour Bookfolio seul. Réglages › Sync et données › « Sauvegarder maintenant »
  demande seulement à Android de lancer la sauvegarde qu'il ferait de toute
  façon.

**La recherche d'un livre.** Quand vous tapez un titre, un auteur ou un ISBN,
ou que vous scannez un code-barres, l'application envoie les termes de
recherche à l'**API Google Books**, et à **Open Library** quand Google Books ne
renvoie rien. Les requêtes vers Google Books portent le nom de paquet et le
certificat de signature de l'application, ce qui permet à Google de restreindre
la clé d'API à cette application ; elles ne portent rien sur vous. Les
couvertures sont ensuite téléchargées depuis Google Books ou Open Library. La
lecture du code-barres se fait sur l'appareil : aucune image ni vidéo n'en
sort.

**La synchronisation Google Drive.** Si vous connectez Google, l'application
demande exactement une autorisation : `drive.appdata`, le dossier privé que
Google Drive réserve à chaque application. Cette autorisation ne donne à
Bookfolio aucun accès au reste de votre Drive, et aucune donnée d'identité :
ni votre nom, ni votre adresse e-mail, ni votre photo de profil. Deux choses
sont écrites dans ce dossier :

- une archive complète de votre bibliothèque et de vos réglages
  (`bookfolio.db` et `bookfolio_prefs.xml`), pour qu'un nouvel appareil puisse
  la restaurer ;
- un fichier de synchronisation par livre : ISBN, statut, dates, progression,
  note, wishlist, possédé, notes, plus le titre, les auteurs et l'adresse de la
  couverture pour qu'un autre appareil reconnaisse le livre. Il est fusionné
  dans les deux sens entre vos appareils.

Le jeton qui autorise tout cela est stocké chiffré sur l'appareil.
Réglages › « Déconnecter le compte Google » supprime ce jeton de cet
appareil ; cela ne supprime pas ce qui est déjà dans votre Drive. Pour cela,
ouvrez les paramètres de Google Drive, Gérer les applications, et supprimez
les données d'application masquées de Bookfolio.

Bookfolio n'écrit plus dans vos étagères Google Books. Cette synchronisation a
été retirée en avril 2026.

**La télémétrie, si vous l'activez.** Bookfolio embarque deux bibliothèques
Google, **Firebase Analytics** (quels écrans sont ouverts, quelles fonctions
sont utilisées, quelle version tourne) et **Firebase Crashlytics** (diagnostics
de plantage). Les deux sont **désactivées par défaut** : l'application est
livrée avec la collecte coupée, et rien ne part tant que vous ne les activez
pas, à l'onboarding ou dans Réglages › Sync et données. Vous pouvez les couper
à tout moment. Les événements envoyés ne contiennent aucune donnée personnelle
et rien sur vos livres. Ce que Firebase en fait, et combien de temps il les
conserve, relève de la politique de confidentialité Firebase de Google.
L'identifiant publicitaire est retiré de l'application dans tous les cas, quel
que soit votre choix.

**Un pourboire ou le badge supporter.** Le paiement est traité par Google
Play, pas par Bookfolio. Vous communiquez vos informations de paiement à
Google ; l'application ne les voit jamais. Bookfolio reçoit uniquement la
confirmation que l'achat a abouti, et relit votre statut de supporter depuis
Google Play. Ce que Google collecte à cette occasion relève de la politique de
confidentialité de Google.

**Un avis sur le Play Store.** Au bout d'un moment, l'application peut
proposer une fois de la noter. L'échange se fait entre votre appareil et
Google Play.

---

## Les autorisations, et pourquoi elles existent

Celles que vous verriez en inspectant l'application :

| Autorisation | Raison |
|---|---|
| Caméra | Scanner des codes-barres, seulement pendant que le scanner est ouvert. Facultative : saisir l'ISBN trouve le même livre, et un appareil sans caméra peut installer l'application. |
| Notifications | Les rappels de lecture : le livre en cours, la wishlist, un bilan hebdomadaire. Locaux, optionnels, et coupés tant que vous ne les activez pas. |
| Internet, état du réseau | La recherche de livres, les couvertures, la synchronisation Google Drive. |
| Vibration | Une courte vibration quand un code-barres est lu. |
| Achats Google Play | Les pourboires et le badge supporter. |

L'autorisation d'identifiant publicitaire que Firebase ajouterait normalement
est explicitement retirée de l'application.

---

## Ce que Bookfolio ne fait pas

- Pas de publicité, pas de régie, pas d'identifiant publicitaire.
- Pas de revente ni de partage de données à des tiers.
- Pas de compte Bookfolio, pas d'adresse e-mail demandée.
- Pas de localisation, pas de contacts, pas d'accès à vos photos ; la caméra
  n'affiche qu'un aperçu en direct et n'enregistre rien.
- Pas de télémétrie sans votre activation.

---

## Enfants

Bookfolio ne s'adresse pas aux enfants de moins de 13 ans et ne collecte
sciemment aucune donnée les concernant.

---

## Vos droits

Vos données sont sur votre appareil et, si vous l'avez choisi, dans votre
propre compte Google. Vous en avez le contrôle direct :

- **Accès et rectification** : tout est visible et modifiable dans
  l'application.
- **Effacement sur l'appareil** : désinstaller l'application supprime la base
  de données, les préférences et le cache. Si la sauvegarde Android est
  active, une copie antérieure peut subsister le temps que la prochaine
  sauvegarde la remplace.
- **Effacement dans Google Drive** : depuis les paramètres de Drive, Gérer les
  applications, supprimer les données d'application masquées de Bookfolio.
- **Télémétrie** : la couper dans Réglages ; les demandes concernant des
  données déjà envoyées à Firebase passent par Google.

Il n'y a rien à nous demander, puisque nous n'avons rien.

---

## Modifications

Cette politique change quand l'application change. La date en haut de page
fait foi ; l'historique complet est dans le dépôt du projet.

## Contact

Écrivez à [mferyapps@gmail.com](mailto:mferyapps@gmail.com).
