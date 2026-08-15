---
layout: default
title: "Politique de confidentialité — Almanac"
description: "Ce que l'application Almanac fait de vos données. Réponse courte : elles ne quittent pas votre téléphone."
permalink: /almanac/privacy/
---

# Politique de confidentialité — Almanac

*Dernière mise à jour : 15 août 2026*

Read this page in [English](/almanac/privacy-en/).

Almanac est une application d'entretien du logement. Cette page décrit ce qu'elle
fait de vos données. Elle est courte parce que l'application en fait peu.

---

## En une phrase

**Votre carnet d'entretien ne quitte jamais votre téléphone.** Il n'existe aucun
serveur Almanac, aucun compte, aucune inscription, et le code de l'application
n'effectue aucun appel réseau.

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

Rien de tout cela n'est transmis, sauvegardé en ligne, ni consultable par
l'auteur de l'application.

**Corollaire à connaître avant d'en avoir besoin :** si vous désinstallez
Almanac, ou si vous utilisez « Tout effacer », ces données disparaissent
définitivement. Il n'existe aucune copie ailleurs à restaurer. L'export PDF est
le seul moyen de conserver votre carnet en dehors de l'application.

---

## Ce qui sort de l'appareil, et quand

Trois cas, tous déclenchés par vous.

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
- Aucun contenu de l'application n'est réservé aux personnes ayant fait un don.

---

## Enfants

Almanac ne s'adresse pas spécifiquement aux enfants et ne collecte sciemment
aucune donnée les concernant — n'en collectant aucune, de personne.

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
