---
layout: post
title:  "Service Fabric Mesh Tools: VS Code Extension"
date:   2018-08-20 05:00:00<
# categories: blog
description: "Dans le dernier post je vous présentais Azure Service Fabric Mesh, la solution cluster-less de Microsoft pour orchestrer vos containers. Cette semaine, je vous propose de découvrir l'extension VS Code que j'ai développée: Service Fabric Mesh Tools."
img: /posts/sfmeshtools-marketplace.png
tags: ["vscode", "azure", "service fabric"]
redirect_from: "/blog/service-fabric-mesh-tools-extension"
---

Dans [le dernier post](/blog/service-fabric-mesh-public-preview/) je vous présentais Azure Service Fabric Mesh, la solution cluster-less de Microsoft pour orchestrer vos containers.

Nous avions vu en détail les composantes du nouveau template de déploiement.

Cette semaine, je vous propose de découvrir l'extension Visual Studio Code que j'ai développée: **Service Fabric Mesh Tools**.

# Visual Studio Code Extension

L'extension est disponible depuis Code ou [sur le MarketPlace](https://marketplace.visualstudio.com/items?itemName=mfery.sf-mesh-tools).

 ![generator-azuresfmesh.png](/assets/img/posts/sfmeshtools-marketplace.png)

# Fonctionnalités

L'objectif de cette extension est de faciliter l'usage de Service Fabric Mesh, de la création d'applications au monitoring, en passant par le déploiement.

Comme Mesh, cette extension vous permettra de créer ces applications à partir de vos images, de les déployer et de les monitorer.

Les fonctionnalités actuelles sont les suivantes:

 ![sfmeshtools-commands.png](/assets/img/posts/sfmeshtools-commands.png)

*   **Service Fabric Mesh: Create Application** (the command creates the mesh template and deployment profile)
*   **Service Fabric Mesh: Add Service** (the command adds a new service to an existing Service Fabric Mesh application)
*   **Service Fabric Mesh: Generate deployment profile** (from template file or uri)
*   **Service Fabric Mesh: Login**
*   **Service Fabric Mesh: Deploy To Azure** (the command creates the resource group if needed)
*   **Service Fabric Mesh: List the deployed applications**
*   **Service Fabric Mesh: List the deployed networks**
*   **Service Fabric Mesh: List the services of an Application**

# Prérequis

 Afin de fonctionner, cette extension utilise VS Code, N la CLI ainsi que Yeoman.

*   [Install Visual Studio Code](https://code.visualstudio.com/)
*   [Install Node.js](https://nodejs.org/en/)
*   [Setup Service Fabric Mesh CLI](https://docs.microsoft.com/en-us/azure/service-fabric-mesh/service-fabric-mesh-howto-setup-cli)
*   [Install Git](https://git-scm.com/)
*   Install Yeoman Generators
    *   npm install -g yo
    *   npm install -g generator-azuresfmesh

## Yeoman Generator

Cette extension a été développée entièrement avec Visual Studio Code et se base sur un générateur **Yeoman** également développé pour l'occasion: **generator-sfmesh** disponible directment depuis la command-line ou [https://www.npmjs.com/package/generator-azuresfmesh](https://www.npmjs.com/package/generator-azuresfmesh).

 ![generator-azuresfmesh.png](/assets/img/posts/generator-azuresfmesh.png)

## Service Fabric Mesh Tools

Pour installer l'extension, rien de plus simple: depuis Visual Studio Code, ouvrez l'onglet Extensions et cherchez _"Service Fabric Mesh"_.

 ![generator-azuresfmesh.png](/assets/img/posts/sfmeshtools-install.png)

# Vous voulez contribuez ?

Y ayant pris goût, j'essaierai probablement de vous faire un retour sur le développement de générateurs Yeoman et d'extensions Visual Studio Code :)

En attendant je vous invite à découvrir le code source du générateur Yeoman et de l'extension Visual Studio Code depuis leur repos respectifs ainsi qu'à contribuer au bugfix et/ou à l'ajout de fonctionnalités:

<span style="color: #0000ee; text-decoration-line: underline;">[Repository Github de vscode-sf-mesh-tools](https://github.com/michaelfery/vscode-sf-mesh-tools)</span>

<span style="color: #0000ee; text-decoration-line: underline;">[Repository Github de generator-azuresfmesh](https://github.com/michaelfery/generator-azuresfmesh)</span>

J'attends vos retours ;)