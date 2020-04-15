---
title: ASP.NET Core için kimlik doğrulama örnekleri
author: rick-anderson
description: ASP.NET Core deposundaki kimlik doğrulama örneklerine bağlantılar sağlar.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308221"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core için kimlik doğrulama örnekleri

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core deposu,](https://github.com/dotnet/AspNetCore) *AspNetCore/src/Security/samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:

* [Talepleri dönüştürme](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Çerez kimlik doğrulaması](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Özel ilke sağlayıcısı - IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Dinamik kimlik doğrulama şemaları ve seçenekleri](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Dış talepler](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [İsteğe dayalı çerez ve başka bir kimlik doğrulama düzeni arasında seçme](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Statik dosyalara erişimi kısıtlar](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Örnekleri çalıştırma

* Bir [dal](https://github.com/dotnet/AspNetCore)seçin. Örneğin, `release/3.1`
* ASP.NET [Core deposunu](https://github.com/dotnet/AspNetCore)klonla veya indirin.
* ASP.NET Core deposunun klonuyla eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.
* *AspNetCore/src/Security/samples'deki* bir örneğe gidin `dotnet run`ve örneği .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core deposu,](https://github.com/dotnet/AspNetCore) *AspNetCore/src/Security/samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:

* [Talepleri dönüştürme](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Çerez kimlik doğrulaması](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Özel ilke sağlayıcısı - IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Dinamik kimlik doğrulama şemaları ve seçenekleri](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Dış talepler](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [İsteğe dayalı çerez ve başka bir kimlik doğrulama düzeni arasında seçme](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Statik dosyalara erişimi kısıtlar](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Örnekleri çalıştırma

* Bir [dal](https://github.com/dotnet/AspNetCore)seçin. Örneğin, `release/2.2`
* ASP.NET [Core deposunu](https://github.com/dotnet/AspNetCore)klonla veya indirin.
* ASP.NET Core deposunun klonuyla eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.
* *AspNetCore/src/Security/samples'deki* bir örneğe gidin `dotnet run`ve örneği .

::: moniker-end
