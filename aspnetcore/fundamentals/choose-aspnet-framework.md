---
title: 4.x ile ASP.NET 4.x arasında seçim yapma ve ASP.NET Core
author: rick-anderson
description: Core ve ASP.NET 4.x ASP.NET ve aralarında nasıl seçim yapılacağını açıklar.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: a7280b59578ee1d96edeeccf9c9df0b0e4eb4eb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665244"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>4.x ile ASP.NET 4.x arasında seçim yapma ve ASP.NET Core

ASP.NET Core, ASP.NET 4.x'in yeniden tasarlanmasıdır. Bu makalede, aralarındaki farklar listeleneb.)

## <a name="aspnet-core"></a>ASP.NET Çekirdeği

ASP.NET Core, Windows, macOS veya Linux'ta modern, bulut tabanlı web uygulamaları oluşturmak için açık kaynak kodlu, çapraz platform çerçevesidir.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x, Windows'da kurumsal sınıf, sunucu tabanlı web uygulamaları oluşturmak için gereken hizmetleri sağlayan olgun bir çerçevedir.

## <a name="framework-selection"></a>Çerçeve seçimi

Aşağıdaki tablo, ASP.NET Core ile ASP.NET 4.x'i karşılaştırır.

| ASP.NET Çekirdeği | ASP.NET 4.x |
|---|---|
|Windows, macOS veya Linux için yapı oluşturma|Windows için yapı|
|[Razor Pages,](xref:razor-pages/index) Core 2.x'ASP.NET itibaren bir Web Web UI oluşturmak için önerilen yaklaşımdır. Ayrıca bakınız [MVC](xref:mvc/overview), [SignalR](xref:signalr/introduction) [Web API](xref:tutorials/first-web-api), ve .|[Web Formlarını,](/aspnet/web-forms) [SignalR](/aspnet/signalr) [MVC'yi,](/aspnet/mvc) [Web API'yi,](/aspnet/web-api/) [Web Hooks'u](/aspnet/webhooks/)veya [Web Sayfalarını](/aspnet/web-pages) kullanma|
|Makine başına birden fazla versiyon|Makine başına bir sürüm|
|Visual [Studio](https://visualstudio.microsoft.com/vs/), [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)veya C# veya F kullanarak Visual Studio [Kodu](https://code.visualstudio.com/) ile geliştirin #|C#, VB veya F kullanarak [Visual Studio](https://visualstudio.microsoft.com/vs/) ile geliştirin #|
|ASP.NET 4.x'ten daha yüksek performans|İyi performans|
|[.NET Core çalışma süresini kullanma](/dotnet/standard/choosing-core-framework-server)|.NET Framework çalışma süresini kullanma|

ASP.NET Core 2.x desteği hakkında bilgi için [ASP.NET .NET Framework](xref:index#target-framework) hedeflemesi.

## <a name="aspnet-core-scenarios"></a>ASP.NET Temel senaryoları

* [Web Siteleri](xref:tutorials/first-mvc-app/index)
* [API'ler](xref:tutorials/first-web-api)
* [Gerçek zamanlı](xref:signalr/introduction)
* [Azure’a ASP.NET Core uygulaması dağıtma](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET 4.x senaryoları

* [Web Siteleri](/aspnet/mvc)
* [API'ler](/aspnet/web-api)
* [Gerçek zamanlı](/aspnet/signalr)
* [Azure'da ASP.NET 4.x web uygulaması oluşturma](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET’e Giriş](/aspnet/overview)
* [ASP.NET Core’a Giriş](xref:index)
* <xref:host-and-deploy/azure-apps/index>
