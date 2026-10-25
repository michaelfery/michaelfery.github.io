---
layout: post
topics: [IA]
title:  "Un AGENTS.md pour les gouverner tous : un seul fichier d'instructions pour tous vos agents"
description: "Claude Code, Codex, Copilot : chaque agent veut son fichier d'instructions. Comment n'en écrire qu'un, et ce que Claude Code fait d'AGENTS.md depuis qu'il le lit."
date:   2026-10-28 09:00:00.000 +01:00
img: posts/agents-md-banner.png
tags: [IA, Claude, Codex, GitHub Copilot, Platform Engineering, Méthode]
---

Pendant des mois, j'ai travaillé avec deux agents sur les mêmes dépôts : Claude Code et Codex, l'agent d'OpenAI. L'un pour certaines tâches, l'autre pour d'autres, parfois les deux sur la même journée. Ça marchait bien, jusqu'à ce que j'ouvre un dépôt et que je compte les fichiers d'instructions.

Chaque outil veut le sien. Claude Code lit `CLAUDE.md`. Codex lit `AGENTS.md`. Copilot lit `.github/copilot-instructions.md`. Gemini CLI lit `GEMINI.md`. Des fichiers qui disent à peu près la même chose : comment builder, comment tester, quelles conventions respecter, ce qu'il ne faut surtout pas faire.

« À peu près » est le mot important. Le jour où une convention change, on met à jour un fichier sur deux, ou sur trois. Les autres continuent d'enseigner l'ancienne règle, avec beaucoup d'assurance. Selon l'agent qu'on lance, on ne reçoit plus les mêmes consignes.

Le problème n'est pas réservé à ceux qui jonglent entre deux outils, comme moi à l'époque. Il se pose dès qu'un dépôt est ouvert par des gens qui n'utilisent pas le même assistant : une équipe qui laisse chacun choisir, un projet open source, ou simplement une migration d'un outil à l'autre, pendant laquelle les deux cohabitent. Voici le modèle que j'ai retenu.

## Le principe : une source, des adaptateurs fins

L'idée tient en une phrase : **`AGENTS.md` fait foi, les autres fichiers ne font que s'y brancher**.

```text
.
├── AGENTS.md                         ← la source unique
├── CLAUDE.md                         ← importe AGENTS.md, + spécificités Claude
└── .github/
    └── copilot-instructions.md       ← couche mince, si Copilot est utilisé
```

`AGENTS.md` a un avantage décisif : il n'appartient à aucun éditeur. C'est un format ouvert, en Markdown libre, que la plupart des agents savent désormais lire. Écrire ses règles là, c'est les écrire pour l'outil d'aujourd'hui et pour celui qu'on n'utilise pas encore.

Les fichiers propres à chaque outil ne disparaissent pas pour autant. Ils changent de rôle : ils ne portent plus les règles, seulement ce qui n'a de sens que pour cet outil.

## Ce qui va dans AGENTS.md

Tout ce qui serait vrai quel que soit l'assistant. Concrètement :

- **Les commandes** : installer, builder, lancer les tests, lancer le linter. C'est la partie la plus utile du fichier, et souvent la plus oubliée.
- **Les conventions** : nommage, structure des dossiers, style des commits, langue du code et des commentaires.
- **Les interdits** : ne jamais commiter de secret, ne pas modifier tel dossier généré, ne pas ajouter de dépendance sans le dire.
- **Les renvois** vers la documentation qui existe déjà, plutôt que de la recopier.

Un exemple réduit :

```markdown
# AGENTS.md

## Commandes
- Installer : `dotnet restore`
- Builder : `dotnet build --no-restore`
- Tester : `dotnet test --no-build`

## Conventions
- Code et commentaires en anglais.
- Commits au format Conventional Commits.
- Toute nouvelle ressource Azure passe par les modules Bicep internes,
  jamais par une ressource déclarée à la main.

## Interdits
- Ne jamais commiter de secret, de chaîne de connexion ou de fichier `.env`.
- Ne pas modifier `src/Generated/` : ce dossier est régénéré à chaque build.

## Pour aller plus loin
- Règles de dépendances et de sécurité : voir `docs/standards/`.
```

