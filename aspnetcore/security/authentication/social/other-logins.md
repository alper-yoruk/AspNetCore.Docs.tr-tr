---
title: Dış OAuth kimlik doğrulama sağlayıcıları
author: rick-anderson
description: ASP.NET Core uygulamalarıyla çalışan dış OAuth kimlik doğrulama sağlayıcılarını bulur.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/otherlogins
ms.openlocfilehash: 3de76b303c469341ba022fa3d33d3cd2a17581db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014912"
---
# <a name="external-oauth-authentication-providers"></a>Dış OAuth kimlik doğrulama sağlayıcıları

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Oystogi](https://github.com/rustd)ve [Valeriy Novi tskyy](https://github.com/01binary)

Aşağıdaki liste ASP.NET Core uygulamalarla çalışan ortak dış OAuth kimlik doğrulama sağlayıcılarını içerir. [ASPNET-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth)tarafından tutulan gibi üçüncü taraf NuGet paketleri, ASP.NET Core ekibi tarafından uygulanan kimlik doğrulama sağlayıcılarını tamamlamak için kullanılabilir.

* [LinkedIn](https://www.linkedin.com/developer/apps) ([yönergeler](https://developer.linkedin.com/docs/oauth2))

* [Sistem durumu](https://www.instagram.com/developer/register/) ([yönergeler](https://www.instagram.com/developer/authentication/))

* [Reddıt](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([yönergeler](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))

* [GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([yönergeler](https://developer.github.com/v3/oauth/))

* [Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([yönergeler](https://developer.yahoo.com/bbauth/user.html))

* [Tumblr](https://www.tumblr.com/oauth/apps) ([yönergeler](https://www.tumblr.com/docs/api/v2#auth))

* [Pilgi](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([yönergeler](https://developers.pinterest.com/docs/api/overview/?))

* [Pocket](https://getpocket.com/developer/apps/new) ([yönergeler](https://getpocket.com/developer/docs/authentication))

* [Flickr](https://www.flickr.com/services/apps/create) ([yönergeler](https://www.flickr.com/services/api/auth.oauth.html))

* [Dribble](https://dribbble.com/signup) ([yönergeler](https://developer.dribbble.com/v1/oauth/))

* [Vimeo](https://vimeo.com/join) ([yönergeler](https://developer.vimeo.com/api/authentication))

* [SoundCloud](https://soundcloud.com/you/apps/new) ([yönergeler](https://developers.soundcloud.com/blog/we-love-oauth-2))

* [VK](https://vk.com/apps?act=manage) ([yönergeler](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
