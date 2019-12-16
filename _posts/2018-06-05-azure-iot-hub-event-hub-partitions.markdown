---
layout: post
title:  Azure IOT Hub, Event Hub endpoints et Partitions
date:   2018-06-05 12:00:00
# categories: blog
description: Dans cet article nous ferons une présentation très rapide d’Azure IOT Hub qui sert à ingérer les messages et nous essaierons d’éclaircir l’intérêt des endpoints Event Hub et des partitions.
tags: ["azure", "iot", "azure iot hub"]
redirect_from: "/blog/azure-iot-hub-event-hub-partitions"
---

Lorsque l’on souhaite créer un backend IOT, deux types de services principaux sont nécessaires:

*   **Ingress** - service d’Ingestion : sert à recevoir les messages des différents appareis
*   **Throughput** - service(s) de débit ou de traitement : sert à traiter les messages reçus et à rediriger les messages ou déclencher des actions associées si nécessaires

Il existe un service Azure d’ingestion scalable permettant d’ingérer une très forte volumétrie de messages: **Azure IOT Hub.**  
Il existe également de nombreuses solutions permettant le déploiement de services scalables de traitement de ces messages ou évènements, telles que **Service Fabric.**  
Maintenant, avouez qu’il serait dommage que la communication entre ces deux services ne soit pas scalable et qu’on ait un vilain bottleneck au milieu.

Dans cet article nous ferons une présentation très rapide d’Azure IOT Hub qui sert à ingérer les messages et nous essaierons d’éclaircir l’intérêt des endpoints Event Hub et des partitions.  
Par la suite, nous aborderons les partitions Event Hub, leur intérêt et la façon de les consommer.  
Nous verrons également comment utiliser Service Fabric et les partitions pour créer un service scalable, capable d’absorber la charge provenant des différentes partitions d’un Event Hub.

# Azure IOT Hub

Une solution pour l’ingestion des messages provenant des appareils est de fournir une passerelle principale vers laquelle les appareils envoient leurs messages.  
Comme indiqué précédemment, dans cet article nous utiliserons Azure IOT Hub, un service très complet pour les scénarios IoT, utilisé notamment comme point d’entrée de la communication des devices vers le cloud.  
Il s’agit d’une passerelle bidirectionnelle de messages apportant également une gestion du parc d’appareils mais son avantage le plus important est bien sûr de savoir accueillir une très forte volumétrie de messages.

![Azure IOT Hub](/posts/636545400234459124/00_IOT_Hub.png "Azure IOT Hub")

Pour en savoir un peu plus sur Azure IOT Hub je vous invite à aller sur [la documentation produit de Microsoft](https://azure.microsoft.com/fr-fr/services/iot-hub/) ou sur un de mes précédents articles expliquant [comment provisionner un Hub dans le portail Azure et enregistrer vos premiers appareils](https://blogs.infinitesquare.com/posts/iot/azure-iot-hub-provisioning-et-configuration). (en espérant que la doc n’est pas trop changée :P ).

# API REST et SDK

L’objectif premier d’un BackEnd IOT est de pouvoir ingérer des messages mais également de les restituer, et de manière ordonnée si nécessaire. Le rôle du service dont nous avons besoin est donc de se connecter en lecture à la passerelle Azure IOT Hub pour en « digérer » les messages.  
Pour restituer les messages entrants dans le Hub, plusieurs solutions s’offrent à nous et notamment les API REST proposées par le service.

Comme la plupart des services Azure, chacune des API REST se voit enrichie de SDK opensource pour de nombreux langages tels que .NET, Java, Node.js, Pyhton ou C.

Pour découvrir ces SDK, consultez: [la documentation officielle](https://docs.microsoft.com/fr-fr/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-service-sdks)

# Event-Hub Endpoints

Dans le cadre d’un projet backend scalable nous n’utiliserons pas les points de terminaison REST classiques en direct ou via un SDK pour digérer les messages dans un souci d'optimisation des performances.

Event Hub est un autre service Azure qui propose des fonctionnalités très intéressantes de gestion d'évènements efficace des messages vers un tableau de bord ou un service de notification cliente par exemple.  
Pour récupérer nos messages nous allons donc utiliser la fonctionnalité de points de terminaison « Event-Hub-endpoint » proposée par Azure IOT Hub. ![Azure IOT Hub Provisioning](/posts/636545400234459124/02_Azure_IOT_Hub_Endpoints.png "IOT Hub Provisioning")

Dans la plupart des cas nous n’utiliserons que le **endpoint ou « point de terminaison » par défaut (messages/events)** mais il faut savoir que le Hub IOT permet de rediriger automatiquement les messages vers des endpoints à définir, en fonction de critères de contenu du message.  

# Partitionnement

Un autre aspect important de l’IOT Hub est sa fonctionnalité native de partitionnement.

Pourquoi utiliser les partitions device-to-cloud dans un IOT Hub unique?  
Lorsque l’on provisionne un Azure IOT Hub il nous est possible de définir la propriété correspondant au nombre de partitions (entre 2 et 32). **Les partitions sont liées au degré de parallélisme en aval requis lors de la consommation des applications**, c’est-à-dire notre capacité à paralléliser les traitements de débit ou de consommation des messages. ![Azure IOT Hub](/posts/636545400234459124/01_Azure_IOT_Hub_Partitions.png "IOT Hub Provisioning")

S’il est déconseillé d’essayer de définir dans quelle partition envoyer les messages, on peut se servir de cette fonctionnalité dont le concept est emprunté aux concentrateurs d’évènements ou Event Hubs. Cette fonctionnalité est utilisée justement pour définir des points de terminaison compatibles Event Hub (messages/events) intégrés nativement dans un Azure IoT Hub.

Un autre aspect très pertinent des partitions dans Azure IOT Hub est la répartition des appareils par partition. En effet, si on laisse le Hub déterminer lui-même la partition la plus pertinente pour y envoyer les messages d’un appareil, il faut noter que **tous les messages du même appareil transiteront par une seule et même partition**. Cela est bien utile, voire indispensable pour la parallélisation de la lecture des messages.

Il faut cependant noter un inconvénient non négligeable à ce système de partitions au niveau IOT Hub. __Si l’on peut choisir le nombre de partitions au provisionnement du Hub, il n’est plus possible de le modifier par la suite__. La recommandation en cas d'augmentation d'appareils et donc de charge est de toute façon de provisionner de nouvelles unités IOT Hub.

Je vous donne rendez-vous tout bientôt pour prochain article dans lequel je vous ferai un petit bilan sur les partitions Event Hub, ce qu’elles apportent et la façon de les consommer.
