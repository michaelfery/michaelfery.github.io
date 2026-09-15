# Pages vitrines d'applications — socle

Ce dossier commence par `_`, Jekyll l'ignore : rien ici n'est publié. Il
contient les gabarits à copier et les conventions à suivre. Les pages d'app
elles-mêmes sont du HTML statique sans front matter, que Jekyll copie tel quel.

## Fichiers du socle

| Fichier | Rôle |
|---|---|
| `assets/css/tokens.css` | Variables CSS partagées. Miroir de `_variables.scss` + extensions. |
| `assets/css/app.css` | Styles des pages d'app (`body.app-page`) et de la liste d'apps de la home (`.app-cards`). |
| `_templates/app-page.html` | Gabarit français → `<slug>/index.html` |
| `_templates/app-page-en.html` | Gabarit anglais → `<slug>/en/index.html` |
| `index.html` | Section `.app-cards` en tête de la home, page 1 seulement. |
| `_includes/head.html` | Charge `tokens.css` et `app.css` sur la home seulement (`{% if paginator %}`). |

## Arborescence d'une app

```
<slug>/                     minuscules ASCII, sans tiret si possible : almanac, roundside, bookfolio
  index.html                page vitrine FR        → /<slug>/
  QUESTIONS.md              trous non sourcés ; à ajouter à `exclude:` dans _config.yml
  en/
    index.html              page vitrine EN        → /<slug>/en/
  privacy.md                politique FR (Jekyll)  → /<slug>/privacy/
  privacy-en.md             politique EN (Jekyll)  → /<slug>/privacy-en/
  assets/
    icon-512.png            icône launcher, carrée, coins droits (le CSS arrondit) ; JSON-LD, pas chargée par la page
    icon-192.png            même image ; hero (affichée 96/128 px) et carte de la home
    icon-180.png            apple-touch-icon
    icon-32.png             favicon
    og.png                  1200×630, aperçu réseaux sociaux (FR)
    og-en.png               idem, EN, si le visuel porte du texte
    shots/
      fr/01-<mot-cle>.webp  captures numérotées à deux chiffres, un dossier par
      en/01-<mot-cle>.webp  langue ; 01 = hero. WebP 540 px de large, ≤ 40 Ko
                            (un seul jeu pour les deux langues : directement dans shots/)
```

