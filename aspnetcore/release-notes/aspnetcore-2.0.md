---
title: ASP.NET Core 2,0 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 2,0 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-2.0
ms.openlocfilehash: b1fc486633ab7c7d7bee4f8981a978747b2890da
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775823"
---
# <a name="whats-new-in-aspnet-core-20"></a>ASP.NET Core 2,0 ' deki yenilikler

Bu makalede, ASP.NET Core 2,0 ' deki en önemli değişiklikler, ilgili belgelerin bağlantılarıyla vurgulanır.

## <a name="razor-pages"></a>RazorSayfaları

RazorSayfalar, kod odaklı senaryoları daha kolay ve daha üretken hale getiren ASP.NET Core MVC 'nin yeni bir özelliğidir.

Daha fazla bilgi için bkz. giriş ve öğretici:

* [Razor Sayfalara giriş](xref:razor-pages/index)
* [Razor Sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>ASP.NET Core metapackage

Yeni bir ASP.NET Core metapackage, ASP.NET Core ve Entity Framework Core ekipleri tarafından oluşturulan ve desteklenen tüm paketleri dahili ve üçüncü taraf bağımlılıklarıyla birlikte içerir. Artık pakete göre bireysel ASP.NET Core özelliklerini seçmeniz gerekmez. Tüm Özellikler [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) paketine dahildir. Varsayılan Şablonlar bu paketi kullanır.

Daha fazla bilgi için bkz. [Microsoft. AspNetCore. All metapackage for ASP.NET Core 2,0](xref:fundamentals/metapackage).

## <a name="runtime-store"></a>Çalışma zamanı deposu

`Microsoft.AspNetCore.All` Metapackage kullanan uygulamalar, yeni .NET Core çalışma zamanı deposundan otomatik olarak yararlanır. Mağaza ASP.NET Core 2,0 uygulamalarını çalıştırmak için gereken tüm çalışma zamanı varlıklarını içerir. `Microsoft.AspNetCore.All` Metapackage kullandığınızda başvurulan ASP.NET Core NuGet paketlerinden hiçbir varlık hedef sistemde zaten bulunduğundan uygulamayla birlikte dağıtılır. Çalışma zamanı deposundaki varlıklar Ayrıca uygulama başlatma süresini geliştirmek için önceden derlenmiş.

Daha fazla bilgi için bkz. [çalışma zamanı deposu](/dotnet/core/deploying/runtime-store)

## <a name="net-standard-20"></a>.NET Standard 2,0

ASP.NET Core 2,0 paketleri .NET Standard 2,0 ' dir. Paketlere diğer .NET Standard 2,0 kitaplıkları tarafından başvurulabilir ve .NET Core 2,0 ve .NET Framework 4.6.1 dahil olmak üzere .NET Standard 2,0 uyumlu .NET uygulamalarında çalıştırılabilir. 

`Microsoft.AspNetCore.All` Metapackage, .net Core 2,0 çalışma zamanı deposuyla kullanılması amaçlanan için yalnızca .net Core 2,0 ' i hedefler.

## <a name="configuration-update"></a>Yapılandırma güncelleştirmesi

ASP.NET Core `IConfiguration` 2,0 ' de varsayılan olarak hizmetler kapsayıcısına bir örnek eklenir. `IConfiguration`Hizmetler kapsayıcısında, uygulamaların kapsayıcıdan yapılandırma değerlerini almasına daha kolay hale gelir.

Planlı belgelerin durumu hakkında daha fazla bilgi için bkz. [GitHub sorunu](https://github.com/dotnet/AspNetCore.Docs/issues/3387).

## <a name="logging-update"></a>Günlük güncelleştirme

ASP.NET Core 2,0 ' de günlüğe kaydetme, varsayılan olarak bağımlılık ekleme (dı) sistemine dahil edilir. Sağlayıcılar ekler ve *Startup.cs* dosyası yerine *program.cs* dosyasında filtrelemeyi yapılandırırsınız. Ve varsayılan `ILoggerFactory` , çapraz sağlayıcı filtreleme ve belirli sağlayıcı filtreleme için tek bir esnek yaklaşım kullanmanıza imkan tanıyan şekilde filtrelemeyi destekler.

Daha fazla bilgi için bkz. [günlüğe giriş](xref:fundamentals/logging/index).

## <a name="authentication-update"></a>Kimlik doğrulama güncelleştirmesi

Yeni bir kimlik doğrulama modeli, DI kullanarak bir uygulama için kimlik doğrulamasını yapılandırmayı kolaylaştırır.

[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)kullanarak Web uygulamaları ve Web API 'lerinin kimlik doğrulamasını yapılandırmak için yeni şablonlar kullanılabilir.

Planlı belgelerin durumu hakkında daha fazla bilgi için bkz. [GitHub sorunu](https://github.com/dotnet/AspNetCore.Docs/issues/3054).

## <a name="identity-update"></a>IdentityUpdate

ASP.NET Core 2,0 ' de kullanarak Identity güvenli Web API 'leri oluşturmayı daha kolay hale geçirdik. [Microsoft kimlik doğrulama kitaplığı 'nı (msal)](https://www.nuget.org/packages/Microsoft.Identity.Client)kullanarak Web API 'lerinize erişmek için erişim belirteçleri edinebilirsiniz.

2,0 sürümündeki kimlik doğrulama değişiklikleriyle ilgili daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [ASP.NET Core hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)
* [ASP.NET Core 'de Authenticator uygulamaları için QR kodu oluşturmayı etkinleştirme](xref:security/authentication/identity-enable-qrcodes)
* [Kimlik doğrulamasını ASP.NET Core Identity ve 2,0 'e geçirin](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>SPA şablonları

Angular, Aurelia, altını gizleme. js, tepki. js ve Redux ile tepki. js için tek sayfalı uygulama (SPA) proje şablonları mevcuttur. Angular şablonu angular 4 ' e güncelleştirildi. Angular ve tepki verme şablonları varsayılan olarak kullanılabilir; diğer şablonları alma hakkında daha fazla bilgi için bkz. [Yeni BIR Spa projesi oluşturma](xref:client-side/spa-services#create-a-new-project). ASP.NET Core 'de SPA oluşturma hakkında daha fazla bilgi için bkz <xref:client-side/spa-services>..

## <a name="kestrel-improvements"></a>Kestrel geliştirmeleri

Kestrel Web sunucusu, Internet 'e yönelik bir sunucu olarak daha uygun hale getirmek için yeni özelliklere sahiptir. `KestrelServerOptions` Sınıfın yeni `Limits` özelliğine bir dizi sunucu kısıtlaması yapılandırma seçeneği eklenir. Aşağıdakiler için sınır ekleyin:

* İstemci bağlantıları üst sınırı
* En büyük istek gövdesi boyutu
* En az istek gövdesi veri hızı

Daha fazla bilgi için bkz. [Kestrel Web Server Implementation For ASP.NET Core](xref:fundamentals/servers/kestrel).

## <a name="weblistener-renamed-to-httpsys"></a>WebListener, HTTP. sys olarak yeniden adlandırıldı

Paketler `Microsoft.AspNetCore.Server.WebListener` ve `Microsoft.Net.Http.Server` yeni bir pakette `Microsoft.AspNetCore.Server.HttpSys`birleştirildi. Ad alanları eşleşecek şekilde güncelleştirildi.

Daha fazla bilgi için [ASP.NET Core 'de http. sys Web sunucusu uygulamasına](xref:fundamentals/servers/httpsys)bakın.

## <a name="enhanced-http-header-support"></a>Gelişmiş HTTP üst bilgisi desteği

Bir `FileStreamResult` veya göndermek için MVC kullanırken `FileContentResult`, artık gönderdiğiniz içerik için bir `ETag` veya bir `LastModified` tarih ayarlama seçeneğiniz vardır. Bu değerleri, döndürülen içerikte aşağıdakine benzer kodla ayarlayabilirsiniz:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

Ziyaretçilere döndürülen dosya `ETag` ve `LastModified` değerleri için uygun http başlıklarına sahiptir.

Bir uygulama ziyaretçisi bir Aralık Isteği üstbilgisiyle içerik isterse, ASP.NET Core isteği tanır ve üstbilgiyi işler. İstenen içerik kısmen teslim edilebiliyorsa, ASP.NET Core uygun şekilde atlar ve yalnızca istenen bayt kümesini döndürür. Bu özelliği uyarlamak veya işlemek için yöntemlerinize özel işleyiciler yazmanız gerekmez; sizin için otomatik olarak işlenir.

## <a name="hosting-startup-and-application-insights"></a>Barındırma başlatma ve Application Insights

Barındırma ortamları artık uygulamanın başlatılması sırasında ek paket bağımlılıklarını ekleyebilir ve uygulama başlatma sırasında kodu yürütebilir. Bu özellik, belirli ortamların, uygulamanın daha önce haberdar olması gerekmeden bu ortama özgü olan "hafif" özelliklere olanak tanımak için kullanılabilir. 

ASP.NET Core 2,0 ' de, bu özellik, Visual Studio 'da hata ayıklarken Application Insights tanılamayı otomatik olarak etkinleştirmek için kullanılır ve Azure Uygulama Hizmetleri 'nde çalışırken (uygulamasına geçtikten sonra). Sonuç olarak, proje şablonları artık varsayılan olarak Application Insights paketleri ve kodu eklemez.

Planlı belgelerin durumu hakkında daha fazla bilgi için bkz. [GitHub sorunu](https://github.com/dotnet/AspNetCore.Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Anti-forgery belirteçlerinin otomatik kullanımı

ASP.NET Core, varsayılan olarak her zaman HTML kodsuz içeriğe yardımcı olur, ancak yeni sürüme, siteler arası istek sahteciliği (XSRF) saldırılarını önlemeye yardımcı olmak için ek bir adım alınmıştır. ASP.NET Core artık varsayılan olarak korsanlığa karşı koruma belirteçleri yayıp bunları, ek yapılandırma olmadan bu eylemleri ve sayfaları form SONRASı olarak doğrular.

Daha fazla bilgi için bkz. [siteler arası Istek forgery (XSRF/CSRF) saldırılarını önleme](xref:security/anti-request-forgery).

## <a name="automatic-precompilation"></a>Otomatik ön derleme

RazorYayımlama ön derleme sırasında varsayılan olarak, yayımlama çıkış boyutunu ve uygulama başlatma süresini azaltarak etkin olarak görüntüle ' yi görüntüleyin.

Daha fazla bilgi için bkz [ Razor . asp.NET Core derleme ve ön derlemeyi görüntüleme](xref:mvc/views/view-compilation).

## <a name="razor-support-for-c-71"></a>RazorC# 7,1 için destek

Razor Görünüm altyapısı, yeni Roslyn derleyicisi ile çalışacak şekilde güncelleştirilmiştir. Bu, varsayılan Ifadeler gibi C# 7,1 özellikleri, gösterilen tanımlama grubu adları ve genel türler ile birlikte kalıp eşleme desteği içerir. Projenizde C# 7,1 kullanmak için, proje dosyanıza aşağıdaki özelliği ekleyin ve çözümü yeniden yükleyin:

```xml
<LangVersion>latest</LangVersion>
```

C# 7,1 özelliklerinin durumu hakkında daha fazla bilgi için bkz. [Roslyn GitHub deposu](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).

## <a name="other-documentation-updates-for-20"></a>2,0 için diğer belge güncelleştirmeleri

* [ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles)
* [Anahtar yönetimi](xref:security/data-protection/implementation/key-management)
* [Facebook kimlik doğrulamasını yapılandırma](xref:security/authentication/facebook-logins)
* [Twitter kimlik doğrulamasını yapılandırma](xref:security/authentication/twitter-logins)
* [Google kimlik doğrulamasını yapılandırma](xref:security/authentication/google-logins)
* [Microsoft hesabı kimlik doğrulamasını yapılandırma](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Geçiş kılavuzu

ASP.NET Core 1. x uygulamalarının ASP.NET Core 2,0 ' e nasıl geçirileceğiyle ilgili yönergeler için aşağıdaki kaynaklara bakın:

* [ASP.NET Core 1. x öğesinden ASP.NET Core 2,0 'e geçirin](xref:migration/1x-to-2x/index)
* [Kimlik doğrulamasını ASP.NET Core Identity ve 2,0 'e geçirin](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Ek Bilgi

Değişikliklerin tamamı listesi için [ASP.NET Core 2,0 sürüm notlarına](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0)bakın.

ASP.NET Core geliştirme ekibinin ilerleme ve planlarıyla bağlantı kurmak için, [ASP.net Community](https://live.asp.net/)' ye ayarlayın.
