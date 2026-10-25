---
layout: post
topics: [Cloud & DevOps]
title:  "Le DNS qui mentait : enquête sur un cache négatif de 30 minutes"
description: "Des enregistrements visibles dans Cloudflare mais introuvables en DNS : l'enquête, jusqu'au cache négatif de ma box."
date:   2026-11-08 09:00:00.000 +01:00
img: posts/dns-cache-negatif-banner.png
tags: [DNS, Cloudflare, GitHub Pages, Réseau]
---

Il y a quelques semaines, j'ai fait passer ce blog de `michaelfery.github.io` à **mfery.dev**, un domaine que j'ai chez Cloudflare. La manipulation est classique : quelques enregistrements DNS vers GitHub Pages, un fichier `CNAME` dans le dépôt, un certificat HTTPS. Vingt minutes, en théorie.

En pratique, j'ai passé une partie de la soirée face à un DNS qui me jurait que mes enregistrements n'existaient pas, alors que je les voyais dans le tableau de bord Cloudflare. Le coupable n'était ni Cloudflare, ni GitHub, ni un pare-feu. C'était un cache, celui de ma box, empoisonné par une requête faite trop tôt. Voici l'enquête, parce qu'elle illustre un mécanisme du DNS que peu de gens connaissent : le **cache négatif**.

## Le décor

J'ai fait la migration avec Claude Code dans mon terminal. Pendant que je créais les enregistrements dans Cloudflare, il préparait le dépôt et vérifiait le DNS avec `dig`. Les enregistrements à créer, pour un domaine racine vers GitHub Pages :

| Type | Nom | Valeur |
|---|---|---|
| A ×4 | `@` | `185.199.108.153` à `185.199.111.153` |
| AAAA ×4 | `@` | `2606:50c0:8000::153` à `2606:50c0:8003::153` |
| CNAME | `www` | `michaelfery.github.io` |
| TXT | `_github-pages-challenge-<compte>` | le code de vérification GitHub |

Tous en **DNS only** (le nuage gris de Cloudflare), pour que GitHub puisse émettre son certificat.

## Le symptôme

Une fois les enregistrements créés, la vérification donnait ceci, en interrogeant **directement** le serveur de noms de Cloudflare, sans passer par aucun résolveur intermédiaire :

```text
$ dig +short mfery.dev A @aisha.ns.cloudflare.com
                                   ← rien
$ dig +short mfery.dev AAAA @aisha.ns.cloudflare.com
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
$ dig +short www.mfery.dev CNAME @aisha.ns.cloudflare.com
                                   ← rien
```

Les AAAA étaient là. Les A, le CNAME et le TXT, non. Conclusion évidente : j'avais oublié de cliquer sur « Save » pour une partie des enregistrements.

Sauf que le tableau de bord Cloudflare les montrait tous, bien enregistrés.

## Premier indice : DNS-over-HTTPS dit autre chose

Pour départager, on a posé la même question en DNS-over-HTTPS, qui passe par le port 443 comme n'importe quelle page web :

```text
$ curl -s -H 'accept: application/dns-json' \
    'https://cloudflare-dns.com/dns-query?name=mfery.dev&type=A'
{"Status":0, ... "Answer":[
  {"name":"mfery.dev","type":1,"TTL":300,"data":"185.199.110.153"},
  {"name":"mfery.dev","type":1,"TTL":300,"data":"185.199.108.153"}, ...]}
```

Tout était là : les quatre A, le CNAME, le TXT. Le DNS « classique », sur le port 53, et le DNS par HTTPS ne voyaient donc pas le même monde. Et le site répondait parfaitement en 4G sur mon téléphone.

## Deuxième indice : quelqu'un répond à la place des autres

Quand on interroge `8.8.8.8`, c'est censé être Google qui répond. Sauf que les réponses portaient un drapeau curieux :

```text
$ dig google.com A @8.8.8.8 | grep flags
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, ...
```

`aa` signifie *authoritative answer* : « je suis le serveur officiel de ce domaine ». Un résolveur public comme celui de Google ne met **jamais** ce drapeau pour `google.com`, et encore moins pour `mfery.dev`. Même chose avec `1.1.1.1`, `9.9.9.9` et le serveur de Cloudflare.

Le test décisif : interroger une adresse qui n'existe pas. `192.0.2.1` fait partie d'une plage réservée à la documentation, rien ne doit y répondre.

```text
$ dig example.com A @192.0.2.1
;; ->>HEADER<<- opcode: QUERY, status: NOERROR
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 2, ...
```

Une réponse est arrivée. **Toutes les requêtes DNS sur le port 53 étaient interceptées** et traitées par un relais, quelle que soit l'adresse visée. C'est ce qu'on appelle une interception DNS transparente.

