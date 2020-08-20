---
title: ASP.NET Core Web SDK 'Sı
author: Rick-Anderson
description: Microsoft. NET. SDK. Web 'e genel bakış.
ms.author: riande
ms.date: 01/25/2020
no-loc:
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
uid: razor-pages/web-sdk
ms.openlocfilehash: 163bc2679deda449f97cb4e50da1093e6b1edda4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634819"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK 'Sı

### <a name="overview"></a>Genel Bakış

`Microsoft.NET.Sdk.Web` , ASP.NET Core uygulamalar oluşturmaya yönelik bir [MSBuild proje SDK 'sına](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) sahiptir. Bu SDK olmadan bir ASP.NET Core uygulaması oluşturmak mümkündür, ancak Web SDK 'Sı şu şekilde olur:

* Birinci sınıf bir deneyim sağlamaya yönelik olarak tasarlanmıştır.
* Çoğu kullanıcı için önerilen hedef.

Bir projede Web. SDK 'Yı kullanın:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Web SDK kullanılarak etkinleştirilen özellikler:

* .NET Core 3,0 veya sonraki bir sürümü hedefleyen projeler:

  * [ASP.NET Core paylaşılan çerçeve](xref:fundamentals/metapackage-app).
  * ASP.NET Core uygulamalar oluşturmak için tasarlanan [çözümleyiciler](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) .
* Web SDK 'Sı, yayımlama profillerinin kullanımını ve WebDeploy kullanarak yayımlamayı etkinleştiren MSBuild hedeflerini içeri aktarır.

### <a name="properties"></a>Özellikler

| Özellik | Açıklama |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Paylaşılan çerçeveye örtük başvuruyu devre dışı bırakır `Microsoft.AspNetCore.App` . |
| `DisableImplicitAspNetCoreAnalyzers` | ASP.NET Core Çözümleyicileri için örtülü başvuruyu devre dışı bırakır. |
| `DisableImplicitComponentsAnalyzers` | Razor(Sunucu) uygulamaları oluştururken çözümleyiciler çözümleyicilere örtülü başvuruyu devre dışı bırakır Blazor . |
