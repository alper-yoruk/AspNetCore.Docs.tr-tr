---
title: ASP.NET Core için ayarlayıcısı yapılandırma Blazor
author: guardrex
description: Uygulama oluştururken ara dil (IL) bağlayıcı (kırpıcıyı) denetimini nasıl denetleyeceğinizi öğrenin Blazor .
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055808"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="35ba3-103">ASP.NET Core için ayarlayıcısı yapılandırma Blazor</span><span class="sxs-lookup"><span data-stu-id="35ba3-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="35ba3-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="35ba3-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="35ba3-105">Blazor WebAssembly yayımlanan çıktının boyutunu azaltmak için [ara dil (IL)](/dotnet/standard/managed-code#intermediate-language--execution) kırpmasını gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="35ba3-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="35ba3-106">Uygulamanın kırpılırken boyut için en iyi duruma getirmek, ancak bu etkilere sebep olabilir.</span><span class="sxs-lookup"><span data-stu-id="35ba3-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="35ba3-107">Yansıma ve ilgili dinamik özellikleri kullanan uygulamalar kırpılmadığınızda kesilebilir, çünkü ayarlayıcısı dinamik davranışı öğrenmez ve çalışma zamanında yansıma için hangi türlerin gerekli olduğunu belirleyemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="35ba3-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="35ba3-108">Bu tür uygulamaları kırpmak için, kırpıcıyı kodda yansıma tarafından gereken tüm türler ve uygulamanın bağımlı olduğu paketler veya çerçeveler hakkında bilgilendirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="35ba3-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="35ba3-109">Kırpılan uygulamanın dağıtıldıktan sonra düzgün çalıştığından emin olmak için, geliştirme sırasında yayımlanan çıktıyı sık sık test etmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="35ba3-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="35ba3-110">.NET uygulamalarının bölünmesi, `PublishTrimmed` MSBuild özelliği uygulamanın proje dosyasında olarak ayarlanarak devre dışı bırakılabilir `false` :</span><span class="sxs-lookup"><span data-stu-id="35ba3-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
<span data-ttu-id="35ba3-111">Ayarlayıcısı yapılandırmak için ek seçenekler, [kırpma seçeneklerinde](/dotnet/core/deploying/trimming-options)bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="35ba3-111">Additional options to configure the trimmer can be found at [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="35ba3-112">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="35ba3-112">Additional resources</span></span>

* [<span data-ttu-id="35ba3-113">Kendi içinde bulunan dağıtımları ve yürütülebilir dosyaları kırp</span><span class="sxs-lookup"><span data-stu-id="35ba3-113">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
