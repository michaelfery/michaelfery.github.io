---
layout: post
title:  Créer un Proxy pour Azure Storage avec Azure API Management
date:   2018-10-01 06:06:10
# categories: blog
img: posts/azure-banner.png
description: Dans cet article nous allons voir comment Azure API Management peut jouer le rôle de proxy entre les ressources Azure Storage et vos utilisateurs afin d'y apporter sécurité, cache, etc.
tags: ["azure", "storage", "proxy", "api management"]
redirect_from: "/blog/azure-storage-proxy-api-management"
---

[Azure API Management](https://azure.microsoft.com/en-in/services/api-management/)<span style="color: #333333; font-family: Lato, sans-serif;"> est un service Azure sécurisé, fiable et scalable permettant de publier, consommer et gérer des APIs. Cette plateforme permet de renforcer la sécurité et d'analyser l'usage des APIs.</span>

<span style="color: #333333; font-family: Lato, sans-serif;">Selon les projets, proposer un front-end pour ses ressources peut répondre à plusieurs besoins:</span>

1.  Gestion de la sécurité directement depuis API Management afin de restreindre l'accès aux ressources à différentes applications ou utilisateurs.
2.  Transformer les accès aux ressources. Aggréger plusieurs ressources en une seule réponse GET par exemple.
3.  Gérer le cache des réponses afin d'alléger les requêtes au Storage.

# Scénario

Dans cet article, nous allons voir ensemble l'implémentation d'une API permettant de télécharger des Blobs privés depuis Azure Storage. Nous n'aborderons pas la sécurisation de cet API puisque celle-ci pourrait faire l'objet d'un article à part entière.

![](/assets/img/posts/apim_storage_proxy.png)

# Azure Storage REST API

Nous allons commencer par créer un Compte de Stockage ou Storage Account.

La sécurisation globale de ce compte de stockage est définie dans l'onglet **Settings >> Access Keys**.

Notez bien comment récupérer la clé **Key** car elle vous sera utile dans la configuration de votre API.

![](/assets/img/posts/storage_sas_key.png)

Dans de compte nous allons créer un Blob Container. Lors de la création du container, veillez à saisir **Public access level  : Private (no anonymous access)**

![](/assets/img/posts/blob_container.png)

Dans ce container, nous allons ajouter un fichier qui sera celui que nous essaierons de télécharger à la fin de cet article.

Pour cela, dans le container, cliquez sur le bouton **Upload** et laissez vous guider.

# API Management

Partons du postulat que vous avez déja créé votre ressource API Management puisque celle-ci prend environ 45 minutes à être provisionnée :S

Il vous faut désormais créer une API que l'on appelera "Storage". Lors de la création de votre API, renseignez **https://{storageAccountName}.blob.core.windows.net** dans la propriété **Web Service URL**.

Notez également la propriété **API URL Suffix**, surtout si vous la customisez, puisqu'elle fera partie de la route d'accès à vos ressources.

![](/assets/img/posts/api_creation.png)

Ouvrez ensuite votre API, sélectionnez **Add Operation** et créez une opération avec comme URL **GET** + **/{container}/{blob}**.

Vous avez désormais une API qui répond aux requêtes entrantes et les redirige vers l'API Blobs d'Azure Storage mais à ce stade, si vous tentez de la consommer vous recevrez une erreur d'authentification.

## Policy

Nous avons défini une sécurité pour notre container dans Azure Storage (Authentication Type SAS). Nous allons donc désormais indiquer à notre API comment utiliser cette SAS Key pour authentifier les requêtes faites aux ressources.

Sélectionnez votre API puis l'opération créée auparavant et, dans l'onglet **Design**, sélectionnez le bouton **Policies** pour passer en mode édition.

![](/assets/img/posts/inbound_policies_btn.png)

La partie **inbound** correspond aux traitements effectuées sur les requêtes entrantes et c'est ici que nous allons travailler.

Nous allons commencer par définir des variables :

<div style="line-height: 17px;">

<div><span style="color: #383838;"><</span><span style="color: #800000;">set</span><span style="color: #c90000;">-variable</span> <span style="color: #c90000;">name</span>=<span style="color: #0451a5;">"APIVersion"</span> <span style="color: #c90000;">value</span>=<span style="color: #0451a5;">"2012-02-12"</span> <span style="color: #383838;">/></span></div>

<div><span style="color: #383838;"><</span><span style="color: #800000;">set</span><span style="color: #c90000;">-variable</span> <span style="color: #c90000;">name</span>=<span style="color: #0451a5;">"RequestPath"</span> <span style="color: #c90000;">value</span>=<span style="color: #0451a5;">"@(context.Request.Url.Path)"</span> <span style="color: #383838;">/></span></div>

<div><span style="color: #383838;"><</span><span style="color: #800000;">set</span><span style="color: #c90000;">-variable</span> <span style="color: #c90000;">name</span>=<span style="color: #0451a5;">"UTCNow"</span> <span style="color: #c90000;">value</span>=<span style="color: #0451a5;">"@(DateTime.UtcNow.ToString("</span><span style="color: #c90000;">R</span><span style="color: #0451a5;">"))"</span> <span style="color: #383838;">/></span></div>

</div>

Nous allons ensuite ajouter un header **date** aux requêtes entrantes :

<div style="background-color: #fffffe; font-family: Consolas, 'Courier New', monospace; line-height: 17px; white-space: pre;">

<div style="font-family: Consolas, 'Courier New', monospace; line-height: 17px;">

<pre><span style="color: #383838;"><</span><span style="color: #800000;">set</span><span style="color: #c90000;">-header</span> <span style="color: #c90000;">name</span>=<span style="color: #0451a5;">"date"</span> <span style="color: #c90000;">exists-action</span>=<span style="color: #0451a5;">"override"</span><span style="color: #383838;">></span>  
 <span style="color: #383838;"><</span><span style="color: #800000;">value</span><span style="color: #383838;">></span><span style="color: #e00000;">@(</span>context.Variables.GetValueOrDefault<span style="color: #383838;"><</span><span style="color: #800000;">string</span><span style="color: #383838;">></span>(<span style="color: #a31515;">"UTCNow"</span>)<span style="color: #e00000;">)</span><span style="color: #383838;"></</span><span style="color: #800000;">value</span><span style="color: #383838;">></span>  
<span style="color: #383838;"></</span><span style="color: #800000;">set</span><span style="color: #c90000;">-header</span><span style="color: #383838;">></span></pre>

</div>

</div>

Enfin nous ajouterons un header **Authorization** :<span style="color: #000000; font-family: Verdana, Arial, Helvetica, sans-serif;"> </span>

<div style="background-color: #fffffe; font-family: Consolas, 'Courier New', monospace; line-height: 17px; white-space: pre;">

<pre><span style="color: #383838;"><</span><span style="color: #800000;">set</span><span style="color: #c90000;">-header</span> <span style="color: #c90000;">name</span>=<span style="color: #0451a5;">"Authorization"</span> <span style="color: #c90000;">exists-action</span>=<span style="color: #0451a5;">"override"</span><span style="color: #383838;">></span>  
 <span style="color: #383838;"><</span><span style="color: #800000;">value</span><span style="color: #383838;">></span><span style="color: #e00000;">@{</span>  
 var account = <span style="color: #a31515;">"storageproxy"</span>;  
 var key = <span style="color: #a31515;">"QOQDHuUMSB9ztzLn49SUph3LpL2gIjVXpfkpOuy8855kkOdMvlvgz7mJ9S2F6zPgN6nCSPdiy0+AgHQFeetBmQ=="</span>;  
 var splitPath = context.Variables.GetValueOrDefault<span style="color: #383838;"><</span><span style="color: #800000;">string</span><span style="color: #383838;">></span>(<span style="color: #a31515;">"RequestPath"</span>).Split(<span style="color: #a31515;">'/'</span>);  
 var container = splitPath.Reverse().Skip(<span style="color: #09885a;">1</span>).First();  
 var file = splitPath.Last();  
 var dateToSign = context.Variables.GetValueOrDefault<span style="color: #383838;"><</span><span style="color: #800000;">string</span><span style="color: #383838;">></span>(<span style="color: #a31515;">"UTCNow"</span>);  
 var stringToSign = string.Format(<span style="color: #a31515;">"GET\n\n\n{0}\n/{1}/{2}/{3}"</span>, dateToSign, account, container, file);  
 string signature;  
 var unicodeKey = Convert.FromBase64String(key);  
 using (var hmacSha256 = new HMACSHA256(unicodeKey))  
 {  
 var dataToHmac = Encoding.UTF8.GetBytes(stringToSign);  
 signature = Convert.ToBase64String(hmacSha256.ComputeHash(dataToHmac));  
 }  
 var authorizationHeader = string.Format(  
 <span style="color: #a31515;">"{0} {1}:{2}"</span>,  
 <span style="color: #a31515;">"SharedKey"</span>,  
 account,  
 signature);  
 return authorizationHeader;  
 <span style="color: #e00000;">}</span><span style="color: #383838;"></</span><span style="color: #800000;">value</span><span style="color: #383838;">></span>  
<span style="color: #383838;"></</span><span style="color: #800000;">set</span><span style="color: #c90000;">-header</span><span style="color: #383838;">></span></pre>

</div>

Vous remplacerez bien évidemment les variables **account** et **key** par le nom du **StorageAccount** et la **clef SAS** définis dans l'étape du Storage.

Les autres lignes du script consistent à récupérer la requêtes entrante, à convertir la SAS Key en signature et à ajouter cette SharedKey composée en tant que header Authorization.

La requête ainsi modifiée est ensuite transmise à l'API de Storage qui en reconnaît l'authentificatione et renvoie la ressource demandée.

# Test du proxy

Une fois toutes ces étapes suivies, vous devriez vous retrouver avec une API consommable depuis n'importe quel client (navigateur, Postman, etc.) permettant de télécharger vos ressources depuis une url dynamique:

**http://{apiManagementUrl}/{apiUrlSuffix}/{container}/{blob}**

# Conclusion

Nous avons vu ensemble comment proxyfier des ressources Azure Storage via une API dans Azure API Management.

Avec cette logique, vous proposez aux utilisateurs ou applications clientes un accès aux ressources agnostique de la plateforme de ressources sous-jacente. Vous pouvez alors désormais gérer votre logique de cache, de connexion aux storages (SPN, MSI ?), apporter de l'intelligence fonctionnelle ou même switcher vers un autre provider sans douleur pour les clients.

# Aller plus loin

Plusieurs pistes d'évolution et d'amélioration pour votre proxy:

*   Ajouter une authentification type JWT sur vos API pour éviter que ce soit open-bar
*   Appliquer la Policy sur l'API au lieu de l'opération afin qu'elle s'applique sur d'autres opérations
*   Utiliser les NamedValues d'API Management pour stocker vos variables account, key, etc.
*   Utiliser un SPN ou MSI pour éviter de stocker votre SAS Key dans API Management