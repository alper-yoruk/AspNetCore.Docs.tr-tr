---
title: ASP.NET Core Web SDK 'Sı
author: Rick-Anderson
description: Microsoft. NET. SDK. Web 'e genel bakış.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: 8cc0a51d3d917300f3add31f5884b4784dd573dd
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059760"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK 'Sı

### <a name="overview"></a>Genel Bakış

`Microsoft.NET.Sdk.Web` , ASP.NET Core uygulamalar oluşturmaya yönelik bir [MSBuild proje SDK 'sına](/visualstudio/msbuild/how-to-use-project-sdk) sahiptir. Bu SDK olmadan bir ASP.NET Core uygulaması oluşturmak mümkündür, ancak Web SDK 'Sı şu şekilde olur:

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