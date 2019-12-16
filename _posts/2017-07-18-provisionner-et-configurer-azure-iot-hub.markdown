---
layout: post
title:  Provisionner et configurer Azure IOT Hub
date:   2017-07-18 09:25:55
# categories: blog
description: Dans cet article nous verrons comment provisionner un Hub, le configurer et enregistrer nos premiers devices. Cela servira de préparatifs à la mise en place d’une suite logicielle IOT.
img: posts/azure-banner.png
tags: ["azure", "iot", "azure iot hub"]
redirect_from: 
  - /blog/provisionner-et-configurer-azure-iot-hub
  - /blog/provisionner-et-configurer-azure-iot-hub/
---

Dans cet article nous verrons comment provisionner un Hub, le configurer et enregistrer nos premiers devices. Cela servira de préparatifs à la mise en place d’une suite logicielle IOT.

![](/assets/img/posts/00Hub_thumb_08F24217.png) ![00Hub](/posts/636545400234459123/00Hub_thumb_08F24217.png "00Hub")

# Présentation

Tout d’abord, qu’est-ce-que ce hub ? Il s’agit de l’arme pas très secrète de Microsoft pour s’imposer sur le marché de l’IOT.

Le Hub est une ressource Azure qui a pour vocation de devenir votre plateforme centrale d’échange de messages entre vos devices, votre back-office, vos apps, etc.

Sa force serait donc de pouvoir recevoir et distribuer des milliards de messages sans se soucier de la charge associée. Oui, mais pas seulement. En effet, Azure IOT Hub se charge également de l’administration des devices, de l’enregistrement à la supervision en passant par les transports bidirectionnels de messages.

# Step 1: Provisioning du IoT Hub depuis Azure et Pricing

Rien de plus simple que provisionner un Hub Azure IOT.

Accessible depuis le nouveau portail Azure, il vous suffit de sélectionner “Nouveau” et de rechercher “Azure IoT Hub”.

![02NewHub](/posts/636545400234459123/02NewHub_thumb_5A978E74.png "02NewHub")

Suivez alors la procédure en cliquant sur le bouton “Créer”.

Vous serez alors invité à choisir le nom de votre hub ainsi que le tier associé.

![03Pricing](/posts/636545400234459123/03Pricing_thumb_63F353A8.png "03Pricing")

Pour info, un Hub configuré en gratuit ne pourra pas être upgradé. Si vous voulez augmenter les capacités d’un tel Hub, il vous faudra donc en provisionner un autre et le configurer de nouveau. La simplicité de provisioning ne rendra pas la tâche trop difficile donc n’hésitez pas à vous faire la main sur du gratuit afin de déterminer vos besoins. Autre information importante, il n’est possible de provisionner qu’un seul Hub Free. Destiné à vos tests cela ne devrait donc pas être gênant.

# Step 2: Configuration

Maintenant que votre Hub est provisionné, il va falloir le configurer pour pouvoir y accéder depuis vos devices et interfaces d’administration.

Par défaut, des clés et des groupes de droits ont été créés. Dans un premier temps nous n’aurons besoin que de récupérer les informations suivantes :

![04Hub](/posts/636545400234459123/04Hub_thumb_39019468.png "04Hub")

![](/assets/img/posts/05HubSharedAccessKeys_4A1F1D79.png) ![05HubSharedAccessKeys](/posts/636545400234459123/05HubSharedAccessKeys_thumb_37D656B7.png "05HubSharedAccessKeys")

# Step 3: Enregistrement des devices

Pour faire communiquer les devices avec notre hub, de nombreuses API sont disponibles ainsi que des SDK pour presque tous les langages de programmation.

Ici nous utiliserons le SDK C# disponible en package NuGet.

Commençons par créer une application console qui nous servira de provisioning pour les devices. C’est à dire qu’elle servira à associer les devices dans le service et à nous fournir les identités créées pour configurer les devices.

Pour nous aider dans cette démarche nous pouvons utiliser les APIs REST fournies par Azure mais le plus simple reste tout de même d’utiliser les SDK fournis. Nous utiliserons le SDK C# mais sachez qu’il existe pour les plateformes/langages suivants :

*   .NET
*   C
*   Node.js
*   Java

Comme à son habitude Microsoft joue le jeu de l’opensource et pour récupérer les SDKs vous pouvez vous rendre sur [https://github.com/Azure/azure-iot-sdks/blob/master/readme.md](https://github.com/Azure/azure-iot-sdks/blob/master/readme.md "https://github.com/Azure/azure-iot-sdks/blob/master/readme.md")

Dans notre exemple nous utiliserons donc NuGet et le package à récupérer porte le nom **Microsoft.Azure.Devices** (le Devices.Client nous servira plus tard).

![06NugetAzureDevices](/posts/636545400234459123/06NugetAzureDevices_thumb_0ECE486C.png "06NugetAzureDevices")

Dans notre application nous allons d’abord commencer par nous connecter au Hub. Pour cela nous aurons besoin de la chaine de connexion récupérée sur Azure dans l’étape 2 et d’un objet RegistryManager :

```csharp
static RegistryManager registryManager; 
static string connectionString = "HostName=iotdeviceshub.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=azureIotHubKey";
```

Nous allons ensuite demander au hub d’enregistrer notre device. Cela se fait en utilisant la méthode **AddDeviceAsync** du RegistryManager qui prend en paramètre l’**identifiant** du device. La méthode permet de récupérer l’id généré et d’en créer. Encapsulons ça dans une méthode à nous :

```csharp
private async static Task AddDeviceAsync(string deviceId) {
  Device device;
  try {
    device = await registryManager.AddDeviceAsync(new Device(deviceId));
  }
  catch (DeviceAlreadyExistsException) {
    device = await registryManager.GetDeviceAsync(deviceId); 
  }
  Console.WriteLine($"Generated device key: {device.Authentication.SymmetricKey.PrimaryKey}");
}
```

Sous Windows 10 vous pourrez utiliser l’id unique du device et intégrer ce code dans le device, mais pour l’exemple nous utiliserons un nom choisi au hasard par l’utilisateur :

Voici le code à ajouter dans le corps de la méthode Main :

```csharp
registryManager = RegistryManager.CreateFromConnectionString(connectionString);
while (true) // Loop indefinitely
{
    Console.WriteLine("Enter a device id to generate/get the hub key or 'q' to quit");
    var message = Console.ReadLine();
    if (message == "q")
    {
        break;
    }
    AddDeviceAsync(message).Wait();
}
```

Lançons l’application et nous devrions obtenir le résultat suivant :

![07GeneratedDeviceKeys](/posts/636545400234459123/07GeneratedDeviceKeys_thumb_24211AF6.png "07GeneratedDeviceKeys")

Notons la clé générée. Il s’agit de la clé unique du device que nous venons d’enregistrer et qui nous servira à faire communiquer celui-ci avec notre hub.

Nous verrons dans le prochain article comment envoyer des messages depuis ledit device, comment lui envoyer un message spécifiquement destiné et comment superviser les messages transitant sur le Hub.

D’ici là, bon dev.
