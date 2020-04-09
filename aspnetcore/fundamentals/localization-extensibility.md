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
# <a name="localization-extensibility"></a>Yerelleştirme Genişletilebilirlik

Yazar: [Hisham Bin Ateya](https://github.com/hishamco)

Bu makalede:

* Yerelleştirme API'lerinde genişletilebilirlik noktalarını listeler.
* Core uygulaması yerelleştirmeASP.NETnin nasıl genişletilenhakkında yönergeler sağlar.

## <a name="extensible-points-in-localization-apis"></a>Yerelleştirme API'lerinde Genişletilebilir Noktalar

ASP.NET Çekirdek yerelleştirme API'leri genişletilebilir olacak şekilde üretilmiştir. Genişletilebilirlik, geliştiricilerin yerelleştirmeyi gereksinimlerine göre özelleştirmelerine olanak tanır. Örneğin, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) bir `POStringLocalizer`. `POStringLocalizer`yerelleştirme kaynaklarını depolamak için `PO` dosyaları kullanmak için [Taşınabilir Nesne yerelleştirme](xref:fundamentals/portable-object-localization) kullanarak ayrıntılı olarak açıklar.

Bu makalede, yerelleştirme API'leri sağladığı iki ana genişletilebilirlik noktaları listelenir: 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>Yerelleştirme Kültür Sağlayıcıları

ASP.NET Çekirdek yerelleştirme API'lerinin yürütme isteğinin geçerli kültürünü belirleyebilen dört varsayılan sağlayıcısı var:

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

Önceki sağlayıcılar [Yerelleştirme Middleware](xref:fundamentals/localization) belgelerinde ayrıntılı olarak açıklanmıştır. Varsayılan sağlayıcılar gereksinimlerinizi karşılamazsa, aşağıdaki yaklaşımlardan birini kullanarak özel bir sağlayıcı oluşturun:

### <a name="use-customrequestcultureprovider"></a>CustomRequestCultureProvider kullanın

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>geçerli yerelleştirme kültürünü belirlemek için basit bir temsilci kullanan bir özel <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> sağlar:

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

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>RequestCultureProvider'ın yeni bir implemetation'ını kullanma

Özel bir <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> kaynaktan istek kültürü bilgilerini belirleyen yeni bir uygulama oluşturulabilir. Örneğin, özel kaynak bir yapılandırma dosyası veya veritabanı olabilir.

Aşağıdaki örnek, `AppSettingsRequestCultureProvider` <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> *appsettings.json*gelen istek kültürü bilgilerini belirlemek için genişletir gösterir:

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

## <a name="localization-resources"></a>Yerelleştirme kaynakları

ASP.NET Çekirdek yerelleştirme sağlar. <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>yerelleştirme kaynaklarını <xref:Microsoft.Extensions.Localization.IStringLocalizer> depolamak `resx` için kullanılan bir uygulamadır.

Dosyaları kullanmakla `resx` sınırlı değilsiniz. Uygulayarak, `IStringLocalized`herhangi bir veri kaynağı kullanılabilir.

Aşağıdaki örnek projeler <xref:Microsoft.Extensions.Localization.IStringLocalizer>uygulamak: 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
