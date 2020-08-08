---
title: ASP.NET Core Genel Veri Koruma Yönetmeliği (GDPR) desteği
author: rick-anderson
description: ASP.NET Core Web uygulamasındaki GDPR uzantı noktalarına nasıl erişebileceğinizi öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 6392a22e316f903da18cd1a91d1eb779d8dde1b3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020021"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>ASP.NET Core içinde AB Genel Veri Koruma Yönetmeliği (GDPR) desteği

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core, bazı [ab genel veri koruma yönetmeliği (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) gereksinimlerini karşılamaya yardımcı olmak Için API 'ler ve şablonlar sağlar:

::: moniker range=">= aspnetcore-3.0"

* Proje şablonları, gizlilik ve ilke kullanma ile değiştireceğiniz uzantı noktalarını ve saplaması işaretlemesini içerir cookie .
* *Sayfalar/gizlilik. cshtml* sayfası veya *Görünümler/Home/privacy. cshtml* görünümü sitenizin gizlilik ilkesini ayrıntılandırmakta olan bir sayfa sağlar.

cookieASP.NET Core 3,0 şablonu tarafından oluşturulan bir uygulamada ASP.NET Core 2,2 şablonlarında bulunan gibi varsayılan onay özelliğini etkinleştirmek için:

* `using Microsoft.AspNetCore.Http`Using yönergeleri listesine ekleyin.
* İlkeye [ Cookie yoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) ekleyin `Startup.ConfigureServices` ve [ Cookie ilkeyi kullanın](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) `Startup.Configure` :

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* cookie *_Layout. cshtml* dosyasına onay kısmını ekleyin:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* * \_ Cookie Consentpartial. cshtml* dosyasını projeye ekleyin:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Onay özelliği hakkında bilgi edinmek için bu makalenin ASP.NET Core 2,2 sürümünü seçin cookie .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Proje şablonları, gizlilik ve ilke kullanma ile değiştireceğiniz uzantı noktalarını ve saplaması işaretlemesini içerir cookie .
* Bir cookie onay özelliği, kişisel bilgileri depolamak için kullanıcılarınıza izin vermenizi (ve izlemenizi) sağlar. Bir kullanıcı veri toplamaya ayrılmamışsa ve uygulamanın [Checkconsentclient](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) , olarak ayarlandıysa `true` , gerekli olmayan cookie öğeleri tarayıcıya gönderilmez.
* Cookies, temel olarak işaretlenebilir. cookieKullanıcı onaylı değil ve izleme devre dışı bırakılmış olsa bile, gerekli olan s tarayıcıya gönderilir.
* İzleme devre dışı olduğunda [TempData ve Session cookie 'lar](#tempdata) işlevsel değildir.
* [ Identity Yönet](#pd) sayfası, Kullanıcı verilerini indirmek ve silmek için bir bağlantı sağlar.

[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) , ASP.NET Core 2,1 ŞABLONLARıNA eklenen GDPR uzantı noktalarının ve API 'lerin çoğunu test etmenize olanak tanır. Test yönergeleri için [Benioku](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) dosyasına bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>Şablon tarafından üretilen kodda ASP.NET Core GDPR desteği

RazorProje Şablonlarıyla oluşturulan sayfalar ve MVC projeleri aşağıdaki GDPR desteğini içerir:

* [ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) ve [Use Cookie ilkesi](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) `Startup` sınıfında ayarlanır.
* * \_ Cookie Consentpartial. cshtml* [kısmi görünümü](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper). Bu dosyaya bir **kabul** düğmesi dahildir. Kullanıcı **kabul et** düğmesine tıkladığında, Store 'lar için onay cookie sağlanır.
* *Sayfalar/gizlilik. cshtml* sayfası veya *Görünümler/Home/privacy. cshtml* görünümü sitenizin gizlilik ilkesini ayrıntılandırmakta olan bir sayfa sağlar. * \_ Cookie Consentpartial. cshtml* dosyası gizlilik sayfasına bir bağlantı oluşturur.
* Bireysel kullanıcı hesaplarıyla oluşturulan uygulamalarda, Yönet sayfası [kişisel kullanıcı verilerini](#pd)indirmek ve silmek için bağlantılar sağlar.

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookiePoliyoptions ve kullanım Cookie ilkesi

[ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) şu şekilde başlatılır `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[Kullanım Cookie İlke](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) şu şekilde çağrılır `Startup.Configure` :

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial. cshtml kısmi görünümü

* \_ Cookie Consentpartial. cshtml* kısmi görünümü:

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Bu kısmi:

* Kullanıcı için izleme durumunu alır. Uygulama izin gerektirecek şekilde yapılandırıldıysa, s 'nin izlenebilmesi için kullanıcının onayı gerekir cookie . İzin gerekliyse, cookie izin paneli * \_ Layout. cshtml* dosyası tarafından oluşturulan gezinti çubuğunun üstünde düzeltilir.
* `<p>`Gizliliğinizi özetlemek ve ilkeyi kullanmak için BIR HTML öğesi sağlar cookie .
* Gizlilik sayfasına bir bağlantı sağlar veya sitenizin gizlilik ilkesini ayrıntılandırınızın ne olduğunu görebilirsiniz.

## <a name="essential-no-loccookies"></a>Gerekli cookie s

Depolama için izin cookie sağlanmazsa, tarayıcıya yalnızca, cookie temel olarak işaretli olan ' a gönderilir. Aşağıdaki kod, temel bir oluşturur cookie :

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>TempData sağlayıcısı ve oturum durumu cookie s önemli değildir

[TempData sağlayıcısı](xref:fundamentals/app-state#tempdata) cookie gerekli değildir. İzleme devre dışıysa, TempData sağlayıcısı işlevsel değildir. İzleme devre dışı bırakıldığında TempData sağlayıcısını etkinleştirmek için, TempData ' ı şu şekilde işaretleyin cookie `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[Oturum durumu](xref:fundamentals/app-state) cookie s gerekli değildir. İzleme devre dışı olduğunda oturum durumu işlevsel değil. Aşağıdaki kod, oturum cookie esasları yapar:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Kişisel veriler

Bireysel kullanıcı hesaplarıyla oluşturulan ASP.NET Core uygulamalar, kişisel verileri indirmek ve silmek için kod içerir.

Kullanıcı adını seçin ve **kişisel veriler**' i seçin:

![Kişisel verileri yönetme sayfası](gdpr/_static/pd.png)

Notlar:

* Kodu oluşturmak için `Account/Manage` bkz. [ Identity Yapı iskelesi ](xref:security/authentication/scaffold-identity).
* **Silme** ve **indirme** bağlantıları yalnızca varsayılan kimlik verilerinde işlem görür. Özel Kullanıcı verileri oluşturan uygulamalar, özel kullanıcı verilerini silmek/indirmek için genişletilmelidir. Daha fazla bilgi için bkz. [Özel Kullanıcı verilerini Identity ekleme, indirme ve silme ](xref:security/authentication/add-user-data).
* Veritabanı tablosunda depolanan kullanıcının kaydedilmiş belirteçleri, Identity `AspNetUserTokens` Kullanıcı [yabancı anahtar](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)nedeniyle basamaklı silme davranışı aracılığıyla silindiğinde silinir.
* Facebook ve Google gibi [dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index), ilke kabul edilmeden önce kullanılamaz cookie .

::: moniker-end

## <a name="encryption-at-rest"></a>Bekleme sırasında şifreleme

Bazı veritabanları ve depolama mekanizmaları, bekleyen şifreleme için izin verir. Bekleyen şifreleme:

* Depolanan verileri otomatik olarak şifreler.
* Veriye erişen yazılımlar için yapılandırma, programlama veya diğer çalışmasız şifreler.
* En kolay ve en güvenli seçenektir.
* Veritabanının anahtarları ve şifrelemeyi yönetmesine izin verir.

Örnek:

* Microsoft SQL ve Azure SQL, [Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption) (tde) sağlar.
* [SQL Azure, varsayılan olarak veritabanını şifreler](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Azure Blobları, dosyalar, tablo ve kuyruk depolaması varsayılan olarak şifrelenir](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Bekleyen şifreleme sağlamayan veritabanları için aynı korumayı sağlamak üzere disk şifrelemeyi kullanabilirsiniz. Örnek:

* [Windows Server için BitLocker](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [Eccryptotfs](https://launchpad.net/ecryptfs)
  * [Şifreleme](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Ek kaynaklar

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR-ASP.NET Core Izin Iptali düğmesi ekleme](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
