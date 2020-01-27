---
layout: post
title:  "Github Actions : Azure CLI & Service Principal"
date:   2020-01-27 07:00:00.000 +01:00
# categories: blog
# description: 
img: posts/github-actions-banner.png
tags: [Azure, Github Actions, DevOps]
---

Cet article est le troisième d'une s&eacute;rie sur **Github Actions** et la mise en place d'une cha&icirc;ne CI/CD.

* [Github Actions 2.0 pour une CI .NET Core](/2019/09/github-actions-2-for-dotnet-core-ci)
* [Github Actions 2.0 : Déploiement Continu sur Azure](/2019/10/github-actions-2-for-continuous-deployment-on-azure)
* [Github Actions : Azure CLI & Service Principal](/2020/01/github-actions-az-cli)

Dans les derniers articles, nous avons vu comment créer un pipeline de CI/CD avec Github Actions et Azure.

Aujourd'hui, nous verrons comment optimiser notre pipeline de d&eacute;ploiement d'applications sur Azure pour y apporter modernité et sécurité en utilisant **az cli** et **service principal**.

## Pr&eacute;paration

Tout d'abord, je vous invite à vous réferer aux étapes des articles précédents pour comprendre les différents éléments qui composent le pipeline "classique" au format YAML.

Une fois ces révisions effectuées et les concepts acquis, nous pouvons commencer avec un pipeline tout fait et prêt à être modifié.

Voici donc le fichier auquel nous avions abouti, avec de très légères améliorations esthétiques dont l'usage de variables:

```yml
name: CI/CD Classic
on: [push]
env:
  ARTIFACT_NAME : webapp
  AZURE_WEBAPP_NAME: actionsSampleApp
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v1
    - name: Setup .NET Core
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 2.2.108
    - name: Build with dotnet
      run: dotnet build ./src --configuration Release
    - name: Package with dotnet
      run: dotnet publish ./src --configuration Release
    - name: Publish artifact
      uses: actions/upload-artifact@master
      with:
        name: {% raw %}${{ secrets.ARTIFACT_NAME }}{% endraw %}
        path: src/bin/Release/netcoreapp2.2/publish
  deploy:
    needs: build
    runs-on: windows-latest
    steps:
      - name: Download Artifacts
        uses: actions/download-artifact@master
        with:
          name: {% raw %}${{ env.ARTIFACT_NAME }}{% endraw %}
      - name: Deploy to Azure App Service
        uses: azure/appservice-actions/webapp@master
        with:
          app-name: {% raw %}${{ env.AZURE_WEBAPP_NAME }}{% endraw %}
          package: {% raw %}${{ env.ARTIFACT_NAME }}{% endraw %}
          publish-profile: {% raw %}${{ secrets.webappPublishProfile }}{% endraw %}
```

## Azure Actions

La première étape pour notre modernisation de pipeline est de remplacer l'usage de l'action `azure/appservice-actions/webapp@master` par une action azure plus récente et non dépréciée.

Nous pourrions donc modifier notre step de déploiement de cette manière:

```yml
# deploy web app using Azure credentials
- name: 'Azure webapp deploy'
  uses: azure/webapps-deploy@v1
  with:
    creds: {% raw %}${{ secrets.azureWebAppPublishProfile }}{% endraw %}
```

C'est un bon début mais on utilise toujours le **Publish Profile** comme mécanisme de connexion et le Publish Profile... c'est mal !

## Azure CLI

L'action `azure/webapps-deploy` peut se passer d'un Publish Profile et utiliser **az cli**. Parfait! Mais, pour cela, elle nécessite d'être authentifiée auparavant.

Nous allons donc remplacer notre job `deploy` pour y ajouter les steps utilisant les actions `azure/login` et `azure/webapps-deploy`.

```yml
deploy:
  needs: build
  runs-on: windows-latest
  steps:
    - name: Download Artifacts
      uses: actions/download-artifact@master
      with:
        name: {% raw %}${{ env.ARTIFACT_NAME }}{% endraw %}
    # set up az cli login
    - uses: azure/login@v1
      with:
        creds: {% raw %}${{ secrets.AZURE_CREDENTIALS }}{% endraw %}
    # deploy web app using Azure credentials
    - name: 'Azure webapp deploy'
      uses: azure/webapps-deploy@v1
      with:
        app-name: {% raw %}${{ env.AZURE_WEBAPP_NAME }}{% endraw %}
        package: 'webapp'
```

Hmmm, ok mais en fait on remplace juste un secret Publish Profile par un autre : AZURE_CREDENTIALS. C'est quoi l'intérêt ?!

## Service Principal

Dans les steps de déploiements, nous avons utilisé une action basé sur la cli d'azure.
Pour cela nous avons introduit un nouveau **secret**: `{% raw %}${{ secrets.AZURE_CREDENTIALS }}{% endraw %}`.

* Le **Publish Profile** est un profil d'authentification permettant de publier notre app mais ne permettant pas réellement d'identifier l'auteur du déploiement ni de gérer finement les droits.
* Le **Service Principal**, quant à lui est une identification liée à une attribution de rôle qui fournit l’accès à des ressources Azure. Dans notre cas, le Service Principal sera donc lié à notre workflow de déploiement via Github Actions et pourra être administré de manière précise depuis Azure.

L'identité nécessaire pour publier notre application doit donc avoir les droits associés. Cela se fait en générant un Service Principal sur un scope défini via la cli depuis **Azure Cloud Shell** par exemple.

Plusieurs options s'offrent à nous. Soit on scope la souscription, soit le ressource group contenant l'appservice, soit la ressource app service directement.
Dans une approche **least privilege** je ne saurais que vous conseillez de restreindre les droits à la ressource.

```bash
az ad sp create-for-rbac --name "my-service-principal-name" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

Dans cet exemple, remplacez les espaces réservés dans la ressource par votre ID d’abonnement, votre groupe de ressources et le nom de votre application.

L'éxécution de cette commande génère un output au format **JSON** fournissant l'accès à votre App Service.

A la manière du **Publish Profile** des articles précédents, nous allons donc pouvoir ajouter un secret via l'interface d'administration de Github.
Vous pourrez donc copier-coller l'output de la commande précédente dans un **secret** Github, nommé **AZURE_CREDENTIALS**.