---
layout: post
title:  "Github Actions 2.0 : Déploiement Continu sur Azure"
date:   2019-10-05 08:32:49.067
# categories: blog
# description: 
img: posts/github-actions-banner.png
tags: [Azure, Github Actions, DevOps]
redirect_from: 
    - /blog/github-actions-2-for-continuous-deployment-on-azure
    - /blog/github-actions-2-for-continuous-deployment-on-azure/
---

Cet article est le second d'une s&eacute;rie sur **Github Actions** et la mise en place d'une cha&icirc;ne CI/CD.

* [Github Actions 2.0 pour une CI .NET Core](/2019/09/github-actions-2-for-dotnet-core-ci)
* [Github Actions 2.0 : Déploiement Continu sur Azure](/2019/10/github-actions-2-for-continuous-deployment-on-azure)
* [Github Actions : Azure CLI & Service Principal](/2020/01/github-actions-az-cli)

Aujourd'hui, nous verrons comment cr&eacute;er un pipeline de d&eacute;ploiement d'applications sur Azure PaaS.

Vous pourrez m&ecirc;me afficher un petit badge ;)

![](https://github.com/michaelfery/pipelines-dotnet-core/workflows/ASP.NET%20Core%20CI-CD/badge.svg)

## Pr&eacute;paration

Tout d'abord, il faut avoir Github Actions activ&eacute;. Oui, je l'ai d&eacute;j&agrave; dit lorsque l'on a mis en place l'int&eacute;gration continue mais bon... Encore une fois, [allez faire votre demande d'acc&egrave;s rapidement](https://github.com/features/actions) ou attendez le 13 novembre pour la disponibilit&eacute; g&eacute;n&eacute;rale.

Il vous faudra &eacute;galement avoir packag&eacute; votre application et cr&eacute;&eacute; un **artifact**. Pour cela je vous invite &agrave; revenir &agrave; [mon article pr&eacute;c&eacute;dent](../github-actions-2-for-dotnet-core-ci), et notamment la section [Package &amp; Artifacts](../github-actions-2-for-dotnet-core-ci#package).

Vous devriez voir votre artifact dans la partie en haut &agrave; droite de vos logs de workflow :

![](/assets/img/posts/github-actions-artifact_637035344507674284.png)

## Azure Web App

Pour d&eacute;ployer notre application dans une Web App Azure, il nous faut ... ? Allez je vous laisse encore quelques sencondes... une Web App?! Ouiiii! Bravo !

 

## Provisionnement

Je ne vous montrerai pas ici comment provisionner une Web App mais je peux vous fournir un lien vers [la documentation App Service](https://docs.microsoft.com/fr-fr/azure/app-service/).

Pour les allergiques du clic ou afficionados de l'automatisation pouss&eacute;e, je vous renvoie vers le blog post de mon coll&egrave;gue Wilfried qui a d&eacute;j&agrave; expliqu&eacute; [comment d&eacute;ployer une infra via ARM depuis Github Actions](https://blog.woivre.fr/blog/2019/09/arm-utiliser-github-actions-pour-deployer-vos-templates).

Bref, dans tous les cas, on partira ici du principe que votre Web App est provisionn&eacute;e, et que votre artifact s'appelle webapp.

 

## Publishing Profile

Pour d&eacute;ployer votre Web App, vous aurez besoin du profil de d&eacute;ploiement. Comme son nom l'indique, il contient les informations n&eacute;cessaires pour le d&eacute;ploiement, telles que l'url, les identifiants de connexion, etc.

Ouvrez le [portail Azure](https://portal.azure.com) et naviguez jusqu'&agrave; votre Web App.

Dans la barre de commandes en haut, cliquez sur "**Get publish profile**".

![Get publish profile](https://azure.github.io/AppService/media/2019/08/GitHubActions-publish-profile.png)

Une fois le fichier t&eacute;l&eacute;charg&eacute;, ouvrez-le avec un &eacute;diteur de texte et copiez le contenu du fichier XML :

`<publishData><publishProfile profileName="actionsSampleApp - Web Deploy" publishMethod\="MSDeploy" ...`

Retournons maintenant dans notre repository Github.

Ouvrez le menu **Settings,** puis dans le menu de gauche, ouvrez le panneau **Secrets**.

![github-secrets.png](/assets/img/posts/github-secrets_637041579443940522.png)

Cliquez ensuite sur **Add a new secret** et collez le contenu de votre publish profile.

Vous devriez donc avoir un nouveau secret dans votre repo :

![github-secrets-publish-profile.png](/assets/img/posts/github-secrets-publish-profile_637041581078655258.png)

Passons ensuite au workflow et aux &eacute;tapes de d&eacute;ploiement.

## Workflow de d&eacute;ploiement

Nous reprendrons ici le fichier workflow que nous avions commenc&eacute; dans l'[article sur l'int&eacute;gration continue](../github-actions-2-for-dotnet-core-ci/).

`/.github/workflows/{workflow_name}.yml`

Dans ce fichier, nous allons ajouter des &eacute;tapes de d&eacute;ploiement, en commen&ccedil;ant par d&eacute;clarer un **job** que nous appelerons **"deploy"** et qui n&eacute;cessite l'&eacute;x&eacute;cution du job **"build"** au pr&eacute;alable.

name: ASP.NET Core CI

```yml
on: [push]
jobs:
  build:
...
  deploy:
    needs: build
    runs-on: windows-latest
```

La premi&egrave;re &eacute;tape consiste &agrave; r&eacute;cup&eacute;rer le **package de notre application**, c'est-&agrave;-dire l'**artifact**. Pour cela, nous utiliserons l'action **download-artifact** en lui pr&eacute;cisant le nom de l'artifact **webapp**.

```yml
    steps:
    - name: Download Artifacts
      uses: actions/download-artifact@master
      with:
        name: webapp
```

Nous voici enfin &agrave; l'&eacute;tape tant attendue de d&eacute;ploiement.

Nous utiliserons ici l'action **appservice-actions/webapp**. Il faudra pr&eacute;ciser &agrave; l'action le nom de la Web App, le nom du package, ainsi que le nom du Secret au format :

 **secrets.{secret\_name}**.

```yml
    steps:
    - ...
    - name: Deploy to Azure App Service
      uses: azure/appservice-actions/webapp@master
      with:
        app-name: actionsSampleApp
        package: webapp
        publish-profile: {% raw %}${{ secrets.webappPublishProfile }}{% endraw %}
```

Il ne vous restera alors qu'&agrave; d&eacute;marrer manuellement votre pipeline ou &agrave; attendre votre prochain commit sur master, via une PR &eacute;videmment ;)

![github-actions-deploy-success.png](/assets/img/posts/github-actions-deploy-success_637041601022595331.png)

## Conclusion

Dans cet article, nous avons d&eacute;couvert comment utiliser Github Actions 2.0 pour mettre en place un pipeline de d&eacute;ploiement continu en quelques minutes.

Dans un prochain article, nous d&eacute;couvrirons des fonctionnalit&eacute;s avanc&eacute;es de Github Actions pour Azure.

A bientôt !
