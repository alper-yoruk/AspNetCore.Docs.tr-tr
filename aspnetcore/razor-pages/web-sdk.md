---
title: ASP.NET Core Web SDK 'Sı
author: Rick-Anderson
description: Microsoft. NET. SDK. Web 'e genel bakış.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: a717d9dc0e326e62ec920d2a838e8b18be3db013
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020697"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="a3784-103">ASP.NET Core Web SDK 'Sı</span><span class="sxs-lookup"><span data-stu-id="a3784-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="a3784-104">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="a3784-104">Overview</span></span>

<span data-ttu-id="a3784-105">`Microsoft.NET.Sdk.Web`, ASP.NET Core uygulamalar oluşturmaya yönelik bir [MSBuild proje SDK 'sına](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) sahiptir.</span><span class="sxs-lookup"><span data-stu-id="a3784-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="a3784-106">Bu SDK olmadan bir ASP.NET Core uygulaması oluşturmak mümkündür, ancak Web SDK 'Sı şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="a3784-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="a3784-107">Birinci sınıf bir deneyim sağlamaya yönelik olarak tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="a3784-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="a3784-108">Çoğu kullanıcı için önerilen hedef.</span><span class="sxs-lookup"><span data-stu-id="a3784-108">The recommended target for most users.</span></span>

<span data-ttu-id="a3784-109">Bir projede Web. SDK 'Yı kullanın:</span><span class="sxs-lookup"><span data-stu-id="a3784-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="a3784-110">Web SDK kullanılarak etkinleştirilen özellikler:</span><span class="sxs-lookup"><span data-stu-id="a3784-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="a3784-111">.NET Core 3,0 veya sonraki bir sürümü hedefleyen projeler:</span><span class="sxs-lookup"><span data-stu-id="a3784-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="a3784-112">[ASP.NET Core paylaşılan çerçeve](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a3784-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="a3784-113">ASP.NET Core uygulamalar oluşturmak için tasarlanan [çözümleyiciler](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) .</span><span class="sxs-lookup"><span data-stu-id="a3784-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="a3784-114">Web SDK 'Sı, yayımlama profillerinin kullanımını ve WebDeploy kullanarak yayımlamayı etkinleştiren MSBuild hedeflerini içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="a3784-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="a3784-115">Özellikler</span><span class="sxs-lookup"><span data-stu-id="a3784-115">Properties</span></span>

| <span data-ttu-id="a3784-116">Özellik</span><span class="sxs-lookup"><span data-stu-id="a3784-116">Property</span></span> | <span data-ttu-id="a3784-117">Açıklama</span><span class="sxs-lookup"><span data-stu-id="a3784-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="a3784-118">Paylaşılan çerçeveye örtük başvuruyu devre dışı bırakır `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="a3784-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="a3784-119">ASP.NET Core Çözümleyicileri için örtülü başvuruyu devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="a3784-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="a3784-120">Razor(Sunucu) uygulamaları oluştururken çözümleyiciler çözümleyicilere örtülü başvuruyu devre dışı bırakır Blazor .</span><span class="sxs-lookup"><span data-stu-id="a3784-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
