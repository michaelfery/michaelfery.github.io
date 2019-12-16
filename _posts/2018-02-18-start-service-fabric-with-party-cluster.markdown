---
layout: post
title:  Débuter sur Service Fabric avec Party Cluster
date:   2018-02-18 09:08:35
# categories: blog
description: Pour débuter avec les micro-services, Service Fabric est une plateforme qui offre une solution simple au développement de conteneurs et microservices et à la gestion de leur cycle de vie. Mais que faire quand vous n'avez pas de machine Windows Server sous la main et que vous ne souhaitez pas payer l'hébergement d'un Cluster sur Azure, uniquement pour faire quelques tests? Party Cluster est là pour répondre à ce besoin !
tags: ["azure", "service fabric", "party cluster"]
redirect_from: 
  - /blog/start-service-fabric-with-party-cluster
  - /blog/start-service-fabric-with-party-cluster/
---

Pour débuter avec les micro-services, Service Fabric est une plateforme qui offre une solution simple au développement de conteneurs et microservices et à la gestion de leur cycle de vie.

Pour tester Service Fabric il vous est possible d'installer le SDK sous Windows, de développer vos services et de les déployer sur un cluster local de développement. Vous pourrez ensuite déployer ces mêmes microservices sur un cluster dédié sur un Windows Server à vous ou sur le Cloud d'Azure assez simplement.

Mais que faire quand vous n'avez pas de machine Windows Server sous la main et que vous ne souhaitez pas payer l'hébergement d'un Cluster sur Azure, uniquement pour faire quelques tests? Party Cluster est là pour répondre à ce besoin !

![](/assets/img/posts/partycluster-background.jpg)

Party Cluster est une solution proposée par Microsoft pour obtenir gratuitement l'accès à un cluster de développement avec 3 noeuds pour une durée limitée.

## Demande d'accès à un cluster

Pour obtenir votre accès à un cluster gratuit, il faut vous rendre sur [https://try.servicefabric.azure.com/](https://try.servicefabric.azure.com/) et cliquer sur un des boutons pour vous connecter via OAuth. A ce jour, seuls les comptes GitHub et Facebbok sont éligibles.

![](/assets/img/posts/oauth.png)

après vous être connecté, vous pourrez choisir de créer un cluster de type Windows ou Linux selon vos préférences.

Une fois que vous aurez choisi le type de cluster, vous obtiendrez l'accès à un cluster pour une durée limitée d'une heure. Ce délai expiré, vous ne pourrez plus vous connecter ni déployer sur le cluster. Heureusement cette opération pourra être répétée autant que vous le voudrez, vous laissant ainsi libre de faire autant de tests que vous le souhaiterez.

## Connexion au cluster

Pour vous connecter vous n'aurez qu'à télécharger le certificat PFX et suivre les indications fournies dans le lien affiché.

Vous devrez notamment installer le certificat via la commande powershell

<span style="font-family: 'Segoe UI', 'Segoe WP', Tahoma, Arial, sans-serif; background-color: #dddddd;">Import-PfxCertificate -FilePath .\party-cluster-817263629-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My</span>

Lorsque vous jouerez cette commande, le thumbprint du certificat sera affiché de cette manière:

![](/assets/img/posts/pfx_ps_import.png)

Pensez à le noter car il vous sera nécessaire pour le déploiement et cela vous évitera de le chercher dans les détails des certificats.

Si toutefois, vous n'aviez pas noté le thumbprint, vous pourrez toujours le récupérer via le Manager de Certificat (command certmgr).

![](/assets/img/posts/certificate_thumprint.png)

## Déploiement sur le cluster

 Pour déployer vos services sur le cluster de test via Visual Studio, il vous faudra établir la connexion avec celui-ci, en spécifiant l'URL, le port ainsi que le certificat à utiliser.

Pour cela ouvrez le fichier Cloud.xml dans le dossier PublishProfiles et renseignez les informations de votre cluster:

```xml
<ClusterConnectionParameter
  ConnectionEndpoint="zwin7qzdrzff.westus.cloudapp.azure.com:19000"
  X509Credential="true"
  ServerCertThumbprint="B945ADF09C59ACD6553D04043FFCFE7B88F9363A"
  FindType="FindByThumbprint"
  FindValue="B945ADF09C59ACD6553D04043FFCFE7B88F9363A"
  StoreLocation="CurrentUser"
  StoreName="My"
/>
```

Parmi les informations à saisir, il y aura le <span style="font-family: monospace; background-color: #bfe6ff;">ConnectionEndpoint</span> fourni sur la page de Party Cluster ainsi que <span style="background-color: #bfe6ff; font-family: monospace;">ServerCertThumbprint </span> et  <span style="background-color: #bfe6ff; font-family: monospace;">FindValue</span> qui correspondent tous les deux au thumbprint du certificat installé via Powershell.

Dès lors, vous pourrez publier vos services en faisant un clic-droit > Publish sur votre projet d'application et vous devriez voir votre connexion au cluster validée:

![](/assets/img/posts/vs-publish.png)

Il ne vous reste plus qu'à coder vos microservices et à tester leur comportement sur le cluster de test !