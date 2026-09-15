# Bookfolio — questions ouvertes

Ce fichier n'est pas publié (`exclude` dans `_config.yml`). Chaque entrée est
une information que la page vitrine ne peut pas affirmer ou montrer faute de
source dans le repo de l'app (`~/src/bookfolio`) ou dans le brief. Tant qu'une
question est ouverte, la page n'en parle pas.

## Bloquant pour finir la page

1. **Captures d'écran.** Le repo n'en contient aucune de récente : `store/README.md`
   dit qu'elles sont « à générer, gitignorées », et `docs/branding/*.png` sont des
   captures d'émulateur de mars 2026 (422×973, UI en anglais, barre d'émulateur,
   avant l'onglet Collections et la refonte de la bibliothèque). La page est
   publiée **sans capture** : ni dans le hero, ni en carrousel. Il faut, par
   langue, 4 à 5 écrans 1080 px de large : Bibliothèque, Fiche d'un livre,
   Recherche / scan, Stats, Collections. Les emplacements sont prêts dans le
   gabarit (`app-hero__shot`, section `app-shots`) ; les légendes sont à écrire
   d'après ce que montrent les écrans livrés.

2. **URL de la politique déclarée dans la Play Console.** Le repo ne la note
   pas. La page publie la politique à `/bookfolio/privacy/` (EN, texte du repo)
   et `/bookfolio/privacy-fr/` (traduction). Si la Console pointe ailleurs :
   soit la mettre à jour, soit me donner l'URL existante pour que la page la
   reprenne.

## À trancher

3. **Contact.** La politique du repo avait `[Your name or company name]` /
   `[Your contact email address]`. J'ai mis « Michaël Fery » et
   `mferyapps@gmail.com`, l'adresse que la page About du site donne pour les
   apps. À confirmer, et à reporter dans `docs/privacy-policy.md` côté app.

4. **Traduction française de la politique.** `privacy-fr.md` est ma traduction
   du texte anglais du repo, marquée « la version anglaise fait foi ». À relire.
   Si vous préférez une seule langue, supprimer le fichier et pointer
   `privacy/` depuis la page FR (`{{PRIVACY_PATH}}`).

5. **Domaine.** `bookfolio.app` est **pris** (NS `whoisdomain.kr`, A actif,
   HTTPS répond 301). Quel nom viser ? Le bloc « URLs absolues » des deux pages
   est le seul endroit à changer.

6. **Fiche Play en français.** Le repo n'a que `store/en/`. Les textes FR de la
   page traduisent la fiche anglaise et reprennent les `strings.xml` FR ; si une
   fiche FR existe dans la Console, elle devrait servir de source à la place.

## Constaté dans les sources, à corriger côté app si voulu

7. **Politique vs code sur Google Sign-In.** La politique (§ 2.1) dit que
   l'Application accède au nom, à l'e-mail et à la photo de profil. Le code ne
   demande que deux scopes (`AuthRemote.kt` : `auth/books`, `auth/drive.appdata`)
   et le README note que l'identité n'est pas utilisée. La page suit le code
   (« l'autorisation demandée se limite à Google Books et à ce dossier ») ; la
   politique publiée reprend le texte du repo tel quel. À réconcilier dans
   `docs/privacy-policy.md`.

8. **Politique vs manifeste sur l'identifiant publicitaire.** La politique § 3
   dit que sans télémétrie aucun identifiant publicitaire n'est collecté ; le
   manifeste retire `AD_ID` dans tous les cas (`tools:node="remove"`). La page
   dit « retiré de l'application », ce qui est plus fort et exact.

9. **README périmé.** « La V1 n'implémente aucun billing réel » alors que
   `BillingManager.kt` interroge Play Billing (pourboires, badge supporter) ;
   la section « V2 / TODO » (UPC, liste d'achat premium) n'est pas reprise.

10. **Icône.** `docs/play-store-icon-512.png` est un glyphe transparent, pas
    l'icône avec fond que Play exige (« 32-bit PNG, no transparency »). La
    page recompose glyphe + fond blanc comme le launcher
    (`ic_launcher_background #FFFFFF`). `docs/branding/bookfolio-app-icon.svg`
    est un autre dessin (trois livres colorés), non livré : lequel est le bon ?

## Non retenu volontairement

- « Parcourez des millions de titres » (`scan_landing_body`) : c'est Google
  Books, pas l'app.
- Widget d'écran d'accueil (`widget_home_*`), collections (`nav_collections`),
  thème clair/sombre et trois couleurs d'accent : réels, mais secondaires sans
  capture pour les montrer. À ajouter aux bénéfices ou aux captures quand les
  écrans existeront.
- Version affichée (1.6.2 au 29 août) : non citée, pour ne pas la maintenir.
