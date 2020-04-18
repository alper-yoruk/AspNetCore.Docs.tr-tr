---
title: ASP.NET Core’a Giriş
author: rick-anderson
description: Modern, bulut özellikli, Internet'e bağlı uygulamalar oluşturmak için çapraz platform, yüksek performanslı, açık kaynak kodlu bir çerçeve olan ASP.NET Core'a giriş yapın.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: c5a5a0ada996d88cb9252da25b5580fe0cf46f0b
ms.sourcegitcommit: 636efd1afc0a1e6fd4b12ae3a542917b356abb93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81615944"
---
# <a name="introduction-to-aspnet-core"></a>ASP.NET Core’a Giriş

[Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Shaun Luttin](https://twitter.com/dicshaunary) tarafından hazırlanmıştır

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core, modern, bulut özellikli, Internet'e bağlı uygulamalar oluşturmak için bir çapraz platform, yüksek performanslı, [açık kaynak kodlu](https://github.com/dotnet/aspnetcore) bir çerçevedir. ASP.NET Core ile şunları yapabilirsiniz:

* Web uygulamaları ve hizmetleri, [Nesnelerin İnterneti (IoT)](https://www.microsoft.com/internet-of-things/) uygulamaları ve mobil arka uçlar oluşturun.
* Windows, macOS ve Linux üzerinde tercih ettiğiniz geliştirme araçlarını kullanın.
* Buluta veya şirket içine dağıtın.
* [.NET Core'da](/dotnet/core/introduction)çalıştırın.

## <a name="why-choose-aspnet-core"></a>Neden ASP.NET Core'u seçmelisiniz?

Milyonlarca geliştirici web uygulamaları oluşturmak için [4.x ASP.NET](/aspnet/overview) kullanır veya kullanır. ASP.NET Core, daha yalın, daha modüler bir çerçeveyle sonuçlanan mimari değişiklikler de dahil olmak üzere 4.x ASP.NET yeniden tasarlanmasıdır.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>ASP.NET Core MVC kullanarak web API'leri ve web kullanıcı arabirimi oluşturma

ASP.NET Core MVC, [web API’leri](xref:tutorials/first-web-api) ve [web uygulamaları](xref:tutorials/razor-pages/index) oluşturmaya yönelik özellikler sağlar:

* [Model Görünümü Denetleyicisi (MVC) deseni,](xref:mvc/overview) web API'lerinizi ve web uygulamalarınızı test edilebilir hale getirmeye yardımcı olur.
* [Razor Pages](xref:razor-pages/index), web kullanıcı arabirimi oluşturmayı daha kolay ve üretken bir hale getiren, sayfa tabanlı bir programlama modelidir.
* [Razor işaretlemesi](xref:mvc/views/razor), [Razor Sayfaları](xref:razor-pages/index) ve [MVC görünümleri](xref:mvc/views/overview) için üretken bir söz dizimi sağlar.
* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.
* [Birden çok veri biçimi ve içerik anlaşması](xref:web-api/advanced/formatting) için sunulan yerleşik destek, web API'lerinizin tarayıcılar ve mobil cihazlar dahil olmak üzere birçok çeşit istemciye ulaşmasına imkan tanır.
* [Model bağlama](xref:mvc/models/model-binding), HTTP isteklerinden alınan verileri otomatik olarak eylem metodu parametreleriyle eşleştirir.
* [Model doğrulama](xref:mvc/models/validation), otomatik olarak istemci ve sunucu tarafı doğrulama gerçekleştirir.

## <a name="client-side-development"></a>İstemci tarafı geliştirme

ASP.NET Core [blazor](xref:blazor/index)dahil popüler istemci tarafı çerçeveleri ve kütüphaneleri ile sorunsuz entegre, [Açısal](xref:spa/angular), [React](xref:spa/react), ve [Bootstrap](https://getbootstrap.com/). Daha fazla bilgi <xref:blazor/index> için, bkz ve *Müşteri tarafı geliştirme*altında ilgili konular.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>ASP.NET Temel hedef çerçeveleri

ASP.NET Core 3.x ve daha sonra sadece .NET Core hedefleyebilirsiniz. Genellikle ASP.NET Core [,NET Standart](/dotnet/standard/net-standard) kitaplıklarından oluşur. .NET Standard 2.0 ile yazılmış [kitaplıklar .NET Standard 2.0'ı uygulayan](/dotnet/standard/net-standard#net-implementation-support)herhangi bir .NET platformunda çalışır.

.NET Core hedeflemesinin çeşitli avantajları vardır ve bu avantajlar her yeni sürümle birlikte artmaktadır. .NET Framework'e göre .NET Core'un bazı avantajları şunlardır:

* Platformlar arası. Windows, macOS ve Linux'ta çalışır.
* Geliştirilmiş performans
* [Yan yana sürüm oluşturma](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* Yeni API'ler
* Açık kaynak

## <a name="recommended-learning-path"></a>Önerilen öğrenme yolu

ASP.NET Core uygulamaları geliştirmeye giriş için aşağıdaki öğreticidizisini öneririz:

1. Geliştirmek veya sürdürmek istediğiniz uygulama türü için bir öğretici izleyin.

   |Uygulama türü  |Senaryo  |Öğretici  |
   |----------|----------|----------|
   |Web uygulaması                   | Yeni sunucu tarafı web ui geliştirme |[Razor Sayfaları kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start) |
   |Web uygulaması                   | Bir MVC uygulamasını koruma |[MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc)|
   |Web uygulaması                   | İstemci tarafı web ui geliştirme |[Blazor ile başlayın](xref:tutorials/first-blazor-app) |
   |Web API                   | RESTful HTTP hizmetleri |[Web API'si oluşturma](xref:tutorials/first-web-api)&dagger; |
   |Uzaktan Yordam Çağrı uygulaması | Protokol Arabelleklerini kullanarak sözleşme ilk hizmetleri |[gRPC hizmetini kullanmaya başlama](xref:tutorials/grpc/grpc-start) |
   |Gerçek zamanlı uygulama             | Sunucular ve bağlı istemciler arasında çift yönlü iletişim |[SignalR ile çalışmaya başlama](xref:tutorials/signalr) |

1. Temel veri erişiminin nasıl yapılacağını gösteren bir öğretici izleyin.

   |Senaryo  |Öğretici  |
   |----------|----------|
   |Yeni gelişme        |[Entity Framework Core ile Razor Pages](xref:data/ef-rp/intro) |
   |Bir MVC uygulamasını koruma |[Entity Framework Core ile MVC](xref:data/ef-mvc/intro) |

1. Tüm uygulama türleri için geçerli olan ASP.NET Temel [temelleri](xref:fundamentals/index) hakkında genel bir bakış okuyun.

1. İlgi nizi çeken diğer konular için içerik tablosuna göz atın.

&dagger;Ayrıca interaktif bir [web API öğretici](/learn/modules/build-web-api-net-core)var. Geliştirme araçlarının yerel yüklemesi gerekmez. Kod tarayıcınızda bir [Azure Bulut Kabuğunda](https://azure.microsoft.com/features/cloud-shell/) çalışır ve [curl](https://curl.haxx.se/) test etmek için kullanılır.

## <a name="migrate-from-net-framework"></a>.NET Framework'den geçirin

Core'a ASP.NET 4.x uygulamaları ASP.NET için bir <xref:migration/proper-to-2x/index>başvuru kılavuzu için bkz.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core, modern, bulut özellikli, Internet'e bağlı uygulamalar oluşturmak için bir çapraz platform, yüksek performanslı, [açık kaynak kodlu](https://github.com/dotnet/aspnetcore) bir çerçevedir. ASP.NET Core ile şunları yapabilirsiniz:

* Web uygulamaları ve hizmetleri, [Nesnelerin İnterneti (IoT)](https://www.microsoft.com/internet-of-things/) uygulamaları ve mobil arka uçlar oluşturun.
* Windows, macOS ve Linux üzerinde tercih ettiğiniz geliştirme araçlarını kullanın.
* Buluta veya şirket içine dağıtın.
* [.NET Core veya .NET Framework](/dotnet/articles/standard/choosing-core-framework-server) üzerinde çalıştırın.

## <a name="why-choose-aspnet-core"></a>Neden ASP.NET Core'u seçmelisiniz?

Milyonlarca geliştirici web uygulamaları oluşturmak için [4.x ASP.NET](/aspnet/overview) kullanır veya kullanır. ASP.NET Core, ASP.NET 4.x sürümünün daha yalın, daha modüler bir çerçeve elde edilmesini sağlayan mimari değişikliklerle yeniden tasarlanmış halidir.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>ASP.NET Core MVC kullanarak web API'leri ve web kullanıcı arabirimi oluşturma

ASP.NET Core MVC, [web API’leri](xref:tutorials/first-web-api) ve [web uygulamaları](xref:tutorials/razor-pages/index) oluşturmaya yönelik özellikler sağlar:

* [Model Görünümü Denetleyicisi (MVC) deseni,](xref:mvc/overview) web API'lerinizi ve web uygulamalarınızı test edilebilir hale getirmeye yardımcı olur.
* [Razor Pages](xref:razor-pages/index), web kullanıcı arabirimi oluşturmayı daha kolay ve üretken bir hale getiren, sayfa tabanlı bir programlama modelidir.
* [Razor işaretlemesi](xref:mvc/views/razor), [Razor Sayfaları](xref:razor-pages/index) ve [MVC görünümleri](xref:mvc/views/overview) için üretken bir söz dizimi sağlar.
* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.
* [Birden çok veri biçimi ve içerik anlaşması](xref:web-api/advanced/formatting) için sunulan yerleşik destek, web API'lerinizin tarayıcılar ve mobil cihazlar dahil olmak üzere birçok çeşit istemciye ulaşmasına imkan tanır.
* [Model bağlama](xref:mvc/models/model-binding), HTTP isteklerinden alınan verileri otomatik olarak eylem metodu parametreleriyle eşleştirir.
* [Model doğrulama](xref:mvc/models/validation), otomatik olarak istemci ve sunucu tarafı doğrulama gerçekleştirir.

## <a name="client-side-development"></a>İstemci tarafı geliştirme

ASP.NET Core [blazor](xref:blazor/index)dahil popüler istemci tarafı çerçeveleri ve kütüphaneleri ile sorunsuz entegre, [Açısal](xref:spa/angular), [React](xref:spa/react), ve [Bootstrap](https://getbootstrap.com/). Daha fazla bilgi <xref:blazor/index> için, bkz ve *Müşteri tarafı geliştirme*altında ilgili konular.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>.NET Framework'ü hedefleyen ASP.NET Core

ASP.NET Core 2.x, .NET Core'u veya .NET Framework'ü hedefleyebilir. .NET Framework'ü hedefleyen ASP.NET Core uygulamaları platformlar arası çalışmaz; bunlar yalnızca Windows üzerinde çalışır. Genel olarak, ASP.NET Core 2.x [.NET Standard](/dotnet/standard/net-standard) kitaplıklarından oluşturulmuştur. .NET Standard 2.0 ile yazılmış [kitaplıklar .NET Standard 2.0'ı uygulayan](/dotnet/standard/net-standard#net-implementation-support)herhangi bir .NET platformunda çalışır.

ASP.NET Core 2.x .NET Standard 2.0 uygulayan .NET Framework sürümlerinde desteklenir:

* .NET Framework en son sürümü önerilir.
* .NET Framework 4.6.1 ve üzeri.

ASP.NET Core 3.0 ve üzeri yalnızca .NET Core’da çalışır. Bu değişiklik hakkında daha fazla bilgi için bkz. [ASP.NET Core 3.0’daki değişikliklere ilk bakış](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

.NET Core hedeflemesinin çeşitli avantajları vardır ve bu avantajlar her yeni sürümle birlikte artmaktadır. .NET Framework'e göre .NET Core'un bazı avantajları şunlardır:

* Platformlar arası. macOS, Linux ve Windows üzerinde çalışır.
* Geliştirilmiş performans
* [Yan yana sürüm oluşturma](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* Yeni API'ler
* Açık kaynak

.NET Framework ile .NET Core arasındaki API açığını kapatmak için çok çalışıyoruz. [Windows Uyumluluk Paketi](/dotnet/core/porting/windows-compat-pack), .NET Core'da yalnızca Windows'a yönelik binlerce API'yi kullanıma sunmaktadır. Bu API'ler .NET Core 1.x'te sağlanmamıştı.

## <a name="recommended-learning-path"></a>Önerilen öğrenme yolu

ASP.NET Core uygulamaları geliştirmeye başlamak için şu öğreticileri ve makaleleri takip etmenizi öneririz:

1. Geliştirmek veya sürdürmek istediğiniz uygulama türü için bir öğretici izleyin.

   |Uygulama türü  |Senaryo  |Öğretici  |
   |----------|----------|----------|
   |Web uygulaması                   | Yeni proje geliştirmek için        |[Razor Sayfaları kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start) |
   |Web uygulaması                   | MVC uygulaması yönetmek için |[MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc)|
   |Web API                   |                            |[Web API'si oluşturma](xref:tutorials/first-web-api)&dagger; |
   |Gerçek zamanlı uygulama             |                            |[SignalR ile çalışmaya başlama](xref:tutorials/signalr) |

1. Temel veri erişiminin nasıl yapılacağını gösteren bir öğretici izleyin.

   |Senaryo  |Öğretici  |
   |----------|----------|
   | Yeni proje geliştirmek için        |[Entity Framework Core ile Razor Pages](xref:data/ef-rp/intro) |
   | MVC uygulaması yönetmek için |[Entity Framework Core ile MVC](xref:data/ef-mvc/intro) |

1. Tüm uygulama türleri için geçerli olan ASP.NET Temel [temelleri](xref:fundamentals/index) hakkında genel bir bakış okuyun.

1. İlgilendiğiniz diğer konular için İçindekiler Tablosu’na göz atın.

&dagger;Ayrıca [tarayıcıda tamamen takip bir web API öğretici](/learn/modules/build-web-api-net-core)var, hiçbir yerel IDE yükleme gerekli. Kod [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)’de çalışır, [curl](https://curl.haxx.se/) ise test için kullanılır.

## <a name="migrate-from-net-framework"></a>.NET Framework'den geçirin

ASP.NET Core'a ASP.NET uygulamaları nın geçişini gösteren bir başvuru kılavuzu için bkz. <xref:migration/proper-to-2x/index>

::: moniker-end

## <a name="how-to-download-a-sample"></a>Örnek indirme

Çoğu makale ve öğretici örnek koda bağlantılar içerir.

1. [ASP.NET depo zip dosyasını indirin](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. *Docs-master.zip* dosyasının sıkıştırmasını açın.
1. Örnek dizinde gezinmek için örnek bağlantıdaki URL’yi kullanın.

### <a name="preprocessor-directives-in-sample-code"></a>Örnek kodda ön işlemci yönergeleri

Örnek uygulamalar, birden çok senaryo `#define` `#if-#else/#elif-#endif` göstermek için, örnek kodun farklı bölümlerini seçip derlemek ve çalıştırmak için ve önişlemci yönergelerini kullanır. Bu yaklaşımı kullanan örnekler için, çalıştırmak `#define` istediğiniz senaryoyla ilişkili simgeyi tanımlamak için yönergeyi C# dosyalarının en üstünde ayarlayın. Bazı örnekler, bir senaryoyu çalıştırmak için birden çok dosyanın üstündeki sembolün tanımlanmasını gerektirir.

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

Bazı örnek [uygulamalar, #region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) ve [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# yönergeleriyle çevrili kod bölümlerini içerir. Belge derleme sistemi bu bölgeleri işlenmiş belge konularının içine ekler.  

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

Kodu çevreleyen yönergeleri `#region` ve yönergeleri güvenle `#endregion` yok sayabilir (veya kaldırabilirsiniz). Konuyla açıklanan örnek senaryoları çalıştırmayı planlıyorsanız, bu yönergeler içindeki kodu değiştirmeyin. Başka senaryolarla denemeler yaparken kodu rahatça değiştirebilirsiniz.

Daha fazla bilgi için bkz. [ASP.NET belgelerine katkıda bulunma: Kod parçacıkları](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [ASP.NET Core temelleri](xref:fundamentals/index)
* [Haftalık ASP.NET topluluğu toplantısında](https://live.asp.net/), takımın ilerleme durumu ve planları ele alınır. Yeni bloglara ve üçüncü taraf yazılıma yer verilir.