## Troisième indice : une zone d'un autre temps

Restait à comprendre pourquoi ce relais mentait. La réponse vide contenait, dans sa section *authority*, l'enregistrement SOA de la zone, avec son numéro de série :

```text
$ dig mfery.dev A @8.8.8.8 +noall +authority
mfery.dev.  208  IN  SOA  aisha.ns.cloudflare.com. dns.cloudflare.com. 2417797143 10000 2400 604800 1800
```

Le vrai serveur de Cloudflare, lui, annonçait une autre version :

```text
mfery.dev.  1143  IN  SOA  aisha.ns.cloudflare.com. dns.cloudflare.com. 2418362860 10000 2400 604800 1800
```

Le relais servait donc **une ancienne version de la zone**, celle d'avant mes enregistrements. Et le `208` devant était un compte à rebours : quelques secondes plus tard, il affichait `196`.

## L'explication : le cache négatif

Un cache DNS ne mémorise pas seulement les réponses positives. Quand un domaine n'a pas d'enregistrement d'un type donné, cette **absence** est mise en cache elle aussi : c'est le cache négatif, décrit par la [RFC 2308](https://www.rfc-editor.org/rfc/rfc2308). Sa durée est le plus petit de deux nombres : la durée de vie du SOA et son dernier champ, le *minimum*. Ici, le dernier champ vaut `1800` : **30 minutes**.

Le scénario devient limpide :

1. Au tout début de la migration, **avant** que je crée quoi que ce soit, Claude a vérifié l'état du domaine avec un `dig mfery.dev A`. Réponse : rien, ce qui était vrai à ce moment-là.
2. Le relais a mis cette absence en cache pour 30 minutes.
3. J'ai créé les enregistrements. Toutes les requêtes IPv4 suivantes ont reçu la réponse mémorisée : « pas d'adresse ».
4. Les AAAA, eux, n'avaient **jamais** été demandés avant leur création. Rien en cache, donc une vraie réponse, à jour. Même logique pour le TXT, demandé une fois avant que je l'ajoute, et resté vide.

D'où le tableau incohérent du début : chaque enregistrement était juste ou faux selon qu'on l'avait interrogé trop tôt ou non.

## Qui est le relais ?

Sur mon Mac tournent un antivirus et un client VPN, deux candidats crédibles pour intercepter le DNS. La réponse est venue du téléphone : **sur le Wi-Fi de la maison, il voyait la même chose ; en 4G, tout fonctionnait**.

Le relais est donc commun à tous les appareils du Wi-Fi : c'est **la box**. Elle intercepte tout ce qui sort sur le port 53, y compris les requêtes adressées à Google ou Cloudflare, et y répond depuis son cache. Les temps de réponse allaient dans le même sens : environ 6 ms pour une « requête à Google », pour 3 ms de ping vers la box.

<!-- TODO : préciser le modèle de la box, et si l'interception est désactivable. -->

## Le dénouement… à deux étages

À 23 h 46, le cache de la box a expiré et elle a enfin renvoyé les quatre adresses. Le téléphone en Wi-Fi a suivi. Le Mac, lui, refusait toujours : macOS a **son propre cache**, qui avait mémorisé la même réponse vide. Pour le vider :

```bash
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
```

Deux étages de cache négatif, donc : la box, puis le système.

## Ce que j'en retiens

- **Ne pas interroger un enregistrement DNS avant de l'avoir créé.** C'est contre-intuitif, puisqu'on a envie de vérifier l'état de départ. Mais cette vérification met l'absence en cache pour la durée du *minimum* du SOA. Si on veut regarder avant, mieux vaut passer par DNS-over-HTTPS ou interroger un autre type d'enregistrement.
- **`dig @serveur` ne garantit pas qu'on parle à ce serveur.** Le drapeau `aa` sur une réponse qui ne devrait pas l'avoir, ou une réponse venue d'une adresse inexistante, trahissent une interception.
- **Le numéro de série du SOA est un excellent détecteur de cache.** S'il est plus ancien que celui du serveur officiel, on regarde une copie périmée.
- **Changer de réseau est le test le plus rapide.** Passer le téléphone en 4G a tranché en dix secondes ce que les commandes n'arrivaient pas à départager.
- **Penser aux deux étages.** Quand la box est à jour, le système d'exploitation, et parfois le navigateur, peuvent encore garder l'ancienne réponse.

Et une leçon plus large sur le travail avec un assistant IA : la vérification qui a créé le problème était faite par réflexe, de bonne foi, et Claude a d'abord conclu à des enregistrements manquants, puis à un filtrage. C'est en lui demandant **pourquoi**, plutôt qu'en acceptant le premier diagnostic, qu'on est arrivés à la vraie cause, preuves à l'appui.
