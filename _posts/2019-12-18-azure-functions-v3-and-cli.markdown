---
layout: post
title:  Azure Functions V3 et CLI
date:   2019-12-18 05:00:00.000
# categories: blog
# description: 
img: posts/azure-functions-banner.png
tags: [Azure, Functions, cli]
---

Le 9 décembre 2019, Microsoft a annoncé [la version Go-Live d'Azure Functions 3.0](https://azure.microsoft.com/fr-fr/updates/announcing-go-live-release-for-azure-functions-v3/) utilisable en production.

Voyons un peu comment profiter de cette nouvelle version, créer des functions avec cette version ou de migrer de la v2 vers la v3.

# Installer les outils

Pour la suite de cet article je vous proposerais les commandes utilisant la **cli functions**, mais également la **cli dotnet** et la **cli azure** que vous pourrez installer séparément si vous le souhaitez.

* [azure cli](https://docs.microsoft.com/fr-fr/cli/azure/install-azure-cli)
* [dotnet cli](https://docs.microsoft.com/fr-fr/dotnet/core/tools/)

## azure-functions-core-tools

Pour la cli functions, la version officielle est encore la version 2 et créera donc nativement des functions ciblant la **v2**.
Pour installer la cli azure functions v3, il faudra utiliser la commande suivante :

```bash
npm install azure-functions-core-tools@3 -g
```

Remarquez le `@3` à la fin de la commande forçant l'installation de la version 3 des outils qui vous permettront de créer des projets ciblant la V3 d'Azure Functions nativement.
Si jamais vous ne pouviez/vouliez pas installer cette version et deviez rester sur la version `core` alors vous pouvez [mettre à jour votre projet manuellement](#migrer-des-functions-de-v2-à-v3).

## vscode-azurefunctions

Si vous utilisze VS Code, sachez qu'il existe une extension permettant de manipuler et créer des functions directement depuis l'interface de VS Code. Cette extension permet également de créer des pour les projets functions ayant été créés en dehors de VS Code.
Pour l'installer, et pour rester dans le thème de la cli (vs code cette fois-ci) je vous propose la commande suivante :

_vs code cli :_

```bash
code --install-extension ms-azuretools.vscode-azurefunctions
```

# Créer un projet Azure Functions

_dotnet cli :_

```bash
dotnet new "Azure Functions"
```

_azure functions cli :_

```bash
func init --worker-runtime dotnet
```

# Créer une function

Pour l'exemple nous allons créer une function HttpTrigger.

_dotnet cli :_

```bash
dotnet new HttpTrigger --name SayHello
```

_azure functions cli :_

```bash
func new --language C# --template "HttpTrigger" --name SayHello
```

# Run votre function

_azure functions cli :_

```bash
func start --build
```

![](/assets/img/posts/func-local-start.png)

# Migrer des functions de V2 à V3

## Projet

Pour exploiter la version 3 d'Azure Functions vous souhaitez peut-être migrer vos functions existantes sans avoir à recréer un projet from scratch.

Pour cela, il vous suffit d'indiquer que vous souhaitez utiliser la version `3.0.1` du Sdk en modifiant le fichier `.csproj` ou en jouant la commande suivante :

_dotnet cli :_

```bash
dotnet add package Microsoft.NET.Sdk.Functions --version 3.0.1
```

Cela modifiera le fichier .csproj de la manière suivante :

```xml
<PackageReference Include="Microsoft.NET.Sdk.Functions" Version="3.0.1" />
```

Il faudra également modifier le fichier `.csproj` pour lui indiquer que le `TargetFramework` est `netcoreapp3.0` :

```xml
<TargetFramework>netcoreapp3.0</TargetFramework>
```

# Migration Azure

Pour bénéficier de la V3 il vous faudra également mettre à jour la plate-forme sur Azure.

![](/assets/img/posts/az_function_v3.png)

Pour cela 2 options, le portail (beurk!) ou la CLI (owi! owi!).

Dans tous les cas il s'agit de metre à jour le setting `FUNCTIONS_EXTENSION_VERSION` en lui assignant la valeur `~3`.

_azure cli :_

```bash
az functionapp config appsettings set --name $functionAppName -g $resourceGroupName --settings FUNCTIONS_EXTENSION_VERSION=~3
```

Vous pourrez alors vérifier que votre migration s'est bien déroulée en [effectuant un run de votre function](#run-votre-function).
