---
title: ASP.NET Çekirdek Web SDK
author: Rick-Anderson
description: Microsoft.NET.Sdk.Web'e Genel Bakış.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661058"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Çekirdek Web SDK

### <a name="overview"></a>Genel Bakış

`Microsoft.NET.Sdk.Web`Core uygulamaları oluşturmak için bir [MSBuild projesi SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) ASP.NET. Bu SDK olmadan bir ASP.NET Core uygulaması oluşturmak mümkündür, ancak, Web SDK:

* Birinci sınıf bir deneyim sağlamak için uyarlanmıştır.
* Çoğu kullanıcı için önerilen hedef.

Web.SDK'yı bir projede kullanın:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Web SDK kullanılarak etkinleştirilen özellikler:

* .NET Core 3.0 veya daha sonra dolaylı olarak başvuru hedefleyen projeler:

  * [ASP.NET Core paylaşılan çerçeve](xref:fundamentals/metapackage-app).
  * Core uygulamaları oluşturmak için tasarlanan [ASP.NET analizörler.](/visualstudio/extensibility/getting-started-with-roslyn-analyzers)
* Web SDK, WebDeploy kullanarak yayımlama profillerinin ve yayımlamanın kullanımını sağlayan MSBuild hedeflerini içeri niçin üstler.

### <a name="properties"></a>Özellikler

| Özellik | Açıklama |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Paylaşılan çerçeveye örtülü başvuruyu `Microsoft.AspNetCore.App` devre dışı kılabilir. |
| `DisableImplicitAspNetCoreAnalyzers` | ASP.NET Core çözümleyicilerine örtülü başvuruları devre dışı kılabilir. |
| `DisableImplicitComponentsAnalyzers` | (sunucu) uygulamaları yaparken Blazor Razor Components çözümleyicilerine örtülü başvuruyu devre dışı katır. |
