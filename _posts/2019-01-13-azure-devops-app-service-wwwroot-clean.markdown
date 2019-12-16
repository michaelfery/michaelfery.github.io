---
layout: post
title:  "Azure DevOps &amp; App Service : faîtes le ménage dans le dossier wwwroot"
date:   2019-01-13 13:54:28.422
# categories: blog
description: Comment faire en sorte que le process de déploiement Azure DevOps fasse le ménage dans le dossier wwwroot de destination d'un App Service avant tout nouveau déploiement
img: posts/azure-banner.png
tags: ["azure", "app service", "azure devops"]
redirect_from: 
    - /blog/azure-devops-app-service-wwwroot-clean
    - /blog/azure-devops-app-service-wwwroot-clean/
---

<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;">Après plusieurs dizaines, voire centaines de déploiement, le dossier</span>`wwwroot`<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;"> de votre **Azure App Service** est complétement rempli de fichiers, pas forcément tous utiles.</span>

<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;">Ces fichiers peuvent provenir du cycle de vie de votre app service mais il se peut également qu'ils soient le résultat historique du développement et des déploiements associés.</span>

<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;">Il se peut même que ces fichiers entre en conflit avec les futurs déploiement et corrompent le fonctionnement normal de votre service.</span>

<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;">Pour nettoyer le contenu de ce dossier, vous pouvez vous connecter via les **Developement Tools** proposés depuis le portail Azure et y supprimer à la main les fichiers inutiles :</span>

<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;">![appServiceDevTools.png](/assets/img/posts/appServiceDevTools_636829856230451962.png)</span>

<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;">Mais une méthode plus simple, plus propre et automatisée, est de configurer votre tâche de déploiement depuis **Azure DevOps** pour qu'elle s'occupe de faire le ménage automatiquement avant chaque déploiement.</span>

<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;">En effet, depuis votre pipeline de déploiement, en utilisant la task </span>**Azure App Service Deploy**<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;"> vous pouvez utiliser l'option </span>**Publish using Web Deploy** dans la sous-partie intitulée Additional Deployment Options.

![azureDevopsWebDeployTask.png](/assets/img/posts/azureDevopsWebDeployTask_636829856231823073.png)

Cette option n'est pas cochée par défaut mais une fois activée elle vous permettra de sélectionner l'option suivante : **Remove additional files at destination** :

![azureDevopsWebDeployTaskRemoveDestinationFiles.png](/assets/img/posts/azureDevopsWebDeployTaskRemoveDestinationFiles_636829856231953279.png)

<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;">Si vous activez cette option, le déploiement va supprimer tout fichier dans le répertoire destination qui n'existe pas dans le package à déployer.</span>

<span style="color: #242729; font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif; font-size: 15px;">En d'autres mots, la tâche de déploiement supprimera tous les fichiers des précédents déploiements qui ne sont plus nécessaires.</span>