# À suivre — questions ouvertes

Ce fichier n'est pas publié (`exclude` dans `_config.yml`). Chaque entrée est
une information que la page vitrine ne peut pas affirmer faute de source dans
le repo de l'app (`~/src/livre-jeu`) ou dans le brief. Tant qu'une question
est ouverte, la page n'en parle pas.

## Le jour de la publication en production

1. **Bouton Google Play.** Commenté dans le hero de `index.html`, lien
   `https://play.google.com/store/apps/details?id=app.asuivre` prêt ; ajouter
   aussi `"installUrl"` dans le JSON-LD. Une seule décision : le jour où
   `docs/publication.md` passe la production à « fait ».

2. **Page anglaise.** Rien n'existe côté app (`values-en/` à venir, fiche
   fr-FR seule) ; le titre anglais prévu est « Nightfall » (décision 0045).
   La politique existe déjà en anglais (`/asuivre/privacy/`). Le jour venu :
   `_templates/app-page-en.html`, `hreflang en`, et retirer `x-default` seul.

## À trancher

3. **Domaine.** `asuivre.app` : aucun serveur de noms, aucun enregistrement A,
   HTTPS muet — **probablement libre**, à confirmer chez un registrar
   (0045 le demande aussi, avec INPI/EUIPO pour le nom). `asuivre.fr` est
   pris. Le bloc « URLs absolues » de la page est le seul endroit à changer.

4. **« Emporter l'histoire ».** La chaîne existe (`accueil_emporter`) et la
   décision 0006 décrit l'export en texte lisible ; la page le cite dans la
   FAQ « Et quand l'histoire est finie ? ». À confirmer que la 0.1.x le fait
   déjà, sinon retirer la phrase.

## Constaté dans les sources

5. **Le pilote est une expérience** (`docs/pilote.md`) : une histoire, cinq
   lecteurs, une question. La page ne parle que de « La relève » et ne promet
   ni catalogue ni achat à l'unité (0025), que l'app elle-même annonce comme
   « une version à venir » (`porte_sans_facturation`).

6. **Contact.** La fiche Play et la politique donnent `fery.michael@gmail.com`,
   pas `mferyapps@gmail.com` comme Almanac et Bookfolio. La page suit la
   fiche. Deux adresses pour les apps du même site : à unifier un jour, dans
   un sens ou dans l'autre.

## Non retenu volontairement

- « Un roman à choix, un épisode par jour » est le titre de la fiche Play ; la
  page garde « À suivre » seul en `<h1>` et met l'accroche en dessous.
- Le réglage de taille du texte, le mode unique sombre (0036), l'étagère
  « D'autres histoires » vide : réels, sans intérêt pour une vitrine à une
  seule histoire.
- Numéro de version (0.1.1 au 15 septembre) : non cité.
