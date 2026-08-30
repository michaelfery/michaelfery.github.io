---
layout: default
title: "Politique de confidentialité d'Almanac"
description: "Ce que l'application Almanac fait de vos données. Réponse courte : il n'y a pas de serveur Almanac, et la seule copie hors de votre téléphone est la sauvegarde chiffrée d'Android."
permalink: /almanac/privacy/
---

# Politique de confidentialité d'Almanac

*Dernière mise à jour : 30 août 2026*

Read this page in [English](/almanac/privacy-en/).

Almanac est une application d'entretien du logement. Cette page décrit ce qu'elle
fait de vos données. Elle est courte parce que l'application en fait peu.

---

## En une phrase

**Il n'existe aucun serveur Almanac, aucun compte, aucune inscription, et le
code de l'application n'effectue aucun appel réseau.** Votre carnet reste sur
votre téléphone, à une exception près : la sauvegarde automatique d'Android en
place une copie chiffrée sur votre espace Google, pour que vous le retrouviez si
vous changez d'appareil. Elle est décrite plus bas, et vous pouvez la couper.

---

## Ce que l'application enregistre, et où

Tout est stocké **localement sur votre appareil**, dans deux fichiers que seule
Almanac peut lire :

| Donnée | Où |
|---|---|
| Les équipements que vous déclarez | base SQLite locale (`almanac.db`) |
| Votre carnet d'interventions : dates, notes, coûts, entreprises | même base |
| Les dates que vous relevez (dernier entretien, âge de la toiture…) | même base |
| Vos réglages : thème, saison, rappels, affichage | préférences locales |
| Le nombre de dons effectués | préférences locales, séparées des précédentes |

Rien de tout cela n'est transmis à l'auteur de l'application, ni consultable
par lui. La seule copie qui existe ailleurs est la sauvegarde d'Android décrite
ci-dessous.

**À connaître avant d'en avoir besoin :** « Tout effacer » supprime ces données
sur l'appareil, immédiatement et sans corbeille. Si la sauvegarde Android est
active, une copie antérieure peut subsister le temps que la prochaine
sauvegarde la remplace. L'export PDF reste le seul moyen de conserver votre
carnet sous une forme que vous maîtrisez, lisible sans Almanac.

---

## Ce qui sort de l'appareil, et quand

Quatre cas. Trois sont déclenchés par vous ; le premier ne l'est pas, et c'est
pour cette raison qu'il est en tête.

**La sauvegarde automatique d'Android.** Android copie la base d'Almanac et vos
réglages sur l'espace de sauvegarde associé à votre compte Google, comme il le
fait pour les autres applications de votre téléphone. C'est ce qui vous rend vos
équipements et votre carnet quand vous réinstallez l'application ou changez de
téléphone.

Ce qu'il faut en savoir, précisément :

- **C'est Android qui la réalise, pas Almanac.** L'application n'y participe pas
  et n'effectue toujours aucun appel réseau ; elle déclare seulement quels
  fichiers méritent d'être sauvegardés.
- **Elle est chiffrée avec le code de verrouillage de votre appareil**, depuis
  Android 9. Google conserve la copie sans pouvoir la lire, et l'auteur
  d'Almanac n'y a aucun accès.
- **Elle est activée par défaut, et vous pouvez la couper.** Dans les réglages
  d'Android, à la rubrique Google puis Sauvegarde, pour tout l'appareil ou pour
  Almanac seul.
- **C'est un choix assumé.** Almanac n'a pas de serveur, donc la base de votre
  téléphone *est* votre carnet, y compris les interventions que vous y avez
  inscrites pour des travaux antérieurs à l'application. Sans cette sauvegarde,
  un téléphone perdu emporterait le document que vous montreriez à un acheteur
  ou à un assureur. Nous avons préféré une copie chiffrée que vous pouvez
  refuser à une perte que vous ne pourriez pas rattraper.

**Un don.** Le paiement est traité par Google Play, pas par Almanac. Vous
communiquez vos informations de paiement à Google ; l'application ne les voit
jamais et n'en conserve rien. Almanac reçoit uniquement la confirmation que
l'achat a abouti, et n'en garde qu'un compteur. Ce que Google collecte à cette
occasion relève de la
[politique de confidentialité de Google](https://policies.google.com/privacy).

**Un avis sur le Play Store.** Si vous utilisez « Noter l'app », l'échange se
fait entre votre appareil et Google Play.

**Un export PDF.** Le fichier est créé sur votre appareil, puis vous choisissez
vous-même quoi en faire via le menu de partage d'Android. Almanac ne l'envoie
nulle part et ne sait pas où vous l'envoyez.

---

## Les autorisations, et pourquoi elles existent

L'honnêteté impose de mentionner celles que vous verriez en inspectant
l'application :

| Autorisation | Raison |
|---|---|
| Notifications | Les rappels d'échéance. Refusez-la et l'application fonctionne, sans rappel. |
| Démarrage de l'appareil | Reprogrammer vos rappels après un redémarrage, sinon ils seraient perdus. |
| Internet, état du réseau | **Apportées par les bibliothèques Google Play** (dons, avis), pas par le code d'Almanac. |
| Service en avant-plan, réveil | Utilisées par la planification des rappels. |

Les bibliothèques Google Play intégrées à l'application peuvent transmettre
leurs propres diagnostics à Google. Almanac ne contrôle pas ce comportement et
n'y ajoute rien : il n'y a **ni analytics, ni traceur publicitaire, ni outil de
rapport de plantage** dans cette application.

---

## Ce qu'Almanac ne fait pas

- Pas de publicité, pas de régie, pas d'identifiant publicitaire.
- Pas de revente ni de partage de données à des tiers.
- Pas de compte, pas d'adresse e-mail demandée.
- Pas de localisation, pas de contacts, pas de photos, pas de micro.

---

## Enfants

Almanac ne s'adresse pas spécifiquement aux enfants et ne collecte sciemment
aucune donnée les concernant : il n'en collecte aucune, de personne.

---

## Vos droits

Vos données étant exclusivement sur votre appareil, vous en avez le contrôle
direct : « Profil → Tout effacer » les supprime intégralement, et désinstaller
l'application produit le même effet. Il n'y a rien à nous demander, puisque nous
n'avons rien.

---

## Modifications

Cette politique changera si l'application change. La date en haut de page fait
foi ; l'historique complet est public dans le dépôt du projet.

## Contact

Écrivez à [mferyapps@gmail.com](mailto:mferyapps@gmail.com).
