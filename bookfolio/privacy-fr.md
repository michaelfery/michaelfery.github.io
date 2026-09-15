---
layout: default
title: "Politique de confidentialité de Bookfolio"
description: "Ce que l'application Bookfolio fait de vos données : votre bibliothèque reste sur votre appareil ou dans votre propre compte Google ; il n'y a pas de serveur Bookfolio ; la télémétrie est sur opt-in."
permalink: /bookfolio/privacy-fr/
---

# Politique de confidentialité — Bookfolio

**Dernière mise à jour : 6 avril 2026**

Read this page in [English](/bookfolio/privacy/). En cas de divergence, la version anglaise fait foi : c'est celle que le dépôt de l'application maintient.

---

## 1. Introduction

Bookfolio (l'« Application ») est développée et publiée dans le cadre d'un projet individuel. Cette politique de confidentialité explique quelles données sont collectées, comment elles sont utilisées, et quels sont vos droits en tant qu'utilisateur.

En utilisant l'Application, vous acceptez les pratiques décrites dans ce document.

---

## 2. Données collectées

### 2.1 Données du compte Google

Lorsque vous vous connectez via **Google Sign-In**, l'Application accède à des informations de base de votre compte Google : nom, adresse e-mail et photo de profil. Ces données servent uniquement à identifier votre session et à synchroniser votre bibliothèque.

Aucun mot de passe Google n'est jamais stocké par l'Application.

### 2.2 Bibliothèque

Les livres que vous ajoutez à votre bibliothèque (titre, auteur, ISBN, notes, statut de lecture, etc.) sont stockés **localement sur votre appareil** et, si vous activez la synchronisation, **dans votre compte Google Drive personnel**.

Ces données ne sont jamais transmises à des serveurs tiers autres que Google Drive.

### 2.3 Caméra

L'Application demande l'accès à la caméra **uniquement pour scanner des codes-barres ISBN** (EAN-13). La caméra n'est activée que lorsque l'utilisateur ouvre explicitement le scanner. Aucune image ni vidéo n'est enregistrée ou transmise.

La reconnaissance des codes-barres est effectuée **localement sur l'appareil** avec **Google ML Kit**, sans envoi de données à des serveurs externes.

### 2.4 Notifications

L'Application peut demander l'autorisation d'envoyer des **notifications locales** (rappels, confirmations). Aucune notification n'est envoyée depuis un serveur distant.

### 2.5 Réseau

L'Application utilise Internet pour :
- rechercher des livres via l'**API Google Books** (requêtes par titre, auteur ou ISBN) ;
- synchroniser votre bibliothèque avec **Google Drive** ;
- charger les images de couverture.

Les requêtes à l'API Google Books ne transmettent que les termes de recherche que vous saisissez.

### 2.6 Télémétrie optionnelle (opt-in uniquement)

Bookfolio inclut une télémétrie Firebase pour la qualité du produit :
- **Firebase Analytics** (mesures anonymes d'usage des fonctionnalités et de version de l'application) ;
- **Firebase Crashlytics** (diagnostics de plantage).

La télémétrie est **désactivée par défaut**. Elle n'est activée que si vous l'acceptez explicitement, à l'onboarding ou dans les réglages. Vous pouvez la désactiver à tout moment.

---

## 3. Données non collectées

Sans activation de la télémétrie, l'Application ne collecte **aucune** des données suivantes :

- données de localisation ;
- contacts ;
- identifiants publicitaires ;
- données biométriques ;
- historique de navigation ;
- données de performance ou de plantage.

---

## 4. Partage des données

Aucune donnée personnelle n'est vendue, louée ou partagée avec des tiers commerciaux.

Les seuls services tiers utilisés sont :

| Service | Usage | Politique de confidentialité |
|---|---|---|
| Google Sign-In | Authentification | [policies.google.com/privacy](https://policies.google.com/privacy) |
| Google Drive | Synchronisation de la bibliothèque | [policies.google.com/privacy](https://policies.google.com/privacy) |
| API Google Books | Recherche de livres | [policies.google.com/privacy](https://policies.google.com/privacy) |
| Google ML Kit | Lecture de codes-barres (sur l'appareil) | [developers.google.com/ml-kit/terms](https://developers.google.com/ml-kit/terms) |
| Firebase Analytics | Télémétrie d'usage anonyme, optionnelle (opt-in) | [firebase.google.com/support/privacy](https://firebase.google.com/support/privacy) |
| Firebase Crashlytics | Diagnostics de plantage, optionnels (opt-in) | [firebase.google.com/support/privacy](https://firebase.google.com/support/privacy) |

---

## 5. Stockage et sécurité

- Les données locales sont stockées dans la base de données privée de l'Application (Room/SQLite), inaccessible aux autres applications.
- Les données synchronisées sont stockées dans votre propre Google Drive et relèvent des politiques de sécurité de Google.
- L'Application n'a aucun serveur propriétaire.

---

## 6. Conservation des données

Les données sont conservées tant que l'Application est installée sur votre appareil. Désinstaller l'Application supprime toutes les données locales. Les données stockées dans Google Drive restent sous votre contrôle et peuvent être supprimées à tout moment depuis votre compte Google.

---

## 7. Vos droits

Conformément au RGPD (lorsqu'il s'applique), vous disposez des droits suivants :

- **Accès** : consulter les données détenues à votre sujet ;
- **Rectification** : corriger vos données directement dans l'Application ;
- **Effacement** : supprimer votre bibliothèque depuis l'Application ou depuis Google Drive ;
- **Portabilité** : vos données sont directement accessibles via Google Drive.

---

## 8. Enfants

L'Application n'est pas destinée aux enfants de moins de 13 ans et ne collecte sciemment aucune donnée les concernant.

---

## 9. Modifications

Cette politique peut être mise à jour. La date de révision en haut du document sera modifiée en conséquence. Les changements importants seront signalés par une mise à jour de l'Application sur le Google Play Store.

---

## 10. Contact

Pour toute question concernant cette politique de confidentialité :

**Michaël Fery**  
**[mferyapps@gmail.com](mailto:mferyapps@gmail.com)**
