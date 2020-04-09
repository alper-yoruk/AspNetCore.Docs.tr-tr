---
title: Yerelleştirme Genişletilebilirlik
author: hishamco
description: ASP.NET Core uygulamalarında yerelleştirme API'lerini nasıl genişleteceklerini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
uid: fundamentals/localization-extensibility
ms.openlocfilehash: dfa2efe78b2e1e118e6b3f09bfc41f3330e1d721
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662101"
---
# <a name="localization-extensibility"></a><span data-ttu-id="11a34-103">Yerelleştirme Genişletilebilirlik</span><span class="sxs-lookup"><span data-stu-id="11a34-103">Localization Extensibility</span></span>

<span data-ttu-id="11a34-104">Yazar: [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="11a34-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="11a34-105">Bu makalede:</span><span class="sxs-lookup"><span data-stu-id="11a34-105">This article:</span></span>

* <span data-ttu-id="11a34-106">Yerelleştirme API'lerinde genişletilebilirlik noktalarını listeler.</span><span class="sxs-lookup"><span data-stu-id="11a34-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="11a34-107">Core uygulaması yerelleştirmeASP.NETnin nasıl genişletilenhakkında yönergeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="11a34-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="11a34-108">Yerelleştirme API'lerinde Genişletilebilir Noktalar</span><span class="sxs-lookup"><span data-stu-id="11a34-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="11a34-109">ASP.NET Çekirdek yerelleştirme API'leri genişletilebilir olacak şekilde üretilmiştir.</span><span class="sxs-lookup"><span data-stu-id="11a34-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="11a34-110">Genişletilebilirlik, geliştiricilerin yerelleştirmeyi gereksinimlerine göre özelleştirmelerine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="11a34-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="11a34-111">Örneğin, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) bir `POStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="11a34-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="11a34-112">`POStringLocalizer`yerelleştirme kaynaklarını depolamak için `PO` dosyaları kullanmak için [Taşınabilir Nesne yerelleştirme](xref:fundamentals/portable-object-localization) kullanarak ayrıntılı olarak açıklar.</span><span class="sxs-lookup"><span data-stu-id="11a34-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="11a34-113">Bu makalede, yerelleştirme API'leri sağladığı iki ana genişletilebilirlik noktaları listelenir:</span><span class="sxs-lookup"><span data-stu-id="11a34-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="11a34-114">Yerelleştirme Kültür Sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="11a34-114">Localization Culture Providers</span></span>

<span data-ttu-id="11a34-115">ASP.NET Çekirdek yerelleştirme API'lerinin yürütme isteğinin geçerli kültürünü belirleyebilen dört varsayılan sağlayıcısı var:</span><span class="sxs-lookup"><span data-stu-id="11a34-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="11a34-116">Önceki sağlayıcılar [Yerelleştirme Middleware](xref:fundamentals/localization) belgelerinde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="11a34-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="11a34-117">Varsayılan sağlayıcılar gereksinimlerinizi karşılamazsa, aşağıdaki yaklaşımlardan birini kullanarak özel bir sağlayıcı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="11a34-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="11a34-118">CustomRequestCultureProvider kullanın</span><span class="sxs-lookup"><span data-stu-id="11a34-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="11a34-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>geçerli yerelleştirme kültürünü belirlemek için basit bir temsilci kullanan bir özel <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> sağlar:</span><span class="sxs-lookup"><span data-stu-id="11a34-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="11a34-120">RequestCultureProvider'ın yeni bir implemetation'ını kullanma</span><span class="sxs-lookup"><span data-stu-id="11a34-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="11a34-121">Özel bir <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> kaynaktan istek kültürü bilgilerini belirleyen yeni bir uygulama oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="11a34-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="11a34-122">Örneğin, özel kaynak bir yapılandırma dosyası veya veritabanı olabilir.</span><span class="sxs-lookup"><span data-stu-id="11a34-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="11a34-123">Aşağıdaki örnek, `AppSettingsRequestCultureProvider` <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> *appsettings.json*gelen istek kültürü bilgilerini belirlemek için genişletir gösterir:</span><span class="sxs-lookup"><span data-stu-id="11a34-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a><span data-ttu-id="11a34-124">Yerelleştirme kaynakları</span><span class="sxs-lookup"><span data-stu-id="11a34-124">Localization resources</span></span>

<span data-ttu-id="11a34-125">ASP.NET Çekirdek yerelleştirme sağlar. <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer></span><span class="sxs-lookup"><span data-stu-id="11a34-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="11a34-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>yerelleştirme kaynaklarını <xref:Microsoft.Extensions.Localization.IStringLocalizer> depolamak `resx` için kullanılan bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="11a34-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="11a34-127">Dosyaları kullanmakla `resx` sınırlı değilsiniz.</span><span class="sxs-lookup"><span data-stu-id="11a34-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="11a34-128">Uygulayarak, `IStringLocalized`herhangi bir veri kaynağı kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="11a34-128">By implementing `IStringLocalized`, any data source can be used.</span></span>

<span data-ttu-id="11a34-129">Aşağıdaki örnek projeler <xref:Microsoft.Extensions.Localization.IStringLocalizer>uygulamak:</span><span class="sxs-lookup"><span data-stu-id="11a34-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="11a34-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="11a34-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="11a34-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="11a34-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="11a34-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="11a34-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)
