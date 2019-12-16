---
layout: post
title:  Scripter son versioning d'API avec Azure API Management
date:   2018-07-16 10:38:31
# categories: blog
description: "Les versions permettent de regrouper plusieurs versions d'une même API au sein d'un Version Set alors que les révisions permettent de tester et déployer serinement des changements d'API.
Nous allons nous concentrer dans cet article sur le versionning d'API en créant un Version Set et en y ajoutant une première version d'API."
# img: /posts/636670925255471048/azure-api-version-set.png
tags: ["azure", "api management"]
redirect_from: "/blog/script-api-versioning-with-azure-api-management"
---

[Azure API Management](https://azure.microsoft.com/en-us/services/api-management) propose depuis quelques mois une fonctionnalité très attendue : la gestion des [versions et révisions](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility).

Les versions permettent de regrouper plusieurs versions d'une même **API** au sein d'un **Version Set** alors que les révisions permettent de tester et déployer serinement des changements d'API.

![azure api version set](/assets/img/posts/azure-api-version-set.png)

Nous allons nous concentrer dans cet article sur le versionning d'API en créant un Version Set et en y ajoutant une première version d'API.

## Azure API REST

Nous allons utiliser l'API REST d'Azure pour créer notre Version Set et notre API. Plusieurs raisons à l'utilisation de l'API REST :

*   La création d'un Version Set n'est pas disponible depuis le portail mais depuis les API Rest d'Azure et les commandes Powershell
*   L'association d'une API ou plutôt d'une version d'API n'est pas encore disponible depuis Azure Powershell
*   Le clic-clic n'est pas vraiment conseillé dans un environnment gérant plusieurs environnement et où le scripting de déploiement est à privilégier)

## Récupérer un token d'accès aux Api REST d'Azure

Pour récupérer le token d'accès à l'AP REST d'Azure je vous invite à consulter le dernier article ([obtenir un access token pour Azure ARM REST API](/blog/get-access-token-for-azure-arm-rest-api/)).

## Créer ou mettre à jour un API Version Set

 Afin de créer un API Version Set il faut connaître comment fonctionne un Version Set.

Un Version Set définit un regroupement logique des différentes version d'une même API. Pour distinguer ces versions, vos requêtes entrantes devront posséder un élément discriminant. Il peut s'agir d'un header ou d'un paramêtre dans l'Url.

Ce type de discriminant correspond au schéma de versionnement ou [versioning scheme](https://docs.microsoft.com/en-us/rest/api/apimanagement/apiversionset/createorupdate#versioningscheme) et il sera le même pour toutes les versions de l'API.

Plusieurs choix de schéma de versionnement s'offrent à vous :

*   **Segment** (ex: mon-api/v1)
*   **Query** (ex: mon-api?version=v1)
*   **Header**

Si vous optez pour le schéma Query, il vous faudra choisir **versioningScheme = "Query"** et définir la valeur de **versionQueryName** ("version" dans l'exemple précédent). En revanche, si vous optez pour le header, il faudra choisir **versioningScheme = "Header"** et définir la valeur de **HeaderName**.

Quand le choix de règle de versionnement est défini, il ne reste qu'à utiliser l'Api Azure REST en veillant à utiliser le verbe PUT afin de pouvoir rejouer le même script à chaque déploiement :

```bash
    # Create the body of the PUT request to the REST API  
    $createOrUpdateApiVersionSetBody = @{  
        name = $apiName  
        versioningScheme = "Query"  
        versionQueryName = $queryName  
        description = "version by api-version"  
    }  

    $apiVersionSet = Invoke-RestMethod `  
        -Method Put `  
        -Uri ("https://management.azure.com/subscriptions/" + $subscriptionId +  
              "/resourceGroups/" + $resourceGroupName +  
              "/providers/Microsoft.ApiManagement/service/" + $apimServiceName +  
              "/api-version-sets/" + $ApiVersionSetId +  
              "?api-version=" + $apimApiVersion) `  
        -Headers @{ Authorization = ("Bearer " + $accessToken)  
                    "Content-Type" = "application/json" } `  
        -Body ($createOrUpdateApiVersionSetBody | ConvertTo-Json -Compress -Depth 3)</pre>
```

## Créer ou mettre à jour une version d'API

Une fois que nous avons créé notre Version Set, nous sommes prêts à créer une nouvelle version d'API associée à ce Version Set.

Afin de créer notre nouvelle version, nous utiliserons à nouveau l'API REST Azure via le endpoint d'Api.

Si la [création d'Api](https://docs.microsoft.com/en-us/rest/api/apimanagement/api/update) est déjà bien documentée, l'association d'une version à un Version Set ne l'est pas.

Pour effectuer cette association dès la création de l'API nous n'avons qu'à rajouter des propriétés au Body de notre requête.

Ces propriétés sont l'id du Version Set (**apiVersionSetId**) et la version de l'API (**apiVersion**) à fournir dans le header, la query ou le segment selon le schéma choisi.

```bash
    $importApiBody = @{  
        properties = @{  
            type = "Http"  
            protocols = , "https"  
            path = $apiPath  
            displayName = $apiDisplayName  
            serviceUrl = $serviceUrl  
            apiVersionSetId = $apiVersionSet.id  
            apiVersion = "v1"  
        }  
    }  

    $api = Invoke-RestMethod `  
        -Method Put `  
        -Uri ("https://management.azure.com/subscriptions/" + $subscriptionId + `  
              "/resourceGroups/" + $resourceGroupName + `  
              "/providers/Microsoft.ApiManagement/service/" + $apimServiceName + `  
              "/apis/" + $apiId + `  
              "?api-version=" + $apimApiVersion) `  
        -Headers @{ Authorization = ("Bearer " + $accessToken)  
                    "Content-Type" = "application/json" } `  
        -Body ($importApiBody | ConvertTo-Json -Compress -Depth 3)</pre>
```

## Conclusion

Nous avons vu dans cet article comment utiliser l'API REST d'Azure pour créer un Version Set , une version d'API et associer cette version au version set. L'utilisation de l'API REST pourra probablement être remplacée par les commandes Azure powershell d'ici peu mais en attendant nous avons déjà de quoi scripter et automatiser nos déploiement de version d'API.

Seule bémol, notre API ne contient pour l'instant aucune route. Dans un prochain article nous verrons donc comment automatiser l'import d'une version d'API directement dans un Version Set depuis une spécification Swagger.