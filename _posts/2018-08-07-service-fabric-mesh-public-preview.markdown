---
layout: post
title:  Service Fabric Mesh Public Preview
date:   2018-08-07 06:00:11
# categories: blog
description: A l'heure du Server-less, Microsoft nous propose du Cluster-less avec la Public Preview d'Azure Service Fabric Mesh.
img: posts/service-fabric-mesh-preview.jpg
tags: ["service fabric mesh", "azure"]
redirect_from: "/blog/service-fabric-mesh-public-preview"
---

Pour ceux qui suivent un peu mon blog et les différents talks que j'ai pu faire cette année, vous avez vu que Service Fabric en est le fil rouge. Il n'y a qu'à voir les [TechLabs](https://www.meetup.com/fr-FR/TechLabs-by-SOAT/events/253376557/) que j'anime avec Wilfried Woivré sur le sujet.

Cependant, pour un développeur comme moi, et malgrè toutes ses qualités, Service Fabric possède un défaut majeur: il nécessite de manager un cluster. En effet, qu'il soit On-Premise ou hosté sur Azure, sous Windows ou sous Linux, un cluster reste un ensemble de machines et un OS à gérer, un runtime et des services à maintenir à jour également.

A l'heure du Server-less, Microsoft nous propose du Cluster-less avec la Public [Preview d'Azure Service Fabric Mesh](https://azure.microsoft.com/en-us/blog/azure-service-fabric-mesh-is-now-in-public-preview/).

![Service Fabric Mesh](/posts/636691872395167321/service-fabric-mesh.png "Service Fabric Mesh")

L'objectif est de ne plus avoir à s'occuper de l'infrastructure sur laquelle nous voulons exécuter nos services et de se concentrer sur le développement et le packaging de nos images.

Dans ce premier post sur Mesh, nous allons nous concentrer sur les spécifités du modèle de déploiement de services sur Service Fabric Mesh.

# Quels sont les pré-requis?

Il vous faut un container à déployer, votre application packagée. Ca peut paraître évident mais bon, le code ne va pas se faire automagiquement.

Je ne reviendrais pas sur le développement de container mais sachez que les images OS de containers supportées sont:

*   Windows - windowsservercore and nanoserver 
    *   Windows Server 2016
    *   Windows Server version 1709
*   Linux
    *   No known limitations

Cela vous laisse une bonne latitude quant aux images à utiliser pour vos services.

# Assez de blabla, je veux déployer! 

Ok, ok, c'est bien beau mais les containers on les a, ce qu'on veut maintenant c'est en faire des services, les déployer, les éxécuter, les exposer.

A mi-chemin entre le Docker Compose et le manifest Service Fabric, le template Mesh entre en jeu.

## Mesh Template

Un template de déploiement Mesh n'est finalement qu'un template ARM avec des ressources particulières. Il commence donc de la manière classique:

```json
{   
 "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",   
 "contentVersion": "1.0.0.0",  
 "parameters": {   
   "location": {   
     "type": "string",   
     "metadata": {   
       "description": "Location of the resources."   
     }   
   }   
 },   
 "resources": [   
 ...
```

Pour déployer une application il faut donc déclarer cette application dans les ressources ainsi qu'un network. Ce sont les deux ressources minimales.

## Mesh Network

Le composant réseau permettant de faire communiquer vos services est une ressource de type '**Microsoft.ServiceFabricMesh/networks**'.

```json
{   
  "apiVersion": "2018-07-01-preview",   
  "name": "helloWorldNetwork",   
  "type": "Microsoft.ServiceFabricMesh/networks",   
  "location": "[parameters('location')]",   
  "dependsOn": [],   
  "properties": {   
    "addressPrefix": "10.0.0.4/22",   
    "ingressConfig": {   
      "layer4": [   
      ...   
      ]   
    }  
  }   
}
```

Cette ressource n'a pas de dépendance mais elle sera celle de notre application. On vient de configurer la seule ressource"infrastructure" de notre mesh. Pas mal non?!

## Mesh Application

Attaquons-nous maintenant à notre applicatif. Pour cela, il faut ajouter une ressource de type 'Microsoft.ServiceFabricMesh/applications':

```json
{   
  "apiVersion": "2018-07-01-preview",   
  "name": "helloWorldApp",   
  "type": "Microsoft.ServiceFabricMesh/applications",   
  "location": "[parameters('location')]",   
  "dependsOn": [ "Microsoft.ServiceFabricMesh/networks/helloWorldNetwork" ],  
  "properties": {   
    "description": "Service Fabric Mesh HelloWorld Application!",   
    "services": [   
   ...  
```

Vous noterez la dépendance à la ressource **helloWorldNetwork** créée auparavant.

## Mesh Service

> _"T'es bien gentil Michaël, mais tu nous enfumes et on a toujours pas intégré nos containers dans le bouzin!"_

En effet, on a créé une belle coquille... vide! Dans cette application vous allez forcément avoir besoin de déclarer des services à l'intérieur.

Sans surprise, les services sont des ressources de type '**Microsoft.ServiceFabricMesh/services**'.

Attention tout de même, les services sont à ajouter dans la propriété '**services**' de l'application et non dans les ressources du template.

```json
{   
  "type": "Microsoft.ServiceFabricMesh/services",   
  "location": "[parameters('location')]",   
  "name": "helloWorldService",   
  "properties": {   
  "description": "Service Fabric Mesh Hello World Service.",   
  "osType": "linux",   
  "codePackages": [   
    {   
      "name": "helloWorldCode",   
      "image": "seabreeze/azure-mesh-helloworld:1.1-alpine",   
      "endpoints": [   
        {   
          "name": "helloWorldListener",   
          "port": "80"   
        }   
      ],   
      "environmentVariables": [   
        {   
          "name": "ASPNETCORE_URLS",   
          "value": "http://+:8288"   
        }   
      ],   
      "resources": {   
        "requests": {   
          "cpu": "1",   
          "memoryInGB": "1"   
        }   
      }   
    }   
  ],   
  "replicaCount": "1",   
  "networkRefs": [   
    {   
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'helloWorldNetwork')]"   
    }   
  ]   
}
```

Les propriétés d'un service à noter sont:

*   **osType**: linux ou windows, correspondant aux images que vous avez créées
*   **codePackages**: qui contient un plusieurs packages
*   **replicaCount**: le nombre de replicas de votre service
*   **networkRefs**: la référence au network créé auparavant

Chaque package comprend plusieurs propriétés notables:

*   **image**
*   **resources**: ce sont les ressources systèmes allouées à ce package
*   **environmentVariables**
*   **endpoints**: point d'entrée de votre package, notamment utilisé pour faire communiquer les packages et services entre eux

## Ingress Endpoint

On a créé nos services, on peut les déployer, on est content mais... on y accède comment ?

Afin de pouvoir accèder à nos services il faut déclarer un endpoint externe et cela se fait à deux endroits.

Nous avons d'abord vu dans la déclaration d'un Mesh Service comment déclarer les endpoints de celui-ci en lui donnant un nom "**helloWorldListener**" et un port "**80**".

Il faut ensuite exposer ce endpoint à l'extérieur du network et cela se fait en modifiant la propriété **ingressConfig/layer4** de notre application:

```json
"properties": {   
  "addressPrefix": "10.0.0.4/22",   
  "ingressConfig": {   
    "layer4": [   
      {   
        "name": "helloWorldIngress",   
        "publicPort": "80",   
        "applicationName": "helloWorldApp",   
        "serviceName": "helloWorldService",  
        "endpointName": "helloWorldListener"   
      }   
    ]   
  }
```

Nous pouvons donner à ce endpoint externe, un nom, un port externe (différent ou non du port interne) ainsi que le service et son endpoint à cibler.

## Déploiement

Pour le déploiement, comme je vous l'indiquais, il s'agit d'un template ARM et son déploiement se fait via la CLI. Etant en preview, il vous faudra installer l'extension de CLI:

```bash
az extension add --source https://meshcli.blob.core.windows.net/cli/mesh-0.9.1-py2.py3-none-any.whl
```

Vous pourrez alors utiliser la commande **az mesh deployment create** en ciblant un template distant ou local:

```bash
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Pour en savoir un peu plus sur le déploiement je vous invite à regarder [la doc Microsoft sur le sujet](https://docs.microsoft.com/en-us/azure/service-fabric-mesh/service-fabric-mesh-howto-deploy-app-template).

# Quid des Registry privés ?

Sachez que Mesh ira chercher par défaut les images déclarées dans vos services sur des registres publics mais il peut également déployer vos containers depuis des registres privés [privés via acr](https://docs.microsoft.com/en-us/azure/service-fabric-mesh/service-fabric-mesh-howto-deploy-app-private-registry) en ajoutant les informations de connexion au registry dans les paramêtres de déploiement:

```bash
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}"
```

# Combien ça coute ?

Niveau pricing, tout cela est gratuit pendant la Preview puis passera à 50% pendant une période définie.

Reste à savoir 50% de quoi, et là c'est le flou total.

# Conclusion

On a vu que déclarer et déployer des services depuis des containers est vraiment simplifié grâce à l'utilisation de Service Fabric Mesh.

Je vous parlais des qualités de Service Fabric et parmi celles-ci on retrouvait la possibilité de développer des Reliable Services et d'utiliser le pattern Actor nativement. Dans cette version preview de Mesh nous n'avons plus accès qu'aux containers et aux Reliable Collections.

Si cela peut sembler être une régression pour les aficionados de Service Fabric, l'objectif est là de draguer les développeurs et créateurs de containers de toute sorte à la manière des Azure Functions et de nombreuses fonctionnalités seront ajoutées avant la GA. Il faut avouer qu'au niveau flexibilité et simplicité d'utilisation, il est difficile de faire mieux.

Si vous souhaitez créer vos propres containers, les déployer sur ACR et sur Service Fabric Mesh depuis Visual Studio, je vous invite à regarder l'extension [Visual Studio Tools for Service Fabric Mesh](https://aka.ms/vstoolssfmblog). 

Si vous voulez jouer avec depuis VS Code j'aurais une petite surprise dans les jours qui viennent ;) Ooohhhh le teasing de fou !!!

Et vous qu'allez-vous hoster sur Service Fabric Mesh ?