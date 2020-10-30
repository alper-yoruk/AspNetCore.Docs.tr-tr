---
title: ASP.NET Core yerelleştirme sorunlarını giderme
author: hishamco
description: ASP.NET Core uygulamalarında Yerelleştirmede sorunları tanılamayı öğrenin.
ms.author: riande
ms.date: 01/24/2019
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
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 995db4c8c9d0c0f1f77b1fd3665e707975406a7f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053624"
---
# <a name="troubleshoot-aspnet-core-localization"></a>ASP.NET Core yerelleştirme sorunlarını giderme

, [Hisham bin Ateya](https://github.com/hishamco) tarafından

Bu makalede ASP.NET Core uygulaması yerelleştirme sorunlarını tanılamaya yönelik yönergeler sağlanmaktadır.

## <a name="localization-configuration-issues"></a>Yerelleştirme yapılandırma sorunları

**Yerelleştirme ara yazılım sırası**  
Yerelleştirme ara yazılımı beklenen şekilde sıralandığı için uygulama yerelleştirilemeyebilir.

Bu sorunu çözmek için, yerelleştirme ara yazılımı 'nın MVC ara yazılım öncesinde kayıtlı olduğundan emin olun. Aksi takdirde, yerelleştirme ara yazılımı uygulanmaz.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Yerelleştirme kaynakları yolu bulunamadı**

**RequestCultureProvider içindeki desteklenen kültürler, kayıtlı bir kez ile eşleşmiyor**  

## <a name="resource-file-naming-issues"></a>Kaynak dosyası adlandırma sorunları

ASP.NET Core, [burada](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)ayrıntılı olarak açıklanan yerelleştirme kaynakları dosya adlandırmasına yönelik önceden tanımlanmış kurallara ve yönergelere sahiptir.

## <a name="missing-resources"></a>Eksik kaynaklar

Kaynakların Bulunamamasının yaygın nedenleri şunlardır:

- Kaynak adları, `resx` dosyada veya yorumdur isteğinde yanlış yazılmıştır.
- Kaynak `resx` bazı diller için öğesinde yok, ancak başkaları içinde var.
- Sorun yaşamaya devam ediyorsanız, `Debug` eksik kaynaklar hakkında daha fazla ayrıntı için yerelleştirme günlüğü iletilerini (günlük düzeyinde) kontrol edin.

_**İpucu:** Kullanırken `CookieRequestCultureProvider` , tek tekliflerin yerelleştirme değeri içindeki kültürler ile kullanılmadığını doğrulayın cookie . Örneğin, `c='en-UK'|uic='en-US'` geçersiz cookie bir değerdir, ancak `c=en-UK|uic=en-US` geçerli olur._

## <a name="resources--class-libraries-issues"></a>Sınıf kitaplığı sorunlarını & kaynaklar

Varsayılan olarak ASP.NET Core, sınıf kitaplıklarının kaynak dosyalarını [Resourcelocationattribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1)aracılığıyla bulmasını sağlamak için bir yol sağlar.

Sınıf kitaplıklarıyla ilgili yaygın sorunlar şunlardır:
- `ResourceLocationAttribute`Bir sınıf kitaplığında eksik olan, `ResourceManagerStringLocalizerFactory` kaynakları keşfetmesini engelleyecek.
- Kaynak dosyası adlandırma. Daha fazla bilgi için bkz. [kaynak dosyası adlandırma sorunları](#resource-file-naming-issues) bölümü.
- Sınıf kitaplığının kök ad alanı değiştiriliyor. Daha fazla bilgi için bkz. [kök ad alanı sorunları](#root-namespace-issues) bölümü.

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider beklendiği gibi çalışmıyor

`RequestLocalizationOptions`Sınıfı üç varsayılan sağlayıcıya sahiptir:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) , yerelleştirme kültürünün uygulamanızda nasıl sağlandığını özelleştirmenize olanak sağlar. , `CustomRequestCultureProvider` Varsayılan sağlayıcılar gereksinimlerinizi karşılamadığında kullanılır.

- Yaygın bir nedenden dolayı özel sağlayıcı, listedeki ilk sağlayıcı değildir `RequestCultureProviders` . Bu sorunu çözmek için:

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

- `AddInitialRequestCultureProvider`Özel sağlayıcıyı ilk sağlayıcı olarak ayarlamak için genişletme yöntemini kullanın.

## <a name="root-namespace-issues"></a>Kök ad alanı sorunları

Bir derlemenin kök ad alanı derleme adından farklıysa, yerelleştirme varsayılan olarak çalışmaz. Bu sorundan kaçınmak için, [burada](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) ayrıntılı olarak açıklanan [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)kullanın

> [!WARNING]
> Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir. Örneğin, `my-project-name.csproj` `my_project_name` Bu hata için kök ad alanını ve derleme adını kullanır `my-project-name` . 

## <a name="resources--build-action"></a>Kaynak & oluşturma eylemi

Yerelleştirme için kaynak dosyaları kullanıyorsanız, uygun bir yapı eylemi olması önemlidir. Bunlara **gömülü kaynak** olmaları gerekir, aksi takdirde `ResourceStringLocalizer` Bu kaynakları bulamaz.
