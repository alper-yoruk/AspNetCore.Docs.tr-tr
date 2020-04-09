---
title: ASP.NET'den ASP.NET Çekirdeğine geçiş
author: isaac2004
description: Varolan ASP.NET mvc veya Web API uygulamalarını core.web'ASP.NET geçirmek için kılavuz alın
ms.author: scaddie
ms.date: 10/18/2019
uid: migration/proper-to-2x/index
ms.openlocfilehash: 68a45dc50e00bead564500a12509b62a4a193ec4
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511099"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>ASP.NET'den ASP.NET Çekirdeğine geçiş

Yazar: [Isaac Levin](https://isaaclevin.com)

Bu makale, ASP.NET Core'a ASP.NET uygulamaları geçirmek için bir başvuru kılavuzu görevi ASP.NET.

## <a name="prerequisites"></a>Ön koşullar

[.NET Core SDK 2.2 veya sonrası](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a>Hedef çerçeveler

ASP.NET Core projeleri geliştiricilere .NET Core, .NET Framework veya her ikisini birden hedefleme esnekliği sunar. Hangi hedef çerçevenin en uygun olduğunu belirlemek [için sunucu uygulamaları için .NET Core ve .NET Framework arasında seçim](/dotnet/standard/choosing-core-framework-server) edersiniz.

.NET Framework hedeflediğinizde, projelerin tek tek NuGet paketlerine başvurması gerekir.

Hedefleme .NET Core, ASP.NET Core [metapaketi](xref:fundamentals/metapackage-app)sayesinde çok sayıda açık paket referansını ortadan kaldırmanızı sağlar. Projenizde `Microsoft.AspNetCore.App` meta paketi yükleyin:

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

Metapaket kullanıldığında, uygulamayla birlikte meta pakete atıfta bulunulan hiçbir paket dağıtılanmez. .NET Core Runtime Store bu varlıkları içerir ve performansı artırmak için önceden derlenir. Daha fazla ayrıntı [için ASP.NET Core için Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app) bakın.

## <a name="project-structure-differences"></a>Proje yapısı farklılıkları

*.csproj* dosya biçimi ASP.NET Core'da basitleştirilmiştir. Bazı önemli değişiklikler şunlardır:

- Dosyaların açık bir şekilde eklenmesi, dosyaların projenin bir parçası olarak kabul edilmesi için gerekli değildir. Bu, büyük ekipler üzerinde çalışırken XML birleştirme çakışma riskini azaltır.
- Dosya okunabilirliğini artıran diğer projelere GUID tabanlı başvuru lar yoktur.
- Dosya Visual Studio'da boşaltılmadan düzenlenebilir:

    ![Visual Studio 2017'de CSPROJ bağlam menüsü seçeneğini edin](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Global.asax dosya değiştirme

ASP.NET Core bir uygulama bootstrapping için yeni bir mekanizma tanıttı. ASP.NET başvurularıiçin giriş noktası *Global.asax* dosyasıdır. Rota yapılandırması ve filtre ve alan kayıtları gibi görevler *Global.asax* dosyasında işlenir.

[!code-csharp[](samples/globalasax-sample.cs)]

Bu yaklaşım, uygulamayı ve dağıtıldığı sunucuyu uygulamayı engelleyecek şekilde çiftleştirilir. Ayırmak amacıyla, [OWIN](https://owin.org/) birlikte birden fazla çerçeve kullanmak için daha temiz bir yol sağlamak için tanıtıldı. OWIN yalnızca gerekli modülleri eklemek için bir boru hattı sağlar. Barındırma ortamı, hizmetleri ve uygulamanın istek ardışık hattını yapılandırmak için bir [Başlangıç](xref:fundamentals/startup) işlevi alır. `Startup`uygulama ile ara yazılım kümesi kaydeder. Her istek için uygulama, ara yazılım bileşenlerinin her birini, bağlı bir listenin baş işaretçisiyle varolan bir işleyici ler kümesine çağırır. Her ara yazılım bileşeni, istek işleme ardışık hattına bir veya daha fazla işleyici ekleyebilir. Bu, listenin yeni başkanı olan işleyiciye bir başvuru döndürülerek gerçekleştirilir. Her işleyici, listedeki bir sonraki işleyiciyi hatırlamak ve çağırmakla yükümlüdür. ASP.NET Core ile bir uygulamaya giriş `Startup`noktası dır ve artık *Global.asax'a*bağımlı değildir. OWIN'i .NET Framework ile kullanırken, aşağıdaki gibi bir şeyi bir boru hattı olarak kullanın:

[!code-csharp[](samples/webapi-owin.cs)]

Bu, varsayılan yollarınızı yapılandırır ve Varsayılan olarak Json üzerinden XmlSerialization olarak yapılandırılır. Gerektiğinde bu ardışık yapıya diğer Ara yazılımları ekleyin (yükleme hizmetleri, yapılandırma ayarları, statik dosyalar, vb.).

ASP.NET Core benzer bir yaklaşım kullanır, ancak girişi işlemek için OWIN güvenmez. Bunun yerine, *bu Program.cs* `Main` yöntemi (konsol uygulamalarına benzer) üzerinden yapılır ve `Startup` orada yüklenir.

[!code-csharp[](samples/program.cs)]

`Startup`bir `Configure` yöntem içermelidir. In `Configure`, boru hattına gerekli ara yazılımı ekleyin. Aşağıdaki örnekte (varsayılan web sitesi şablonundan), uzantı yöntemleri ardışık alanı aşağıdakiler için destekle yapılandırır:

- Hata sayfaları
- HTTP Sıkı Ulaşım Güvenliği
- HTTPS için HTTP yönlendirme
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

Ana bilgisayar ve uygulama, gelecekte farklı bir platforma geçme esnekliği sağlayan ayrılmıştır.

> [!NOTE]
> Core Startup ve Middleware'ASP.NET daha ayrıntılı bir başvuru için ASP.NET [Core'da Başlangıç'a](xref:fundamentals/startup) bakın

## <a name="store-configurations"></a>Mağaza yapılandırmaları

ASP.NET depolama ayarlarını destekler. Bu ayar, örneğin, uygulamaların dağıtıldığı ortamı desteklemek için kullanılır. Yaygın bir uygulama `<appSettings>` *Web.config* dosyasının bölümünde tüm özel anahtar değeri çiftleri depolamak oldu:

[!code-xml[](samples/webconfig-sample.xml)]

Uygulamalar `ConfigurationManager.AppSettings` `System.Configuration` ad alanında toplama kullanarak bu ayarları okuyun:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core, uygulama için yapılandırma verilerini herhangi bir dosyada saklayabilir ve ara yazılım bootstrapping'in bir parçası olarak yükleyebilir. Proje şablonlarında kullanılan varsayılan dosya *appsettings.json:*

[!code-json[](samples/appsettings-sample.json)]

Bu dosyayı uygulamanızın `IConfiguration` içinde bir örneğin içine yüklemek *Startup.cs*yapılır:

[!code-csharp[](samples/startup-builder.cs)]

Uygulama ayarları `Configuration` almak için okur:

[!code-csharp[](samples/read-appsettings.cs)]

Bu yaklaşımın, bir hizmeti bu değerlerle yüklemek için [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) (DI) kullanmak gibi daha sağlam hale getirmek için uzantıları vardır. DI yaklaşımı, güçlü bir şekilde yazılmış bir yapılandırma nesnesi kümesi sağlar.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> ASP.NET Core yapılandırmasına daha ayrıntılı bir başvuru [için, ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index)bakın.

## <a name="native-dependency-injection"></a>Yerel bağımlılık enjeksiyonu

Büyük, ölçeklenebilir uygulamalar inşa ederken önemli bir hedef bileşenlerin ve hizmetlerin gevşek bağlantı olduğunu. [Bağımlılık Enjeksiyon](xref:fundamentals/dependency-injection) bunu başarmak için popüler bir tekniktir, ve ASP.NET Core yerli bir bileşenidir.

ASP.NET uygulamalarda, geliştiriciler Bağımlılık Enjeksiyonu uygulamak için üçüncü taraf kitaplığına güvenir. Bu tür bir [kitaplık,](https://github.com/unitycontainer/unity)Microsoft Desenleri & Uygulamaları tarafından sağlanan Unity'dir.

Birlik ile Bağımlılık Enjeksiyon kurma bir örnek `IDependencyResolver` bir `UnityContainer`sarar uygulanmaktadır:

[!code-csharp[](samples/sample8.cs)]

Hizmetinizin `UnityContainer`bir örneğini oluşturun ve kapsayıcınızın yeni `HttpConfiguration` örneğine `UnityResolver` bağımlılık çözümleyicisini ayarlayın:

[!code-csharp[](samples/sample9.cs)]

İhtiyaç `IProductRepository` duyulan yerlerde enjekte edin:

[!code-csharp[](samples/sample5.cs)]

Bağımlılık Enjeksiyonu ASP.NET Çekirdek'in bir parçası olduğundan, `ConfigureServices` hizmetinizi *Startup.cs*yöntemine ekleyebilirsiniz:

[!code-csharp[](samples/configure-services.cs)]

Depo, Unity ile doğru olduğu gibi her yere enjekte edilebilir.

> [!NOTE]
> Bağımlılık enjeksiyonu hakkında daha fazla bilgi için [bkz.](xref:fundamentals/dependency-injection)

## <a name="serve-static-files"></a>Statik dosyalara hizmet

Web geliştirmenin önemli bir parçası statik, istemci tarafı varlıklara hizmet edebilme yeteneğidir. Statik dosyaların en yaygın örnekleri HTML, CSS, Javascript ve görüntülerdir. Bu dosyaların uygulamanın (veya CDN) yayımlanmış konumuna kaydedilmesi ve bir istekle yüklenebilmeleri için başvurulması gerekir. Bu işlem ASP.NET Core'da değişti.

ASP.NET, statik dosyalar çeşitli dizinlerde depolanır ve görünümlerde başvurur.

ASP.NET Core'da statik dosyalar, aksi yapılandırılmadığı sürece "web root"*&lt;(içerik kökü&gt;/wwwroot)* olarak depolanır. Dosyalar, aşağıdaki uzantı `UseStaticFiles` `Startup.Configure`yöntemini çağırarak istek ardışık hattına yüklenir:

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> .NET Framework hedefleniniyorsanız, `Microsoft.AspNetCore.StaticFiles`NuGet paketini yükleyin.

Örneğin, *wwwroot/images* klasöründeki bir görüntü varlığı tarayıcı tarafından `http://<app>/images/<imageFileName>`.

> [!NOTE]
> ASP.NET Core'da statik dosyalara hizmet vermek için daha ayrıntılı bir başvuru için [Statik dosyalara](xref:fundamentals/static-files)bakın.

## <a name="multi-value-cookies"></a>Çok değerli tanımlama bilgileri

[Çok değerli tanımlama bilgileri](xref:System.Web.HttpCookie.Values) ASP.NET Core'da desteklenmez. Değer başına bir çerez oluşturun.

## <a name="partial-app-migration"></a>Kısmi uygulama geçişi

Kısmi uygulama geçişine bir yaklaşım, bir IIS alt uygulaması oluşturmak ve uygulamanın URL yapısını korurken yalnızca ASP.NET 4,x'ten ASP.NET Core'a belirli yolları taşımaktır. Örneğin, *applicationHost.config* dosyasından uygulamanın URL yapısını göz önünde bulundurun:

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

Dizin yapısı:

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a>Ek kaynaklar

- [Kütüphaneleri .NET Core'a Taşıma](/dotnet/core/porting/libraries)
