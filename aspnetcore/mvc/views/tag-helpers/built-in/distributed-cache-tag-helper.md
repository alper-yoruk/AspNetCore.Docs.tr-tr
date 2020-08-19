---
title: ASP.NET Core dağıtılmış önbellek etiketi Yardımcısı
author: pkellner
description: Dağıtılmış önbellek etiketi yardımcısını nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
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
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 67e5b7ef09525063da6e6b7dfce6fd084d279869
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633909"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="b3bab-103">ASP.NET Core dağıtılmış önbellek etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="b3bab-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="b3bab-104">By [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="b3bab-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="b3bab-105">Dağıtılmış önbellek etiketi Yardımcısı, içeriğini dağıtılmış bir önbellek kaynağına önbelleğe alarak ASP.NET Core uygulamanızın performansını önemli ölçüde iyileştirebilme olanağı sağlar.</span><span class="sxs-lookup"><span data-stu-id="b3bab-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="b3bab-106">Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..</span><span class="sxs-lookup"><span data-stu-id="b3bab-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="b3bab-107">Dağıtılmış önbellek etiketi Yardımcısı, önbellek etiketi Yardımcısı ile aynı temel sınıftan devralınır.</span><span class="sxs-lookup"><span data-stu-id="b3bab-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="b3bab-108">Tüm [önbellek etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) öznitelikleri, dağıtılmış etiket Yardımcısı tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b3bab-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="b3bab-109">Dağıtılmış önbellek etiketi Yardımcısı, [Oluşturucu Ekleme](xref:fundamentals/dependency-injection#constructor-injection-behavior)işlemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b3bab-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="b3bab-110"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek etiketi Yardımcısı 'nın oluşturucusuna geçirilir.</span><span class="sxs-lookup"><span data-stu-id="b3bab-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="b3bab-111">`IDistributedCache`(Startup.cs) içinde somut bir uygulama oluşturulmadıysa `Startup.ConfigureServices` , dağıtılmış önbellek etiketi Yardımcısı önbelleğe alınmış verileri [önbellek etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)olarak depolamak için aynı bellek içi sağlayıcıyı kullanır.*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b3bab-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="b3bab-112">Dağıtılmış önbellek etiketi Yardımcısı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="b3bab-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="b3bab-113">Önbellek etiketi Yardımcısı ile paylaşılan öznitelikler</span><span class="sxs-lookup"><span data-stu-id="b3bab-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="b3bab-114">Dağıtılmış önbellek etiketi Yardımcısı, önbellek etiketi Yardımcısı ile aynı sınıftan devralınır.</span><span class="sxs-lookup"><span data-stu-id="b3bab-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="b3bab-115">Bu özniteliklerin açıklamaları için [önbellek etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)' na bakın.</span><span class="sxs-lookup"><span data-stu-id="b3bab-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="b3bab-116">name</span><span class="sxs-lookup"><span data-stu-id="b3bab-116">name</span></span>

| <span data-ttu-id="b3bab-117">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="b3bab-117">Attribute Type</span></span> | <span data-ttu-id="b3bab-118">Örnek</span><span class="sxs-lookup"><span data-stu-id="b3bab-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="b3bab-119">Dize</span><span class="sxs-lookup"><span data-stu-id="b3bab-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="b3bab-120">`name` gereklidir.</span><span class="sxs-lookup"><span data-stu-id="b3bab-120">`name` is required.</span></span> <span data-ttu-id="b3bab-121">`name`Özniteliği, depolanan her önbellek örneği için bir anahtar olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b3bab-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="b3bab-122">Sayfadaki sayfa adı ve konuma göre her örneğe bir önbellek anahtarı atayan önbellek etiketi Yardımcısı 'nın aksine Razor Razor , dağıtılmış önbellek etiketi Yardımcısı yalnızca anahtarını özniteliğinde temel alır `name` .</span><span class="sxs-lookup"><span data-stu-id="b3bab-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="b3bab-123">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b3bab-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="b3bab-124">Dağıtılmış önbellek etiketi Yardımcısı ıdistributedönbellek uygulamaları</span><span class="sxs-lookup"><span data-stu-id="b3bab-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="b3bab-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>ASP.NET Core için yerleşik iki uygulama vardır.</span><span class="sxs-lookup"><span data-stu-id="b3bab-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="b3bab-126">Biri SQL Server tabanlıdır ve diğeri redin tabanlıdır.</span><span class="sxs-lookup"><span data-stu-id="b3bab-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="b3bab-127">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)gibi üçüncü taraf uygulamalar da mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="b3bab-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="b3bab-128">Bu uygulamaların ayrıntıları adresinde bulunabilir <xref:performance/caching/distributed> .</span><span class="sxs-lookup"><span data-stu-id="b3bab-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="b3bab-129">Her iki uygulama da bir örneğini ayarlamayı `IDistributedCache` içerir `Startup` .</span><span class="sxs-lookup"><span data-stu-id="b3bab-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="b3bab-130">Özellikle belirli bir uygulamasını kullanmayla ilişkili etiket özniteliği yok `IDistributedCache` .</span><span class="sxs-lookup"><span data-stu-id="b3bab-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3bab-131">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b3bab-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
