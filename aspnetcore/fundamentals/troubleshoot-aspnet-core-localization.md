---
title: Sorun Giderme ASP.NET Çekirdek Yerelleştirme
author: hishamco
description: ASP.NET Core uygulamalarında yerelleştirmeyle ilgili sorunları nasıl tanılayarak tanılamayı öğrenin.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660379"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Sorun Giderme ASP.NET Çekirdek Yerelleştirme

Yazar: [Hisham Bin Ateya](https://github.com/hishamco)

Bu makalede, ASP.NET Core uygulaması yerelleştirme sorunları nın nasıl tanılanacak hakkında yönergeler verilmektedir.

## <a name="localization-configuration-issues"></a>Yerelleştirme yapılandırma sorunları

**Yerelleştirme ara yazılım sırası**  
Yerelleştirme ara ware beklendiği gibi sipariş değil, çünkü uygulama yerelleştiremeyebilir.

Bu sorunu gidermek için, yerelleştirme ara ware MVC ara önce kayıtlı olduğundan emin olun. Aksi takdirde, yerelleştirme ara ware uygulanmaz.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Yerelleştirme kaynakları yolu bulunamadı**

**RequestCultureProvider'da Desteklenen Kültürler Bir kez kayıtlı ile eşleşmiyor**  

## <a name="resource-file-naming-issues"></a>Kaynak dosyası adlandırma sorunları

ASP.NET Core [burada](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)ayrıntılı olarak açıklanan yerelleştirme kaynakları dosya adlandırma için önceden tanımlanmış kurallar ve yönergeler vardır.

## <a name="missing-resources"></a>Eksik kaynaklar

Kaynakların bulunamaz yaygın nedenleri şunlardır:

- `resx` Kaynak adları dosyada veya yerelleştirici istekte yanlış yazılır.
- Kaynak bazı diller `resx` için eksik, ancak diğerlerinde var.
- Sorun olmaya devam ediyorsanız, eksik kaynaklar hakkında daha fazla `Debug` bilgi için yerelleştirme günlük iletilerini (günlük düzeyinde) denetleyin.

_**İpucu:** Kullanırken, `CookieRequestCultureProvider`tek tırnak yerelleştirme çerez değeri içinde kültürleri ile kullanılmaz doğrulayın. Örneğin, `c='en-UK'|uic='en-US'` geçersiz bir çerez değeri, `c=en-UK|uic=en-US` geçerli bir ise._

## <a name="resources--class-libraries-issues"></a>Sınıflar & Kitaplıklar sorunları

ASP.NET Core varsayılan olarak sınıf kitaplıklarının [Kaynak KonumAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1)üzerinden kaynak dosyalarını bulmasına izin vermek için bir yol sağlar.

Sınıf kitaplıklarıyla ilgili sık karşılaşılan sorunlar şunlardır:
- Sınıf `ResourceLocationAttribute` kitaplığında eksik `ResourceManagerStringLocalizerFactory` olan kaynak bulunmasını engeller.
- Kaynak dosyası adlandırma. Daha fazla bilgi için [Kaynak dosyası adlandırma sorunları](#resource-file-naming-issues) bölümüne bakın.
- Sınıf kitaplığınkök ad alanını değiştirme. Daha fazla bilgi için [Kök Ad Alanı sorunları](#root-namespace-issues) bölümüne bakın.

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider beklendiği gibi çalışmıyor

Sınıfın `RequestLocalizationOptions` üç varsayılan sağlayıcısı vardır:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider,](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) uygulamanızda yerelleştirme kültürünün nasıl sağlandığını özelleştirmenize olanak tanır. Varsayılan `CustomRequestCultureProvider` sağlayıcılar gereksinimlerinizi karşılamadığında kullanılır.

- Özel sağlayıcının düzgün çalışmamasının yaygın bir nedeni, `RequestCultureProviders` listedeki ilk sağlayıcı olmamasıdır. Bu sorunu çözmek için:

- Özel sağlayıcıyı listedeki 0 konumuna `RequestCultureProviders` aşağıdaki gibi ekleyin:

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- Özel `AddInitialRequestCultureProvider` sağlayıcıyı başlangıç sağlayıcısı olarak ayarlamak için uzantı yöntemini kullanın.

## <a name="root-namespace-issues"></a>Kök Ad Alanı sorunları

Bir derlemenin kök ad alanı derleme adından farklı olduğunda, yerelleştirme varsayılan olarak çalışmaz. Bu sorunu önlemek için [burada](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) ayrıntılı olarak açıklanan [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)kullanın

> [!WARNING]
> Bu, bir projenin adı geçerli bir .NET tanımlayıcısı olmadığında oluşabilir. Örneğin, `my-project-name.csproj` kök ad alanını `my_project_name` ve bu `my-project-name` hataya yol açan derleme adını kullanır. 

## <a name="resources--build-action"></a>Kaynak & Oluşturma Eylemi

Yerelleştirme için kaynak dosyaları kullanıyorsanız, uygun bir yapı eylemi olması önemlidir. Bunlar **Gömülü Kaynak**olmalıdır, `ResourceStringLocalizer` aksi takdirde bu kaynakları bulmak mümkün değildir.
