# Almanac — questions ouvertes

Ce fichier n'est pas publié (`exclude` dans `_config.yml`). Chaque entrée est
une information que la page vitrine ne peut pas affirmer faute de source dans
le repo de l'app ou dans le brief. Tant qu'une question est ouverte, la page
n'en parle pas.

## À trancher

1. **Statut Play Store.** Le brief dit « considère que c'est fait ». Le backlog
   de l'app (`docs/backlog.md`, M8) marque « Production release ⬜ » au
   30 août 2026. La page affiche donc le bouton Play avec l'URL déduite de
   l'`applicationId` : `https://play.google.com/store/apps/details?id=io.github.michaelfery.almanac`.
   À vérifier le jour de la mise en production : le lien répond-il ?

2. **Domaine dédié.** `almanac.app` est **pris** (DNS délégué à
   `ns1-3.webnames.ca`, enregistrement A actif, site qui refuse la connexion
   HTTPS). Sondage DNS des voisins, sans vérification chez un registrar :
   `almanac.fr`, `almanac.io`, `almanac.eu`, `almanac.org`, `almanacapp.com`
   ont tous des serveurs de noms ; `almanac-app.com` n'en a pas. Quel nom
   viser ? Le bloc « URLs absolues » des deux pages est le seul endroit à
   changer.

3. **Prix de l'option Apparence et montants des dons.** Fixés dans la Play
   Console, absents du repo. La page dit « un achat, une fois » et « un don est
   possible » sans chiffre. Faut-il afficher un prix ?

4. **Version affichée.** La page ne cite aucun numéro de version (dérivé du tag
   Git, `v1.0.0` au 30 août). En afficher un imposerait de le tenir à jour.

## Constaté dans les sources, à corriger côté app si voulu

5. **Capture anglaise `detail`.** Le titre est « Clean the dryer vent » mais
   les étapes sont celles des bouches de VMC (« Unscrew each extract vent
   (kitchen, bathroom, toilet) »). La légende de la page décrit ce qui est
   visible sans reprendre le titre. Fixture `StoreFixtures` à revoir.

6. **Capture `logbook` (FR et EN).** Le bouton dit « EXPORTER EN PDF » /
   « EXPORT AS PDF » ; le libellé actuel est « EXPORTER LE CARNET » depuis
   l'export CSV (`next.fr.md`, non livré). La page parle d'« export du carnet
   en PDF », ce que montre l'écran et ce que fait la 1.0.0. À régénérer avec
   la 1.1.

7. **`README.md` de l'app.** Le bloc « Status : M0 — foundation » et la
   mascotte « Mirette, a worker ant » ne correspondent plus à l'app livrée
   (1.0.0, personnage en casquette). La page ne nomme pas la mascotte.

8. **Fichiers sensibles à la racine du repo de l'app** (`almanac-upload.jks`,
   `almanac-upload.jks.zip`, `play-service-account.json`). Non ouverts. À
   vérifier dans `.gitignore`.

## Non retenu volontairement

- Export CSV : dans `next.fr.md`, pas dans une version livrée.
- Nombre de fiches (33 tâches, 28 équipements) : exact au 30 août mais
  mouvant ; le listing Play ne le cite pas non plus.
- Thème sombre, langue anglaise dans l'app, choix du pays pour les
  obligations légales : réels (`profile/strings.xml`) mais secondaires ;
  à ajouter si vous voulez une section « Réglages ».
