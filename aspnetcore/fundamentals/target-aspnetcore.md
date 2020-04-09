---
title: Sınıf kitaplığında ASP.NET Çekirdek API'lerini kullanma
author: scottaddie
description: Sınıf kitaplığında ASP.NET Core API'leri nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977203"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Sınıf kitaplığında ASP.NET Çekirdek API'lerini kullanma

Yazar: [Scott Addie](https://github.com/scottaddie)

Bu belge, bir sınıf kitaplığında ASP.NET Çekirdek API'leri kullanmak için kılavuz sağlar. Diğer tüm kitaplık kılavuzu için [Açık kaynak kitaplık kılavuzuna](/dotnet/standard/library-guidance/)bakın.

## <a name="determine-which-aspnet-core-versions-to-support"></a>Hangi ASP.NET Core sürümlerini destekleyeceğini belirleme

ASP.NET Core [.NET Core destek politikasına](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)bağlıdır. Kitaplıkta hangi ASP.NET Core sürümlerini destekleyeceğini belirlerken destek ilkesine başvurun. Bir kitaplık şunları yapmalı:

* *Uzun Vadeli Destek* (LTS) olarak sınıflandırılan tüm ASP.NET Core sürümlerini desteklemek için çaba gösterin.
* *Yaşam Sonu* (EOL) olarak sınıflandırılan ASP.NET Core sürümlerini desteklemek zorunda hissetmiyorum.

ASP.NET Core'un önizleme sürümleri kullanıma sunulduğundan, kesme değişiklikleri [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub deposunda yayınlanır. Çerçeve özellikleri geliştirildikçe kitaplıkların uyumluluk testleri yapılabilir.

## <a name="use-the-aspnet-core-shared-framework"></a>ASP.NET Core paylaşılan çerçevesini kullanma

.NET Core 3.0 sürümüyle, birçok ASP.NET Core derlemesi artık NuGet'de paket olarak yayınlanmaz. Bunun yerine, derlemeler .NET `Microsoft.AspNetCore.App` Core SDK ve runtime yükleyiciler ile yüklenen paylaşılan çerçeveye dahil edilir. Artık yayınlanmamaktadır paketlerin listesi için [bkz.](xref:migration/22-to-30#remove-obsolete-package-references)

.NET Core 3.0 itibariyle, `Microsoft.NET.Sdk.Web` MSBuild SDK'yı kullanan projeler paylaşılan çerçeveye dolaylı olarak başvurur. `Microsoft.NET.Sdk` Veya `Microsoft.NET.Sdk.Razor` SDK kullanan projeler, paylaşılan çerçevede ASP.NET Core API'lerini kullanmak için ASP.NET Core'a başvurmalıdır.

Core ASP.NET başvurmak için `<FrameworkReference>` proje dosyanıza aşağıdaki öğeyi ekleyin:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Bu şekilde ASP.NET Core'a başvurmak yalnızca .NET Core 3.x'i hedefleyen projeler için desteklenir.

## <a name="include-blazor-extensibility"></a>Blazor genişletilebilirlik dahil

Blazor WebAssembly (WASM) ve Sunucu [barındırma modellerini](xref:blazor/hosting-models)destekler. Belirli bir neden olmadığı sürece, bir [Razor bileşenleri](xref:blazor/components) kitaplığı her iki barındırma modellerini desteklemelidir. Bir Razor bileşenleri kitaplığı [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk)kullanmanız gerekir.

### <a name="support-both-hosting-models"></a>Her iki barındırma modelini de destekleyin

[Hem Blazor Server](xref:blazor/hosting-models#blazor-server) hem de [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projelerinden Razor bileşen tüketimini desteklemek için düzenleyiciniz için aşağıdaki yönergeleri kullanın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor **Class Kitaplığı** proje şablonunu kullanın. Şablonun **Destek sayfaları ve görünümleri** onay kutusu seçili olmalıdır.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Tümleşik terminalde aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Razor **Class Kitaplığı** proje şablonunu kullanın.

---

Şablondan oluşturulan proje aşağıdaki leri yapar:

* Hedefler .NET Standart 2.0.
* Özelliği `RazorLangVersion` ' `3.0`ye ayarlar. `3.0`.NET Core 3.x için varsayılan değerdir.
* Aşağıdaki paket referansları ekler:
  * [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Örneğin:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Belirli bir barındırma modelini destekleme

Tek bir Blazor barındırma modelini desteklemek çok daha az yaygındır. Örnek olarak, [yalnızca Blazor Server](xref:blazor/hosting-models#blazor-server) projelerinden Razor bileşen tüketimini desteklemek için:

* Hedef .NET Çekirdek 3.x.
* Paylaşılan `<FrameworkReference>` çerçeve için bir öğe ekleyin.

Örneğin:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Razor bileşenleri içeren kitaplıklar hakkında daha fazla bilgi için [core razor bileşenleri sınıf kitaplıkları ASP.NET](xref:blazor/class-libraries)bakın.

## <a name="include-mvc-extensibility"></a>MVC genişletilebilirlik dahil

Bu bölümde kitaplıklar için öneriler sıralanıyor:

* [Jilet görünümleri veya Jilet Sayfaları](#razor-views-or-razor-pages)
* [Etiket Yardımcıları](#tag-helpers)
* [Görünüm bileşenleri](#view-components)

Bu bölümde, MVC'nin birden çok sürümü desteklemek için çoklu hedefleme tartışılmıyor. Birden çok ASP.NET Core sürümlerini destekleme kılavuzu için, [birden çok ASP.NET Çekirdek sürümlerini destekle' ye](#support-multiple-aspnet-core-versions)bakın.

### <a name="razor-views-or-razor-pages"></a>Jilet görünümleri veya Jilet Sayfaları

[Razor görünümleri](xref:mvc/views/overview) veya [Razor Pages](xref:razor-pages/index) içeren bir proje [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk)kullanmanız gerekir.

Proje .NET Core 3.x'i hedefliyorsa, şunları gerektirir:

* Bir `AddRazorSupportForMvc` MSBuild özelliği `true`.
* Paylaşılan `<FrameworkReference>` çerçeve için bir öğe.

**Razor Class Library** proje şablonu .NET Core 3.x'i hedefleyen projeler için önceki gereksinimleri karşılar. Düzenleyiciniz için aşağıdaki yönergeleri kullanın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor **Class Kitaplığı** proje şablonunu kullanın. Şablonun **Destek sayfaları ve görünümleri** onay kutusu seçilmelidir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Tümleşik terminalde aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Şu anda proje şablonu desteği yok.

---

Örneğin:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Proje bunun yerine .NET Standard'ı hedefliyorsa, [bir Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) paket başvurusu gereklidir. Paket, `Microsoft.AspNetCore.Mvc` Core 3.0 ASP.NET paylaşılan çerçeveye taşındı ve bu nedenle artık yayınlanmadı. Örneğin:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Etiket Yardımcıları

[Tag Helpers](xref:mvc/views/tag-helpers/intro) içeren bir `Microsoft.NET.Sdk` proje SDK kullanmalıdır. .NET Core 3.x hedefleme, `<FrameworkReference>` paylaşılan çerçeve için bir öğe ekleyin. Örneğin:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

.NET Standard hedefleme (ASP.NET Core 3.x'ten önceki sürümleri desteklemek için), [Microsoft.AspNetCore.Mvc.Razor'a](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)bir paket başvurusu ekleyin. Paket `Microsoft.AspNetCore.Mvc.Razor` paylaşılan çerçeveye taşındı ve bu nedenle artık yayınlanmaz. Örneğin:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Görünüm bileşenleri

[Görünüm bileşenlerini](xref:mvc/views/view-components) içeren bir `Microsoft.NET.Sdk` proje SDK'yı kullanmalıdır. .NET Core 3.x hedefleme, `<FrameworkReference>` paylaşılan çerçeve için bir öğe ekleyin. Örneğin:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

.NET Standard hedefleme (ASP.NET Core 3.x'ten önceki sürümleri desteklemek için), [Microsoft.AspNetCore.Mvc.ViewFeatures'e](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures)bir paket başvurusu ekleyin. Paket `Microsoft.AspNetCore.Mvc.ViewFeatures` paylaşılan çerçeveye taşındı ve bu nedenle artık yayınlanmaz. Örneğin:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Birden çok ASP.NET Core sürümlerini destekleyin

ASP.NET Core'un birden çok türevlerini destekleyen bir kitaplığı yazarsa, çoklu hedefleme gereklidir. Etiket Yardımcıları kitaplığı aşağıdaki ASP.NET Çekirdek türevlerini desteklemesi gereken bir senaryo düşünün:

* ASP.NET Core 2.1 hedefleme .NET Framework 4.6.1
* ASP.NET Core 2.x hedefleme .NET Core 2.x
* ASP.NET Core 3.x hedefleme .NET Core 3.x

Aşağıdaki proje dosyası `TargetFrameworks` özellik üzerinden bu türevleri destekler:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Önceki proje dosyası ile:

* Paket `Markdig` tüm tüketiciler için eklenmiştir.
* .NET Framework 4.6.1 veya sonraki veya .NET Core 2.x'i hedefleyen tüketiciler için [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) adresine bir başvuru eklenir. Paketin Sürüm 2.1.0 geriye dönük uyumluluk nedeniyle ASP.NET Core 2.2 ile çalışır.
* Paylaşılan çerçeve,.NET Core 3.x'i hedefleyen tüketiciler için başvurulmaktadır. Paket `Microsoft.AspNetCore.Mvc.Razor` paylaşılan çerçeveye dahildir.

Alternatif olarak, .NET Standart 2.0 hem .NET Core 2.1 ve .NET Framework 4.6.1 hedefleme yerine hedeflenebilir:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

Önceki proje dosyasında aşağıdaki uyarılar vardır:

* Kitaplık yalnızca Tag Helpers içerdiğinden, ASP.NET Core'un çalıştığı belirli platformları hedeflemek daha kolaydır: .NET Core ve .NET Framework. Tag Yardımcıları Birlik, UWP ve Xamarin gibi diğer .NET Standart 2.0 uyumlu hedef çerçeveler tarafından kullanılamaz.
* .NET Framework'den .NET Standard 2.0'ın kullanılmasında .NET Framework 4.7.2'de ele alınan bazı sorunlar vardır. .NET Framework 4.6.1 ile 4.7.1'i kullanarak tüketicilerin deneyimini geliştirebilirsiniz.

Kitaplığınız platforma özgü API'leri çağırması gerekiyorsa, .NET Standardı yerine belirli .NET uygulamalarını hedefle. Daha fazla bilgi için çoklu [hedefleme](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)ye bakın.

## <a name="use-an-api-that-hasnt-changed"></a>Değişmemiş bir API kullanma

Bir ara yazılım kitaplığını .NET Core 2.2'den 3.0'a yükselttiğinizi bir senaryo düşünün. Kitaplıkta kullanılan ASP.NET Core ara yazılım API'leri ASP.NET Core 2.2 ile 3.0 arasında değişmedi. .NET Core 3.0'daki ara yazılım kitaplığını desteklemeye devam etmek için aşağıdaki adımları izleyin:

* Standart [kitaplık kılavuzunu](/dotnet/standard/library-guidance/)izleyin.
* İlgili derleme paylaşılan çerçevede yoksa, her API'nın NuGet paketi için bir paket başvurusu ekleyin.

## <a name="use-an-api-that-changed"></a>Değiştirilen bir API kullanma

Bir kitaplığı .NET Core 2.2'den .NET Core 3.0'a yükselttiğinizi bir senaryo düşünün. Kitaplıkta kullanılan ASP.NET Core API'nın ASP.NET Core 3.0'da [bir kırılma değişikliği](/dotnet/core/compatibility/breaking-changes) vardır. Kitaplığın bozuk API'yi tüm sürümlerde kullanmamak için yeniden yazıp yazamayacağını düşünün.

Kitaplığı yeniden yazabilirseniz, bunu yapın ve paket referansları içeren daha önceki bir hedef çerçeveyi (örneğin, .NET Standart 2.0 veya .NET Framework 4.6.1) hedeflemeye devam edin.

Kitaplığı yeniden yazamıyorsanız, aşağıdaki adımları izleyin:

* .NET Core 3.0 için bir hedef ekleyin.
* Paylaşılan `<FrameworkReference>` çerçeve için bir öğe ekleyin.
* Kodu koşullu olarak derlemek için uygun hedef çerçeve sembolüne sahip [#if önişlemci yönergesini](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) kullanın.

Örneğin, http istek ve yanıt akışları üzerinde senkron okuma ve yazma core 3.0 ASP.NET varsayılan olarak devre dışı bırakılır. ASP.NET Core 2.2 varsayılan olarak senkron davranışı destekler. G/Ç'nin gerçekleştiği yerde eşzamanlı okuma ve yazmanın etkinleştirilmesi gereken bir ara yazılım kitaplığı düşünün. Kitaplık, senkron özellikleri uygun önişlemci yönergesinde etkinleştirmek için kodu içine almalıdır. Örneğin:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>3.0'da tanıtılan bir API kullanma

Core 3.0'ASP.NET tanıtılan ASP.NET Core API kullanmak istediğinizi düşünün. Aşağıdaki soruları göz önünde bulundurun:

1. Kitaplık işlevsel olarak yeni API gerektirir mi?
1. Kitaplık bu özelliği farklı bir şekilde uygulayabilir mi?

Kitaplık işlevsel olarak API gerektiriyorsa ve bunu alt düzey uygulamanın bir yolu yoksa:

* Hedef .NET Core 3.x yalnızca.
* Paylaşılan `<FrameworkReference>` çerçeve için bir öğe ekleyin.

Kitaplık özelliği farklı bir şekilde uygulayabiliyorsa:

* Hedef çerçeve olarak .NET Core 3.x ekleyin.
* Paylaşılan `<FrameworkReference>` çerçeve için bir öğe ekleyin.
* Kodu koşullu olarak derlemek için uygun hedef çerçeve sembolüne sahip [#if önişlemci yönergesini](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) kullanın.

Örneğin, aşağıdaki Tag Helper, <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> Core 3.0ASP.NET tanıtılan arabirimi kullanır. .NET Core 3.0'ı hedefleyen `NETCOREAPP3_0` tüketiciler, hedef çerçeve simgesi tarafından tanımlanan kod yolunu yürütür. Etiket Yardımcısı'nın oluşturucu parametre türü <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> .NET Core 2.1 ve .NET Framework 4.6.1 tüketicileri için değişir. ASP.NET Core 3.0 eski olarak `IHostingEnvironment` işaretlenmiş ve yedek `IWebHostEnvironment` olarak önerilen çünkü bu değişiklik gerekliydi.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

Aşağıdaki çok hedefli proje dosyası bu Tag Helper senaryosunu destekler:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Paylaşılan çerçeveden kaldırılan bir API kullanma

Paylaşılan çerçeveden kaldırılan bir ASP.NET Çekirdek derlemesi kullanmak için uygun paket başvuruclarını ekleyin. ASP.NET Core 3.0'da paylaşılan çerçeveden kaldırılan [paketlerin](xref:migration/22-to-30#remove-obsolete-package-references)listesi için bkz.

Örneğin, web API istemcisi eklemek için:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [.NET uygulama desteği](/dotnet/standard/net-standard#net-implementation-support)
* [.NET destek politikaları](https://dotnet.microsoft.com/platform/support/policy)
