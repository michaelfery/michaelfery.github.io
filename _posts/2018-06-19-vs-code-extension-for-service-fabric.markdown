---
layout: post
title:  Visual Studio Code Extension pour Service Fabric
date:   2018-06-19 11:00:00
# categories: blog
description: "Vous avez découvert dans un post précédent comment disposer d'un cluster Service Fabric de test, gratuit et en quelques clics. Je vous présente maintenant comment vous passer de Visual Studio en utilisant l'outil multi-plateformes:: VS Code et ses extensions pour créer vos premiers microservices sur Service Fabric."
img: posts/vscode-banner.png
tags: ["service fabric", "vs code", "extension", "microservices"]
redirect_from: 
  - /blog/vs-code-extension-for-service-fabric
  - /blog/vs-code-extension-for-service-fabric/
---

**Service Fabric** est une plateforme qui offre une solution simple au développement de conteneurs et microservices et à la gestion de leur cycle de vie et pouvant fonctionner sur Linux ou Windows. Il semble donc tout naturel de ne pas être contraint à utiliser Visual Studio pour créer, compiler et déployer des applications sur un cluster Service Fabric.

**Visual Studio Code** ou **VS Code** est un éditeur de code extensible développé par Microsoft pour Windows, Linux et OS X. Son fonctionnement "par extensions" permet d'enrichir simplement les fonctionnalités qu'il propose.

Vous avez découvert [dans un post précédent](/blog/start-service-fabric-with-party-cluster/) comment disposer d'un cluster Service Fabric de test, gratuit et en quelques clics. Je vous présente maintenant comment vous passer de Visual Studio en utilisant l'outil multi-plateformes: VS Code et ses extensions pour créer vos premiers microservices sur Service Fabric.

# Extension VS Code

Il y a à peine quelques jours, une nouvelle extension VS Code à été [publiée sur le marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) et comme à son habitude Microsoft a rendu [le code source de ce projet disponible sur github](https://github.com/Microsoft/vscode-service-fabric-reliable-services). L'auteur principal de cette extension est un stagiaire (Peter Pogorski) qui a découvert Service Fabric et TypeScript sur ce projet alors félicitations à lui!

![VS Code extension for C#](/posts/636649190560587483/SF_Extension_MarketPlace.png "VS Code extension for Service Fabric")

# Features

A ce jour, l'extension permet de :

*   Créer des applications Service Fabric Applications (Java, C#, Container, Guest Executables)
*   Compiler des applications Java et C#
*   Déployer des applications sur le cluster local
*   Publier des applications sur un cluster distant
*   Supprimer des applications sur un cluster (distant ou local)
*   Débugger des applications Java et C# (uniquement sur Linux)

L'upgrade des applications n'est pas encore disponible mais doit être dans les tuyaux, sinon je vous rappelle que le projet est opensourcé sur Github ;)

# Installation

<span style="color: #000000; font-family: Verdana, Arial, Helvetica, sans-serif; font-size: 14px;">Pour utiliser cette extension il vous faut quelques prérequis :

*   [Installer Visual Studio Code](https://code.visualstudio.com/)
*   [Installer Node.js](https://nodejs.org/en/)
*   [Installer Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
*   [Installer Git](https://git-scm.com/)
*   [Installer Service Fabric SDK](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started)
*   [Installer .Net Core](https://www.microsoft.com/net/download)

L'extension Service Fabric utilise l'outil Yeoman et des générateurs pour les différents types de projets. Pour installer Yeoman et ces générateurs, utilisez les commandes suivantes :

```bash
npm install -g yo  

npm install -g generator-azuresfjava  

npm install -g generator-azuresfcsharp  

npm install -g generator-azuresfcontainer  

npm install -g generator-azuresfguest
```

Si vous souhaitez créer et compiler des applications C#, il vous sera également demandé d'instaler l'extension associée disponible [ici](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) ou directement depuis Visual Studio Code.

![VS Code extension for C#](/posts/636649190560587483/VS_Code_Extension_CSharp.png "VS Code extension for C#")

Pour publier vos applications depuis Windows, vous devrez également installer l'extension Powershell.

![VS Code extension for C#](/posts/636649190560587483/VS_Code_Extension_PS.png "VS Code extension for PowerShell")

Une fois tous ces prérequis remplis, vous pourrez passer aux choses sérieuses en installant l'extension Service Fabric de Microsoft.

![VS Code extension for C#](/posts/636649190560587483/VS_Code_Extension_SF.png "VS Code extension for Service Fabric")![VS Code extension for C#](/posts/636649190560587483/VS_Code_Extension_SF_Install.png "VS Code extension for Service Fabric Installation")

# Commandes

Pour créer votre première application il vous faut créer un dossier/workspace et ouvrir l'invite de commandes (Ctrl+Shift+P), puis saisir la commande suivante:

```
Service Fabric: Create Aplication
```

Vous serez alors invité à choisir le type d'application (azuresfcsharp par exemple), le nom de votre application, de votre service et le type de service comme vous le feriez depuis Visual Studio classique.

Vous pourrez ensuite compiler votre application via l'invite de commandes:

```
Service Fabric: Build Application
```

# Déploiement

Vous pourrez enfin déployer votre application sur votre cluster local:

```
Service Fabric: Deploy Application
```

Concernant le déploiement sur un cluster distant, vous n'aurez qu'à remplir le fichier Cloud.json en y inscrivant l'url et le port de votre cluster ainsi que les thumbprint de certificats en cas de connexion à un cluster sécurisé.

```json
{
  "ConnectionIPOrURL": "yourcluster.westus.cloudapp.azure.com",
  "ConnectionPort": "19000",
  "ClientKey": "",
  "ClientCert": "",
  "ServerCertThumbprint": "DDF58FC0816A400F77998D166AC68D7448EFF5FB",
  "ClientCertThumbprint": "DDF58FC0816A400F77998D166AC68D7448EFF5FB"
}
```

D'ailleurs, il existe un bug sur Windows qui empêche la connexion via Powershell aux clusters sécurisés.

Je vous invite donc dans le terminal Powershell à taper vous même les commandes de connexion et de déploiement.

Si vous utiliser un cluster gratuit "Party cluster" présenté dans un article précédent, vous pourrez taper les commandes suivantes par exemple:

```bash
Import-PfxCertificate `
  -FilePath .\party-cluster-xxxxx-client-cert.pfx `
  -CertStoreLocation Cert:\CurrentUser\My `
  -Password (ConvertTo-SecureString pfxpassword -AsPlainText -Force)

$cert = Get-ChildItem `
  -Path Cert:\CurrentUser\My | Where-Object {$_.Subject -eq "cn=yourcluster.westus.cloudapp.azure.com"}
  
Connect-ServiceFabricCluster `
  -ConnectionEndpoint 'yourcluster.westus.cloudapp.azure.com:19000' `
  -KeepAliveIntervalInSec 10 `
  -X509Credential `
  -ServerCertThumbprint $cert.Thumbprint `
  -FindType 'FindByThumbprint' `
  -FindValue $cert.Thumbprint `
  -StoreLocation 'CurrentUser' `
  -StoreName 'My' `
  -Verbose  

./votreApp\install.ps1
```

Pour information, j'ai effectué une Pull Request sur le repo concernant le déploiement depuis Windows qui vient d'être validée. Le bugfix devrait donc être disponible dans la prochaine version de l'extension sur le marketplace.

Je vous souhaite une bonne installation et vive l'open-source !!!