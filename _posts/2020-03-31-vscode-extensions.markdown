---
layout: post
title:  "VS Code : Créer ses propres extensions"
date:   2020-02-31 07:00:00.000 +01:00
# categories: blog
# description: 
img: posts/vscode-banner.png
tags: [Azure, VSCode]
---

Aujourd'hui je vais vous montrer comment créer vous-même une extension dans VS Code quand on n'y connaît rien... et même si on est une quiche en TypeScript ;)

Si vous êtes arrivé sur ce post, c'est que vous êtes probablement un utilisateur de VS Code et de ses extensions. Vous avez probablement installé les basiques pour avoir l'intellisense sur votre langage préféré, vous connecter à votre source control Git ou pour vous aider à créer des fichiers JSON ou YAML.

Dans cet article, nous referons un tour d'horizon de ce qu'est une extension VS Code et nous verrons les bases de la création d'une extension.

<!-- Pour l'exercice je vous propose de créer une extension assez simple mais utile.
Mais, qu'est-ce qu'une extension utile ?
Pour ma part j'ai une mémoire de poisson rouge quand et j'oublie toujours les commandes de git, dotnet ou autre CLI (Command-Line Interface). J'ai donc créé des cheat-sheet pour git, puis pour dotnet, permettant de retrouver les commandes via une recherche "logique".
Nous allons voir ici comment faire la même chose pour la cli d'azure ou "az cli".
Mais surtout, nous allons essayer de créer un modèle que vous pourrez réutiliser et adapter à vos besoins. -->

**\[TLDR\]** Si vous souhaitez directement voir comment créer une extension sans la partie théorique, je vous propose d'aller directement à la partie [Environnement de travail](#Environnement-de-travail).

## Visual Studio Code

> " VS Code est un éditeur de code source léger et un environnement de développement puissant pour la création et le débogage d'applications Web et cloud modernes.

Il est gratuit et disponible sur votre plate-forme préférée :

- Linux,
- MacOS,
- Windows.

Parmi ses fonctionnalités, on compte :

- Intellisense : Code-complétion intelligente (variables, méthodes, modules)
- Debug tools (breakpoints, terminal)
- Edition puissante (multi-curseur, info paramêtres, refactoring, navigation, etc.)
- Source Control (Git, TFS, …)

## Les Extensions

VS Code est extensible. C'est-à-dire que des fonctionnalités peuvent être ajoutées telles des plugins à VSCode. Ces nouvelles fonctionnalités s'appellent des **extensions**.

Vous pouvez les retrouver via le panneau latéral en ouvrant l'Extensions View.

![AZ-400 Pluralsight Path details](/assets/img/posts/vsce-extensions-view.png)

Dans cette vue vous pourrez:

- Rechercher des extensions
- Inspecter (changelog, commands, etc.) des extensions pour connaître les détails
- Installer une extension (Install, Reload, Update)

## Extension Generator

La création d'extension se fait en utilisant un moteur intitulé **Yeoman** qui orchestre des **générateurs**, dont un spécifiquement pour les extensions VS Code.
Ce moteur se base sur Node.js et fonctionne en ligne de commande.

![Yeoman](/assets/img/posts/vsce-yeoman.png)

## Extension Description

Les extensions contiennent un fichier de description servant plusieurs buts.
Tout d'abord, nous allons pouvoir y déclarer son *identité*, avec son nom, celui de l'éditeur, voire du repository si jamais l'extension est open-source.
Nous allons également pouvoir y renseigner des informations plus techniques sur le mode de fonctionnement (lancement, settings, etc.).

Les extensions peuvent avoir des dépendances supplémentaires telles que des linters autonomes, des compilateurs ou des fichiers de configuration personnalisés afin de fonctionner correctement.
Le fichier README de l'extension, affiché dans le volet *Détails* de la vue *Extensions*, inclut des détails sur la configuration et l'utilisation de l'extension.

Voici, par exemple, la description de mon extension **Git CLI Explorer**.

![extension details view](/assets/img/posts/vsce-details-view.png)

## Extension API

### Activation Events

Votre extension va devoir être déclenchée. Elle peut être déclenchée par un évènement ou un contexte particulier. Ces *évènements* d'activation sont décrits dans le fichier **package.json**

![extension details view](/assets/img/posts/vsce-activationevents.png)

Nous pourrons y trouver des **activation events** tels que:

- onLanguage:${language}
- onCommand:${command}
- onDebug
- workspaceContains:${toplevelfilename}
- onFileSystem:${scheme}
- onView:${viewId}
- onUri
- onWebviewPanel:${viewType}

### Commands (ou Command Handler)

Une extension peut également être déclenchée via des commandes spécifiques qui seront appelées, soit par l'utilisateur, soit par d'autres extensions.

Ces commandes sont décrites dans le fichier **extension.ts**

![extension details view](/assets/img/posts/vsce-commands-declaration.png)

Elles sont ensuite déclarées via un *binding* ou *liaison* dans le fichier **package.json**.

![extension details view](/assets/img/posts/vsce-commands-binding.png)

L'erreur de débutant est de définir la logique de la commande, de faire le *registerCommand* et d'oublier sa déclaration.
Ne l'oubliez surtout pas car c'est le *contrat* de votre extension qui permettra donc à VS Code de savoir quels en sont les déclencheurs.

