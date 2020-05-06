---
title: ASP.NET Core yerelleştirme sorunlarını giderme
author: hishamco
description: ASP.NET Core uygulamalarında Yerelleştirmede sorunları tanılamayı öğrenin.
ms.author: riande
ms.date: 01/24/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: f5c2be93be4f896b1822bf93deef24f091e30442
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774294"
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
- Kaynak bazı diller `resx` için öğesinde yok, ancak başkaları içinde var.
- Sorun yaşamaya devam ediyorsanız, eksik kaynaklar hakkında daha fazla ayrıntı için yerelleştirme günlüğü iletilerini ( `Debug` günlük düzeyinde) kontrol edin.

_**İpucu:** Kullanırken `CookieRequestCultureProvider`, tek tırnak, yerelleştirme tanımlama bilgisi değeri içindeki kültürler ile kullanılmaz. Örneğin, `c='en-UK'|uic='en-US'` geçersiz bir tanımlama bilgisi değeridir, ancak `c=en-UK|uic=en-US` geçerli olur._

## <a name="resources--class-libraries-issues"></a>Sınıf kitaplığı sorunlarını & kaynaklar

Varsayılan olarak ASP.NET Core, sınıf kitaplıklarının kaynak dosyalarını [Resourcelocationattribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1)aracılığıyla bulmasını sağlamak için bir yol sağlar.

Sınıf kitaplıklarıyla ilgili yaygın sorunlar şunlardır:
- Bir sınıf `ResourceLocationAttribute` kitaplığında eksik olan, kaynakları keşfetmesini engelleyecek `ResourceManagerStringLocalizerFactory` .
- Kaynak dosyası adlandırma. Daha fazla bilgi için bkz. [kaynak dosyası adlandırma sorunları](#resource-file-naming-issues) bölümü.
- Sınıf kitaplığının kök ad alanı değiştiriliyor. Daha fazla bilgi için bkz. [kök ad alanı sorunları](#root-namespace-issues) bölümü.

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider beklendiği gibi çalışmıyor

`RequestLocalizationOptions` Sınıfı üç varsayılan sağlayıcıya sahiptir:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) , yerelleştirme kültürünün uygulamanızda nasıl sağlandığını özelleştirmenize olanak sağlar. , `CustomRequestCultureProvider` Varsayılan sağlayıcılar gereksinimlerinizi karşılamadığında kullanılır.

- Yaygın bir nedenden dolayı özel sağlayıcı, `RequestCultureProviders` listedeki ilk sağlayıcı değildir. Bu sorunu çözmek için:

- Özel sağlayıcıyı `RequestCultureProviders` listedeki 0 konumuna aşağıdaki gibi ekleyin:

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

- Özel `AddInitialRequestCultureProvider` sağlayıcıyı ilk sağlayıcı olarak ayarlamak için genişletme yöntemini kullanın.

## <a name="root-namespace-issues"></a>Kök ad alanı sorunları

Bir derlemenin kök ad alanı derleme adından farklıysa, yerelleştirme varsayılan olarak çalışmaz. Bu sorundan kaçınmak için, [burada](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) ayrıntılı olarak açıklanan [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)kullanın

> [!WARNING]
> Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir. `my-project-name.csproj` Örneğin, bu hata için kök ad `my_project_name` alanını ve derleme adını `my-project-name` kullanır. 

## <a name="resources--build-action"></a>Kaynak & oluşturma eylemi

Yerelleştirme için kaynak dosyaları kullanıyorsanız, uygun bir yapı eylemi olması önemlidir. Bunlara **gömülü kaynak**olmaları gerekir, aksi takdirde `ResourceStringLocalizer` bu kaynakları bulamaz.
