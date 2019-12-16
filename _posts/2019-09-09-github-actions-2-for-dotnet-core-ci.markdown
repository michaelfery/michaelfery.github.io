---
layout: post
title:  Github Actions 2.0 pour une CI .NET Core
date:   2019-09-09 05:30:00.000
# categories: blog
# description: 
img: posts/github-actions-banner.png
tags: [dotnet, Github Actions, DevOps, Continuous Integration, Github]
redirect_from: 
    - /blog/github-actions-2-for-dotnet-core-ci
    - /blog/github-actions-2-for-dotnet-core-ci/
---

Il y a quelques jours j'ai re&ccedil;u le Saint-Graal: l'activation de la fonctionnalit&eacute; Actions de Github.

Il s'agit l&agrave; de pouvoir g&eacute;rer ses pipelines de Continuous Delivery directement depuis Github.

Apr&egrave;s avoir pu jouer un peu, je vais pouvoir vous faire un petit &eacute;tat des lieux sur Github Actions et notamment l'utilisation pour construire un pipeline d'int&eacute;gration continue pour vos applications dotnet core.

Vous pourrez m&ecirc;me afficher un petit badge ;)

![](https://github.com/michaelfery/pipelines-dotnet-core/workflows/ASP.NET%20Core%20CI-CD/badge.svg)

# Pr&eacute;paration

Tout d'abord, il faut avoir Github Actions activ&eacute;. Oui &ccedil;a para&icirc;t con, mais je pr&eacute;f&egrave;re le dire avant qu'on me dise "Ouiiin &ccedil;a marche pas !". Pour ceux qui ne l'auraient pas encore et se tiennent pr&ecirc;ts &agrave; me dire "Oh tu te la p&egrave;tes avec ton acc&egrave;s &agrave; Github Actions", sachez qu'il semble y avoir des vagues de validation en masse ces derniers jours. Alors [allez faire votre demande d'acc&egrave;s rapidement](https://github.com/features/actions)&nbsp;ou attendez le 13 novembre pour la disponibilit&eacute; g&eacute;n&eacute;rale.

Ensuite, il vous faut un repository avec le code d'une application dotnet core. Si vous n'en avez pas, vous pouvez toujours forker le repository suivant : [https://github.com/MicrosoftDocs/pipelines-dotnet-core](https://github.com/MicrosoftDocs/pipelines-dotnet-core). C'est celui que j'utiliserai dans ma d&eacute;mo.

# Workflow template

Passons aux choses s&eacute;rieuses.

Dans votre repository, cliquez sur Actions.

![github-actions.png](/assets/img/posts/github-actions_637034779851632865.png)

Dans la fen&ecirc;tre Actions, vous pouvez choisir de partir sur&nbsp;un workflow compl&egrave;tement vide ou de choisir un template correspondant &agrave; votre projet.

Pour cet exemple, nous choisirons le template ASP.NET Core. Vous pouvez aussi partir d'un template vide et tout retaper mais bon...

![aspnetcore-github-workflow-template.png](/assets/img/posts/aspnetcore-github-workflow-template_637034779853122445.png)

Une fois votre template choisi, Github cr&eacute;e pour vous un fichier au format YAML. C'est l&agrave; que la magie op&egrave;re.

Si vous avez d&eacute;ja touch&eacute; au YAML avec Azure DevOps ou un autre gestionnaire de pipeline, alors vous ne serez pas d&eacute;pays&eacute;s, m&ecirc;me si des sp&eacute;cifit&eacute;s existent entre les &eacute;diteurs: "Just another flavor of YAML" ;) Pour les autres et ceux qui voudraient conna&icirc;tre les fonctionnalit&eacute;s disponibles dans le YAML &agrave; la sauce Github Actions, vous pouvez vous rendre sur&nbsp;[la documentation associ&eacute;e](https://help.github.com/en/articles/workflow-syntax-for-github-actions#about-yaml-syntax-for-workflows).

# Build

Regardons un peu notre fichier YAML de workflow.

![github-actions-aspnetcore-template.png](/assets/img/posts/github-actions-aspnetcore-template_637035321061673205.png)

Il faut tout d'abord d&eacute;finir le "trigger" ou "d&eacute;clencheur" du workflow. A chaque push sur le repository, le workflow sera d&eacute;clench&eacute;.

```yml
on: [push]
```

Ok, maintenant on s'attaque au build puisque l'on souhaite r&eacute;aliser notre int&eacute;gration continue.

```yml
jobs:
build:
```

On fait du .NET Core donc on peut compiler sur une distrbution Linux et on ne va pas se priver.

```yml
runs-on: ubuntu-latest
```

On utilise ici une action packag&eacute;e et propos&eacute;e par Github pour installer la CLI dotnet sur l'agent en sp&eacute;cifiant la version.

```yml
uses: actions/setup-dotnet@v1
with:
dotnet-version: 2.2.108
```

Enfin, on utilise la CLI dotnet pour lancer la commande de build de notre projet. Nous ne sp&eacute;cifions pas le dossier dans cet exemple mais, selon votre arborescence de repository, il faudra peut-&ecirc;tre sp&eacute;cifier le ou les fichiers .csproj &agrave; compiler.

```yml
- name: Build with dotnet
  run: dotnet build --configuration Release
```

# Logs

Une fois le fichier sauv&eacute; et inclus dans le r&eacute;pository Github, l'action sera d&eacute;clench&eacute;e et vous pourrez suivre le d&eacute;roulement de celui-ci.

![github-actions-build-success.png](/assets/img/posts/github-actions-build-success_637035344507261652.png)

Par d&eacute;faut un email est envoy&eacute; en cas d'&eacute;chec du workflow.

Vous pouvez &eacute;galement ajouter un petit badge dans le d&eacute;scriptif de votre repository ou tout autre endroit pertinent en utilisant le format d'url suivant :

```
https://github.com/{github_id}/{repository}/workflows/{workflow_name}/badge.svg
```

![](https://github.com/michaelfery/pipelines-dotnet-core/workflows/ASP.NET%20Core%20CI-CD/badge.svg)

Cool, on a cr&eacute;&eacute; un workflow qui, &agrave; chaque push sur master, d&eacute;marre un build sur notre projet et nous informe en cas d'erreur. Essayons d'aller un peu plus loin.

# Tests

> "Hey Micha&euml;l, tu nous saoules avec tes tests sur tous tes projets et tu crois qu'on va te laisser t'en sortir comme &ccedil;a ?"

Vous avez raison, notre application compile, c'est bien mais pas suffisant pour une int&eacute;gration continue. Voyons donc comment effectuer quelques tests unitaires.

La CLI dotnet comporte une commande **test**&nbsp;qu'il ne nous reste qu'&agrave; ajouter comme step de notre fichier de workflow :

```yml
- name: Test with dotnet
  run: dotnet test --configuration Release
```

L&agrave; encore, je ne pr&eacute;cise pas les projets de tests car mon arborescence me le permet, mais libre &agrave; vous de les sp&eacute;cifier.

# <a id="package"></a>Package &amp; Artifacts

Une fois notre Build effectu&eacute;e et les tests pass&eacute;s avec succ&egrave;s, dans une optique de pipeline d'int&eacute;gration continue (voire de d&eacute;ploiement continu), il nous faut g&eacute;n&eacute;rer un package de notre application.

Pour cela, rien de plus simple, il nous suffit de rajouter quelques steps.

Tout d'abord, nous allons **packager** notre application :

```yml
- name: Package with dotnet
  run: dotnet publish --configuration Release
```

Et enfin, nous allons publier le r&eacute;sultat dans un **artifact**.

```yml
name: Publish artifact
uses: actions/upload-artifact@master
with:
  name: webapp
  path: bin/Release/netcoreapp2.2/publish
```

Nous utilisons ici l'action packag&eacute;e et propos&eacute;e par Github **actions/upload-artifact**. Cette action n&eacute;cessite de d&eacute;finir un nom ainsi que le r&eacute;pertoire &agrave; inclure dans cet artifact.

Une fois ces actions ajout&eacute;es et ajust&eacute;es &agrave; vos besoins, vous devriez voir appara&icirc;tre un nouveau bouton en haut &agrave; droite de vos logs de worflow.

![github-actions-artifact.png](/assets/img/posts/github-actions-artifact_637035344507674284.png)

&nbsp;

# Conclusion

Dans cet article, nous avons d&eacute;couvert Github Actions 2.0\. Cela pourra s'av&eacute;rer d&eacute;suet pour ceux qui connaissaient d&eacute;j&agrave; Actions ou Azure DevOps, mais il me semble que l'outil soit peu connu et notamment dans l'&eacute;cosyst&egrave;me .NET. De plus, la mise en place d'une int&eacute;gration continue en quelques minutes, de mani&egrave;re gratuite et sans sortir de Github me para&icirc;t vraiment int&eacute;ressante.

Dans un prochain article, nous d&eacute;couvrirons Github Actions for Azure.

A bient&ocirc;t !