---
title: ASP.NET 'den ASP.NET Core 2,0 'ye geçiş
author: isaac2004
description: Mevcut ASP.NET MVC veya Web API uygulamalarını ASP.NET Core 2,0 ' ye geçirmeye yönelik rehberlik alın.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- appsettings.json
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
uid: migration/mvc2
ms.openlocfilehash: cf7d2e3a94c14fb752180d9349536d17b4557e0a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051336"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a>ASP.NET 'den ASP.NET Core 2,0 'ye geçiş

İle [Isaac Levi](https://isaaclevin.com) tarafından

Bu makale, ASP.NET uygulamalarını ASP.NET Core 2,0 ' ye geçirmeye yönelik bir başvuru kılavuzu görevi görür.

## <a name="prerequisites"></a>Önkoşullar

.Net downloads 'lerden **aşağıdakilerden birini** yükler [: Windows](https://dotnet.microsoft.com/download):

* .NET Core SDK
* Windows için Visual Studio
  * **ASP.net ve Web geliştirme** iş yükü
  * **.NET Core platformlar arası geliştirme** iş yükü

## <a name="target-frameworks"></a>Hedef çerçeveler

ASP.NET Core 2,0 projeleri, geliştiricilere .NET Core, .NET Framework veya her ikisinin de hedeflenme esnekliği sunar. En uygun hedef Framework 'ü belirlemek için, bkz. [sunucu uygulamaları için .NET Core ve .NET Framework arasından seçim yapma](/dotnet/standard/choosing-core-framework-server) .

.NET Framework hedeflenirken, projelerin ayrı NuGet paketlerine başvurması gerekir.

.NET Core 'u hedeflemek, ASP.NET Core 2,0 [metapackage](xref:fundamentals/metapackage)sayesinde çok sayıda açık paket başvurularını ortadan kaldırmanıza olanak tanır. `Microsoft.AspNetCore.All`Meta paketini projenize yükler:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

Metapackage kullanıldığında, metapackage içinde başvurulan hiçbir paket uygulamayla birlikte dağıtılır. .NET Core çalışma zamanı deposu bu varlıkları içerir ve performansı artırmak için önceden ön derlenmiş hale getiriyoruz. <xref:fundamentals/metapackage>Daha fazla ayrıntı için bkz..

## <a name="project-structure-differences"></a>Proje yapısı farkları

*. Csproj* dosya biçimi ASP.NET Core basitleştirilmiştir. Bazı önemli değişiklikler şunları içerir:

* Dosyaların açıkça eklenmesi, projenin bir parçası olarak kabul edilmesi için gerekli değildir. Bu, büyük ekipler üzerinde çalışırken XML birleştirme çakışmalarının riskini azaltır.
* Diğer projelere GUID tabanlı başvurular yoktur ve bu da dosya okunabilirliğini geliştirir.
* Dosya Visual Studio 'da kaldırmadan düzenlenebilir:

  ![Visual Studio 2017 ' de CSPROJ bağlam menüsü seçeneğini Düzenle](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Global. asax dosyası değiştirme

ASP.NET Core, bir uygulamayı önyüklemeden yeni bir mekanizma getirmiştir. ASP.NET uygulamaları için giriş noktası *Global. asax* dosyasıdır. Rota yapılandırması ve filtre ve alan kayıtları gibi görevler, *Global. asax* dosyasında işlenir.

[!code-csharp[](samples/globalasax-sample.cs)]

Bu yaklaşım, uygulamayı ve dağıtıldığı sunucuyu uygulamayı kesintiye uğratan bir şekilde bağar. Bağımsız olarak, [Owin](https://owin.org/) , birden çok çerçeveyi birlikte kullanmanın bir temizleyici yolunu sağlamak için sunulmuştur. OWIN yalnızca gereken modülleri eklemek için bir işlem hattı sağlar. Barındırma ortamı, hizmetleri ve uygulamanın istek ardışık düzenini yapılandırmak için bir [Başlangıç](xref:fundamentals/startup) işlevi alır. `Startup` uygulamayla bir ara yazılım kümesini kaydeder. Her istek için, uygulama bir ara yazılım bileşeninin her birini bağlantılı listenin baş işaretçisi ile mevcut bir işleyici kümesine çağırır. Her bir ara yazılım bileşeni, istek işleme ardışık düzenine bir veya daha fazla işleyici ekleyebilir. Bu, listenin yeni başlığı olan işleyiciye bir başvuru döndürülerek gerçekleştirilir. Her işleyici, listedeki bir sonraki işleyiciyi hatırlayıp çağırmaktan sorumludur. ASP.NET Core, bir uygulamaya giriş noktası olur `Startup` ve artık *Global. asax* ' a bağımlılığı yoktur. .NET Framework ile OWIN kullanırken, işlem hattı olarak aşağıdaki gibi bir şey kullanın:

[!code-csharp[](samples/webapi-owin.cs)]

Bu, varsayılan rotalarınızı yapılandırır ve varsayılan olarak JSON üzerinden XmlSerialization olur. Gerektiğinde bu işlem hattına başka bir ara yazılım ekleyin (Yükleme Hizmetleri, yapılandırma ayarları, statik dosyalar vb.).

ASP.NET Core benzer bir yaklaşım kullanır, ancak girişi işlemek için OWıN 'u kullanmaz. Bunun yerine, Program.cs yöntemi aracılığıyla yapılır *Program.cs* `Main` (konsol uygulamalarına benzer) ve bunlar `Startup` üzerinden yüklenir.

[!code-csharp[](samples/program.cs)]

`Startup` bir yöntem içermelidir `Configure` . ' De `Configure` , gerekli ara yazılımı ardışık düzene ekleyin. Aşağıdaki örnekte (varsayılan Web sitesi şablonundan), işlem hattını desteğiyle yapılandırmak için birkaç uzantı yöntemi kullanılır:

* [BrowserLink](https://vswebessentials.com/features/browserlink)
* Hata sayfaları
* Statik dosyalar
* ASP.NET Core MVC
* Identity

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

Ana bilgisayar ve uygulama, gelecekte farklı bir platforma geçme esnekliği sağlayan bir şekilde ayrılmış.

ASP.NET Core başlangıç ve ara yazılım için daha ayrıntılı bir başvuru için bkz <xref:fundamentals/startup> ..

## <a name="storing-configurations"></a>Yapılandırma depolama

ASP.NET, ayarları depolamayı destekler. Bu ayar, örneğin, uygulamaların dağıtıldığı ortamı desteklemek için kullanılır. Ortak bir uygulama, tüm özel anahtar-değer çiftlerini `<appSettings>` *Web.config* dosyasının bölümünde depolandı:

[!code-xml[](samples/webconfig-sample.xml)]

Uygulamalar `ConfigurationManager.AppSettings` ad alanındaki koleksiyonu kullanarak bu ayarları okur `System.Configuration` :

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core, uygulama için yapılandırma verilerini herhangi bir dosyada depolayıp ara yazılım önyükleme 'nin bir parçası olarak yükleyebilir. Proje şablonlarında kullanılan varsayılan dosya *appsettings.json* :

[!code-json[](samples/appsettings-sample.json)]

Bu dosyayı uygulamanızın içindeki bir örneğine yüklemek `IConfiguration` *Startup.cs* içinde yapılır:

[!code-csharp[](samples/startup-builder.cs)]

Uygulama, `Configuration` ayarları almak için öğesinden okur:

[!code-csharp[](samples/read-appsettings.cs)]

Bu şekilde, bu değerlere sahip bir hizmeti yüklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) kullanma gibi işlemleri daha sağlam hale getirmek için bu yaklaşımın uzantıları vardır. Dı yaklaşımı, kesin türü belirtilmiş bir yapılandırma nesneleri kümesi sağlar.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

**Note:** ASP.NET Core yapılandırmaya yönelik daha ayrıntılı bir başvuru için bkz <xref:fundamentals/configuration/index> ..

## <a name="native-dependency-injection"></a>Yerel bağımlılığı ekleme

Büyük, ölçeklenebilir uygulamalar, bileşenlerin ve hizmetlerin gevşek bağlantısı olan önemli bir hedef. [Bağımlılık ekleme](xref:fundamentals/dependency-injection) , bunu elde etmek için popüler bir tekniktir ve ASP.NET Core yerel bir bileşenidir.

ASP.NET uygulamalarında, geliştiriciler bağımlılık ekleme işlemini uygulamak için bir üçüncü taraf kitaplığı kullanır. Bu tür bir kitaplık [, Microsoft](https://github.com/unitycontainer/unity)düzenleri & uygulamalar tarafından sağlanır.

Unity ile bağımlılık ekleme ayarlamayı bir örnek şunu `IDependencyResolver` sarmalayan bir örnektir `UnityContainer` :

[!code-csharp[](samples/sample8.cs)]

Bir örneği oluşturun `UnityContainer` , hizmetinizi kaydedin ve bağımlılık çözümleyicisini `HttpConfiguration` kapsayıcının yeni örneğine ayarlayın `UnityResolver` :

[!code-csharp[](samples/sample9.cs)]

`IProductRepository`Gerektiğinde ekle:

[!code-csharp[](samples/sample5.cs)]

Bağımlılık ekleme ASP.NET Core bir parçası olduğundan, hizmetinizi şu şekilde ekleyebilirsiniz `Startup.ConfigureServices` :

[!code-csharp[](samples/configure-services.cs)]

Depo, Unity ile doğru olduğu için her yerde eklenebilir.

ASP.NET Core bağımlılık ekleme hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection> ..

## <a name="serving-static-files"></a>Statik dosyaları sunma

Web geliştirmenin önemli bir bölümü, statik, istemci tarafı varlıkları sunma olanağıdır. Statik dosyaların en yaygın örnekleri HTML, CSS, JavaScript ve görüntülerdir. Bu dosyaların, uygulamanın yayınlanan konumuna (veya CDN) kaydedilmesi gerekir ve bu dosyalar bir istek tarafından yüklenebilmeleri için başvurulmalıdır. Bu işlem ASP.NET Core değişti.

ASP.NET ' de statik dosyalar çeşitli dizinlerde depolanır ve görünümlerde başvurulur.

ASP.NET Core, statik dosyalar, aksi belirtilmedikçe "Web kökünde" ( *&lt; içerik kökü &gt; /Wwwroot* ) içinde depolanır. Dosyalar, şuradan genişletme yöntemi çağrılarak istek ardışık düzenine yüklenir `UseStaticFiles` `Startup.Configure` :

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

**Note:** .NET Framework hedefliyorsanız, NuGet paketini yükler `Microsoft.AspNetCore.StaticFiles` .

Örneğin, *Wwwroot/görüntüler* klasöründeki bir görüntü varlığı, tarayıcı tarafından gibi bir konumda erişilebilir `http://<app>/images/<imageFileName>` .

**Note:** ASP.NET Core içinde statik dosyaları sunmaya yönelik daha ayrıntılı bir başvuru için bkz <xref:fundamentals/static-files> ..

## <a name="additional-resources"></a>Ek kaynaklar

* [Kitaplıkları .NET Core 'a taşıma](/dotnet/core/porting/libraries)