Rien de spécifique à un outil, rien qui suppose une syntaxe particulière. Un humain qui arrive sur le dépôt peut le lire aussi, et c'est bon signe.

## Claude Code : il lit AGENTS.md… tant qu'il n'y a pas de CLAUDE.md

Quand j'ai commencé à écrire cet article, Claude Code ne lisait pas `AGENTS.md`. Depuis la version 2.1.277, en septembre, il le lit de lui-même. Mais la règle a une subtilité qui change tout : **il le lit à la place de `CLAUDE.md`, pas en plus**.

Par défaut, Claude Code charge `AGENTS.md` seulement s'il ne trouve ni `CLAUDE.md`, ni `.claude/CLAUDE.md`, ni `CLAUDE.local.md` dans le dossier de travail ou au-dessus. Dès qu'un de ces fichiers existe, `AGENTS.md` est ignoré. Le comportement se change dans `/config`, avec le réglage *Project instructions*, mais c'est un réglage de chaque poste, pas du dépôt.

Deux conséquences pratiques :

- **Si vous n'avez rien de propre à Claude, n'écrivez pas de `CLAUDE.md`.** `AGENTS.md` suffit, et c'est la configuration la plus simple qui soit.
- **Le piège, c'est `CLAUDE.local.md`.** Ce fichier sert à garder ses consignes personnelles hors du dépôt. Le jour où un développeur en crée un, Claude cesse de lire `AGENTS.md` sur son poste, sans le moindre message d'erreur. Les règles de l'équipe disparaissent pour lui seul.

C'est pour ça que je garde quand même un `CLAUDE.md`, qui importe `AGENTS.md` avec la syntaxe `@chemin` :

```markdown
@AGENTS.md

## Spécifique à Claude Code
- Utilise les skills du dossier `.claude/skills/` quand elles s'appliquent.
- Avant toute modification d'infrastructure, propose un plan et attends
  une validation.
```

La première ligne fait tout le travail : le contenu d'`AGENTS.md` est chargé comme s'il était écrit là, quelle que soit la version de Claude Code, quel que soit le réglage, avec ou sans `CLAUDE.local.md`. Ce qui suit ne concerne que Claude. Et Claude Code ne charge jamais `AGENTS.md` deux fois : s'il le lit déjà par l'import, il ne le relit pas.

On pourrait aussi faire de `CLAUDE.md` un lien symbolique vers `AGENTS.md`. Je ne le fais pas, pour deux raisons. Les liens symboliques se comportent mal sur certains postes Windows et dans certains outils. Et surtout, l'import laisse la place d'ajouter une section propre à Claude sans toucher à la source commune. Un lien symbolique, c'est tout ou rien.

## Les autres agents : Codex, Copilot

**Codex** est le plus simple : `AGENTS.md` est son format natif. Il en lit un par dossier, de la racine jusqu'au dossier de travail, et les concatène. Deux détails à connaître. Un `AGENTS.override.md` remplace l'`AGENTS.md` du même dossier, ce qui permet une consigne locale sans toucher à la source. Et Codex arrête de lire au-delà de 32 Kio d'instructions par défaut : un argument de plus pour un fichier court.

**Copilot** lit `AGENTS.md` dans son agent de code GitHub, mais le support dépend encore de l'endroit où on l'utilise : éditeur, CLI, revue de PR. `.github/copilot-instructions.md` reste le fichier qu'il connaît partout. Si votre dépôt est ouvert avec Copilot, gardez-le, avec une règle stricte : **il ne contient aucune règle métier**.

```markdown
Les règles de ce dépôt sont dans AGENTS.md, à la racine.
Elles s'appliquent en priorité sur toute autre consigne.

## Spécifique à Copilot
- Pour les revues de PR, commente uniquement les écarts aux règles
  d'AGENTS.md, pas le style.
```

Si une phrase de ce fichier pourrait servir à un autre agent, elle n'a rien à y faire : elle remonte dans `AGENTS.md`.

