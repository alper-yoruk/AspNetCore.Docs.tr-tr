---
title: ASP.NET Core’a Giriş
author: rick-anderson
description: Modern, bulut özellikli ve Internet 'e bağlı uygulamalar oluşturmaya yönelik platformlar arası, yüksek performanslı, açık kaynaklı bir çatı olan ASP.NET Core tanıtım edin.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: index
ms.openlocfilehash: f1235337748bacdb6eaa775a5853238bf729e605
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634559"
---
# <a name="introduction-to-aspnet-core"></a>ASP.NET Core’a Giriş

[Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Shaun Luttin](https://twitter.com/dicshaunary) tarafından hazırlanmıştır

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core modern, bulut özellikli, Internet 'e bağlı uygulamalar oluşturmaya yönelik platformlar arası, yüksek performanslı, [Açık kaynaklı](https://github.com/dotnet/aspnetcore) bir çerçevedir. ASP.NET Core ile şunları yapabilirsiniz:

* Web uygulamaları ve Hizmetleri, [nesnelerin interneti (IoT)](https://www.microsoft.com/internet-of-things/) uygulamaları ve mobil arka uçlar oluşturun.
* Windows, macOS ve Linux üzerinde tercih ettiğiniz geliştirme araçlarını kullanın.
* Buluta veya şirket içine dağıtın.
* [.NET Core](/dotnet/core/introduction)üzerinde çalıştırın.

## <a name="why-choose-aspnet-core"></a>Neden ASP.NET Core seçmelisiniz?

Milyonlarca geliştirici, Web uygulamaları oluşturmak için [ASP.NET 4. x](/aspnet/overview) kullanır veya kullandı. ASP.NET Core, daha modüler bir Framework ile sonuçlanan mimari değişiklikler dahil olmak üzere ASP.NET 4. x ' in bir yeniden tasarlayıcısı.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>ASP.NET Core MVC kullanarak web API'leri ve web kullanıcı arabirimi oluşturma

ASP.NET Core MVC, [web API’leri](xref:tutorials/first-web-api) ve [web uygulamaları](xref:tutorials/razor-pages/index) oluşturmaya yönelik özellikler sağlar:

* [Model-View-Controller (MVC) modeli](xref:mvc/overview) , Web API 'lerinizi ve Web uygulamalarınızı test etmenize yardımcı olur.
* [ Razor Sayfalar](xref:razor-pages/index) , Web Kullanıcı arabirimi oluşturmayı daha kolay ve daha üretken hale getiren sayfa tabanlı bir programlama modelidir.
* [ Razor biçimlendirme](xref:mvc/views/razor) , [ Razor Sayfalar](xref:razor-pages/index) ve [MVC görünümleri](xref:mvc/views/overview)için üretken bir sözdizimi sağlar.
* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .
* [Birden çok veri biçimi ve içerik anlaşması](xref:web-api/advanced/formatting) için sunulan yerleşik destek, web API'lerinizin tarayıcılar ve mobil cihazlar dahil olmak üzere birçok çeşit istemciye ulaşmasına imkan tanır.
* [Model bağlama](xref:mvc/models/model-binding), HTTP isteklerinden alınan verileri otomatik olarak eylem metodu parametreleriyle eşleştirir.
* [Model doğrulama](xref:mvc/models/validation), otomatik olarak istemci ve sunucu tarafı doğrulama gerçekleştirir.

## <a name="client-side-development"></a>İstemci tarafı geliştirme

ASP.NET Core [Blazor](xref:blazor/index) , [angular](xref:spa/angular), [tepki](xref:spa/react)verme ve [önyükleme](https://getbootstrap.com/)gibi popüler istemci tarafı çerçeveleri ve kitaplıkları ile sorunsuz bir şekilde tümleşir. Daha fazla bilgi için bkz <xref:blazor/index> . *istemci tarafı geliştirme*altındaki ilgili konular.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>ASP.NET Core hedef çerçeveler

ASP.NET Core 3. x ve üzeri yalnızca .NET Core ' u hedefleyebilir. Genellikle, ASP.NET Core [.NET Standard](/dotnet/standard/net-standard) kitaplıklarından oluşur. .NET Standard 2,0 ile yazılan kitaplıklar .NET Standard 2,0 ' i uygulayan herhangi bir [.NET platformunda](/dotnet/standard/net-standard#net-implementation-support)çalışır.

.NET Core hedeflemesinin çeşitli avantajları vardır ve bu avantajlar her yeni sürümle birlikte artmaktadır. .NET Framework'e göre .NET Core'un bazı avantajları şunlardır:

* Platformlar arası. Windows, macOS ve Linux üzerinde çalışır.
* Geliştirilmiş performans
* [Yan yana sürüm oluşturma](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Yeni API'ler
* Açık kaynak

## <a name="recommended-learning-path"></a>Önerilen öğrenme yolu

ASP.NET Core uygulamalar geliştirmeye giriş için aşağıdaki öğretici dizisini öneririz:

1. Geliştirmek veya sürdürmek istediğiniz uygulama türü için bir öğreticiyi izleyin.

   |Uygulama türü  |Senaryo  |Öğretici  |
   |----------|----------|----------|
   |Web uygulaması                   | Yeni sunucu tarafı Web UI geliştirmesi |[Sayfalarla çalışmaya başlama Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Web uygulaması                   | MVC uygulamasını sürdürme |[MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc)|
   |Web uygulaması                   | İstemci tarafı Web UI geliştirmesi |[Kullanmaya başlayın Blazor](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |Web API                   | Yeniden takip eden HTTP Hizmetleri |[Web API 'SI oluşturma](xref:tutorials/first-web-api)&dagger; |
   |Uzak yordam çağrısı uygulaması | Sözleşme-protokol arabellekleri kullanan ilk hizmetler |[gRPC hizmetini kullanmaya başlama](xref:tutorials/grpc/grpc-start) |
   |Gerçek zamanlı uygulama             | Sunucular ve bağlı istemciler arasında çift yönlü iletişim |[Kullanmaya başlayın SignalR](xref:tutorials/signalr) |

1. Temel veri erişiminin nasıl yapılacağını gösteren bir öğreticiyi izleyin.

   |Senaryo  |Öğretici  |
   |----------|----------|
   |Yeni geliştirme        |[Razor Entity Framework Core olan sayfalar](xref:data/ef-rp/intro) |
   |MVC uygulamasını sürdürme |[Entity Framework Core ile MVC](xref:data/ef-mvc/intro) |

1. Tüm uygulama türleri için uygulanan ASP.NET Core [temelleri](xref:fundamentals/index) hakkında genel bakış makalesini okuyun.

1. İlgilendiğiniz diğer konular için içindekiler tablosuna gözatamazsınız.

&dagger;Ayrıca [etkileşimli bir Web API öğreticisi](/learn/modules/build-web-api-net-core)de vardır. Geliştirme araçlarının yerel yüklemesi gerekmez. Kod tarayıcınızda bir [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) çalışır ve test etmek için [kıvrımlı](https://curl.haxx.se/) kullanılır.

## <a name="migrate-from-net-framework"></a>.NET Framework geçir

ASP.NET 4. x uygulamalarını ASP.NET Core 'e geçirmeye yönelik bir başvuru kılavuzu için, bkz <xref:migration/proper-to-2x/index> ..

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core modern, bulut özellikli, Internet 'e bağlı uygulamalar oluşturmaya yönelik platformlar arası, yüksek performanslı, [Açık kaynaklı](https://github.com/dotnet/aspnetcore) bir çerçevedir. ASP.NET Core ile şunları yapabilirsiniz:

* Web uygulamaları ve Hizmetleri, [nesnelerin interneti (IoT)](https://www.microsoft.com/internet-of-things/) uygulamaları ve mobil arka uçlar oluşturun.
* Windows, macOS ve Linux üzerinde tercih ettiğiniz geliştirme araçlarını kullanın.
* Buluta veya şirket içine dağıtın.
* [.NET Core veya .NET Framework](/dotnet/articles/standard/choosing-core-framework-server) üzerinde çalıştırın.

## <a name="why-choose-aspnet-core"></a>Neden ASP.NET Core seçmelisiniz?

Milyonlarca geliştirici, Web uygulamaları oluşturmak için [ASP.NET 4. x](/aspnet/overview) kullanır veya kullandı. ASP.NET Core, ASP.NET 4.x sürümünün daha yalın, daha modüler bir çerçeve elde edilmesini sağlayan mimari değişikliklerle yeniden tasarlanmış halidir.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>ASP.NET Core MVC kullanarak web API'leri ve web kullanıcı arabirimi oluşturma

ASP.NET Core MVC, [web API’leri](xref:tutorials/first-web-api) ve [web uygulamaları](xref:tutorials/razor-pages/index) oluşturmaya yönelik özellikler sağlar:

* [Model-View-Controller (MVC) modeli](xref:mvc/overview) , Web API 'lerinizi ve Web uygulamalarınızı test etmenize yardımcı olur.
* [ Razor Sayfalar](xref:razor-pages/index) , Web Kullanıcı arabirimi oluşturmayı daha kolay ve daha üretken hale getiren sayfa tabanlı bir programlama modelidir.
* [ Razor biçimlendirme](xref:mvc/views/razor) , [ Razor Sayfalar](xref:razor-pages/index) ve [MVC görünümleri](xref:mvc/views/overview)için üretken bir sözdizimi sağlar.
* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .
* [Birden çok veri biçimi ve içerik anlaşması](xref:web-api/advanced/formatting) için sunulan yerleşik destek, web API'lerinizin tarayıcılar ve mobil cihazlar dahil olmak üzere birçok çeşit istemciye ulaşmasına imkan tanır.
* [Model bağlama](xref:mvc/models/model-binding), HTTP isteklerinden alınan verileri otomatik olarak eylem metodu parametreleriyle eşleştirir.
* [Model doğrulama](xref:mvc/models/validation), otomatik olarak istemci ve sunucu tarafı doğrulama gerçekleştirir.

## <a name="client-side-development"></a>İstemci tarafı geliştirme

ASP.NET Core [Blazor](xref:blazor/index) , [angular](xref:spa/angular), [tepki](xref:spa/react)verme ve [önyükleme](https://getbootstrap.com/)gibi popüler istemci tarafı çerçeveleri ve kitaplıkları ile sorunsuz bir şekilde tümleşir. Daha fazla bilgi için bkz <xref:blazor/index> . *istemci tarafı geliştirme*altındaki ilgili konular.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>.NET Framework'ü hedefleyen ASP.NET Core

ASP.NET Core 2.x, .NET Core'u veya .NET Framework'ü hedefleyebilir. .NET Framework'ü hedefleyen ASP.NET Core uygulamaları platformlar arası çalışmaz; bunlar yalnızca Windows üzerinde çalışır. Genel olarak, ASP.NET Core 2.x [.NET Standard](/dotnet/standard/net-standard) kitaplıklarından oluşturulmuştur. .NET Standard 2,0 ile yazılan kitaplıklar .NET Standard 2,0 ' i uygulayan herhangi bir [.NET platformunda](/dotnet/standard/net-standard#net-implementation-support)çalışır.

ASP.NET Core 2. x, .NET Standard 2,0 ' i uygulayan .NET Framework sürümlerinde desteklenir:

* En son sürümü .NET Framework önerilir.
* .NET Framework 4.6.1 ve üzeri.

ASP.NET Core 3.0 ve üzeri yalnızca .NET Core’da çalışır. Bu değişiklik hakkında daha fazla bilgi için bkz. [ASP.NET Core 3.0’daki değişikliklere ilk bakış](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

.NET Core hedeflemesinin çeşitli avantajları vardır ve bu avantajlar her yeni sürümle birlikte artmaktadır. .NET Framework'e göre .NET Core'un bazı avantajları şunlardır:

* Platformlar arası. macOS, Linux ve Windows üzerinde çalışır.
* Geliştirilmiş performans
* [Yan yana sürüm oluşturma](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Yeni API'ler
* Açık kaynak

.NET Framework API boşluğunu .NET Core 'a kapatmanıza yardımcı olması için, [Windows Uyumluluk Paketi](/dotnet/core/porting/windows-compat-pack) .NET Core 'da yalnızca binlerce Windows-yalnızca Windows API 'si yaptı. Bu API'ler .NET Core 1.x'te sağlanmamıştı.

## <a name="recommended-learning-path"></a>Önerilen öğrenme yolu

ASP.NET Core uygulamaları geliştirmeye başlamak için şu öğreticileri ve makaleleri takip etmenizi öneririz:

1. Geliştirmek veya sürdürmek istediğiniz uygulama türü için bir öğreticiyi izleyin.

   |Uygulama türü  |Senaryo  |Öğretici  |
   |----------|----------|----------|
   |Web uygulaması                   | Yeni proje geliştirmek için        |[Sayfalarla çalışmaya başlama Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Web uygulaması                   | MVC uygulaması yönetmek için |[MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc)|
   |Web API                   |                            |[Web API 'SI oluşturma](xref:tutorials/first-web-api)&dagger; |
   |Gerçek zamanlı uygulama             |                            |[Kullanmaya başlayın SignalR](xref:tutorials/signalr) |

1. Temel veri erişiminin nasıl yapılacağını gösteren bir öğreticiyi izleyin.

   |Senaryo  |Öğretici  |
   |----------|----------|
   | Yeni proje geliştirmek için        |[Razor Entity Framework Core olan sayfalar](xref:data/ef-rp/intro) |
   | MVC uygulaması yönetmek için |[Entity Framework Core ile MVC](xref:data/ef-mvc/intro) |

1. Tüm uygulama türleri için uygulanan ASP.NET Core [temelleri](xref:fundamentals/index) hakkında genel bakış makalesini okuyun.

1. İlgilendiğiniz diğer konular için İçindekiler Tablosu’na göz atın.

&dagger;Ayrıca, [tamamen tarayıcıda izlemeniz gereken bir Web API öğreticisi](/learn/modules/build-web-api-net-core)de vardır, yerel IDE yüklemesi gerekmez. Kod [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)’de çalışır, [curl](https://curl.haxx.se/) ise test için kullanılır.

## <a name="migrate-from-net-framework"></a>.NET Framework geçir

ASP.NET uygulamalarını ASP.NET Core geçirmeye yönelik bir başvuru kılavuzu için, bkz <xref:migration/proper-to-2x/index> ..

::: moniker-end

## <a name="how-to-download-a-sample"></a>Örnek indirme

Çoğu makale ve öğretici örnek koda bağlantılar içerir.

1. [ASP.NET depo zip dosyasını indirin](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. *Docs-master.zip* dosyasının sıkıştırmasını açın.
1. Örnek dizinde gezinmek için örnek bağlantıdaki URL’yi kullanın.

### <a name="preprocessor-directives-in-sample-code"></a>Örnek kodda ön işlemci yönergeleri

Birden çok senaryoyu göstermek için örnek uygulamalar, `#define` `#if-#else/#elif-#endif` örnek kodun farklı bölümlerini seçmeli olarak derlemek ve çalıştırmak için ve ön işlemci yönergelerini kullanır. Bu yaklaşımı kullanan örnekler için, `#define` C# dosyalarının en üstündeki yönergesini, çalıştırmak istediğiniz senaryoyla ilişkili sembolü tanımlamak için ayarlayın. Bazı örnekler, bir senaryoyu çalıştırmak için birden fazla dosyanın en üstünde sembolün tanımlanmasını gerektirir.

Örneğin, aşağıdaki simge listesi `#define` dört senaryonun kullanılabilir olduğunu gösterir (her simge için bir senaryo). Geçerli örnek yapılandırması `TemplateCode` senaryosunu çalıştırır:

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

Örneği `ExpandDefault` senaryosunu çalıştıracak şekilde değiştirmek için `ExpandDefault` simgesini tanımlayın ve kalan simgeleri açıklama satırı yapılmış şekilde bırakın:

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

Kod bölümlerini seçmeli olarak derlemek üzere [C# ön işlemci yönergelerini](/dotnet/csharp/language-reference/preprocessor-directives/) kullanma hakkında daha fazla bilgi için bkz. [#define (C# Başvurusu)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) ve [#if (C# Başvurusu)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).

### <a name="regions-in-sample-code"></a>Örnek kodda bölgeler

Bazı örnek uygulamalar, [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) ve [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# yönergelerinin içine alınmış kod bölümlerini içerir. Belge derleme sistemi bu bölgeleri işlenmiş belge konularının içine ekler.  

Bölge adları çoğunlukla şu sözcüğü içerir: "snippet." Aşağıdaki örnekte `snippet_WebHostDefaults` adlı bir bölge gösterilir:

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

Önündeki C# kod parçacığına, konunun markdown dosyasında aşağıdaki satırla başvurulur:

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

`#region`Kodu çevreleyen ve yönergeleri güvenli bir şekilde yoksayabilir (veya kaldırabilirsiniz) `#endregion` . Konusunda açıklanan örnek senaryoları çalıştırmayı planlıyorsanız, bu yönergelerin içindeki kodu değiştirmeyin. Başka senaryolarla denemeler yaparken kodu rahatça değiştirebilirsiniz.

Daha fazla bilgi için bkz. [ASP.NET belgelerine katkıda bulunma: Kod parçacıkları](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [ASP.NET Core temelleri](xref:fundamentals/index)
* [Haftalık ASP.NET topluluğu toplantısında](https://live.asp.net/), takımın ilerleme durumu ve planları ele alınır. Yeni bloglara ve üçüncü taraf yazılıma yer verilir.
