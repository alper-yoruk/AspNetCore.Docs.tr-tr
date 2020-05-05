---
title: ASP.NET Core 'de uygulama başlatma
author: rick-anderson
description: ASP.NET Core ' deki başlangıç sınıfının Hizmetleri ve uygulamanın istek ardışık düzenini nasıl yapılandırdığını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/startup
ms.openlocfilehash: 39fba5ccc99ec0ecf32df5681cfc025c52bc5469
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776441"
---
# <a name="app-startup-in-aspnet-core"></a>ASP.NET Core 'de uygulama başlatma

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra)ve [Steve Smith](https://ardalis.com)

`Startup` Sınıfı Hizmetleri ve uygulamanın istek ardışık düzenini yapılandırır.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>Başlangıç sınıfı

ASP.NET Core uygulamalar, kural `Startup` tarafından adlandırılan `Startup` bir sınıfı kullanır. `Startup` sınıfı:

* İsteğe bağlı olarak <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> , uygulamanın *hizmetlerini*yapılandırmak için bir yöntem içerir. Hizmet, uygulama işlevselliği sağlayan yeniden kullanılabilir bir bileşendir. Hizmetler, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) veya <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>ile uygulama *genelinde kullanılır.* `ConfigureServices`
* Uygulamanın istek <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> işleme ardışık düzenini oluşturmak için bir yöntem içerir.

`ConfigureServices`ve `Configure` uygulama başlatıldığında ASP.NET Core çalışma zamanı tarafından çağrılır:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Yukarıdaki örnek, [ Razor sayfalar](xref:razor-pages/index)içindir; MVC sürümü benzerdir.


