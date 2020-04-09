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
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="428ad-103">ASP.NET Çekirdek Web SDK</span><span class="sxs-lookup"><span data-stu-id="428ad-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="428ad-104">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="428ad-104">Overview</span></span>

<span data-ttu-id="428ad-105">`Microsoft.NET.Sdk.Web`Core uygulamaları oluşturmak için bir [MSBuild projesi SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="428ad-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="428ad-106">Bu SDK olmadan bir ASP.NET Core uygulaması oluşturmak mümkündür, ancak, Web SDK:</span><span class="sxs-lookup"><span data-stu-id="428ad-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="428ad-107">Birinci sınıf bir deneyim sağlamak için uyarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="428ad-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="428ad-108">Çoğu kullanıcı için önerilen hedef.</span><span class="sxs-lookup"><span data-stu-id="428ad-108">The recommended target for most users.</span></span>

<span data-ttu-id="428ad-109">Web.SDK'yı bir projede kullanın:</span><span class="sxs-lookup"><span data-stu-id="428ad-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="428ad-110">Web SDK kullanılarak etkinleştirilen özellikler:</span><span class="sxs-lookup"><span data-stu-id="428ad-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="428ad-111">.NET Core 3.0 veya daha sonra dolaylı olarak başvuru hedefleyen projeler:</span><span class="sxs-lookup"><span data-stu-id="428ad-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="428ad-112">[ASP.NET Core paylaşılan çerçeve](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="428ad-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="428ad-113">Core uygulamaları oluşturmak için tasarlanan [ASP.NET analizörler.](/visualstudio/extensibility/getting-started-with-roslyn-analyzers)</span><span class="sxs-lookup"><span data-stu-id="428ad-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="428ad-114">Web SDK, WebDeploy kullanarak yayımlama profillerinin ve yayımlamanın kullanımını sağlayan MSBuild hedeflerini içeri niçin üstler.</span><span class="sxs-lookup"><span data-stu-id="428ad-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="428ad-115">Özellikler</span><span class="sxs-lookup"><span data-stu-id="428ad-115">Properties</span></span>

| <span data-ttu-id="428ad-116">Özellik</span><span class="sxs-lookup"><span data-stu-id="428ad-116">Property</span></span> | <span data-ttu-id="428ad-117">Açıklama</span><span class="sxs-lookup"><span data-stu-id="428ad-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="428ad-118">Paylaşılan çerçeveye örtülü başvuruyu `Microsoft.AspNetCore.App` devre dışı kılabilir.</span><span class="sxs-lookup"><span data-stu-id="428ad-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="428ad-119">ASP.NET Core çözümleyicilerine örtülü başvuruları devre dışı kılabilir.</span><span class="sxs-lookup"><span data-stu-id="428ad-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="428ad-120">(sunucu) uygulamaları yaparken Blazor Razor Components çözümleyicilerine örtülü başvuruyu devre dışı katır.</span><span class="sxs-lookup"><span data-stu-id="428ad-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
