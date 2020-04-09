---
title: ASP.NET Core 2.0'daki yenilikler
author: rick-anderson
description: ASP.NET Core 2.0'daki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: aspnetcore-2.0
ms.openlocfilehash: 5ca43bab1496aa9fda65282cbb0b1177ad8689eb
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667260"
---
# <a name="whats-new-in-aspnet-core-20"></a>ASP.NET Core 2.0'daki yenilikler

Bu makalede, ilgili belgelere bağlantılar ile ASP.NET Core 2.0 en önemli değişiklikleri vurgulamaktadır.

## <a name="razor-pages"></a>Razor Pages

Razor Pages, ASP.NET Core MVC'nin sayfa odaklı senaryoları kodlamayı daha kolay ve üretken hale getiren yeni bir özelliğidir.

Daha fazla bilgi için giriş ve öğreticiye bakın:

* [Razor Pages’e giriş](xref:razor-pages/index)
* [Razor Sayfaları kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>ASP.NET Çekirdek metapaketi

Yeni bir ASP.NET Core meta paketi, ASP.NET Çekirdek ve Entity Framework Core ekipleri tarafından yapılan ve desteklenen tüm paketlerive iç ve üçüncü taraf bağımlılıklarını içerir. Artık pakete göre tek tek ASP.NET Core özelliklerini seçmeniz gerekmez. Tüm özellikler [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) paketinde yer alıyor. Varsayılan şablonlar bu paketi kullanır.

Daha fazla bilgi için, [Core 2.0 ASP.NET için Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)bakın.

## <a name="runtime-store"></a>Runtime Mağazası

`Microsoft.AspNetCore.All` Metapaketi kullanan uygulamalar otomatik olarak yeni .NET Core Runtime Store'dan yararlanır. Mağaza, Core 2.0 uygulamalarını çalıştırmak için gereken tüm çalışma zamanı varlıklarını içerir ASP.NET. `Microsoft.AspNetCore.All` Meta paketi kullandığınızda, başvurulan ASP.NET Core NuGet paketlerinden hiçbir varlık uygulamayla birlikte dağıtılan çünkü zaten hedef sistemde bulunmaz. Runtime Store'daki varlıklar da uygulama başlatma süresini iyileştirmek için önceden derlenir.

Daha fazla bilgi için [Runtime mağazasına](/dotnet/core/deploying/runtime-store) bakın

## <a name="net-standard-20"></a>.NET Standart 2.0

ASP.NET Core 2.0 paketleri hedef .NET Standart 2.0. Paketler diğer .NET Standart 2.0 kitaplıkları tarafından başvurulabilir ve .NET Core 2.0 ve .NET Framework 4.6.1 dahil olmak üzere .NET Standard 2.0 uyumlu uygulamalarda çalıştırılabilir. 

Meta `Microsoft.AspNetCore.All` paket sadece .NET Core 2.0'ı hedefler, çünkü .NET Core 2.0 Runtime Store ile kullanılmak üzere tasarlanmıştır.

## <a name="configuration-update"></a>Yapılandırma güncelleştirmesi

Bir `IConfiguration` örnek, varsayılan olarak ASP.NET Core 2.0'da hizmet kapsayıcısına eklenir. `IConfiguration`hizmet kapsayıcısında, uygulamaların konfigürasyon değerlerini kapsayıcıdan almalarını kolaylaştırır.

Planlanan belgelerin durumu hakkında bilgi için [GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/3387)bakın.

## <a name="logging-update"></a>Günlük güncelleştirmesi

Core 2.0ASP.NET, günlük varsayılan olarak bağımlılık enjeksiyon (DI) sistemine dahil edilmiştir. *Sağlayıcılar* ekler ve Startup.cs dosyası yerine *Program.cs* dosyasında filtreleme yapılandırmak. Ve varsayılan `ILoggerFactory` filtreleme, hem çapraz sağlayıcı filtreleme hem de belirli sağlayıcı filtreleme için tek bir esnek yaklaşım kullanmanızı sağlayacak şekilde filtrelemeyi destekler.

Daha fazla bilgi için, [bkz.](xref:fundamentals/logging/index)

## <a name="authentication-update"></a>Kimlik doğrulama güncelleştirmesi

Yeni bir kimlik doğrulama modeli, DI kullanarak bir uygulama için kimlik doğrulamayapılandırmayı kolaylaştırır.

[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)kullanarak web uygulamaları ve web API'leri için kimlik doğrulamayı yapılandırmak için yeni şablonlar mevcuttur.

Planlanan belgelerin durumu hakkında bilgi için [GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/3054)bakın.

## <a name="identity-update"></a>Kimlik güncelleştirmesi

ASP.NET Core 2.0'da Identity'i kullanarak güvenli web API'leri oluşturmayı kolaylaştırdık. [Microsoft Kimlik Doğrulama Kitaplığı'nı (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client)kullanarak web API'larınıza erişmek için erişim belirteçleri edinebilirsiniz.

2.0'daki kimlik doğrulama değişiklikleri hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [ASP.NET Core'da hesap onayı ve parola kurtarma](xref:security/authentication/accconfirm)
* [ASP.NET Core'daki kimlik doğrulama uygulamaları için QR Kodu oluşturmayı etkinleştirin](xref:security/authentication/identity-enable-qrcodes)
* [Kimlik Doğrulamayı ve Kimliği ASP.NET Core 2.0'a geçirin](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>SPA şablonları

Angular, Aurelia, Knockout.js, React.js ve React.js redux için tek sayfalık uygulama (SPA) proje şablonları mevcuttur. Açısal şablon Açısal 4 olarak güncelleştirildi. Açısal ve Tepki şablonları varsayılan olarak kullanılabilir; diğer şablonları nasıl edineceklerine ilişkin bilgi için [bkz.](xref:client-side/spa-services#create-a-new-project) ASP.NET Core'da spa nasıl inşa edilebilenhakkında bilgi için bkz. <xref:client-side/spa-services>

## <a name="kestrel-improvements"></a>Kerkenez iyileştirmeleri

Kestrel web sunucusu, internete bakan bir sunucu olarak daha uygun hale getiren yeni özelliklere sahiptir. Sınıfın yeni `KestrelServerOptions` `Limits` özelliğine bir dizi sunucu kısıtlaması yapılandırma seçeneği eklenir. Aşağıdakiler için sınırlar ekleyin:

* Maksimum istemci bağlantıları
* Maksimum istek gövde boyutu
* Minimum istek gövde veri oranı

Daha fazla bilgi için [ASP.NET Core'daki Kestrel web sunucusu uygulamasına](xref:fundamentals/servers/kestrel)bakın.

## <a name="weblistener-renamed-to-httpsys"></a>WebListener http.sys olarak yeniden adlandırıldı

Paketler `Microsoft.AspNetCore.Server.WebListener` ve `Microsoft.Net.Http.Server` yeni bir paket `Microsoft.AspNetCore.Server.HttpSys`içine birleştirilmiştir. Ad alanları eşleşecek şekilde güncelleştirildi.

Daha fazla bilgi için, [ASP.NET Core HTTP.sys web sunucusu uygulamasıbakın.](xref:fundamentals/servers/httpsys)

## <a name="enhanced-http-header-support"></a>Geliştirilmiş HTTP üstbilgi desteği

`FileStreamResult` Bir veya bir `FileContentResult`iletmek için MVC kullanırken, şimdi `ETag` ilettiğiniz içerik üzerinde bir veya tarih `LastModified` belirleme seçeneğiniz var. Bu değerleri döndürülen içeriğe aşağıdakilere benzer bir kodla ayarlayabilirsiniz:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

Ziyaretçilerinize döndürülen dosya, değerler ve değerler `ETag` `LastModified` için uygun HTTP üstbilgilerine sahiptir.

Bir uygulama ziyaretçisi Bir Aralık İsteği üstbilgisiyle içerik isterse, ASP.NET Core isteği tanır ve üstbilgiyi işler. İstenen içerik kısmen teslim edilebilirse, ASP.NET Core yalnızca istenen bayt kümesini atlar ve döndürür. Bu özelliği uyarlamak veya işlemek için yöntemlerinize özel işleyiciler yazmanız gerekmez; sizin için otomatik olarak işlenir.

## <a name="hosting-startup-and-application-insights"></a>Başlangıç ve Uygulama Öngörülerini Barındırma

Barındırma ortamları artık uygulamanın başlatılması sırasında, uygulamanın açıkça bir bağımlılık almasına veya herhangi bir yöntem emretmeye gerek kalmadan ek paket bağımlılıkları enjekte edebilir ve kodu yürütebilir. Bu özellik, uygulamanın önceden bilmesine gerek kalmadan belirli ortamların o ortama özgü özellikleri "aydınlatmasını" sağlamak için kullanılabilir. 

Core 2.0ASP.NETbu özellik, Visual Studio'da hata ayıklama yaparken ve Azure Uygulama Hizmetleri'nde çalışırken (kabul ettikten sonra) Uygulama Öngörüleri tanılamalarını otomatik olarak etkinleştirmek için kullanılır. Sonuç olarak, proje şablonları artık varsayılan olarak Uygulama Öngörüleri paketleri ve kodu eklemez.

Planlanan belgelerin durumu hakkında bilgi için [GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/3389)bakın.

## <a name="automatic-use-of-anti-forgery-tokens"></a>Sahteciliğin önleyici belirteçlerinin otomatik kullanımı

ASP.NET Core her zaman varsayılan olarak HTML-kod içeriği yardımcı oldu, ancak yeni sürüm ile ekstra bir adım siteleri arası istek sahteciliği (XSRF) saldırılarını önlemeye yardımcı olmak için alınır. ASP.NET Core şimdi varsayılan olarak sahtecilik karşıtı belirteçler yatacak ve bunları ekstra yapılandırma olmadan form POST eylemleri ve sayfalarında doğrulayacaktır.

Daha fazla bilgi için [bkz.](xref:security/anti-request-forgery)

## <a name="automatic-precompilation"></a>Otomatik precompilation

Razor view ön derlemesi varsayılan olarak yayımlama sırasında etkinleştirilerek çıktı boyutunu ve uygulama başlatma süresini azaltır.

Daha fazla bilgi için, [ASP.NET Core'da Razor görünümü derlemesi ve precompilation'a](xref:mvc/views/view-compilation)bakın.

## <a name="razor-support-for-c-71"></a>C# 7.1 için jilet desteği

Razor görünüm motoru yeni Roslyn derleyicisi ile çalışmak üzere güncellendi. Buna Varsayılan İfadeler, Çıkarılan Tuple Adları ve Genel Özelliklerle Desen Eşleştirme gibi C# 7.1 özellikleri desteği de dahildir. Projenizde C# 7.1'i kullanmak için proje dosyanıza aşağıdaki özelliği ekleyin ve ardından çözümü yeniden yükleyin:

```xml
<LangVersion>latest</LangVersion>
```

C# 7.1 özelliklerinin durumu hakkında bilgi için [Roslyn GitHub deposuna](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md)bakın.

## <a name="other-documentation-updates-for-20"></a>2.0 için diğer dokümantasyon güncellemeleri

* [Visual Studio ASP.NET Core uygulama dağıtımı için profiller yayınlamak](xref:host-and-deploy/visual-studio-publish-profiles)
* [Anahtar Yönetimi](xref:security/data-protection/implementation/key-management)
* [Facebook kimlik doğrulamasını yapılandırma](xref:security/authentication/facebook-logins)
* [Twitter kimlik doğrulamasını yapılandırma](xref:security/authentication/twitter-logins)
* [Google kimlik doğrulamasını yapılandırma](xref:security/authentication/google-logins)
* [Microsoft Hesabı kimlik doğrulamasını yapılandırma](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Geçiş kılavuzu

Core 1.x ASP.NET uygulamalarının ASP.NET Core 2.0'a nasıl geçirilir ekiyle ilgili rehberlik için aşağıdaki kaynaklara bakın:

* [ASP.NET Core 1.x'ten ASP.NET Core 2.0'a geçirin](xref:migration/1x-to-2x/index)
* [Kimlik Doğrulamayı ve Kimliği ASP.NET Core 2.0'a geçirin](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Ek Bilgi

Değişikliklerin tam listesi için [Core 2.0 Sürüm Notları ASP.NET](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0)bakın.

ASP.NET Core geliştirme ekibinin ilerleme ve planları ile bağlantı kurmak [için, ASP.NET Topluluk Standup'ı](https://live.asp.net/)ile bağlantı kurun.
