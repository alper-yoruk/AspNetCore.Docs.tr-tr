---
title: ASP.NET Core'daki statik dosyalar
author: rick-anderson
description: Statik dosyalara nasıl hizmet edinip güvenli hale getirmek ve ASP.NET Core web uygulamasında ara yazılım barındırma statik dosyayı yapılandırmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 95a77defc7e98328e1f4e3615648b1d14485e51e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660127"
---
# <a name="static-files-in-aspnet-core"></a>ASP.NET Core'daki statik dosyalar

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Scott Addie](https://twitter.com/Scott_Addie)

HTML, CSS, görüntüler ve JavaScript gibi statik dosyalar, ASP.NET Core uygulamasının doğrudan istemcilere sunduğu varlıklardır. Bu dosyaların sunulmasını etkinleştirmek için bazı yapılandırmalar gereklidir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Statik dosyalara hizmet

Statik dosyalar projenin [web kök](xref:fundamentals/index#web-root) dizini içinde depolanır. Varsayılan dizin *{içerik kökü}/wwwroot'dur,* ancak [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) yöntemi yle değiştirilebilir. Daha fazla bilgi için [İçerik kökü](xref:fundamentals/index#content-root) ve [Web köküne](xref:fundamentals/index#web-root) bakın.

Uygulamanın web barındırma içeriği kök dizininden haberdar edilmelidir.

::: moniker range=">= aspnetcore-2.0"

Yöntem, `WebHost.CreateDefaultBuilder` içerik kökünü geçerli dizine ayarlar:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[İçindeuseContentRoot'u](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) çağırarak içerik kökünü geçerli dizine `Program.Main`ayarlayın:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Statik dosyalara [web köküne](xref:fundamentals/index#web-root)göre bir yol üzerinden erişilebilir. Örneğin, **Web Uygulaması** proje şablonu *wwwroot* klasöründe birkaç klasör içerir:

* **wwwroot**
  * **Css**
  * **Görüntü**
  * **Js**

*Resimler* alt klasöründeki bir dosyaya erişmek için URI *biçimi,\<>/resim/\<image_file_name>server_address http://. * Örneğin, *http://localhost:9189/images/banner3.svg*.

::: moniker range=">= aspnetcore-2.1"

.NET Framework'u hedefliyorsanız, projeye [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) paketini ekleyin. .NET Core hedefalıyorsanız, [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) bu paketi içerir.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

.NET Framework'u hedefliyorsanız, projeye [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) paketini ekleyin. .NET Core hedefalıyorsanız, [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) bu paketi içerir.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Projeye [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) paketini ekleyin.

::: moniker-end

Statik dosyaların sunulmasını sağlayan [ara yazılımı](xref:fundamentals/middleware/index) yapılandırın.

### <a name="serve-files-inside-of-web-root"></a>Dosyaları web kökü içinde servis edin

[UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) yöntemini `Startup.Configure`aşağıdakiler içinde çağırır:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Parametresiz `UseStaticFiles` yöntem aşırı [yük, web kökündeki](xref:fundamentals/index#web-root) dosyaları servis edilebilir olarak işaretler. Aşağıdaki biçimlendirme referansları *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

Önceki kodda, tilde karakteri `~/` web [köküne](xref:fundamentals/index#web-root)işaret ediyor.

### <a name="serve-files-outside-of-web-root"></a>Dosyaları web kökü dışında servis edin

Hizmet verilecek statik dosyaların [web kökünün](xref:fundamentals/index#web-root)dışında bulunduğu bir dizin hiyerarşisi düşünün:

* **wwwroot**
  * **Css**
  * **Görüntü**
  * **Js**
* **MyStaticFiles**
  * **Görüntü**
    * *banner1.svg*

Bir istek aşağıdaki gibi Statik Dosya Middleware yapılandırarak *banner1.svg* dosyasına erişebilirsiniz:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

Önceki kodda, *MyStaticFiles* dizin hiyerarşisi *StaticFiles* URI segmenti aracılığıyla herkese açık olarak açıklanır. *>/StaticFiles/images/banner1.svg server_address http://\<* için bir istek *banner1.svg* dosya hizmet vermektedir.

Aşağıdaki biçimlendirme *referansları MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>HTTP yanıt üstbilgilerini ayarlama

[Bir StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) nesnesi HTTP yanıt üstbilgilerini ayarlamak için kullanılabilir. [Web kökünden](xref:fundamentals/index#web-root)hizmet veren statik dosyayı yapılandırmaya ek `Cache-Control` olarak, aşağıdaki kod üstbilgi yi ayarlar:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

[HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) yöntemi [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunmaktadır.

Dosyalar Geliştirme ortamında 10 dakika (600 saniye) kamuya açık hale getirilmiştir:

![Önbellek Denetimi üstbilgisini gösteren yanıt üstbilgisi eklendi](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Statik dosya yetkilendirmesi

Statik Dosya Middleware yetkilendirme denetimleri sağlamaz. *Wwwroot*altında olanlar da dahil olmak üzere, tarafından sunulan tüm dosyalar, genel olarak erişilebilir. Yetkilendirmeye dayalı dosyaları sunmak için:

* *Bunları wwwroot'un* ve Statik Dosya Middleware'in erişebileceği herhangi bir dizinin dışında saklayın.
* Yetkilendirmenin uygulandığı bir eylem yöntemi yle bunları servis edin. [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) nesnesi döndürme:

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Dizin taramasını etkinleştirme

Dizin tarama web uygulamanızın kullanıcılarının belirli bir dizin içinde bir dizin listesi ve dosyaları görmenizi sağlar. Dizin tarama güvenlik nedenleriyle varsayılan olarak devre dışı bırakılır [(bkz.](#considerations) [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) yöntemini şu şekilde çağırarak dizin taramasını `Startup.Configure`etkinleştirin:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

[AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) yöntemini çağırarak gerekli hizmetleri `Startup.ConfigureServices`ekleyin:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Önceki kod, her dosya ve klasöre bağlantılar la *>/MyImages server_address\<* server_address URL http:// kullanarak *wwwroot/images* klasörünün dizin le gezinmesini sağlar:

![dizin tarama](static-files/_static/dir-browse.png)

Bkz. Göz atma etkinleştirirken güvenlik riskleri hakkında [dikkat](#considerations) edilmesi gerekenler.

Aşağıdaki örnekte iki `UseStaticFiles` çağrıya dikkat edin. İlk *arama, wwwroot* klasöründe statik dosyaların sunulmasını sağlar. İkinci arama, *>/MyImages\<* server_address URL http:// kullanarak *wwwroot/images* klasörünün dizin le gezinmesini sağlar:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Varsayılan bir belgeye hizmet

Varsayılan bir giriş sayfası ayarlamak, ziyaretçilerin sitenizi ziyaret ederken mantıksal bir başlangıç noktası sağlar. Kullanıcı URI'yi tam olarak nitelemeden varsayılan bir sayfa sunmak `Startup.Configure`için [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) yöntemini şu telefondan arayın:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles`varsayılan dosyayı `UseStaticFiles` sunmak için önce çağrılması gerekir. `UseDefaultFiles`dosyaya gerçekte hizmet vermeyen bir URL rewriter'ıdır. Dosyaya hizmet etmek `UseStaticFiles` için Statik Dosya Middleware'i etkinleştirin.

Ile `UseDefaultFiles`, bir klasör arama istekleri için:

* *Default.htm*
* *default.html*
* *Index.htm*
* *Index.html*

Listeden bulunan ilk dosya, istek tam nitelikli URI'ymiş gibi sunulur. Tarayıcı URL'si URI'nin istediği yansıtmaya devam ediyor.

Aşağıdaki kod varsayılan dosya adını *mydefault.html*olarak değiştirir:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>işlevselliğini birleştirir `UseStaticFiles` `UseDefaultFiles`, ve `UseDirectoryBrowser`isteğe bağlı olarak .

Aşağıdaki kod statik dosyaların ve varsayılan dosyanın sunulmasını sağlar. Dizin tarama sı etkinleştirildi.

```csharp
app.UseFileServer();
```

Aşağıdaki kod, dizin taramasını etkinleştirerek parametresiz aşırı yükleme üzerine oluşturur:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:

* **wwwroot**
  * **Css**
  * **Görüntü**
  * **Js**
* **MyStaticFiles**
  * **Görüntü**
    * *banner1.svg*
  * *default.html*

Aşağıdaki kod statik dosyaları, varsayılan dosyaları ve dizin `MyStaticFiles`tarama sağlar:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser``EnableDirectoryBrowsing` özellik değeri olduğunda `true`çağrılmalıdır:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Dosya hiyerarşisi ve önceki kodu kullanarak URL'ler aşağıdaki gibi çözer:

| URI            |                             Yanıt  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

*MyStaticFiles* dizininde varsayılan adlandırılmış dosya yoksa, *http://\<server_address server_address>/StaticFiles* dizin listesini tıklanabilir bağlantılarla döndürür:

![Statik dosya listesi](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>ve <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> istemci tarafı yönlendirme `http://{SERVER ADDRESS}/StaticFiles` (bir iz kesme olmadan) `http://{SERVER ADDRESS}/StaticFiles/` gerçekleştirmek (bir izleme eğik çizgi ile). *StaticFiles* dizinindeki göreli URL'ler, sondalı bir eğik çizgi olmadan geçersizdir.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

[FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) sınıfı, `Mappings` MIME içerik türlerine dosya uzantılarının eşlemeolarak hizmet veren bir özellik içerir. Aşağıdaki örnekte, bilinen MIME türlerine birkaç dosya uzantısı kaydedilir. *.rtf* uzantısı değiştirilir ve *.mp4* kaldırılır.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Bakınız [MIME içerik türleri.](https://www.iana.org/assignments/media-types/media-types.xhtml)

## <a name="non-standard-content-types"></a>Standart olmayan içerik türleri

Statik Dosya Middleware neredeyse 400 bilinen dosya içeriği türleri anlar. Kullanıcı bilinmeyen bir dosya türüne sahip bir dosya isterse, Statik Dosya Middleware isteği ardışık ardışık alandaki bir sonraki ara yazılıma geçirir. Hiçbir ara yazılım isteği işlemezse, *404 Bulunamadı* yanıtı döndürülür. Dizin taraması etkinleştirilmişse, dosyanın bağlantısı bir dizin listesinde görüntülenir.

Aşağıdaki kod bilinmeyen türlere hizmet edilmesini sağlar ve bilinmeyen dosyayı görüntü olarak işler:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Önceki kodla, bilinmeyen içerik türüne sahip bir dosya isteği görüntü olarak döndürülür.

> [!WARNING]
> [ServeUnknownFileTypes'yi](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) etkinleştirmek bir güvenlik riskidir. Varsayılan olarak devre dışı bırakılır ve kullanımı önerilmez. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) standart olmayan uzantıları ile dosyaları hizmet için daha güvenli bir alternatif sağlar.

## <a name="serve-files-from-multiple-locations"></a>Dosyaları birden çok konumdan servis edin

`UseStaticFiles`ve `UseFileServer` *wwwroot'u*işaret eden dosya sağlayıcısına varsayılan dır. Diğer konumlardan dosyaları `UseStaticFiles` sunmak `UseFileServer` için ek örnekleri ve diğer dosya sağlayıcıları ile sağlayabilir. Daha fazla bilgi için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/15578)bakın.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

> [!WARNING]
> `UseDirectoryBrowser`ve `UseStaticFiles` sırları sızdırabilir. Üretimde dizin tarama devre dışı bırakılması şiddetle tavsiye edilir. Hangi dizinlerin veya `UseStaticFiles` '' `UseDirectoryBrowser`aracılığıyla etkinleştirilmiş olduğunu dikkatle inceleyin. Tüm dizin ve alt dizinleri genel olarak erişilebilir hale gelir. * \<content_root>/wwwroot*gibi özel bir dizinde halka hizmet vermeye uygun dosyaları saklayın. Bu dosyaları MVC görünümlerinden, Razor Pages'dan (yalnızca 2.x), yapılandırma dosyalarından vb. ayırın.

* İçerikle açıkta `UseDirectoryBrowser` kalan ve `UseStaticFiles` altta yatan dosya sisteminin durum hassasiyeti ve karakter kısıtlamalarına tabi olan URL'ler. Örneğin, Windows büyük bir&mdash;durumda duyarsız macOS ve Linux değildir.

* iIS'de barındırılan ASP.NET Core uygulamaları, statik dosya istekleri de dahil olmak üzere tüm istekleri uygulamaya iletmek için [ASP.NET Çekirdek Modülü'ni](xref:host-and-deploy/aspnet-core-module) kullanır. IIS statik dosya işleyicisi kullanılmaz. Modül tarafından ele alınamadan önce istekleri işleme şansı yoktur.

* Sunucu veya web sitesi düzeyinde IIS statik dosya işleyicisini kaldırmak için IIS Manager'da aşağıdaki adımları tamamlayın:
    1. **Modüller** özelliğine gidin.
    1. Listede **StaticFileModule'i** seçin.
    1. **Eylemler** kenar çubuğunda **Kaldır'ı** tıklatın.

> [!WARNING]
> IIS statik dosya işleyicisi **etkinleştirilmişse ve** ASP.NET Çekirdek Modülü yanlış yapılandırılırsa, statik dosyalar servis edilir. Bu, örneğin, *web.config* dosyası dağıtılmazsa olur.

* Kod dosyalarını *(.cs* ve *.cshtml*dahil) uygulama projesinin [web kökünün](xref:fundamentals/index#web-root)dışına yerleştirin. Bu nedenle, uygulamanın istemci tarafındaki içeriği yle sunucu tabanlı kodu arasında mantıksal bir ayrım oluşturulur. Bu, sunucu tarafındaki kodun sızdırılmasını önler.

## <a name="additional-resources"></a>Ek kaynaklar

* [Ara yazılım](xref:fundamentals/middleware/index)
* [ASP.NET Core’a Giriş](xref:index)
