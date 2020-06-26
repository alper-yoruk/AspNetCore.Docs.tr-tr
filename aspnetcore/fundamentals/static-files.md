---
title: ASP.NET Core statik dosyalar
author: rick-anderson
description: Statik dosyaları sunma ve güvenli hale getirme ve bir ASP.NET Core Web uygulamasındaki statik dosya barındırma ara yazılım davranışlarını yapılandırma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 33b4d43471932ed5e9e328163672028b00e6b5b8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399484"
---
# <a name="static-files-in-aspnet-core"></a>ASP.NET Core statik dosyalar

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Scott Ade](https://twitter.com/Scott_Addie)

HTML, CSS, resim ve JavaScript gibi statik dosyalar, ASP.NET Core bir uygulamanın doğrudan istemcilere hizmet verdiği varlıklardır. Bu dosyalara hizmet sunma özelliğini etkinleştirmek için bazı yapılandırmalar gerekir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Statik dosyaları sunma

Statik dosyalar projenin [Web kök](xref:fundamentals/index#web-root) dizininde depolanır. Varsayılan dizin *{Content root}/Wwwroot*, ancak [usewebroot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) yöntemi aracılığıyla değiştirilebilir. Daha fazla bilgi için bkz. [içerik kökü](xref:fundamentals/index#content-root) ve [Web kök](xref:fundamentals/index#web-root) .

Uygulamanın Web ana bilgisayarı, içerik kök dizininden haberdar olmalıdır.

::: moniker range=">= aspnetcore-2.0"

`WebHost.CreateDefaultBuilder`Yöntemi, içerik kökünü geçerli dizine ayarlar:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

İçinde [Usecontentroot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) 'yi çağırarak içerik kökünü geçerli dizine ayarlayın `Program.Main` :

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Statik dosyalara, [Web köküne](xref:fundamentals/index#web-root)göre bir yol aracılığıyla erişilebilir. Örneğin, **Web uygulaması** proje şablonu *Wwwroot* klasörü içinde birkaç klasör içerir:

* **wwwroot**
  * **Self**
  * **yansımasını**
  * **JS**

*Görüntüler* alt klasöründeki bir dosyaya erışmek için urı biçimi *http:// \<server_address> /images/ \<image_file_name> *olur. Örneğin, *http://localhost:9189/images/banner3.svg* .

::: moniker range=">= aspnetcore-2.1"

.NET Framework hedefliyorsanız, [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) paketini projeye ekleyin. .NET Core hedefleniyorsa, [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) bu paketi içerir.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

.NET Framework hedefliyorsanız, [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) paketini projeye ekleyin. .NET Core hedefleniyorsa, [Microsoft. AspNetCore. All metapackage](xref:fundamentals/metapackage) bu paketi içerir.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Projeye [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) paketini ekleyin.

::: moniker-end

Statik dosyaları sunmaya izin veren [ara yazılımı](xref:fundamentals/middleware/index) yapılandırın.

### <a name="serve-files-inside-of-web-root"></a>Web kökünün içindeki dosyaları sunma

İçinde [Usestaticfiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) yöntemini çağır `Startup.Configure` :

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Parametresiz `UseStaticFiles` yöntemi aşırı yüklemesi, [Web kökündeki](xref:fundamentals/index#web-root) dosyaları servable olarak işaretler. Aşağıdaki biçimlendirme *Wwwroot/Images/banner1. SVG*öğesine başvuruyor:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

Yukarıdaki kodda, tilde karakteri `~/` [Web köküne](xref:fundamentals/index#web-root)işaret eder.

### <a name="serve-files-outside-of-web-root"></a>Dosyaları Web kökünün dışında sunma

Sunulacak statik dosyaların [Web kökünün](xref:fundamentals/index#web-root)dışında yer aldığı bir dizin hiyerarşisini göz önünde bulundurun:

* **wwwroot**
  * **Self**
  * **yansımasını**
  * **JS**
* **MyStaticFiles**
  * **yansımasını**
    * *banner1. SVG*

Bir istek statik dosya ara yazılımını aşağıdaki şekilde yapılandırarak *banner1. SVG* dosyasına erişebilir:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

Yukarıdaki kodda, *mystaticfiles* dizin hiyerarşisi, *staticfiles* URI segmenti aracılığıyla herkese açıktır. *Http:// \<server_address> /StaticFiles/images/banner1.SVG* için bir istek *banner1. SVG* dosyasına hizmet verir.

Aşağıdaki biçimlendirme *Mystaticfiles/Images/banner1. SVG*' ye başvurur:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>HTTP yanıt üstbilgilerini ayarla

[Staticfileoptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) NESNESI, http yanıt üst bilgilerini ayarlamak için kullanılabilir. [Web kökünden](xref:fundamentals/index#web-root)statik dosya sunma yapılandırmasına ek olarak, aşağıdaki kod `Cache-Control` üst bilgisini ayarlar:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

[Headerdictionaryextensions. Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) yöntemi, [Microsoft. Aspnetcore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunur.

Dosyalar, geliştirme ortamında 10 dakika (600 saniye) için genel olarak önbelleklenebilir hale getirilir:

![Cache-Control üst bilgisini gösteren yanıt üstbilgileri eklendi](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Statik dosya yetkilendirmesi

Statik dosya ara yazılımı yetkilendirme denetimleri sağlamıyor. *Wwwroot*altındakiler de dahil olmak üzere hizmet tarafından sunulan tüm dosyalar herkese açık olarak erişilebilir. Dosyalara yetkilendirme temelinde hizmeti sağlamak için:

* Onları *Wwwroot* dışında ve statik dosya ara yazılımı tarafından erişilebilen herhangi bir dizinle saklayın.
* Yetkilendirmeyi uygulanan bir eylem yöntemi aracılığıyla onlara sunar. Bir [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) nesnesi döndürür:

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Dizin taramayı etkinleştir

Dizin tarama, Web uygulamanızın kullanıcılarına belirtilen bir dizin içindeki bir dizin listesini ve dosyalarını görmesini sağlar. Dizin tarama, güvenlik nedenleriyle varsayılan olarak devre dışıdır (bkz. [hususlar](#considerations)). İçinde [Usedirectorybrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) metodunu çağırarak dizin taramayı etkinleştirin `Startup.Configure` :

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Adresinden [Adddirectorybrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) yöntemini çağırarak gerekli hizmetleri ekleyin `Startup.ConfigureServices` :

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Yukarıdaki kod, her bir dosya ve klasörün bağlantılarıyla birlikte *http:// \<server_address> /MYıMAGES*URL 'sini kullanarak *Wwwroot/görüntüler* klasöründe Dizin taramasına izin verir:

![dizin tarama](static-files/_static/dir-browse.png)

Göz atmayı etkinleştirirken güvenlik riskleri hakkındaki [noktalara](#considerations) göz atın.

Aşağıdaki örnekteki iki çağrının olduğunu aklınızda edin `UseStaticFiles` . İlk çağrı *Wwwroot* klasöründeki statik dosyaları sunmaya izin veriyor. İkinci çağrı, *http:// \<server_address> /MYıMAGES*URL 'sini kullanarak *Wwwroot/görüntüler* klasöründe dizin taramayı mümkün bir şekilde sunar:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Varsayılan bir belge sunar

Varsayılan ana sayfanın ayarlanması, ziyaretçi sitenizi ziyaret ederken mantıksal bir başlangıç noktası sağlar. Kullanıcı URI 'yi tamamen nitelemeden varsayılan bir sayfaya hizmeti sağlamak için, şu kaynaktan [Usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) metodunu çağırın `Startup.Configure` :

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles`Varsayılan dosyaya kullanılmadan önce çağrılmalıdır `UseStaticFiles` . `UseDefaultFiles`, dosyayı gerçekten sunan bir URL yeniden yazar. Dosya hizmeti sağlamak için statik dosya ara yazılımını etkinleştirin `UseStaticFiles` .

İle `UseDefaultFiles` , bir klasör için arama istekleri:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Listedeki ilk dosya, istek tam URI olmasına rağmen olarak sunulur. Tarayıcı URL 'SI, istenen URI 'yi yansıtacak şekilde devam ediyor.

Aşağıdaki kod, varsayılan dosya adını *mydefault.html*olarak değiştirir:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>Usedosya sunucusu

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>`UseStaticFiles`, `UseDefaultFiles` ve isteğe bağlı olarak işlevselliğini birleştirir `UseDirectoryBrowser` .

Aşağıdaki kod, statik dosyaların ve varsayılan dosyanın kullanılmasına izin veriyor. Dizin tarama etkin değil.

```csharp
app.UseFileServer();
```

Aşağıdaki kod, dizin taramayı etkinleştirerek Parametresiz aşırı yüklemeden sonra oluşturulur:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:

* **wwwroot**
  * **Self**
  * **yansımasını**
  * **JS**
* **MyStaticFiles**
  * **yansımasını**
    * *banner1. SVG*
  * *default.html*

Aşağıdaki kod statik dosyaları, varsayılan dosyaları ve dizin taramayı mümkün bir şekilde sunar `MyStaticFiles` :

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser``EnableDirectoryBrowsing`özellik değeri şu olduğunda çağrılmalıdır `true` :

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Dosya hiyerarşisini ve önceki kodu kullanarak, URL 'Ler aşağıdaki şekilde çözümlenir:

| URI            |                             Yanıt  |
| ------- | ------|
| *http:// \<server_address> /StaticFiles/images/banner1.SVG*    |      MyStaticFiles/Images/banner1. SVG |
| *http:// \<server_address> /Staticfiles*             |     MyStaticFiles/default.html |

*Mystaticfiles* dizininde varsayılan adlı dosya yoksa *http:// \<server_address> /staticfiles* , tıklatılabilir bağlantılarla dizin listesini döndürür:

![Statik dosyalar listesi](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>ve <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> `http://{SERVER ADDRESS}/StaticFiles` (sonunda eğik çizgi olmadan) ile (sonunda eğik çizgiyle) bir istemci tarafı yeniden yönlendirmesi gerçekleştirin `http://{SERVER ADDRESS}/StaticFiles/` . *Staticfiles* dizinindeki göreli URL 'ler, sondaki eğik çizgi olmadan geçersizdir.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

[Fileextensioncontenttypeprovider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) sınıfı, `Mappings` MIME içerik türlerine dosya uzantılarının eşlemesi olarak hizmet veren bir özellik içerir. Aşağıdaki örnekte, bazı dosya uzantıları bilinen MIME türlerine kaydedilir. *. Rtf* uzantısı değiştirilmiştir ve *. mp4* kaldırılır.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Bkz. [MIME içerik türleri](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Standart olmayan içerik türleri

Statik dosya ara yazılımı, neredeyse 400 bilinen dosya içerik türlerini anlamıştır. Kullanıcı bilinmeyen bir dosya türüne sahip bir dosya isterse, statik dosya ara yazılımı isteği ardışık düzendeki bir sonraki ara yazılıma geçirir. Bir ara yazılım, isteği işlediğinde, bir *404 bulunamadı* yanıtı döndürülür. Dizin tarama etkinse, bir dizin listesinde dosyaya bir bağlantı görüntülenir.

Aşağıdaki kod, bilinmeyen türlere hizmet olarak bilinmeyen türler sunar ve bilinmeyen dosyayı görüntü olarak işler:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Yukarıdaki kodla, bilinmeyen içerik türüne sahip bir dosya isteği görüntü olarak döndürülür.

> [!WARNING]
> [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) etkinleştirme bir güvenlik riskidir. Varsayılan olarak devre dışıdır ve kullanımı önerilmez. [Fileextensioncontenttypeprovider](#fileextensioncontenttypeprovider) standart olmayan uzantılara sahip dosyalara hizmet vermeye yönelik daha güvenli bir alternatif sağlar.

## <a name="serve-files-from-multiple-locations"></a>Birden çok konumdan dosyaları sunma

`UseStaticFiles`ve `UseFileServer` Varsayılan olarak *Wwwroot*' a işaret eden dosya sağlayıcısına sahiptir. `UseStaticFiles` `UseFileServer` Diğer konumlardan dosya sunmak için diğer dosya sağlayıcılarının yanı sıra ek örnekler de sağlayabilirsiniz. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/15578)bakın.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

> [!WARNING]
> `UseDirectoryBrowser`ve `UseStaticFiles` gizli dizileri sızdırabilirsiniz. Üretimde dizin taramayı devre dışı bırakmak önemle önerilir. Veya ile hangi dizinlerin etkinleştirildiğini dikkatle gözden `UseStaticFiles` geçirin `UseDirectoryBrowser` . Tüm dizin ve alt dizinleri herkese açık şekilde erişilebilir hale gelir. Özel bir dizinde genel kullanıma sunma için uygun dosyaları, örneğin * \<content_root> /Wwwroot*gibi depolayın. Bu dosyaları MVC görünümlerini, Razor sayfaları (yalnızca 2. x), yapılandırma dosyalarını vb. ayırın.

* Ve ile sunulan içerik URL 'Leri `UseDirectoryBrowser` , `UseStaticFiles` temel dosya sisteminin büyük küçük harf duyarlılığı ve karakter kısıtlamalarına tabidir. Örneğin, Windows büyük/küçük harfe duyarsız &mdash; MacOS ve Linux değildir.

* IIS 'de barındırılan ASP.NET Core uygulamalar, statik dosya istekleri de dahil olmak üzere tüm istekleri uygulamaya iletmek için [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) kullanır. IIS statik dosya işleyicisi kullanılmıyor. Modül tarafından işlenmek üzere istekleri işleme şansı yoktur.

* Sunucu veya Web sitesi düzeyinde IIS statik dosya işleyicisini kaldırmak için IIS Yöneticisi ' nde aşağıdaki adımları uygulayın:
    1. **Modüller** özelliğine gidin.
    1. Listeden **StaticFileModule ' ü** seçin.
    1. **Eylemler** kenar çubuğunda **Kaldır** ' a tıklayın.

> [!WARNING]
> IIS statik dosya işleyicisi etkinse **ve** ASP.NET Core modülü yanlış yapılandırılmışsa, statik dosyalar sunulur. Bu, örneğin *web.config* dosyası dağıtılmamışsa oluşur.

* Kod dosyalarını ( *. cs* ve *. cshtml*dahil) uygulama projesinin [Web kökünün](xref:fundamentals/index#web-root)dışına yerleştirin. Bu nedenle, uygulamanın istemci tarafı içeriği ile sunucu tabanlı kod arasında bir mantıksal ayrım oluşturulur. Bu, sunucu tarafı kodun sızmasını önler.

## <a name="additional-resources"></a>Ek kaynaklar

* [Ara yazılım](xref:fundamentals/middleware/index)
* [ASP.NET Core’a Giriş](xref:index)