Quand le repo de l'app n'a pas de captures, la fiche Google Play publique les a :
`curl` la page `play.google.com/store/apps/details?id=<applicationId>`, extraire les
`src` des `<img alt="Capture d'écran">` et télécharger chaque URL avec le suffixe `=s0`
(taille d'origine).

Produire les images sans rien installer (macOS) : `sips -Z 192 icon-512.png --out icon-192.png`
pour les icônes, `sips -Z 1171 ecran.png --out tmp.png && cwebp -q 78 -m 6 -sharp_yuv tmp.png -o 01-accueil.webp`
pour les captures (cwebp : `brew install webp`). Budget : page complète < 300 Ko, images comprises.

Règles :

- Tout ce qui appartient à l'app vit dans `<slug>/`. Rien dans `assets/img/`.
- Noms en kebab-case ASCII, sans espace ni accent, sans majuscule.
- Les captures gardent leur ordre d'affichage dans leur nom.
- Seuls `tokens.css`, `app.css`, l'avatar et les polices sont partagés avec le site.

## Créer une page

1. `mkdir -p <slug>/en <slug>/assets/shots`
2. `cp _templates/app-page.html <slug>/index.html`
   `cp _templates/app-page-en.html <slug>/en/index.html`
3. Remplacer chaque `{{PLACEHOLDER}}` et supprimer le commentaire de mode d'emploi
   en tête du fichier — `grep -n '{{' <slug>/index.html <slug>/en/index.html` doit rendre vide.
4. Remplir les blocs `▼ CONTENU … ▲`, supprimer les blocs `▼ OPTIONNEL` inutiles.
5. Choisir l'accent (`--app-accent`) et vérifier 4,5:1 avec `--app-accent-ink`.
6. Ajouter la carte sur la home (`index.html`, section `.app-cards`) et, s'il existe, `<slug>/QUESTIONS.md` à `exclude:` dans `_config.yml`.
7. Ouvrir la page à 360 px de large ; parcourir au clavier (Tab, Entrée, flèches sur les captures).

## Placeholders

| Placeholder | Contenu |
|---|---|
| `{{APP_NAME}}` | Nom de l'app |
| `{{APP_TAGLINE}}` | Accroche, une ou deux phrases. Sert aussi de `description`. |
| `{{APP_ACCENT}}` | Couleur hex de l'app |
| `{{PACKAGE_ID}}` | Identifiant Play (`com.example.app`) |
| `{{ABSOLUTE_URL}}` | URL absolue du dossier de l'app, sans slash final ni `/en/` (`https://michaelfery.github.io/almanac`). Alimente le bloc « URLs absolues » : `canonical`, `hreflang`, `og:url`, `og:image`. Seule exception au tout-relatif : les scrapers OG et Google ignorent les URL relatives. |
| `{{PRIVACY_PATH}}` | FR : `privacy/`. EN : `../privacy-en/` si la politique existe dans les deux langues (almanac), `../privacy/` si elle n'existe qu'en anglais (roundside). |
| `{{META_LINE}}` | Une ligne : prix, Android minimal, « sans publicité »… |
| `{{HERO_SHOT_KEY}}` / `{{HERO_SHOT_ALT}}` | Capture principale du hero (`01-<mot-cle>.webp`), optionnelle |
| `{{SHOT_n_KEY}}` / `{{SHOT_n_ALT}}` / `{{SHOT_n_CAPTION}}` | mot-clé du fichier ; alt = ce que montre l'écran ; caption = ce qu'on y comprend |
| `{{STEP_n}}` | « Comment ça marche », trois phrases au plus, optionnel |
| `{{FEATURE_n_TITLE}}` / `{{FEATURE_n_TEXT}}` | 3-5 mots / une phrase |
| `{{PRIVACY_SUMMARY}}` | La section « En une phrase » de la politique |
| `{{FAQ_n_QUESTION}}` / `{{FAQ_n_ANSWER}}` | Optionnel |
| `{{SECONDARY_URL}}` / `{{SECONDARY_LABEL}}` | Second bouton, optionnel |
| `{{YEAR}}` | Année du pied de page |

## Véracité

Chaque phrase de la page se rattache à une source du repo de l'app (fiche Play,
strings.xml, notes de version, politique, manifeste) ou au brief. Pas de
superlatif, pas de chiffre d'installations, pas de « bientôt », pas de promesse
sur les données qui ne soit pas dans la politique et le manifeste. Ce qui n'est
pas sourçable va dans `<slug>/QUESTIONS.md`, sous forme de questions.

## Ton

Phrases courtes, concret, aucun superlatif. On dit ce que l'app fait et ce
qu'elle ne fait pas. L'app est le sujet des phrases (« Almanac garde vos
interventions sur le téléphone »), jamais « nous » ni « je » : les politiques
`privacy` disent « nous », la home dit « I », la vitrine ne tranche pas.

## Déplacer une app sur son propre domaine

Copier `<slug>/` tel quel, ajouter `tokens.css`, `app.css` et
`michael-fery-64.png` dans `<slug>/assets/`, puis :

- `index.html` : remplacer `../assets/` par `assets/` et `href="../"` par `href="./"` ;
- `en/index.html` : remplacer `../../assets/` par `../assets/` et `../../` par `../` ;
- dans les deux : mettre à jour `{{ABSOLUTE_URL}}` dans le bloc « URLs absolues ».

Les pages `privacy*.md` sont des pages Jekyll : à convertir en HTML (ou à
reprendre dans le nouveau site) au moment du déplacement.

## Contraintes tenues

- HTML et CSS statiques, pas de build supplémentaire, pas de JS, pas de CDN nouveau.
- Chemins relatifs uniquement, hors le bloc « URLs absolues » du `<head>` (voir plus haut).
- Mobile d'abord, gouttière 16 px, lisible à 360 px.
- Contraste AA sur toutes les couleurs de `tokens.css` ; focus visible ; lien d'évitement ; cibles 44 px ; zoom non bloqué ; `prefers-reduced-motion` respecté.
- Polices : Lato et PT Serif, déjà chargées par le site, avec `display=swap` pour que le texte s'affiche avant l'arrivée des polices. Icônes en SVG inline.

## Vérifier une page sans Jekyll

Les pages d'app sont du HTML statique : ouvrir `<slug>/index.html` directement dans un navigateur suffit. Pour vérifier 360 px, les outils de développement en mode appareil ; Chrome headless ne descend pas sous 500 px de fenêtre.