`Startup` Sınıf, uygulamanın [ana makinesi](xref:fundamentals/index#host) yapılandırıldığında belirtilir. Sınıf genellikle konak Oluşturucu 'da [Webhostbuilderextensions. usestartup\<tstartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) yöntemi çağırarak belirtilir: `Startup`

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

Ana bilgisayar, `Startup` sınıf oluşturucusunun kullanabildiği hizmetleri sağlar. Uygulama aracılığıyla `ConfigureServices`ek hizmetler ekler. Hem konak hem de uygulama Hizmetleri uygulama içinde ve `Configure` üzerinde kullanılabilir.

`Startup` [Genel ana bilgisayar](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

Çoğu hizmet, `Configure` Yöntem çağrılana kadar kullanılabilir değildir.

### <a name="multiple-startup"></a>Çoklu başlangıç

Uygulama farklı ortamlar için ayrı `Startup` sınıflar tanımladığında (örneğin, `StartupDevelopment`), çalışma zamanında uygun `Startup` sınıf seçilir. Geçerli ortamla eşleşen ad sonekine sahip olan sınıf önceliklendirilir. Uygulama geliştirme ortamında çalıştırıldıysanız ve hem `Startup` sınıf hem de `StartupDevelopment` sınıf içeriyorsa, `StartupDevelopment` sınıfı kullanılır. Daha fazla bilgi için bkz. [birden çok ortam kullanma](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Konak hakkında daha fazla bilgi için [konağa](xref:fundamentals/index#host) bakın. Başlatma sırasında hataları işleme hakkında daha fazla bilgi için bkz. [Başlangıç özel durum işleme](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>ConfigureServices yöntemi

<xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> Yöntemi:

* İsteğe bağlı.
* Uygulamanın hizmetlerini yapılandırma `Configure` yönteminden önce ana bilgisayar tarafından çağırılır.
* [Yapılandırma seçeneklerinin](xref:fundamentals/configuration/index) kurala göre ayarlandığı yer.

Konak, Yöntemler çağrılmadan önce `Startup` bazı hizmetleri yapılandırabilir. Daha fazla bilgi için bkz. [ana bilgisayar](xref:fundamentals/index#host).

Önemli kurulum gerektiren özellikler için üzerinde `Add{Service}` <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>uzantı yöntemleri vardır. Örneğin, DbContext **ekleyin** **, defaultıdentity ekleyin,** entityframeworkmağazalarını **ekleyin ve**RazorPages **ekleyin**:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

Hizmet kapsayıcısına hizmet eklemek, bunları uygulama içinde ve `Configure` yönteminde kullanılabilir hale getirir. Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection) veya konumundan <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>çözümlenir.

## <a name="the-configure-method"></a>Configure yöntemi

<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> Yöntemi, uygulamanın http isteklerine nasıl yanıt verdiğini belirtmek için kullanılır. İstek ardışık düzeni, bir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> örneğe [Ara yazılım](xref:fundamentals/middleware/index) bileşenleri eklenerek yapılandırılır. `IApplicationBuilder`, `Configure` yöntemi için kullanılabilir, ancak hizmet kapsayıcısına kayıtlı değildir. Barındırma bir `IApplicationBuilder` oluşturur ve doğrudan öğesine `Configure`geçirir.

[ASP.NET Core şablonlar](/dotnet/core/tools/dotnet-new) işlem hattını desteğiyle birlikte yapılandırır:

* [Geliştirici özel durum sayfası](xref:fundamentals/error-handling#developer-exception-page)
* [Özel durum işleyicisi](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP katı taşıma güvenliği (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [HTTPS yönlendirmesi](xref:security/enforcing-ssl)
* [Statik dosyalar](xref:fundamentals/static-files)
* [MVC](xref:mvc/overview) ve [ Razor sayfaları](xref:razor-pages/index) ASP.NET Core


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Yukarıdaki örnek, [ Razor sayfalar](xref:razor-pages/index)içindir; MVC sürümü benzerdir.

Her `Use` genişletme yöntemi, istek ardışık düzenine bir veya daha fazla ara yazılım bileşeni ekler. Örneğin, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> [ara yazılımı](xref:fundamentals/middleware/index) [statik dosyaları](xref:fundamentals/static-files)sunacak şekilde yapılandırır.

İstek ardışık düzeninde bulunan her bir ara yazılım bileşeni, uygun olduğunda, zincirdeki bir sonraki bileşeni çağırmaktan veya zincirde kısa bir süre sonra sağlanmasından sorumludur.

`IWebHostEnvironment`, Veya `ILoggerFactory` `ConfigureServices`gibi ek hizmetler, `Configure` Yöntem imzasında belirtilebilir. Bu hizmetler varsa eklenir.

Kullanımı `IApplicationBuilder` ve ara yazılım işleme sırası hakkında daha fazla bilgi için bkz <xref:fundamentals/middleware/index>..

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Hizmetleri başlatmadan yapılandırma

Hizmetleri ve istek işleme işlem hattını, ana bilgisayar Oluşturucu üzerinde `Startup` bir sınıf, `ConfigureServices` çağrı `Configure` ve kullanışlı yöntemler kullanmadan yapılandırmak için. Birbirine eklenecek birden `ConfigureServices` çok çağrı. Birden çok `Configure` yöntem varsa, son `Configure` çağrı kullanılır.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Başlangıç filtreleriyle başlatmayı Genişlet

Şunu <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>kullanın:

* Bir uygulamanın başlangıcında veya sonunda, açık bir çağrı olmadan ara yazılım [yapılandırma](#the-configure-method) ardışık düzenini yapılandırmak için `Use{Middleware}`. `IStartupFilter`ASP.NET Core tarafından, uygulama yazarının varsayılan ara yazılımı açıkça kaydetmesi gerekmeden, işlem hattının başlangıcına varsayılanlar eklemek için kullanılır. `IStartupFilter`Uygulama yazarı adına farklı bir `Use{Middleware}` bileşen çağrısının yapılmasına izin verir.
* `Configure` Yöntem işlem hattı oluşturmak için. [Itartupfilter. configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) , bir ara yazılımı kitaplıklar tarafından eklenen bir veya daha sonra çalışacak şekilde ayarlayabilir.

`IStartupFilter`<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>öğesini alır ve döndürür `Action<IApplicationBuilder>`. Bir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> uygulamanın istek ardışık düzenini yapılandırmak için bir sınıf tanımlar. Daha fazla bilgi için bkz. [IApplicationBuilder ile bir ara yazılım işlem hattı oluşturma](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Her `IStartupFilter` biri, istek ardışık düzeninde bir veya daha fazla middlewares ekleyebilir. Filtreler, hizmet kapsayıcısına eklendikleri sırada çağrılır. Filtreler bir sonraki filtreye denetimi geçirmeden önce veya sonra bir ara yazılım ekleyebilir, böylece uygulama işlem hattının başına veya sonuna eklenir.

Aşağıdaki örnek, ile `IStartupFilter`bir ara yazılımın nasıl kaydettirildiğini gösterir. `RequestSetOptionsMiddleware` Ara yazılım bir sorgu dizesi parametresinden bir seçenek değeri ayarlar:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

, `RequestSetOptionsMiddleware` `RequestSetOptionsStartupFilter` Sınıfında yapılandırılır:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

, `IStartupFilter` İçindeki <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>hizmet kapsayıcısına kaydedilir.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

İçin `option` bir sorgu dizesi parametresi sağlandığında, ara yazılım ASP.NET Core ara yazılım tarafından yanıt oluşturmadan önce değer atamasını işler.

Ara yazılım yürütme sırası, `IStartupFilter` kayıt sırasıyla ayarlanır:

* Birden `IStartupFilter` çok uygulama aynı nesnelerle etkileşime geçebilir. Sıralama önemliyse, `IStartupFilter` hizmet kayıtlarını middlewares 'in çalıştırması gereken sırayla eşleşecek şekilde sıralayın.
* Kitaplıklar, ile `IStartupFilter`kaydolmadan önce veya sonra `IStartupFilter` çalışan bir veya daha fazla uygulamayla bir ara yazılım ekleyebilir. Bir ara yazılımı `IStartupFilter` , bir kitaplık tarafından eklenen bir ara yazılımı çağırmak `IStartupFilter`için:

  * Kitaplık hizmet kapsayıcısına eklenmeden önce hizmet kaydını konumlandırın.
  * Daha sonra çağırmak için, kitaplık eklendikten sonra hizmet kaydını konumlandırın.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Başlangıçta bir dış derlemeden yapılandırma Ekle

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Uygulama, uygulamanın `Startup` sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Ek kaynaklar

* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Başlangıç sınıfı

ASP.NET Core uygulamalar, kural `Startup` tarafından adlandırılan `Startup` bir sınıfı kullanır. `Startup` sınıfı:

* İsteğe bağlı olarak <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> , uygulamanın *hizmetlerini*yapılandırmak için bir yöntem içerir. Hizmet, uygulama işlevselliği sağlayan yeniden kullanılabilir bir bileşendir. Hizmetler, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) veya <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>ile uygulama *genelinde kullanılır.* `ConfigureServices`
* Uygulamanın istek <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> işleme ardışık düzenini oluşturmak için bir yöntem içerir.

`ConfigureServices`ve `Configure` uygulama başlatıldığında ASP.NET Core çalışma zamanı tarafından çağrılır:

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

`Startup` Sınıf, uygulamanın [ana makinesi](xref:fundamentals/index#host) yapılandırıldığında belirtilir. Sınıf genellikle konak Oluşturucu 'da [Webhostbuilderextensions. usestartup\<tstartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) yöntemi çağırarak belirtilir: `Startup`

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

Ana bilgisayar, `Startup` sınıf oluşturucusunun kullanabildiği hizmetleri sağlar. Uygulama aracılığıyla `ConfigureServices`ek hizmetler ekler. Hem konak hem de uygulama Hizmetleri uygulama içinde ve üzerinde `Configure` kullanılabilir.

Sınıfa bağımlılık ekleme 'nin yaygın bir kullanımı, şu ekleme yapmak için kullanılır: [dependency injection](xref:fundamentals/dependency-injection) `Startup`

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>Hizmetleri ortama göre yapılandırmak için.
* <xref:Microsoft.Extensions.Configuration.IConfiguration>yapılandırmasını okuyun.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory>içinde `Startup.ConfigureServices`bir günlükçü oluşturmak için.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

Çoğu hizmet, `Configure` Yöntem çağrılana kadar kullanılabilir değildir.

### <a name="multiple-startup"></a>Çoklu başlangıç

Uygulama farklı ortamlar için ayrı `Startup` sınıflar tanımladığında (örneğin, `StartupDevelopment`), çalışma zamanında uygun `Startup` sınıf seçilir. Geçerli ortamla eşleşen ad sonekine sahip olan sınıf önceliklendirilir. Uygulama geliştirme ortamında çalıştırıldıysanız ve hem `Startup` sınıf hem de `StartupDevelopment` sınıf içeriyorsa, `StartupDevelopment` sınıfı kullanılır. Daha fazla bilgi için bkz. [birden çok ortam kullanma](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Konak hakkında daha fazla bilgi için [konağa](xref:fundamentals/index#host) bakın. Başlatma sırasında hataları işleme hakkında daha fazla bilgi için bkz. [Başlangıç özel durum işleme](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>ConfigureServices yöntemi

<xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> Yöntemi:

* İsteğe bağlı.
* Uygulamanın hizmetlerini yapılandırma `Configure` yönteminden önce ana bilgisayar tarafından çağırılır.
* [Yapılandırma seçeneklerinin](xref:fundamentals/configuration/index) kurala göre ayarlandığı yer.

Konak, Yöntemler çağrılmadan önce `Startup` bazı hizmetleri yapılandırabilir. Daha fazla bilgi için bkz. [ana bilgisayar](xref:fundamentals/index#host).

Önemli kurulum gerektiren özellikler için üzerinde `Add{Service}` <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>uzantı yöntemleri vardır. Örneğin, DbContext **ekleyin** **, defaultıdentity ekleyin,** entityframeworkmağazalarını **ekleyin ve**RazorPages **ekleyin**:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

Hizmet kapsayıcısına hizmet eklemek, bunları uygulama içinde ve `Configure` yönteminde kullanılabilir hale getirir. Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection) veya konumundan <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>çözümlenir.

Hakkında `SetCompatibilityVersion`daha fazla bilgi için bkz. [setcompatibilityversion](xref:mvc/compatibility-version) .

## <a name="the-configure-method"></a>Configure yöntemi

<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> Yöntemi, uygulamanın http isteklerine nasıl yanıt verdiğini belirtmek için kullanılır. İstek ardışık düzeni, bir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> örneğe [Ara yazılım](xref:fundamentals/middleware/index) bileşenleri eklenerek yapılandırılır. `IApplicationBuilder`, `Configure` yöntemi için kullanılabilir, ancak hizmet kapsayıcısına kayıtlı değildir. Barındırma bir `IApplicationBuilder` oluşturur ve doğrudan öğesine `Configure`geçirir.

[ASP.NET Core şablonlar](/dotnet/core/tools/dotnet-new) işlem hattını desteğiyle birlikte yapılandırır:

* [Geliştirici özel durum sayfası](xref:fundamentals/error-handling#developer-exception-page)
* [Özel durum işleyicisi](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP katı taşıma güvenliği (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [HTTPS yönlendirmesi](xref:security/enforcing-ssl)
* [Statik dosyalar](xref:fundamentals/static-files)
* [MVC](xref:mvc/overview) ve [ Razor sayfaları](xref:razor-pages/index) ASP.NET Core
* [Genel Veri Koruma Yönetmeliği (GDPR)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Her `Use` genişletme yöntemi, istek ardışık düzenine bir veya daha fazla ara yazılım bileşeni ekler. Örneğin, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> [ara yazılımı](xref:fundamentals/middleware/index) [statik dosyaları](xref:fundamentals/static-files)sunacak şekilde yapılandırır.

İstek ardışık düzeninde bulunan her bir ara yazılım bileşeni, uygun olduğunda, zincirdeki bir sonraki bileşeni çağırmaktan veya zincirde kısa bir süre sonra sağlanmasından sorumludur.

`IHostingEnvironment` Ve `ILoggerFactory`gibi ek hizmetler veya ' de `ConfigureServices`tanımlı herhangi bir şey, `Configure` Yöntem imzasında belirtilebilir. Bu hizmetler varsa eklenir.

Kullanımı `IApplicationBuilder` ve ara yazılım işleme sırası hakkında daha fazla bilgi için bkz <xref:fundamentals/middleware/index>..

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Hizmetleri başlatmadan yapılandırma

Hizmetleri ve istek işleme işlem hattını, ana bilgisayar Oluşturucu üzerinde `Startup` bir sınıf, `ConfigureServices` çağrı `Configure` ve kullanışlı yöntemler kullanmadan yapılandırmak için. Birbirine eklenecek birden `ConfigureServices` çok çağrı. Birden çok `Configure` yöntem varsa, son `Configure` çağrı kullanılır.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Başlangıç filtreleriyle başlatmayı Genişlet

Şunu <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>kullanın:

* Bir uygulamanın başlangıcında veya sonunda, açık bir çağrı olmadan ara yazılım [yapılandırma](#the-configure-method) ardışık düzenini yapılandırmak için `Use{Middleware}`. `IStartupFilter`ASP.NET Core tarafından, uygulama yazarının varsayılan ara yazılımı açıkça kaydetmesi gerekmeden, işlem hattının başlangıcına varsayılanlar eklemek için kullanılır. `IStartupFilter`Uygulama yazarı adına farklı bir `Use{Middleware}` bileşen çağrısının yapılmasına izin verir.
* `Configure` Yöntem işlem hattı oluşturmak için. [Itartupfilter. configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) , bir ara yazılımı kitaplıklar tarafından eklenen bir veya daha sonra çalışacak şekilde ayarlayabilir.

`IStartupFilter`<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>öğesini alır ve döndürür `Action<IApplicationBuilder>`. Bir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> uygulamanın istek ardışık düzenini yapılandırmak için bir sınıf tanımlar. Daha fazla bilgi için bkz. [IApplicationBuilder ile bir ara yazılım işlem hattı oluşturma](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Her `IStartupFilter` biri, istek ardışık düzeninde bir veya daha fazla middlewares ekleyebilir. Filtreler, hizmet kapsayıcısına eklendikleri sırada çağrılır. Filtreler bir sonraki filtreye denetimi geçirmeden önce veya sonra bir ara yazılım ekleyebilir, böylece uygulama işlem hattının başına veya sonuna eklenir.

Aşağıdaki örnek, ile `IStartupFilter`bir ara yazılımın nasıl kaydettirildiğini gösterir. `RequestSetOptionsMiddleware` Ara yazılım bir sorgu dizesi parametresinden bir seçenek değeri ayarlar:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

, `RequestSetOptionsMiddleware` `RequestSetOptionsStartupFilter` Sınıfında yapılandırılır:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

, `IStartupFilter` İçindeki <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>hizmet kapsayıcısına kaydedilir.

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

İçin `option` bir sorgu dizesi parametresi sağlandığında, ara yazılım ASP.NET Core ara yazılım tarafından yanıt oluşturmadan önce değer atamasını işler.

Ara yazılım yürütme sırası, `IStartupFilter` kayıt sırasıyla ayarlanır:

* Birden `IStartupFilter` çok uygulama aynı nesnelerle etkileşime geçebilir. Sıralama önemliyse, `IStartupFilter` hizmet kayıtlarını middlewares 'in çalıştırması gereken sırayla eşleşecek şekilde sıralayın.
* Kitaplıklar, ile `IStartupFilter`kaydolmadan önce veya sonra `IStartupFilter` çalışan bir veya daha fazla uygulamayla bir ara yazılım ekleyebilir. Bir ara yazılımı `IStartupFilter` , bir kitaplık tarafından eklenen bir ara yazılımı çağırmak `IStartupFilter`için:

  * Kitaplık hizmet kapsayıcısına eklenmeden önce hizmet kaydını konumlandırın.
  * Daha sonra çağırmak için, kitaplık eklendikten sonra hizmet kaydını konumlandırın.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Başlangıçta bir dış derlemeden yapılandırma Ekle

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Uygulama, uygulamanın `Startup` sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Ek kaynaklar

* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end