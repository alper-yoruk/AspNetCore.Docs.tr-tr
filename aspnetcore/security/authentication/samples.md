---
title: ASP.NET Core için kimlik doğrulama örnekleri
author: rick-anderson
description: ASP.NET Core deposundaki kimlik doğrulama örneklerine bağlantılar sağlar.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 4153a443748dbff40be19e25fc1c719ee4e39609
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060345"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core için kimlik doğrulama örnekleri

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core deposu](https://github.com/dotnet/AspNetCore) , *aspnetcore/src/Security/Samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:

* [Talepleri dönüştürme](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Cookie yetkilendirmesi](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Özel ilke sağlayıcısı-ıauthorizationpolicyprovider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Dinamik kimlik doğrulama şemaları ve seçenekleri](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Dış talepler](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [cookieİsteğe bağlı olarak ve başka bir kimlik doğrulama düzeni arasında seçim yapma](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Statik dosyalara erişimi kısıtlar](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Örnekleri çalıştırma

* Bir [dal](https://github.com/dotnet/AspNetCore)seçin. Örneğin, `release/3.1`
* [ASP.NET Core deposunu](https://github.com/dotnet/AspNetCore)kopyalayın veya indirin.
* ASP.NET Core deposunun kopyası ile eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.
* *Aspnetcore/src/Security/Samples* içindeki bir örneğe gidin ve örneği ile çalıştırın `dotnet run` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core deposu](https://github.com/dotnet/AspNetCore) , *aspnetcore/src/Security/Samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:

* [Talepleri dönüştürme](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* [Cookie yetkilendirmesi](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)
* [Özel ilke sağlayıcısı-ıauthorizationpolicyprovider](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [Dinamik kimlik doğrulama şemaları ve seçenekleri](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* [Dış talepler](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)
* [cookieİsteğe bağlı olarak ve başka bir kimlik doğrulama düzeni arasında seçim yapma](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [Statik dosyalara erişimi kısıtlar](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Örnekleri çalıştırma

* Bir [dal](https://github.com/dotnet/AspNetCore)seçin. Örneğin, `release/2.1`
* [ASP.NET Core deposunu](https://github.com/dotnet/AspNetCore)kopyalayın veya indirin.
* ASP.NET Core deposunun kopyası ile eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.
* *Aspnetcore/src/Security/Samples* içindeki bir örneğe gidin ve örneği ile çalıştırın `dotnet run` .

::: moniker-end
