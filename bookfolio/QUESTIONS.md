# Bookfolio — questions ouvertes

Ce fichier n'est pas publié (`exclude` dans `_config.yml`). Chaque entrée est
une information que la page vitrine ne peut pas affirmer ou montrer faute de
source dans le repo de l'app (`~/src/bookfolio`) ou dans le brief. Tant qu'une
question est ouverte, la page n'en parle pas.

## Résolu depuis la fiche Play publique

1. **Captures d'écran.** Le repo n'en a aucune de récente ; les quatre de la
   page sont celles de la fiche Google Play publique (1080×2340, un seul jeu
   pour FR et EN, accent Océan, UI mixte : « Ma bibliotheque » / « Search title,
   author… » / « My reading journey »). La page le dit sous le titre de la
   section. Si vous refaites des captures par langue, remplacer
   `assets/shots/0N-*.webp` et retirer cette phrase.

2. **Fiche Play en français.** Elle existe dans la Console (titre « Bookfolio :
   Suivi de lecture », description longue complète) mais pas dans le repo, qui
   n'a que `store/en/`. La description courte FR n'apparaît pas dans le HTML
   public ; l'accroche FR de la page reste une traduction de la courte EN. À
   faire côté app : commiter `store/fr/` pour que la fiche soit versionnée, et
   me donner la courte FR si vous voulez qu'elle soit reprise mot pour mot.
   Les statuts y sont « À lire, En cours, Lu ou Abandonné » et la wishlist
   « Liste d'envies » ; l'app affiche « DNF » et « Wishlist » : la page suit
   l'app.

## Bloquant

3. **URL de la politique déclarée dans la Play Console.** Le repo ne la note
   pas. La page publie la politique à `/bookfolio/privacy/` (EN, texte du repo)
   et `/bookfolio/privacy-fr/` (traduction). Si la Console pointe ailleurs :
   soit la mettre à jour, soit me donner l'URL existante pour que la page la
   reprenne.

## À trancher

4. **Contact.** La politique du repo avait `[Your name or company name]` /
   `[Your contact email address]`. J'ai mis « Michaël Fery » et
   `mferyapps@gmail.com`, l'adresse que la page About du site donne pour les
   apps. À confirmer, et à reporter dans `docs/privacy-policy.md` côté app.

5. **Traduction française de la politique.** `privacy-fr.md` est ma traduction
   du texte anglais du repo, marquée « la version anglaise fait foi ». À relire.
   Si vous préférez une seule langue, supprimer le fichier et pointer
   `privacy/` depuis la page FR (`{{PRIVACY_PATH}}`).

6. **Domaine.** `bookfolio.app` est **pris** (NS `whoisdomain.kr`, A actif,
   HTTPS répond 301). Quel nom viser ? Le bloc « URLs absolues » des deux pages
   est le seul endroit à changer.

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
    (`ic_launcher_background #FFFFFF`) — et c'est bien l'icône affichée sur la
    fiche Play. `docs/branding/bookfolio-app-icon.svg` (trois livres colorés)
    n'est pas livré : à archiver ou à documenter.

## Non retenu volontairement

- « Parcourez des millions de titres » (`scan_landing_body`) : c'est Google
  Books, pas l'app.
- Widget d'écran d'accueil (`widget_home_*`), collections (`nav_collections`),
  thème clair/sombre et trois couleurs d'accent : réels, mais absents des
  quatre captures Play. À ajouter quand des écrans les montreront.
- Version affichée (1.6.2 au 29 août) : non citée, pour ne pas la maintenir.
