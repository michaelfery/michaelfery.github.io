---
layout: post
title:  "[Azure Storage Table] Suppression massive d'entités"
date:   2018-01-09 10:00:00
# categories: blog
description: "Cet article traite de la suppression totale ou partielle des entités présentes dans dans une Table Azure Storage.
Nous y aborderons les suppressions complète de table, les batchs et le requêtage par segmentation et les limitations de ces différentes méthodes"
img: posts/azure-banner.png
tags: ["azure", "storage"]
redirect_from: 
  - /blog/azure_storage_table_suppression_massive_d_entitees
  - /blog/azure_storage_table_suppression_massive_d_entitees/
---

Cet article traite de la suppression totale ou partielle des entités présentes dans dans une Table Azure Storage.  
Nous y aborderons les suppressions complète de table, les batchs et le requêtage par segmentation et les limitations de ces différentes méthodes.

## Suppression de la table

Si votre table comporte un nombre importants d'enregistrements, le plus simple est probablement de supprimer la table elle-même et de la recréer.

```csharp
var storageAccount =
  CloudStorageAccount.Parse( ConfigurationManager.ConnectionStrings["AzureStorage"].ConnectionString);
var tableClient =
  storageAccount.CreateCloudTableClient();
var rmsTable =
  tableClient.GetTableReference("ResourceManagerStorage");
rmsTable.DeleteIfExists();
```

Il est important de noter que l'opération de suppression n'est pas instantanée mais se met dans une file d'éléments à supprimer et que cela peut prendre environ 40 secondes (source MSDN: [http://msdn.microsoft.com/en-us/library/windowsazure/dd179387.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/dd179387.aspx) ).

Si vous tenter de recréer la table durant ce laps de temps vous recevrez une erreur 409 (Conflict) vous indiquant que la table est en train d'être supprimée.

`rmsTable.Create();`

> The remote server returned an error: (409) Conflict.
>
> HTTP/1.1 409 Conflict TableBeingDeleted
>
> The specified table is being deleted.
>
> Try operation later. RequestId:xxxxxxx Time:xxxxxxxx

Le temps de traitement de cette opération dépend de l'utilisation du système. Elle dépend également du nombre d'entrées dans la table.

Le véritable point noir de cette solution est qu'il vous sera donc impossible d'utiliser ou de recréer cette table durant ce laps de temps.

Une piste à creuser est probablement de coder une policy de retry adéquate sur le create  de la table en cas de conflit avant de passer aux solutions suivantes.

## Suppression par Batch Transactions

Il se peut toutefois que vous ayez besoin de garder votre table disponible et que le downtime évoqué précédemment ne soit pas envisageable surtout si d'autres applications y accèdent en continu. Dans ce cas là, vous pouvez supprimer les entités en utilisant **Entity Batch Transactions**.

La documentation concernant les batch transactions est disponible dans la documentation [Performing Entity Group Transactions](https://docs.microsoft.com/en-us/rest/api/storageservices/performing-entity-group-transactions) qui explique notamment les détails des API REST utilisées.

Mais là encore nous trouvons des limitations.

En effet les batchs transactions ne sont possibles que sur les entités donc la propriété PartitionKey est identique. Si cela est votre cas, alors pas de problème, sinon, vous devrez supprimer les entités une à une.

Une autre limitation, plus importante est le nombre d'entités qu'un batch peut traiter et il s'agit de 100 entités au maximum.

## Requêtage par segmentation

Afin de supprimer un ensemble conséquent de données, nous avons vu qu'il était nécessaire de requêter dans un premier temps les entités concernées. Hors cette étape peut prendre un temps conséquent dépendant notamment du nombre d'entrées dans la table.

Comme le précise la documentation : Il est important de préciser qu'une Table Query est une requête qui retrouve une ensemble d'entités qui ne partagent pas de Partition Key. Ces requêtes ne sont pas performantes et vous devriez les éviter dans la mesure du possible.

Si ce type de requêtage est nécessaire, il convient alors de requêter par segments de manière asynchrone. En réalité, si l'on veut faire les choses proprement avec des méthodes asynchrones, on n'a pas vraiment le choix puisque les méthodes **ExecuteAsync** et **ExecuteQueryAsync** n'existent pas.

```csharp
var query = table.CreateQuery()
    .Where(r => r.MyCustomProperty.Equals("test"))
    .AsTableQuery();
var continuationToken = default(TableContinuationToken);
do {
    // Execute the query segment
    var result= await query.ExecuteQuerySegmentedAsync(query, continuationToken);
    // Get entities from result
    var entities = result.Results;
    // Delete entities if (entities.Any())
    {
        entities.ForEach(async e => await table.ExecuteAsync(TableOperation.Delete(e)));
    }
    // Get the continuation information from results
    continuationToken = results.ContinuationToken;
} while (continuationToken != null && !ct.IsCancellationTokenRequested);
```

Notez que si le paramètre **TakeCount** est supérieur à 1000 alors il sera ignoré et la valeur 1000 sera utilisée par défaut.

## Optimisation des requêtes et suppression

Pour utiliser les batchs ou les effectuer des suppressions unitaires il va vous falloir requêter les entités au préalable. N'oubliez pas que pour la suppression, seules les propriétés **PartitionKey** et **RowKey** sont nécessaires, alors n'hésitez pas à ne projeter que ces deux propriétés pour optimiser le traitement.

Pour cela, il vous suffit de rajouter un :

`.Select(new[] {"PartitionKey", "RowKey"})`

dans la construction de votre requête pour effectuer la projection et optimiser votre temps de requête.

Dans le paragraphe précédent, vous noterez également que la suppression est réalisée en utilisant la méthode **ExecuteAsync** asynchrone. Vous pourrez optimiser ces batch de traitements avec un **ConfigureAwait(false)** si le suivi du contexte de ces suppressions n'est pas important dans votre script.
