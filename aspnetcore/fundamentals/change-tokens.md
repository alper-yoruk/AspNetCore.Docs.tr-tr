---
title: ASP.NET Core'daki değişiklik belirteçleriyle değişiklikleri algılama
author: rick-anderson
description: Değişiklikleri izlemek için değişiklik belirteçlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 70451e219f1295b854e2f84aac55f0cfd1786b19
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656347"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>ASP.NET Core'daki değişiklik belirteçleriyle değişiklikleri algılama

::: moniker range=">= aspnetcore-3.0"

*Değişiklik belirteci,* durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzeyli bir yapı taşıdır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>IChangeToken arabirimi

<xref:Microsoft.Extensions.Primitives.IChangeToken>bir değişikliğin meydana geldiğini bildirimleri yayır. `IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur. [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi ASP.NET Core uygulamalarına dolaylı olarak sağlanır.

`IChangeToken`iki özelliği vardır:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>belirteç leri proaktif olarak geri aramaları yükseltip yükseltmeyin. `ActiveChangedCallbacks` Ayarlanmışsa, geri arama asla çağrılmez ve uygulamanın değişiklikler için anket `HasChanged` yapması `false`gerekir. Bir belirteci hiçbir değişiklik meydana gelmezse veya temel değişiklik dinleyicisi imha edilirse veya devre dışı bırakılırsa asla iptal edilmeyebilir.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>bir değişiklik gerçekleşip gerçekleşmediğini gösteren bir değer alır.

Arabirim, belirteç değiştiğinde çağrılan bir geri aramayı kaydeden [RegisterChangeCallback (Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir. `IChangeToken` `HasChanged`geri arama çağrılmadan önce ayarlanmalıdır.

## <a name="changetoken-class"></a>ChangeToken sınıfı

<xref:Microsoft.Extensions.Primitives.ChangeToken>bir değişikliğin meydana geldiği bildirimleri yaymak için kullanılan statik bir sınıftır. `ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur. [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi ASP.NET Core uygulamalarına dolaylı olarak sağlanır.

[ChangeToken.OnChange(Func\<IChangeToken>, Eylem)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi `Action` belirteç değiştiğinde aramak için bir kayıt:

* `Func<IChangeToken>`belirteci üretir.
* `Action`belirteç değiştiğinde çağrılır.

[ChangeToken.OnChange\<TState\<>(Func IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yük belirteç tüketici `TState` `Action`içine geçti ek bir parametre alır.

`OnChange`bir <xref:System.IDisposable>' yi döndürür. Arama, <xref:System.IDisposable.Dispose*> belirtecinin daha fazla değişiklik için dinlemesini durdurur ve belirteç kaynaklarını serbest bırakır.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>ASP.NET Core'daki değişiklik belirteçlerinin örnek kullanımları

Değişiklik belirteçleri, nesnelerdeki değişiklikleri izlemek için ASP.NET Core'un önemli alanlarında kullanılır:

* Dosyalardaki değişiklikleri izlemek <xref:Microsoft.Extensions.FileProviders.IFileProvider>için, <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 'ın `IChangeToken` yöntemi, belirtilen dosyaların veya klasörün izlemesi için bir yöntem oluşturur.
* `IChangeToken`belirteçleri, değişiklik üzerindeki önbellek tahliyelerini tetiklemek için önbellek girişlerine eklenebilir.
* Değişiklikler `TOptions` için varsayılan <xref:Microsoft.Extensions.Options.OptionsMonitor`1> uygulama, bir veya daha fazla <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> örneği kabul eden aşırı yüklemeye sahiptir. Her örnek, `IChangeToken` izleme seçenekleri değişiklikleri için bir değişiklik bildirimi geri araması kaydetmek için bir geri döndürür.

## <a name="monitor-for-configuration-changes"></a>Yapılandırma değişiklikleri için monitör

Varsayılan olarak, ASP.NET Core şablonları [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) *(appsettings.json*, *appsettings) kullanır. Development.json*ve *uygulama ayarları. Production.json*) uygulama yapılandırma ayarlarını yüklemek için.

Bu dosyalar [addjsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) uzantı yöntemi <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> `reloadOnChange` kullanılarak yapılandırılır. `reloadOnChange`dosya değişikliklerinde yapılandırmanın yeniden yüklenmesi gerekiyorsa gösterir. Bu ayar <xref:Microsoft.Extensions.Hosting.Host> kolaylık yönteminde <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>görünür:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Dosya tabanlı yapılandırma <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource`dosyaları <xref:Microsoft.Extensions.FileProviders.IFileProvider> izlemek için kullanır.

Varsayılan olarak, `IFileMonitor` yapılandırma dosyası <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>değişiklikleri <xref:System.IO.FileSystemWatcher> için izlemek için kullanan bir , tarafından sağlanır.

Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir. Herhangi bir *uygulama ayarları* dosyaları değişirse, dosya izleme uygulamalarının her ikisi de özel kod&mdash;yürütmek örnek uygulama konsola bir mesaj yazar.

Yapılandırma dosyası, `FileSystemWatcher` tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri aramasını tetikleyebilir. Özel kodun birden çok belirteç geri araması tetiklendiğinde yalnızca bir kez çalıştırıldığından emin olmak için, örneğin uygulama dosyası işlenir. Örnek SHA1 dosya karma kullanır. Bir yeniden deneme üstel geri leme ile uygulanır. Dosya kilitleme, dosyaüzerinde yeni bir karmanın bilgisayara kullanımını geçici olarak engelleyen oluşabilir, çünkü yeniden deneme vardır.

*Yardımcı Programlar/Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Basit başlangıç değişikliği belirteci

Yapılandırma yeniden yükleme `Action` belirteci değişiklik bildirimleri için bir belirteç tüketici geri arama kaydedin.

`Startup.Configure` içinde:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`belirteci sağlar. Geri arama `InvokeChanged` yöntemidir:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Geri arama, izlemek için doğru `IWebHostEnvironment`uygulama *ayarları* yapılandırma dosyasını (örneğin, uygulama ayarları) belirtmek için yararlı olan , içinde geçmek için kullanılır. `state` * Development.json* zaman Geliştirme ortamında). Dosya işlenmeleri, yapılandırma `WriteConsole` dosyası yalnızca bir kez değiştiğinde birden çok belirteç geri araması nedeniyle deyimin birden çok kez çalışmasını önlemek için kullanılır.

Bu sistem, uygulama çalıştığı sürece çalışır ve kullanıcı tarafından devre dışı tutulamaz.

### <a name="monitor-configuration-changes-as-a-service"></a>Hizmet olarak yapılandırma değişikliklerini izleme

Örnek uygular:

* Temel başlangıç belirteç izleme.
* Bir hizmet olarak izleme.
* İzlemeyi etkinleştirmek ve devre dışı kılabilir bir mekanizma.

Örnek bir `IConfigurationMonitor` arayüz kurar.

*Uzantılar/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Uygulanan sınıfın oluşturucusu, `ConfigurationMonitor`değişiklik bildirimleri için bir geri arama kaydeder:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`belirteci sağlar. `InvokeChanged`geri arama yöntemidir. Bu `state` örnekte, izleme durumuna `IConfigurationMonitor` erişmek için kullanılan örneğin başvurusudur. İki özellik kullanılır:

* `MonitoringEnabled`&ndash; Geri aramanın özel kodunu çalıştırıp çalıştırmaması gerektiğini gösterir.
* `CurrentState`&ndash; Kullanıcı Arabirimi'nde kullanılmak üzere geçerli izleme durumunu açıklar.

Yöntem, `InvokeChanged` önceki yaklaşıma benzer, ancak bu yöntem:

* Kodu çalışmadığı sürece `MonitoringEnabled` `true`çalışmaz.
* `WriteConsole` Çıktısındaki akımı `state` çıkar.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Bir `ConfigurationMonitor` örnek bir hizmet `Startup.ConfigureServices`olarak kaydedilir:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Dizin sayfası yapılandırma izleme üzerinde kullanıcı denetimi sunar. Örneği içine `IConfigurationMonitor` enjekte `IndexModel`edilir.

*Sayfalar/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Yapılandırma monitörü`_monitor`( ) izlemeyi etkinleştirmek veya devre dışı kalmak ve Kullanıcı Arabirimi geri bildirimi için geçerli durumu ayarlamak için kullanılır:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

`OnPostStartMonitoring` Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir. `OnPostStopMonitoring` Tetiklendiğinde, izleme devre dışı bırakılır ve durum izlemenin oluşmadığını yansıtacak şekilde ayarlanır.

Kullanıcı UI'deki düğmeler izlemeyi etkinleştiriyor ve devre dışı bıtır.

*Sayfalar/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Önbelleğe alınmış dosya değişikliklerini izleme

Dosya içeriği bellekte önbelleğe <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>alınabilir. Bellek içi önbelleğe alma, [Bellek tesisi](xref:performance/caching/memory) başlığında açıklanır. Aşağıda açıklanan uygulama gibi ek adımlar atmadan, kaynak veri değişirse *eski* (eski) veriler önbellekten döndürülür.

Örneğin, [sürgülü](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) bir son kullanma süresini yenilerken önbelleğe alınmış bir kaynak dosyanın durumunu hesaba katmamak, eski önbelleğe alınmış dosya verilerine yol açar. Veriler için her istek kayan son kullanma süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez. Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, muhtemelen eski içeriğe tabidir.

Dosya önbelleğe alma senaryosunda değişiklik belirteçleri kullanmak önbellekte eski dosya içeriğinin bulunmasını engeller. Örnek uygulama, yaklaşımın bir uygulamasını gösterir.

Örnek için `GetFileContent` kullanır:

* Dosya içeriğini iade edin.
* Dosya kilidinin dosyanın okunmasını geçici olarak engellediği durumları kapsayacak şekilde üstel yedekleme içeren bir yeniden deneme algoritması uygulayın.

*Yardımcı Programlar/Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Önbelleğe alınmış dosya aramalarını işlemek için a `FileService` oluşturulur. Hizmetin `GetFileContent` yöntem çağrısı, bellek içi önbellekten dosya içeriğini elde etmeye ve arayana *(Hizmetler/FileService.cs)* döndürmeye çalışır.

Önbelleğe alınan içerik önbellek anahtarı kullanılarak bulunmazsa, aşağıdaki işlemler yapılır:

1. Dosya içeriği kullanılarak `GetFileContent`elde edilir.
1. Bir değişiklik belirteci [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile dosya sağlayıcısından elde edilir. Dosya değiştirildiğinde belirteç geri arama tetiklenir.
1. Dosya içeriği sürgülü bir [son kullanma](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alındı. Dosya önbelleğe alınmışken değişirse önbellek girişini çıkarmak için [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte değişiklik belirteci eklenir.

Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)depolanır. `IWebHostEnvironment.ContentRootFileProvider`uygulamanın bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> işaret elde etmek `IWebHostEnvironment.ContentRootPath`için kullanılır. `filePath` [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

Bellek `FileService` önbelleğe alma hizmeti ile birlikte servis konteynerinde kayıtlıdır.

`Startup.ConfigureServices` içinde:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.

Dizin sayfasının `OnGet` yönteminde (*Pages/Index.cshtml.cs):*

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken sınıfı

Tek bir nesnede `IChangeToken` bir veya daha fazla <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> örneği temsil etmek için sınıfı kullanın.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged`temsil edilen herhangi `true` bir belirteç `HasChanged` ise `true`bileşik belirteç raporları. `ActiveChangeCallbacks`temsil edilen herhangi `true` bir belirteç `ActiveChangeCallbacks` ise `true`bileşik belirteç raporları. Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri arama sı bir kez çağrılır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Değişiklik belirteci,* durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzeyli bir yapı taşıdır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>IChangeToken arabirimi

<xref:Microsoft.Extensions.Primitives.IChangeToken>bir değişikliğin meydana geldiğini bildirimleri yayır. `IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur. [Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app)kullanmayan uygulamalar [için, Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.

`IChangeToken`iki özelliği vardır:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>belirteç leri proaktif olarak geri aramaları yükseltip yükseltmeyin. `ActiveChangedCallbacks` Ayarlanmışsa, geri arama asla çağrılmez ve uygulamanın değişiklikler için anket `HasChanged` yapması `false`gerekir. Bir belirteci hiçbir değişiklik meydana gelmezse veya temel değişiklik dinleyicisi imha edilirse veya devre dışı bırakılırsa asla iptal edilmeyebilir.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>bir değişiklik gerçekleşip gerçekleşmediğini gösteren bir değer alır.

Arabirim, belirteç değiştiğinde çağrılan bir geri aramayı kaydeden [RegisterChangeCallback (Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir. `IChangeToken` `HasChanged`geri arama çağrılmadan önce ayarlanmalıdır.

## <a name="changetoken-class"></a>ChangeToken sınıfı

<xref:Microsoft.Extensions.Primitives.ChangeToken>bir değişikliğin meydana geldiği bildirimleri yaymak için kullanılan statik bir sınıftır. `ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur. [Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app)kullanmayan uygulamalar [için, Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.

[ChangeToken.OnChange(Func\<IChangeToken>, Eylem)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi `Action` belirteç değiştiğinde aramak için bir kayıt:

* `Func<IChangeToken>`belirteci üretir.
* `Action`belirteç değiştiğinde çağrılır.

[ChangeToken.OnChange\<TState\<>(Func IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yük belirteç tüketici `TState` `Action`içine geçti ek bir parametre alır.

`OnChange`bir <xref:System.IDisposable>' yi döndürür. Arama, <xref:System.IDisposable.Dispose*> belirtecinin daha fazla değişiklik için dinlemesini durdurur ve belirteç kaynaklarını serbest bırakır.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>ASP.NET Core'daki değişiklik belirteçlerinin örnek kullanımları

Değişiklik belirteçleri, nesnelerdeki değişiklikleri izlemek için ASP.NET Core'un önemli alanlarında kullanılır:

* Dosyalardaki değişiklikleri izlemek <xref:Microsoft.Extensions.FileProviders.IFileProvider>için, <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 'ın `IChangeToken` yöntemi, belirtilen dosyaların veya klasörün izlemesi için bir yöntem oluşturur.
* `IChangeToken`belirteçleri, değişiklik üzerindeki önbellek tahliyelerini tetiklemek için önbellek girişlerine eklenebilir.
* Değişiklikler `TOptions` için varsayılan <xref:Microsoft.Extensions.Options.OptionsMonitor`1> uygulama, bir veya daha fazla <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> örneği kabul eden aşırı yüklemeye sahiptir. Her örnek, `IChangeToken` izleme seçenekleri değişiklikleri için bir değişiklik bildirimi geri araması kaydetmek için bir geri döndürür.

## <a name="monitor-for-configuration-changes"></a>Yapılandırma değişiklikleri için monitör

Varsayılan olarak, ASP.NET Core şablonları [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) *(appsettings.json*, *appsettings) kullanır. Development.json*ve *uygulama ayarları. Production.json*) uygulama yapılandırma ayarlarını yüklemek için.

Bu dosyalar [addjsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) uzantı yöntemi <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> `reloadOnChange` kullanılarak yapılandırılır. `reloadOnChange`dosya değişikliklerinde yapılandırmanın yeniden yüklenmesi gerekiyorsa gösterir. Bu ayar <xref:Microsoft.AspNetCore.WebHost> kolaylık yönteminde <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>görünür:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Dosya tabanlı yapılandırma <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource`dosyaları <xref:Microsoft.Extensions.FileProviders.IFileProvider> izlemek için kullanır.

Varsayılan olarak, `IFileMonitor` yapılandırma dosyası <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>değişiklikleri <xref:System.IO.FileSystemWatcher> için izlemek için kullanan bir , tarafından sağlanır.

Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir. Herhangi bir *uygulama ayarları* dosyaları değişirse, dosya izleme uygulamalarının her ikisi de özel kod&mdash;yürütmek örnek uygulama konsola bir mesaj yazar.

Yapılandırma dosyası, `FileSystemWatcher` tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri aramasını tetikleyebilir. Özel kodun birden çok belirteç geri araması tetiklendiğinde yalnızca bir kez çalıştırıldığından emin olmak için, örneğin uygulama dosyası işlenir. Örnek SHA1 dosya karma kullanır. Bir yeniden deneme üstel geri leme ile uygulanır. Dosya kilitleme, dosyaüzerinde yeni bir karmanın bilgisayara kullanımını geçici olarak engelleyen oluşabilir, çünkü yeniden deneme vardır.

*Yardımcı Programlar/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Basit başlangıç değişikliği belirteci

Yapılandırma yeniden yükleme `Action` belirteci değişiklik bildirimleri için bir belirteç tüketici geri arama kaydedin.

`Startup.Configure` içinde:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`belirteci sağlar. Geri arama `InvokeChanged` yöntemidir:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Geri arama, izlemek için doğru `IHostingEnvironment`uygulama *ayarları* yapılandırma dosyasını (örneğin, uygulama ayarları) belirtmek için yararlı olan , içinde geçmek için kullanılır. `state` * Development.json* zaman Geliştirme ortamında). Dosya işlenmeleri, yapılandırma `WriteConsole` dosyası yalnızca bir kez değiştiğinde birden çok belirteç geri araması nedeniyle deyimin birden çok kez çalışmasını önlemek için kullanılır.

Bu sistem, uygulama çalıştığı sürece çalışır ve kullanıcı tarafından devre dışı tutulamaz.

### <a name="monitor-configuration-changes-as-a-service"></a>Hizmet olarak yapılandırma değişikliklerini izleme

Örnek uygular:

* Temel başlangıç belirteç izleme.
* Bir hizmet olarak izleme.
* İzlemeyi etkinleştirmek ve devre dışı kılabilir bir mekanizma.

Örnek bir `IConfigurationMonitor` arayüz kurar.

*Uzantılar/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Uygulanan sınıfın oluşturucusu, `ConfigurationMonitor`değişiklik bildirimleri için bir geri arama kaydeder:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`belirteci sağlar. `InvokeChanged`geri arama yöntemidir. Bu `state` örnekte, izleme durumuna `IConfigurationMonitor` erişmek için kullanılan örneğin başvurusudur. İki özellik kullanılır:

* `MonitoringEnabled`&ndash; Geri aramanın özel kodunu çalıştırıp çalıştırmaması gerektiğini gösterir.
* `CurrentState`&ndash; Kullanıcı Arabirimi'nde kullanılmak üzere geçerli izleme durumunu açıklar.

Yöntem, `InvokeChanged` önceki yaklaşıma benzer, ancak bu yöntem:

* Kodu çalışmadığı sürece `MonitoringEnabled` `true`çalışmaz.
* `WriteConsole` Çıktısındaki akımı `state` çıkar.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Bir `ConfigurationMonitor` örnek bir hizmet `Startup.ConfigureServices`olarak kaydedilir:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Dizin sayfası yapılandırma izleme üzerinde kullanıcı denetimi sunar. Örneği içine `IConfigurationMonitor` enjekte `IndexModel`edilir.

*Sayfalar/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Yapılandırma monitörü`_monitor`( ) izlemeyi etkinleştirmek veya devre dışı kalmak ve Kullanıcı Arabirimi geri bildirimi için geçerli durumu ayarlamak için kullanılır:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

`OnPostStartMonitoring` Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir. `OnPostStopMonitoring` Tetiklendiğinde, izleme devre dışı bırakılır ve durum izlemenin oluşmadığını yansıtacak şekilde ayarlanır.

Kullanıcı UI'deki düğmeler izlemeyi etkinleştiriyor ve devre dışı bıtır.

*Sayfalar/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Önbelleğe alınmış dosya değişikliklerini izleme

Dosya içeriği bellekte önbelleğe <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>alınabilir. Bellek içi önbelleğe alma, [Bellek tesisi](xref:performance/caching/memory) başlığında açıklanır. Aşağıda açıklanan uygulama gibi ek adımlar atmadan, kaynak veri değişirse *eski* (eski) veriler önbellekten döndürülür.

Örneğin, [sürgülü](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) bir son kullanma süresini yenilerken önbelleğe alınmış bir kaynak dosyanın durumunu hesaba katmamak, eski önbelleğe alınmış dosya verilerine yol açar. Veriler için her istek kayan son kullanma süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez. Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, muhtemelen eski içeriğe tabidir.

Dosya önbelleğe alma senaryosunda değişiklik belirteçleri kullanmak önbellekte eski dosya içeriğinin bulunmasını engeller. Örnek uygulama, yaklaşımın bir uygulamasını gösterir.

Örnek için `GetFileContent` kullanır:

* Dosya içeriğini iade edin.
* Dosya kilidinin dosyanın okunmasını geçici olarak engellediği durumları kapsayacak şekilde üstel yedekleme içeren bir yeniden deneme algoritması uygulayın.

*Yardımcı Programlar/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Önbelleğe alınmış dosya aramalarını işlemek için a `FileService` oluşturulur. Hizmetin `GetFileContent` yöntem çağrısı, bellek içi önbellekten dosya içeriğini elde etmeye ve arayana *(Hizmetler/FileService.cs)* döndürmeye çalışır.

Önbelleğe alınan içerik önbellek anahtarı kullanılarak bulunmazsa, aşağıdaki işlemler yapılır:

1. Dosya içeriği kullanılarak `GetFileContent`elde edilir.
1. Bir değişiklik belirteci [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile dosya sağlayıcısından elde edilir. Dosya değiştirildiğinde belirteç geri arama tetiklenir.
1. Dosya içeriği sürgülü bir [son kullanma](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alındı. Dosya önbelleğe alınmışken değişirse önbellek girişini çıkarmak için [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte değişiklik belirteci eklenir.

Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)depolanır. [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) uygulamanın bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> işaret elde etmek <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>için kullanılır. `filePath` [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

Bellek `FileService` önbelleğe alma hizmeti ile birlikte servis konteynerinde kayıtlıdır.

`Startup.ConfigureServices` içinde:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.

Dizin sayfasının `OnGet` yönteminde (*Pages/Index.cshtml.cs):*

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken sınıfı

Tek bir nesnede `IChangeToken` bir veya daha fazla <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> örneği temsil etmek için sınıfı kullanın.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged`temsil edilen herhangi `true` bir belirteç `HasChanged` ise `true`bileşik belirteç raporları. `ActiveChangeCallbacks`temsil edilen herhangi `true` bir belirteç `ActiveChangeCallbacks` ise `true`bileşik belirteç raporları. Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri arama sı bir kez çağrılır.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