### Complex Commands

Bien evidemment, vous pouvez aller plus loin et créer des extensions avancées avec des logiques complexes. Pour cela vous pourrez vous baser sur des API VSCode :

- vscode.diff
- vscode.open
- cursorMove
- vscode.ExectureDocumentRenameProvider
- etc.

```javascript
let uri = Uri.file('/some/path/to/folder’);
let success = await commands.executeCommand('vscode.openFolder', uri);
```

Vous pourrez trouver toutes les API disponibles en suivant ce lien vers la documentation officielle :
[Built-in Commands](https://code.visualstudio.com/docs/extensionAPI/vscode-api-commands)

<!-- ## Publication

### vsce - packaging

Command-Line

```bash
npm install –g vsce
```

Packaging (local, sideloading, CI, etc.)

```bash
vsce package
```

### vsce - publish

Création d’un compte Publisher
Azure DevOps – Personal Acces Token

```bash
vsce create-publisher <your_name>
```

(vous demandera le token)

ou depuis la page de management https://marketplace.visualstudio.com/manage

```bash
vsce-login
```

Publication

```bash
vsce publish
vsce publish –t <token>
vsce publish minor
vsce publish 2.0.1
``` -->

## Environnement de travail

Pour commencer il faut installer quelques outils.

Il faudra bien évidemment avoir VS Code que vous pourrez télécharger en suivant ce lien: [https://code.visualstudio.com/Download](https://code.visualstudio.com/Download)

Vous devrez ensuite installer le *moteur de générateurs* **Yeoman**. Il s'agit d'un outil basé sur **Node.js** qui vous permettra de créer des extensions via un formulaire en ligne de commande.

Commencez donc par installer Node.js si ce n'est pas déja fait: [https://nodejs.org/en/](https://nodejs.org/en/)

Vous pourrez alors installer le générateur *VS Code Extension Generator* avec la commande suivante:

```bash
npm install -g yo generator-code
```

## Générer l'extension

### Espace de travail

Nous allons commencer par initialiser notre espace de travail. Il faut donc se placer dans le répertoire qui contiendra le code de votre extension. C'est dans ce dossier parent que le générateur créera un dossier pour votre extension. Pour ma part, il s'agit du répertoire *'repos'*.

Placez-vous donc dans le dossier de votre choix et ouvrez celui-ci dans VS Code ou une fenêtre de commande.

### Utiliser le générateur

Pour utiliser Yeoman et plus spécifiquement le générateur d'extension VS Code, il faut lancer la commande suivante:

```bash
yo code
```

Un *formulaire* s'ouvre alors dans votre terminal.
Pour ma part j'ai créé une extension nommée **helloWorld**, mais vous pouvez bien évidemment choisir le nom de votre choix et remplacer dans les insctructions suivantes.
Renseignez donc les paramètres suivants :

- type : **New Extension (TypeScript)**
- name : **helloWorld**
- identifier : **helloWorld**
- description : **Dynamic Cheat Sheet for Azure CLI**
- publisher name : **votre publisher name**
- Enable stricter TypeScript checking : **Y**
- Setup linting using 'tslint' : **Y**
- Initialize a git repository? : **n**

![yo code success](/assets/img/posts/vsce-yoCodeHelloWorld.png)

Un nouveau dossier **helloWorld** a été créé.

### Initialiser le workspace

Le code de base de notre extension étant généré, nous pouvons ouvrir cet espace de travail dans VS Code:

```bash
cd helloWorld
code .
```

## Debug de l'extension

Appuyez sur `F5`

Une nouvelle fenêtre VS Code doit s'ouvrir, intitulée `Extension Development Host`.
Dans cette fenêtre, vous pouvez utiliser votre extension.

## Exécution de la commande

Appuyez sur `Ctrl + Shift + P` pour Windows (`Cmd +Shift + P` pour Mac) et lancez la commande `Hello World`.

![VSCE command hello world](/assets/img/posts/vsce-commandHelloWorld.png)

Une fenêtre de message doit alors apparaître avec le message `Hello World`.

![VSCE Dialog window hello world](/assets/img/posts/vsce-dialogHelloWorld.png)

## Modifier la commande

Ouvrez le fichier *package.json* et remplacez le code suivant :

```javascript
"commands": [
    {
        "command": "extension.sayHello",
        "title": "Say Good Evening"
    }
]
```

Ouvrez le fichier *extension.ts* et remplacez le code suivant :

```javascript
// Display a message box to the user
vscode.window.showInformationMessage('Good Evening !');
```

Appuyez sur `F5` pour lancer l'extension et lancez la commande `Say Good Evening`.
Vous devriez alors voir le message suivant :

![VSCE dialog Good Evening](/assets/img/posts/vsce-dialogGoodEvening.png)

## Conclusion

Voilà, vous avez créé votre première extension. 🥳

Ok elle est un peu simple mais vous avez les bases pour créer des extensions un peu plus utiles.

Je vous donne d'ailleurs rendez-vous dans quelques jours pour découvrir comment créer une cheat-sheet dynamique avec VS Code.

D'ici là, **#restezchezvous** !!!
