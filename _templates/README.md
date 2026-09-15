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
  en/
    index.html              page vitrine EN        → /<slug>/en/
  privacy.md                politique FR (Jekyll)  → /<slug>/privacy/
  privacy-en.md             politique EN (Jekyll)  → /<slug>/privacy-en/
  assets/
    icon-512.png            icône launcher, carrée, coins droits (le CSS arrondit)
    icon-192.png            même image ; sert à la carte de la home
    icon-180.png            apple-touch-icon
    icon-32.png             favicon
    og.png                  1200×630, aperçu réseaux sociaux
    shots/
      01-<mot-cle>.png      captures numérotées à deux chiffres, portrait,
      02-<mot-cle>.png      1080×2400 max, ≤ 250 Ko chacune
```

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
6. Ajouter la carte sur la home (`index.html`, section `.app-cards`).
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
| `{{SLUG}}` | Le slug, utilisé dans les noms de captures |
| `{{SHOT_n_ALT}}` / `{{SHOT_n_CAPTION}}` | alt = ce que montre l'écran ; caption = son nom |
| `{{FEATURE_n_TITLE}}` / `{{FEATURE_n_TEXT}}` | 3-5 mots / une phrase |
| `{{PRIVACY_SUMMARY}}` | La section « En une phrase » de la politique |
| `{{FAQ_n_QUESTION}}` / `{{FAQ_n_ANSWER}}` | Optionnel |
| `{{SECONDARY_URL}}` / `{{SECONDARY_LABEL}}` | Second bouton, optionnel |
| `{{YEAR}}` | Année du pied de page |

## Ton

Phrases courtes, concret, aucun superlatif. On dit ce que l'app fait et ce
qu'elle ne fait pas. L'app est le sujet des phrases (« Almanac garde vos
interventions sur le téléphone »), jamais « nous » ni « je » : les politiques
`privacy` disent « nous », la home dit « I », la vitrine ne tranche pas.

## Déplacer une app sur son propre domaine

Copier `<slug>/` tel quel, ajouter `tokens.css`, `app.css` et
`michael-fery.png` dans `<slug>/assets/`, puis :

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