> Le support d'`AGENTS.md` évolue presque chaque mois chez les éditeurs : cet article a dû être corrigé pendant son écriture. Avant de supprimer un fichier spécifique « parce que l'outil lit AGENTS.md maintenant », vérifiez-le dans la version et le mode que vos équipes utilisent vraiment.

## La règle de tri

Pour savoir où écrire une consigne, une seule question suffit :

| Si la consigne… | Elle va dans… |
|---|---|
| serait vraie pour n'importe quel agent | `AGENTS.md` |
| dépend d'une fonctionnalité propre à Claude Code (skills, mode plan, permissions) | `CLAUDE.md`, sous l'import |
| dépend d'une fonctionnalité propre à un autre outil (revue de PR Copilot…) | le fichier de cet outil |

En pratique, la première ligne du tableau absorbe presque tout. Les autres fichiers font quelques lignes, et c'est le but.

## Pour que ça tienne dans le temps

Un modèle comme celui-ci ne résiste pas longtemps aux bonnes intentions. Il tient s'il est outillé :

- **Il arrive tout seul.** Si vous fournissez des modèles de dépôt, mettez-y les fichiers. Une équipe qui crée un repo n'a rien à inventer, elle complète `AGENTS.md`.
- **Il se vérifie.** Un contrôle en CI de trois lignes suffit à rendre la dérive visible :

  ```bash
  head -n 1 CLAUDE.md | grep -qx '@AGENTS.md' || { echo "CLAUDE.md doit commencer par @AGENTS.md"; exit 1; }
  [ "$(wc -l < .github/copilot-instructions.md)" -le 15 ] || { echo "copilot-instructions.md : les règles vont dans AGENTS.md"; exit 1; }
  ```

- **Il se relit en PR.** Modifier `AGENTS.md`, c'est modifier les consignes de tous les agents qui ouvrent le dépôt. Ça mérite un relecteur, comme n'importe quel fichier de configuration.

## Ce que ça ne règle pas

Une source unique ne garantit pas un comportement unique. Deux agents qui lisent la même consigne ne l'appliquent pas de la même façon, et c'est normal : les modèles, les modes et les fenêtres de contexte diffèrent.

Un fichier d'instructions n'est d'ailleurs qu'un conseil. L'agent le lit comme du contexte, pas comme une règle qu'il est obligé de suivre. Pour un interdit qui compte vraiment, comme « jamais de secret dans un commit », la consigne dans `AGENTS.md` ne suffit pas : il faut aussi un garde-fou qui bloque, comme un scan de secrets en pré-commit et en CI, ou un hook de l'agent.

Tout n'est pas non plus portable. Les skills, la configuration des serveurs MCP, les permissions : chaque outil a encore son format. `AGENTS.md` couvre les consignes, pas l'outillage autour.

Enfin, un fichier d'instructions coûte du contexte à chaque session. Plus il est long, moins chaque règle pèse. La tentation de tout y mettre est forte, surtout quand il devient « le » fichier de référence. Mieux vaut un `AGENTS.md` de quarante lignes qui renvoie vers la documentation qu'un roman que l'agent survole.

## Pour démarrer

Si vous avez déjà un `CLAUDE.md` ou un `copilot-instructions.md` bien rempli, la migration prend une heure :

1. Créez `AGENTS.md` et déplacez-y tout ce qui n'est pas propre à un outil.
2. Remplacez le contenu de `CLAUDE.md` par `@AGENTS.md`, puis remettez dessous ce qui ne concerne que Claude. S'il ne reste rien dessous, vous pouvez même supprimer `CLAUDE.md`, à condition que personne n'ait de `CLAUDE.local.md`.
3. Si Copilot ouvre le dépôt, videz `copilot-instructions.md` de ses règles, gardez un renvoi vers `AGENTS.md` et les seules spécificités Copilot.
4. Ajoutez le tout à votre modèle de dépôt, pour que la question ne se pose plus.

Le gain n'est pas spectaculaire le premier jour. Il apparaît le jour où une convention change : on la modifie une fois, et tous les agents la connaissent. Y compris celui qu'on adoptera l'an prochain.
