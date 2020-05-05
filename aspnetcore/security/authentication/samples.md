---
title: ASP.NET Core için kimlik doğrulama örnekleri
author: rick-anderson
description: ASP.NET Core deposundaki kimlik doğrulama örneklerine bağlantılar sağlar.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 7cd0fe60d7917abda7d8ac0e071deca13a4136ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776558"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core için kimlik doğrulama örnekleri

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core deposu](https://github.com/dotnet/AspNetCore) , *aspnetcore/src/Security/Samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:

* [Talepleri dönüştürme](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Tanımlama bilgisi kimlik doğrulaması](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Özel ilke sağlayıcısı-ıauthorizationpolicyprovider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Dinamik kimlik doğrulama şemaları ve seçenekleri](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Dış talepler](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [İsteğe bağlı tanımlama bilgisi ve başka bir kimlik doğrulama düzeni arasında seçim yapma](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Statik dosyalara erişimi kısıtlar](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Örnekleri çalıştırma

* Bir [dal](https://github.com/dotnet/AspNetCore)seçin. Örneğin, `release/3.1`
* [ASP.NET Core deposunu](https://github.com/dotnet/AspNetCore)kopyalayın veya indirin.
* ASP.NET Core deposunun kopyası ile eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.
* *Aspnetcore/src/Security/Samples* içindeki bir örneğe gidin ve örneği ile `dotnet run`çalıştırın.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core deposu](https://github.com/dotnet/AspNetCore) , *aspnetcore/src/Security/Samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:

* [Talepleri dönüştürme](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Tanımlama bilgisi kimlik doğrulaması](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Özel ilke sağlayıcısı-ıauthorizationpolicyprovider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Dinamik kimlik doğrulama şemaları ve seçenekleri](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Dış talepler](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [İsteğe bağlı tanımlama bilgisi ve başka bir kimlik doğrulama düzeni arasında seçim yapma](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Statik dosyalara erişimi kısıtlar](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Örnekleri çalıştırma

* Bir [dal](https://github.com/dotnet/AspNetCore)seçin. Örneğin, `release/2.2`
* [ASP.NET Core deposunu](https://github.com/dotnet/AspNetCore)kopyalayın veya indirin.
* ASP.NET Core deposunun kopyası ile eşleşen [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) sürümünü yüklediğinizi doğrulayın.
* *Aspnetcore/src/Security/Samples* içindeki bir örneğe gidin ve örneği ile `dotnet run`çalıştırın.

::: moniker-end
