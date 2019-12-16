---
layout: post
title:  Obtenir un Access Token pour Azure ARM REST API
date:   2018-07-06 10:00:00
# categories: blog
description: "Il est parfois nécessaire d'utiliser l'API REST d'Azure ARM directement plutôt que via Powershell ou la CLI, notamment quand des fonctionnalités de l'API n'y sont pas encore implémentées.
Voici comment récuper un token d'accès sans passer par le portail."
# img: /posts/636649190560587484/GetAzureRMAccessToken.png
tags: ["azure", "arm"]
redirect_from: "/blog/get-access-token-for-azure-arm-rest-api"
---

Il est parfois nécessaire d'utiliser l'API REST d'Azure ARM directement plutôt que via Powershell ou la CLI, notamment quand des fonctionnalités de l'API n'y sont pas encore implémentées.

Voici comment récuper un token d'accès sans passer par le portail.

# Présentation

Afin de récupèrer un token d'accès, il faut:

*   Se connecter à Azure
*   Sélectionner la souscription cible
*   Extraire le token du cache

# Connection à Azure

La première étape consiste à demander à l'utilisateur de se connecter à Azure via l'interface de connexion OAuth:

```bash
# Login To Azure  
$rmAccount = Add-AzureRmAccount -SubscriptionId $subscriptionId</pre>
```

# Sélection de la souscription

Si vous possédez plusieurs souscriptions, il faut choisir celle qui vous intéresse afin de pouvoir en récuperer un jeton d'accès:

```bash
# Set Subscription ID  
$subscriptionId = "YOUR_SUBSCRIPTION-ID"  

# Select Azure Subscription  
$tenantId = (Get-AzureRmSubscription -SubscriptionId $subscriptionId).TenantId</pre>

# Récupération du jeton
```

Une fois la connexion établie, il ne reste qu'à extraire les tokens depuis le cache:

```bash
# Extract token from Cache
$cachedTokens = $tokenCache.ReadItems() `
 | where { $_.TenantId -eq $tenantId } `
 | sort-object -property ExpiresOn -descending $accessToken = $cachedTokens[0].Accesstoken</pre>
```

# Utilisation du jeton

Maintenant que nous avons récupéré le jeton depuis le cache de connexion, il vous est possible de l'utiliser dans des requêtes aux API REST d'Azure RM.

La connexion à l'API REST se fait via la commande Invoke-RestMethod en lui spécifiant le jeton dans le Header Authorization:

```bash
Invoke-RestMethod `
 -Method GET `
 -Uri ("https://management.azure.com/subscriptions/" + $subscriptionId + "/resources/" + "?api-version=2018-02-01") `
 -Headers @{ Authorization = ("Bearer " + $accessToken) "Content-Type" = "application/json" }
```

Il est vrai que la plupart des endpoints de l'API REST d'Azure RM sont intégrées à la CLI mais ce n'est pas toujours le cas et nous avons vu comment profiter des nouvelles features sans attendre leur intégration aux outils.

Je vous donne rendez-vous dans un prochain article pour voir un exemple plus concret de l'utilisation de ces API REST.