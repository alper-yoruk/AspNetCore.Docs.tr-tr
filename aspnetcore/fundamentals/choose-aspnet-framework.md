---
title: ASP.NET 4. x ve ASP.NET Core arasında seçim yapın
author: rick-anderson
description: ASP.NET Core vs. ASP.NET 4. x ve aralarında nasıl seçim yapılacağını açıklar.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
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
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 192784b4f2cb3b80511814de4f777c4a49fc594b
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710743"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>ASP.NET 4. x ve ASP.NET Core arasında seçim yapın

ASP.NET Core, ASP.NET 4. x ' in bir tasarlayadır. Bu makalede aralarındaki farklar listelenir.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core, Windows, macOS veya Linux 'ta modern, bulut tabanlı Web uygulamaları oluşturmaya yönelik açık kaynaklı, platformlar arası bir çerçevedir.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4. x, Windows 'ta kurumsal düzeyde, sunucu tabanlı Web uygulamaları oluşturmak için gereken hizmetleri sağlayan, yetişkinlere yönelik bir çerçevedir.

## <a name="framework-selection"></a>Çerçeve seçimi

Aşağıdaki tabloda ASP.NET Core ASP.NET 4. x ile karşılaştırılır.

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Windows, macOS veya Linux için derleme|Windows için derleme|
|[ Razor Sayfalar](xref:razor-pages/index) , ASP.NET Core 2. x itibariyle bir Web Kullanıcı arabirimi oluşturmak için önerilen yaklaşımdır. Ayrıca bkz. [MVC](xref:mvc/overview), [Web API 'si](xref:tutorials/first-web-api)ve [SignalR](xref:signalr/introduction) .|[Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr) , [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), Web [kancaları](/aspnet/webhooks/)veya [Web sayfaları](/aspnet/web-pages) kullanın|
|Makine başına birden çok sürüm|Makine başına bir sürüm|
|C# veya F kullanarak [Visual Studio](https://visualstudio.microsoft.com/vs/), [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)veya [Visual Studio Code](https://code.visualstudio.com/) ile geliştirme #|C#, VB veya F kullanarak [Visual Studio](https://visualstudio.microsoft.com/vs/) ile geliştirme #|
|ASP.NET 4. x 'ten daha yüksek performans|İyi performans|
|[.NET Core çalışma zamanı kullan](/dotnet/standard/choosing-core-framework-server)|.NET Framework çalışma zamanını kullan|

.NET Framework üzerinde ASP.NET Core 2. x desteği hakkında bilgi için bkz. [ASP.NET Core .NET Framework Hedefleme](xref:index#target-framework) .

## <a name="aspnet-core-scenarios"></a>ASP.NET Core senaryolar

* [Web Siteleri](xref:tutorials/first-mvc-app/start-mvc)
* [API'ler](xref:tutorials/first-web-api)
* [Gerçek zamanlı](xref:signalr/introduction)
* [Azure’a ASP.NET Core uygulaması dağıtma](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET 4. x senaryoları

* [Web Siteleri](/aspnet/mvc)
* [API'ler](/aspnet/web-api)
* [Gerçek zamanlı](/aspnet/signalr)
* [Azure 'da bir ASP.NET 4. x Web uygulaması oluşturma](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET’e Giriş](/aspnet/overview)
* [ASP.NET Core’a Giriş](xref:index)
* <xref:host-and-deploy/azure-apps/index>
