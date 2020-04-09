---
title: ASP.NET Core'da uygulama başlatma
author: rick-anderson
description: ASP.NET Core'daki Başlangıç sınıfının hizmetleri ve uygulamanın istek ardışık hattını nasıl yapılandırdığını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/startup
ms.openlocfilehash: e3249df4b7388beeff13fe4b4e0ff481c35725c5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667659"
---
# <a name="app-startup-in-aspnet-core"></a>ASP.NET Core'da uygulama başlatma

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), ve [Steve Smith](https://ardalis.com)

Sınıf, `Startup` hizmetleri ve uygulamanın istek ardışık hattını yapılandırır.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>Başlangıç sınıfı

ASP.NET Core uygulamaları, kuralla `Startup` `Startup` adlandırılan bir sınıf kullanır. `Startup` sınıfı:

* İsteğe bağlı <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> olarak uygulamanın *hizmetlerini*yapılandırmak için bir yöntem içerir. Hizmet, uygulama işlevselliği sağlayan yeniden kullanılabilir bir bileşendir. Hizmetler, *registered* `ConfigureServices` [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection) veya <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.
* Uygulamanın <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> istek işleme ardışık hattını oluşturmak için bir yöntem içerir.

`ConfigureServices`ve `Configure` uygulama başladığında ASP.NET Core çalışma zamanı tarafından çağrılır:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Önceki örnek Razor [Pages](xref:razor-pages/index)içindir; MVC sürümü benzer.


Uygulamanın `Startup` [ana bilgisayarı](xref:fundamentals/index#host) oluşturulunca sınıf belirtilir. Sınıf `Startup` genellikle [webhostBuilderExtensions.UseStartup TStartup\<>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) yöntemi ana oluşturucu çağırarak belirtilir:

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

Ana `Startup` bilgisayar, sınıf oluşturucutarafından kullanılabilen hizmetler sağlar. Uygulama üzerinden `ConfigureServices`ek hizmetler ekler. Hem ev sahibi hem de `Configure` uygulama hizmetleri uygulama içinde ve uygulama boyunca mevcuttur.

[Genel Ana Bilgisayar](xref:fundamentals/host/generic-host) kullanırken<xref:Microsoft.Extensions.Hosting.IHostBuilder> `Startup` oluşturucuya yalnızca aşağıdaki hizmet türleri enjekte edilebilir ( ):

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

Çoğu hizmet, yöntem `Configure` çağrılana kadar kullanılamaz.

### <a name="multiple-startup"></a>Çoklu Başlangıç

Uygulama farklı ortamlar `Startup` için ayrı sınıflar tanımladığında (örneğin, `StartupDevelopment`), çalışma zamanında uygun `Startup` sınıf seçilir. Ad soneki geçerli ortamla eşleşen sınıfa öncelik verilir. Uygulama Geliştirme ortamında çalıştırılırsa ve hem `Startup` bir sınıf `StartupDevelopment` hem `StartupDevelopment` de bir sınıf içeriyorsa, sınıf kullanılır. Daha fazla bilgi için [bkz.](xref:fundamentals/environments#environment-based-startup-class-and-methods)

Ana bilgisayar hakkında daha fazla bilgi için [ana bilgisayara](xref:fundamentals/index#host) bakın. Başlangıç sırasında işleme hataları hakkında bilgi için [Başlangıç özel durum işleme'ye](xref:fundamentals/error-handling#startup-exception-handling)bakın.

## <a name="the-configureservices-method"></a>ConfigureServices yöntemi

Yöntem: <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>

* İsteğe bağlı.
* Uygulamanın hizmetlerini yapılandırma `Configure` yönteminden önce ana bilgisayar tarafından çağrılır.
* [Yapılandırma seçeneklerinin](xref:fundamentals/configuration/index) konvansiyona göre ayarlandığı yer.

Ana bilgisayar, yöntemler çağrılmadan önce `Startup` bazı hizmetleri yapılandırabilir. Daha fazla bilgi için [ana bilgisayara](xref:fundamentals/index#host)bakın.

Önemli kurulum gerektiren özellikler için, ''de `Add{Service}` <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>uzantı yöntemleri vardır. Örneğin, DbContext **Ekle,** Varsayılan Kimlik **Ekle,** EntityFrameworkStores **Ekle**ve RazorPages **Ekle:**

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

Hizmet kapsayıcısına hizmet eklemek, bu hizmetleri uygulama `Configure` içinde ve yöntemde kullanılabilir hale getirir. Hizmetler [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection) veya <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

## <a name="the-configure-method"></a>Yapılandırma yöntemi

Yöntem, <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> uygulamanın HTTP isteklerine nasıl yanıt veriş olduğunu belirtmek için kullanılır. İstek ardışık bir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> örnek için ara [yazılım](xref:fundamentals/middleware/index) bileşenleri ekleyerek yapılandırılır. `IApplicationBuilder``Configure` yöntem kullanılabilir, ancak servis kapsayıcısında kayıtlı değildir. Barındırma bir `IApplicationBuilder` oluşturur ve doğrudan `Configure`geçer.

[ASP.NET Core şablonları,](/dotnet/core/tools/dotnet-new) aşağıdakiler için destek le birlikte ardışık yapılandırır:

* [Geliştirici Özel Durum Sayfası](xref:fundamentals/error-handling#developer-exception-page)
* [Özel durum işleyicisi](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP Sıkı Ulaşım Güvenliği (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [HTTPS yeniden yönlendirme](xref:security/enforcing-ssl)
* [Statik dosyalar](xref:fundamentals/static-files)
* ASP.NET Çekirdek [MVC](xref:mvc/overview) ve [Jilet Sayfaları](xref:razor-pages/index)


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Önceki örnek Razor [Pages](xref:razor-pages/index)içindir; MVC sürümü benzer.

Her `Use` uzantı yöntemi, istek ardışık hattına bir veya daha fazla ara yazılım bileşeni ekler. Örneğin, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> [statik dosyalara](xref:fundamentals/static-files)hizmet etmek için [ara yazılımı](xref:fundamentals/middleware/index) yapılandırır.

İstek ardışık ardışık ardışık her ara yazılım bileşeni, uygunsa, ardışık ardışık takimsonraki bileşeni çağırmak veya zinciri kısa devre için sorumludur.

`IWebHostEnvironment`Yöntem imzasında `ILoggerFactory` `ConfigureServices` `Configure` , veya tanımlanan herhangi bir şey gibi ek hizmetler belirtilebilir. Bu hizmetler varsa enjekte edilir.

Nasıl kullanılacağı `IApplicationBuilder` ve ara yazılım işleme sırası hakkında <xref:fundamentals/middleware/index>daha fazla bilgi için bkz.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Hizmetleri Başlangıç olmadan yapılandırma

Hizmetleri ve istek işleme ardışık hattını `Startup` bir `ConfigureServices` sınıf `Configure` kullanmadan yapılandırmak için, ana bilgisayar oluşturucuüzerinde arama ve kolaylık yöntemleri. Birbiriniz `ConfigureServices` için birden fazla çağrı. Birden `Configure` çok yöntem çağrısı `Configure` varsa, son arama kullanılır.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Başlangıç filtreleriyle Başlangıç'ı genişlet

Kullanım <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>Tarihi :

* Bir uygulamanın [Yapılandırma](#the-configure-method) aracı ardışık ardışık ardışık ardışık ardışık ardışık ardışık başında veya sonunda açık bir çağrı olmadan middleware yapılandırmak için. `Use{Middleware}` `IStartupFilter`ASP.NET Core tarafından, uygulama yazarının varsayılan ara yazılımı açıkça kaydetmesini sağlamak zorunda kalmadan, ardışık yolun başına varsayılanlar eklemek için kullanılır. `IStartupFilter`uygulama yazarı adına `Use{Middleware}` farklı bir bileşen araması yapılmasına izin verir.
* Bir `Configure` yöntem ardışık hattı oluşturmak için. [iStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) önce veya sonra ara yazılım kitaplıklar tarafından eklenen çalıştırmak için bir ara ayarlayabilirsiniz.

`IStartupFilter`uygular <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, bir alır `Action<IApplicationBuilder>`ve döndürür. Bir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> uygulamanın istek ardışık yapıyı yapılandırmak için bir sınıf tanımlar. Daha fazla bilgi için bkz: [IApplicationBuilder ile bir ara yazılım ardışık hattı oluşturun.](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

Her `IStartupFilter` biri istek ardışık hattına bir veya daha fazla ara yazılım ekleyebilir. Filtreler, hizmet kapsayıcısına eklendikleri sırada çağrılır. Filtreler denetimi bir sonraki filtreye geçirmeden önce veya sonra ara yazılım ekleyebilir, böylece uygulama ardışık oyunun başına veya sonuna eklenir.

Aşağıdaki örnek, bir ara yazılımın `IStartupFilter`nasıl kaydedilebildiğini gösterir. Ara `RequestSetOptionsMiddleware` yazılım, sorgu dize parametresinden bir seçenek değeri ayarlar:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsStartupFilter` Sınıfta `RequestSetOptionsMiddleware` yapılandırılır:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

Servis `IStartupFilter` konteynerine <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>kayıtlıdır.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

Bir `option` sorgu dize parametresi sağlandığında, ASP.NET Core ara yazılımı yanıtı oluşturmadan önce ara yazılım değer atagörevini işler.

Middleware yürütme emri kayıtların `IStartupFilter` sırasına göre ayarlanır:

* Birden `IStartupFilter` çok uygulama aynı nesnelerle etkileşime olabilir. Sipariş vermek önemliyse, `IStartupFilter` hizmet kayıtlarını ara yazılımlarının çalışması gereken sıraya uyacak şekilde sipariş edin.
* Kitaplıklar, kayıtlı diğer `IStartupFilter` uygulama ara yazılımlarından önce veya sonra `IStartupFilter`çalışan bir veya daha fazla uygulamayla ara yazılım ekleyebilir. Bir kitaplığın ki tarafından eklenen bir ara yazılımdan önce bir `IStartupFilter` ara yazılımı çağırmak `IStartupFilter`için:

  * Kitaplık servis kapsayıcısına eklenmeden önce servis kaydını yerleştirin.
  * Daha sonra çağırmak için, kitaplık eklendikten sonra servis kaydını yerleştirin.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Harici bir derlemeden başlangıçta yapılandırma ekleme

Uygulama, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> uygulamanın sınıfının dışındaki harici bir derlemeden başlangıçta bir `Startup` uygulamaya geliştirme eklemeye olanak tanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Ek kaynaklar

* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Başlangıç sınıfı

ASP.NET Core uygulamaları, kuralla `Startup` `Startup` adlandırılan bir sınıf kullanır. `Startup` sınıfı:

* İsteğe bağlı <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> olarak uygulamanın *hizmetlerini*yapılandırmak için bir yöntem içerir. Hizmet, uygulama işlevselliği sağlayan yeniden kullanılabilir bir bileşendir. Hizmetler, *registered* `ConfigureServices` [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection) veya <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.
* Uygulamanın <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> istek işleme ardışık hattını oluşturmak için bir yöntem içerir.

`ConfigureServices`ve `Configure` uygulama başladığında ASP.NET Core çalışma zamanı tarafından çağrılır:

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

Uygulamanın `Startup` [ana bilgisayarı](xref:fundamentals/index#host) oluşturulunca sınıf belirtilir. Sınıf `Startup` genellikle [webhostBuilderExtensions.UseStartup TStartup\<>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) yöntemi ana oluşturucu çağırarak belirtilir:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

Ana `Startup` bilgisayar, sınıf oluşturucutarafından kullanılabilen hizmetler sağlar. Uygulama üzerinden `ConfigureServices`ek hizmetler ekler. Hem ana bilgisayar hem de `Configure` uygulama hizmetleri daha sonra uygulama içinde ve tüm dünyada kullanılabilir.

Sınıfa [bağımlılık enjeksiyonortak](xref:fundamentals/dependency-injection) bir kullanımı enjekte etmektir: `Startup`

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>çevreye göre hizmetleri yapılandırmak için.
* <xref:Microsoft.Extensions.Configuration.IConfiguration>yapılandırmayı okumak için.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory>`Startup.ConfigureServices`içinde bir logger oluşturmak için.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

Çoğu hizmet, yöntem `Configure` çağrılana kadar kullanılamaz.

### <a name="multiple-startup"></a>Çoklu Başlangıç

Uygulama farklı ortamlar `Startup` için ayrı sınıflar tanımladığında (örneğin, `StartupDevelopment`), çalışma zamanında uygun `Startup` sınıf seçilir. Ad soneki geçerli ortamla eşleşen sınıfa öncelik verilir. Uygulama Geliştirme ortamında çalıştırılırsa ve hem `Startup` bir sınıf `StartupDevelopment` hem `StartupDevelopment` de bir sınıf içeriyorsa, sınıf kullanılır. Daha fazla bilgi için [bkz.](xref:fundamentals/environments#environment-based-startup-class-and-methods)

Ana bilgisayar hakkında daha fazla bilgi için [ana bilgisayara](xref:fundamentals/index#host) bakın. Başlangıç sırasında işleme hataları hakkında bilgi için [Başlangıç özel durum işleme'ye](xref:fundamentals/error-handling#startup-exception-handling)bakın.

## <a name="the-configureservices-method"></a>ConfigureServices yöntemi

Yöntem: <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>

* İsteğe bağlı.
* Uygulamanın hizmetlerini yapılandırma `Configure` yönteminden önce ana bilgisayar tarafından çağrılır.
* [Yapılandırma seçeneklerinin](xref:fundamentals/configuration/index) konvansiyona göre ayarlandığı yer.

Ana bilgisayar, yöntemler çağrılmadan önce `Startup` bazı hizmetleri yapılandırabilir. Daha fazla bilgi için [ana bilgisayara](xref:fundamentals/index#host)bakın.

Önemli kurulum gerektiren özellikler için, ''de `Add{Service}` <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>uzantı yöntemleri vardır. Örneğin, DbContext **Ekle,** Varsayılan Kimlik **Ekle,** EntityFrameworkStores **Ekle**ve RazorPages **Ekle:**

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

Hizmet kapsayıcısına hizmet eklemek, bu hizmetleri uygulama `Configure` içinde ve yöntemde kullanılabilir hale getirir. Hizmetler [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection) veya <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

Daha fazla bilgi için [SetCompatibilityVersion'a](xref:mvc/compatibility-version) `SetCompatibilityVersion`bakın.

## <a name="the-configure-method"></a>Yapılandırma yöntemi

Yöntem, <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> uygulamanın HTTP isteklerine nasıl yanıt veriş olduğunu belirtmek için kullanılır. İstek ardışık bir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> örnek için ara [yazılım](xref:fundamentals/middleware/index) bileşenleri ekleyerek yapılandırılır. `IApplicationBuilder``Configure` yöntem kullanılabilir, ancak servis kapsayıcısında kayıtlı değildir. Barındırma bir `IApplicationBuilder` oluşturur ve doğrudan `Configure`geçer.

[ASP.NET Core şablonları,](/dotnet/core/tools/dotnet-new) aşağıdakiler için destek le birlikte ardışık yapılandırır:

* [Geliştirici Özel Durum Sayfası](xref:fundamentals/error-handling#developer-exception-page)
* [Özel durum işleyicisi](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP Sıkı Ulaşım Güvenliği (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [HTTPS yeniden yönlendirme](xref:security/enforcing-ssl)
* [Statik dosyalar](xref:fundamentals/static-files)
* ASP.NET Çekirdek [MVC](xref:mvc/overview) ve [Jilet Sayfaları](xref:razor-pages/index)
* [Genel Veri Koruma Yönetmeliği (GDPR)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Her `Use` uzantı yöntemi, istek ardışık hattına bir veya daha fazla ara yazılım bileşeni ekler. Örneğin, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> [statik dosyalara](xref:fundamentals/static-files)hizmet etmek için [ara yazılımı](xref:fundamentals/middleware/index) yapılandırır.

İstek ardışık ardışık ardışık her ara yazılım bileşeni, uygunsa, ardışık ardışık takimsonraki bileşeni çağırmak veya zinciri kısa devre için sorumludur.

Yöntem imzası gibi `IHostingEnvironment` `ILoggerFactory`ek hizmetler veya `ConfigureServices`tanımlanan herhangi bir şey belirtilebilir. `Configure` Bu hizmetler varsa enjekte edilir.

Nasıl kullanılacağı `IApplicationBuilder` ve ara yazılım işleme sırası hakkında <xref:fundamentals/middleware/index>daha fazla bilgi için bkz.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Hizmetleri Başlangıç olmadan yapılandırma

Hizmetleri ve istek işleme ardışık hattını `Startup` bir `ConfigureServices` sınıf `Configure` kullanmadan yapılandırmak için, ana bilgisayar oluşturucuüzerinde arama ve kolaylık yöntemleri. Birbiriniz `ConfigureServices` için birden fazla çağrı. Birden `Configure` çok yöntem çağrısı `Configure` varsa, son arama kullanılır.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Başlangıç filtreleriyle Başlangıç'ı genişlet

Kullanım <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>Tarihi :

* Bir uygulamanın [Yapılandırma](#the-configure-method) aracı ardışık ardışık ardışık ardışık ardışık ardışık ardışık başında veya sonunda açık bir çağrı olmadan middleware yapılandırmak için. `Use{Middleware}` `IStartupFilter`ASP.NET Core tarafından, uygulama yazarının varsayılan ara yazılımı açıkça kaydetmesini sağlamak zorunda kalmadan, ardışık yolun başına varsayılanlar eklemek için kullanılır. `IStartupFilter`uygulama yazarı adına `Use{Middleware}` farklı bir bileşen araması yapılmasına izin verir.
* Bir `Configure` yöntem ardışık hattı oluşturmak için. [iStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) önce veya sonra ara yazılım kitaplıklar tarafından eklenen çalıştırmak için bir ara ayarlayabilirsiniz.

`IStartupFilter`uygular <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, bir alır `Action<IApplicationBuilder>`ve döndürür. Bir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> uygulamanın istek ardışık yapıyı yapılandırmak için bir sınıf tanımlar. Daha fazla bilgi için bkz: [IApplicationBuilder ile bir ara yazılım ardışık hattı oluşturun.](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

Her `IStartupFilter` biri istek ardışık hattına bir veya daha fazla ara yazılım ekleyebilir. Filtreler, hizmet kapsayıcısına eklendikleri sırada çağrılır. Filtreler denetimi bir sonraki filtreye geçirmeden önce veya sonra ara yazılım ekleyebilir, böylece uygulama ardışık oyunun başına veya sonuna eklenir.

Aşağıdaki örnek, bir ara yazılımın `IStartupFilter`nasıl kaydedilebildiğini gösterir. Ara `RequestSetOptionsMiddleware` yazılım, sorgu dize parametresinden bir seçenek değeri ayarlar:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsStartupFilter` Sınıfta `RequestSetOptionsMiddleware` yapılandırılır:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

Servis `IStartupFilter` konteynerine <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>kayıtlıdır.

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Bir `option` sorgu dize parametresi sağlandığında, ASP.NET Core ara yazılımı yanıtı oluşturmadan önce ara yazılım değer atagörevini işler.

Middleware yürütme emri kayıtların `IStartupFilter` sırasına göre ayarlanır:

* Birden `IStartupFilter` çok uygulama aynı nesnelerle etkileşime olabilir. Sipariş vermek önemliyse, `IStartupFilter` hizmet kayıtlarını ara yazılımlarının çalışması gereken sıraya uyacak şekilde sipariş edin.
* Kitaplıklar, kayıtlı diğer `IStartupFilter` uygulama ara yazılımlarından önce veya sonra `IStartupFilter`çalışan bir veya daha fazla uygulamayla ara yazılım ekleyebilir. Bir kitaplığın ki tarafından eklenen bir ara yazılımdan önce bir `IStartupFilter` ara yazılımı çağırmak `IStartupFilter`için:

  * Kitaplık servis kapsayıcısına eklenmeden önce servis kaydını yerleştirin.
  * Daha sonra çağırmak için, kitaplık eklendikten sonra servis kaydını yerleştirin.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Harici bir derlemeden başlangıçta yapılandırma ekleme

Uygulama, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> uygulamanın sınıfının dışındaki harici bir derlemeden başlangıçta bir `Startup` uygulamaya geliştirme eklemeye olanak tanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Ek kaynaklar

* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end