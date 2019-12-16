---
layout: post
title:  .NET Core 3.0 disponible sur Azure App Service
date:   2019-10-05 08:32:49.067
# categories: blog
# description: 
img: posts/dotnet-core-banner.png
tags: [Azure, dotnet, .net core 3.0]
redirect_from: "/blog/dotnet-core-3-azure-availability"
---
TLDR;) Lisez le titre et vous avez l'info.

&nbsp;

Jeudi dernier nous organisions chez **YounitedCredit** un meetup [**DevTalks.NET**](https://www.meetup.com/fr-FR/DevTalks-Net) pour faire un [retour sur la conférence dotnetconf](https://www.meetup.com/fr-FR/DevTalks-Net/events/264376474/).

Nous avons ainsi passé en revue les dernières annonces et fait quelques démos liés à la dernière version de .NET Core.

Pour ma part j'ai fait une session de live-coding pour présenter **Github Actions 2.0** et son utilisation pour créer un pipeline de CI/CD (Continuous Integration et Continuous Deployment) d'une application **.NET Core 3.0 sur Azure, tout ça depuis Github**.

Tout fier de moi, je finis ma démo par un _"ça y est on a déployé notre app et.... ça ne fonctionne pas ! :)"_.&nbsp;On m'a bien sûr fait la remarque que le [self-contained deployment](https://docs.microsoft.com/en-us/dotnet/core/deploying/#self-contained-deployments-scd) était une solution mais je trouve que ce n'est pas idéal car trop lourd à déployer et que les équipes&nbsp; .NET et Azure auraient pu anticiper et le rendre disponible le jour de l'annonce.

...J'aurais dû me douter que je provoquais le karma...

En effet, depuis le 25 septembre, le dernier runtime .NET Core disponible pour les web appp sur Azure était la version 2.2. Notre app ciblant .NET Core 3.0 ne pouvait donc pas fonctionner.

![Annotation 2019-10-05 102957.png](/assets/img/posts/Annotation 2019-10-05 102957_637058611693088235.png)

Bien mal m'en a pris car le karma m'attendait au tournant. C'est dés le lendemain, aprés mon écriture d'un blog post sur le sujet et juste avant sa publication, que **[Microsoft a annoncé la disponibilité de la dernière version du runtime pour les App Service](https://github.com/Azure/app-service-announcements/issues/204),** m'obligeant par la m&ecirc;me occasion à annuler mon blogpost et le remplacer par ce mea-culpa, bien trop long pour une news si simple ;)

![Annotation 2019-10-05 102931.png](/assets/img/posts/Annotation 2019-10-05 102931_637058611696723263.png)

&nbsp;

**Conclusion**: Soyez patients, ne provoquez pas le karma et amusez-vous avec .NET Core 3.0 sur Azure !

&nbsp;

PS: Pour ceux qui verraient que ce n'est pas encore disponible pour Linux, sachez que [ça arrive trés bientôt](https://github.com/Azure/app-service-announcements-discussions/issues/118#issuecomment-538553674) (on ne m'y reprendra pas).
