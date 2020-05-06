---
title: ASP.NET Core değişiklik belirteçleriyle değişiklikleri Algıla
author: rick-anderson
description: Değişiklikleri izlemek için değişiklik belirteçlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/change-tokens
ms.openlocfilehash: 40868c57507989e1d3040df2cbe2feb4871d4394
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774801"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>ASP.NET Core değişiklik belirteçleriyle değişiklikleri Algıla

::: moniker range=">= aspnetcore-3.0"

*Değişiklik belirteci* , durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzey bir yapı taşıdır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Ichangetoken arabirimi

<xref:Microsoft.Extensions.Primitives.IChangeToken>bir değişikliğin gerçekleştiği bildirimleri yayar. `IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur. [Microsoft. Extensions. Ilkel öğeler](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi, ASP.NET Core uygulamalarına örtük olarak sağlanır.

`IChangeToken`iki özelliğe sahiptir:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>belirtecin etkin olup geri çağırmaları harekete geçirmediğini belirtir. `ActiveChangedCallbacks` Olarak ayarlanırsa, bir `false`geri çağırma hiçbir şekilde çağrılmaz ve uygulamanın değişiklikleri yoklamalıdır `HasChanged` . Hiçbir değişiklik gerçekleşmüyorsa veya temeldeki değişiklik dinleyicisi atıldığı veya devre dışı bırakıldığında belirtecin hiçbir şekilde iptal edilmemesi de mümkündür.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>bir değişikliğin oluşup gerçekleşmediğini gösteren bir değer alır.

Arabirim, belirteç değiştirildiğinde çağrılan bir geri aramayı kaydeden [Registerchangecallback (Action\<nesnesi>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir. `IChangeToken` `HasChanged`geri çağırma çağrılmadan önce ayarlanmalıdır.

## <a name="changetoken-class"></a>ChangeToken sınıfı

<xref:Microsoft.Extensions.Primitives.ChangeToken>, bir değişikliğin gerçekleştiği bildirimleri yaymak için kullanılan statik bir sınıftır. `ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur. [Microsoft. Extensions. Ilkel öğeler](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi, ASP.NET Core uygulamalarına örtük olarak sağlanır.

[ChangeToken. OnChange (Func\<IChannel>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi, belirtecin her değiştirişinde bir `Action` çağrı kaydeder:

* `Func<IChangeToken>`belirteci üretir.
* `Action`belirteç değiştiğinde çağrılır.

[ChangeToken. OnChange\<TState> (Func\<IChannel>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yüklemesi, belirteç tüketicisine `Action`geçirilen `TState` ek bir parametre alır.

`OnChange`döndürür <xref:System.IDisposable>. Çağırma <xref:System.IDisposable.Dispose*> , belirteci daha fazla değişiklik için dinlemeyi durdurup belirtecin kaynaklarını serbest bırakır.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>ASP.NET Core değişiklik belirteçlerinin örnek kullanımları

Değişiklik belirteçleri, nesnelerde yapılan değişiklikleri izlemek için ASP.NET Core belirgin alanlarında kullanılır:

* Dosyalarda yapılan değişiklikleri izlemek için, <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> yöntemi belirtilen dosya veya `IChangeToken` klasör için bir oluşturur.
* `IChangeToken`değişiklik üzerine önbellek çıkarmaları tetiklemek için, belirteç önbellek girişlerine eklenebilir.
* Değişiklikler `TOptions` için, varsayılan <xref:Microsoft.Extensions.Options.OptionsMonitor`1> uygulamasının bir veya <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> daha fazla <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> örnek kabul eden bir aşırı yüklemesi vardır. Her örnek, izleme `IChangeToken` seçenekleri değişiklikleri için değişiklik bildirimi geri aramasını kaydetmek üzere bir döndürür.

## <a name="monitor-for-configuration-changes"></a>Yapılandırma değişikliklerini izle

Varsayılan olarak, ASP.NET Core şablonlar [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, appSettings) kullanır *. Development. JSON*ve *appSettings. Üretim. JSON*), uygulama yapılandırma ayarlarını yükler.

Bu dosyalar, üzerinde <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> bir `reloadOnChange` parametre kabul eden [Addjsonfile (IController, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) genişletme yöntemi kullanılarak yapılandırılır. `reloadOnChange`yapılandırmanın dosya değişikliklerinde yeniden yüklenmesi gerekip gerekmediğini gösterir. Bu ayar <xref:Microsoft.Extensions.Hosting.Host> kolaylık yönteminde <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>görünür:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Dosya tabanlı yapılandırma tarafından <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>temsil edilir. `FileConfigurationSource`dosyalarını <xref:Microsoft.Extensions.FileProviders.IFileProvider> izlemek için kullanır.

Varsayılan `IFileMonitor` olarak, yapılandırma dosyası değişikliklerini izlemek için <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>kullanılan <xref:System.IO.FileSystemWatcher> bir tarafından sağlanır.

Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir. *AppSettings* dosyalarından herhangi biri değiştiğinde, dosya izleme uygulamalarının her ikisi de özel kod&mdash;yürütür örnek uygulama konsola bir ileti yazar.

Bir yapılandırma dosyası `FileSystemWatcher` , tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri çağırmaları tetikleyebilir. Özel kodun, birden fazla belirteç geri çağırma işlemi tetiklendiğinde yalnızca bir kez çalıştığından emin olmak için, örnek uygulama dosya karmalarını denetler. Örnek, SHA1 dosya karma kullanır. Bir yeniden deneme, üstel geri dönme ile uygulanır. Dosya kilitlemesi, geçici olarak bir dosyada yeni bir karma işlem yapılmasını önleyen dosya kilitleme gerçekleşebileceğinden, yeniden deneme vardır.

*Yardımcı programlar/yardımcı programlar. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Basit başlangıç değiştirme belirteci

Değişiklik bildirimleri için bir `Action` belirteç tüketicisi geri aramasını yapılandırma yeniden yükleme belirtecine kaydedin.

`Startup.Configure` içinde:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`belirteci sağlar. Geri çağırma `InvokeChanged` yöntemi:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Geri aramanın, izlemek için doğru *appSettings* yapılandırma dosyasını ( `IWebHostEnvironment`Örneğin, appSettings) belirtmek için yararlı olan ' a geçmek için kullanılır. `state` * Geliştirme ortamında geliştirme. JSON* ). Dosya karmaları, bir yapılandırma dosyası yalnızca `WriteConsole` bir kez değiştirildiğinde, daha fazla belirteç geri çağırmaları nedeniyle deyimin birden çok kez çalışmasını engellemek için kullanılır.

Uygulama çalıştığı sürece bu sistem çalışır ve Kullanıcı tarafından devre dışı bırakılamaz.

### <a name="monitor-configuration-changes-as-a-service"></a>Yapılandırma değişikliklerini hizmet olarak izle

Örnek şunları uygular:

* Temel başlangıç belirteci izleme.
* Hizmet olarak izleme.
* İzlemeyi etkinleştirme ve devre dışı bırakma mekanizması.

Örnek bir `IConfigurationMonitor` arabirim oluşturur.

*Uzantılar/ConfigurationMonitor. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Uygulanan sınıfın `ConfigurationMonitor`Oluşturucusu, değişiklik bildirimleri için bir geri çağırma kaydeder:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`belirteci sağlar. `InvokeChanged`geri çağırma yöntemidir. Bu `state` örnekte, izleme durumuna erişmek için kullanılan `IConfigurationMonitor` örneğe bir başvuru vardır. İki özellik kullanılır:

* `MonitoringEnabled`&ndash; Geri aramanın özel kodunu çalıştırıp çalıştırmayacağını gösterir.
* `CurrentState`&ndash; Kullanıcı arabiriminde kullanım için geçerli izleme durumunu açıklar.

`InvokeChanged` Yöntemi önceki yaklaşımla benzerdir, bunun dışında:

* , `MonitoringEnabled` `true`Olmadığı müddetçe kodunu çalıştırmaz.
* `WriteConsole` Çıktıda geçerli `state` olan çıktıyı verir.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Bir örnek `ConfigurationMonitor` , içinde `Startup.ConfigureServices`bir hizmet olarak kaydedilir:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Dizin sayfası, yapılandırma izleme üzerinde Kullanıcı denetimi sağlar. Örneği öğesine `IConfigurationMonitor` eklenir `IndexModel`.

*Pages/Index. cshtml. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Yapılandırma İzleyicisi (`_monitor`), izlemeyi etkinleştirmek veya devre dışı BıRAKMAK ve UI geri bildirimi için geçerli durumu ayarlamak üzere kullanılır:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

`OnPostStartMonitoring` Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir. `OnPostStopMonitoring` Tetiklendiğinde, izleme devre dışıdır ve durum, izlemenin gerçekleşmediğinden emin olmak üzere ayarlanır.

Kullanıcı arabirimindeki düğmeler izlemeyi etkinleştirir ve devre dışı bırakır.

*Sayfa/dizin. cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Önbelleğe alınmış dosya değişikliklerini izle

Dosya içeriği, kullanarak <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>bellek içinde önbelleğe alınabilir. Bellek içi önbelleğe alma, [bellek Içi önbellek](xref:performance/caching/memory) konusunda açıklanmaktadır. Aşağıda açıklanan uygulama gibi ek adımlar uygulamadan, kaynak veriler değişirse önbellekten *eski* (eski) veriler döndürülür.

Örneğin, bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) dönemini yenilerken önbelleğe alınmış bir kaynak dosyanın durumu, eski önbelleğe alınmış dosya verilerine yol açar. Verilerin her isteği, Kayan süre sonu süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez. Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, büyük olasılıkla eski içerik alınmasına tabidir.

Bir dosya önbelleğe alma senaryosunda değişiklik belirteçlerini kullanmak önbellekte eski dosya içeriğinin varlığını engeller. Örnek uygulama, yaklaşımın bir uygulamasını gösterir.

Örnek şu amaçlarla `GetFileContent` kullanılır:

* Dosya içeriğini döndürür.
* Bir dosya kilidinin geçici olarak bir dosya okumayı engellediği durumları kapsamak için üstel geri ile yeniden deneme algoritması uygulayın.

*Yardımcı programlar/yardımcı programlar. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

`FileService` Önbelleğe alınmış dosya aramalarını işlemek için oluşturulur. Hizmetin `GetFileContent` Yöntem çağrısı, bellek içi önbellekten dosya içeriğini almaya çalışır ve bunu çağırana (*Services/FileService. cs*) döndürebilir.

Önbellek anahtarı kullanılarak önbelleğe alınmış içerik bulunamazsa, aşağıdaki eylemler gerçekleştirilir:

1. Dosya içeriği kullanılarak `GetFileContent`elde edilir.
1. Dosya sağlayıcısından [ıfileproviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile bir değişiklik belirteci elde edilir. Dosya değiştirildiğinde belirtecin geri çağırması tetiklenir.
1. Dosya içeriği bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alınır. Değişiklik belirteci, önbelleğe alınmış durumdayken dosya değişirse önbellek girdisini çıkarmak için [Memorycacheentryextensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte eklenir.

Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)saklanır. `IWebHostEnvironment.ContentRootFileProvider`, uygulamanın üzerine gelindiğinde bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> işaret elde etmek için kullanılır `IWebHostEnvironment.ContentRootPath`. , `filePath` [Ifıleınfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

, `FileService` Hizmet kapsayıcısına bellek önbelleği hizmeti ile birlikte kaydedilir.

`Startup.ConfigureServices` içinde:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.

Dizin sayfasının `OnGet` yönteminde (*Pages/Index. cshtml. cs*):

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken sınıfı

Tek bir nesnedeki bir veya `IChangeToken` daha fazla örneği temsil etmek için <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> sınıfını kullanın.

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

`HasChanged`herhangi bir temsil edilen belirteç `true` `HasChanged` varsa bileşik belirteç raporlarında `true`. `ActiveChangeCallbacks`herhangi bir temsil edilen belirteç `true` `ActiveChangeCallbacks` varsa bileşik belirteç raporlarında `true`. Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri çağırması bir kez çağrılır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Değişiklik belirteci* , durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzey bir yapı taşıdır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Ichangetoken arabirimi

<xref:Microsoft.Extensions.Primitives.IChangeToken>bir değişikliğin gerçekleştiği bildirimleri yayar. `IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur. [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmayan uygulamalar Için, [Microsoft. Extensions. ilkel](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.

`IChangeToken`iki özelliğe sahiptir:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>belirtecin etkin olup geri çağırmaları harekete geçirmediğini belirtir. `ActiveChangedCallbacks` Olarak ayarlanırsa, bir `false`geri çağırma hiçbir şekilde çağrılmaz ve uygulamanın değişiklikleri yoklamalıdır `HasChanged` . Hiçbir değişiklik gerçekleşmüyorsa veya temeldeki değişiklik dinleyicisi atıldığı veya devre dışı bırakıldığında belirtecin hiçbir şekilde iptal edilmemesi de mümkündür.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>bir değişikliğin oluşup gerçekleşmediğini gösteren bir değer alır.

Arabirim, belirteç değiştirildiğinde çağrılan bir geri aramayı kaydeden [Registerchangecallback (Action\<nesnesi>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir. `IChangeToken` `HasChanged`geri çağırma çağrılmadan önce ayarlanmalıdır.

## <a name="changetoken-class"></a>ChangeToken sınıfı

<xref:Microsoft.Extensions.Primitives.ChangeToken>, bir değişikliğin gerçekleştiği bildirimleri yaymak için kullanılan statik bir sınıftır. `ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName> ad alanında bulunur. [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmayan uygulamalar Için, [Microsoft. Extensions. ilkel](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.

[ChangeToken. OnChange (Func\<IChannel>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi, belirtecin her değiştirişinde bir `Action` çağrı kaydeder:

* `Func<IChangeToken>`belirteci üretir.
* `Action`belirteç değiştiğinde çağrılır.

[ChangeToken. OnChange\<TState> (Func\<IChannel>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yüklemesi, belirteç tüketicisine `Action`geçirilen `TState` ek bir parametre alır.

`OnChange`döndürür <xref:System.IDisposable>. Çağırma <xref:System.IDisposable.Dispose*> , belirteci daha fazla değişiklik için dinlemeyi durdurup belirtecin kaynaklarını serbest bırakır.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>ASP.NET Core değişiklik belirteçlerinin örnek kullanımları

Değişiklik belirteçleri, nesnelerde yapılan değişiklikleri izlemek için ASP.NET Core belirgin alanlarında kullanılır:

* Dosyalarda yapılan değişiklikleri izlemek için, <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> yöntemi belirtilen dosya veya `IChangeToken` klasör için bir oluşturur.
* `IChangeToken`değişiklik üzerine önbellek çıkarmaları tetiklemek için, belirteç önbellek girişlerine eklenebilir.
* Değişiklikler `TOptions` için, varsayılan <xref:Microsoft.Extensions.Options.OptionsMonitor`1> uygulamasının bir veya <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> daha fazla <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> örnek kabul eden bir aşırı yüklemesi vardır. Her örnek, izleme `IChangeToken` seçenekleri değişiklikleri için değişiklik bildirimi geri aramasını kaydetmek üzere bir döndürür.

## <a name="monitor-for-configuration-changes"></a>Yapılandırma değişikliklerini izle

Varsayılan olarak, ASP.NET Core şablonlar [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, appSettings) kullanır *. Development. JSON*ve *appSettings. Üretim. JSON*), uygulama yapılandırma ayarlarını yükler.

Bu dosyalar, üzerinde <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> bir `reloadOnChange` parametre kabul eden [Addjsonfile (IController, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) genişletme yöntemi kullanılarak yapılandırılır. `reloadOnChange`yapılandırmanın dosya değişikliklerinde yeniden yüklenmesi gerekip gerekmediğini gösterir. Bu ayar <xref:Microsoft.AspNetCore.WebHost> kolaylık yönteminde <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>görünür:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Dosya tabanlı yapılandırma tarafından <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>temsil edilir. `FileConfigurationSource`dosyalarını <xref:Microsoft.Extensions.FileProviders.IFileProvider> izlemek için kullanır.

Varsayılan `IFileMonitor` olarak, yapılandırma dosyası değişikliklerini izlemek için <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>kullanılan <xref:System.IO.FileSystemWatcher> bir tarafından sağlanır.

Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir. *AppSettings* dosyalarından herhangi biri değiştiğinde, dosya izleme uygulamalarının her ikisi de özel kod&mdash;yürütür örnek uygulama konsola bir ileti yazar.

Bir yapılandırma dosyası `FileSystemWatcher` , tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri çağırmaları tetikleyebilir. Özel kodun, birden fazla belirteç geri çağırma işlemi tetiklendiğinde yalnızca bir kez çalıştığından emin olmak için, örnek uygulama dosya karmalarını denetler. Örnek, SHA1 dosya karma kullanır. Bir yeniden deneme, üstel geri dönme ile uygulanır. Dosya kilitlemesi, geçici olarak bir dosyada yeni bir karma işlem yapılmasını önleyen dosya kilitleme gerçekleşebileceğinden, yeniden deneme vardır.

*Yardımcı programlar/yardımcı programlar. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Basit başlangıç değiştirme belirteci

Değişiklik bildirimleri için bir `Action` belirteç tüketicisi geri aramasını yapılandırma yeniden yükleme belirtecine kaydedin.

`Startup.Configure` içinde:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`belirteci sağlar. Geri çağırma `InvokeChanged` yöntemi:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Geri aramanın, izlemek için doğru *appSettings* yapılandırma dosyasını ( `IHostingEnvironment`Örneğin, appSettings) belirtmek için yararlı olan ' a geçmek için kullanılır. `state` * Geliştirme ortamında geliştirme. JSON* ). Dosya karmaları, bir yapılandırma dosyası yalnızca `WriteConsole` bir kez değiştirildiğinde, daha fazla belirteç geri çağırmaları nedeniyle deyimin birden çok kez çalışmasını engellemek için kullanılır.

Uygulama çalıştığı sürece bu sistem çalışır ve Kullanıcı tarafından devre dışı bırakılamaz.

### <a name="monitor-configuration-changes-as-a-service"></a>Yapılandırma değişikliklerini hizmet olarak izle

Örnek şunları uygular:

* Temel başlangıç belirteci izleme.
* Hizmet olarak izleme.
* İzlemeyi etkinleştirme ve devre dışı bırakma mekanizması.

Örnek bir `IConfigurationMonitor` arabirim oluşturur.

*Uzantılar/ConfigurationMonitor. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Uygulanan sınıfın `ConfigurationMonitor`Oluşturucusu, değişiklik bildirimleri için bir geri çağırma kaydeder:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`belirteci sağlar. `InvokeChanged`geri çağırma yöntemidir. Bu `state` örnekte, izleme durumuna erişmek için kullanılan `IConfigurationMonitor` örneğe bir başvuru vardır. İki özellik kullanılır:

* `MonitoringEnabled`&ndash; Geri aramanın özel kodunu çalıştırıp çalıştırmayacağını gösterir.
* `CurrentState`&ndash; Kullanıcı arabiriminde kullanım için geçerli izleme durumunu açıklar.

`InvokeChanged` Yöntemi önceki yaklaşımla benzerdir, bunun dışında:

* , `MonitoringEnabled` `true`Olmadığı müddetçe kodunu çalıştırmaz.
* `WriteConsole` Çıktıda geçerli `state` olan çıktıyı verir.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Bir örnek `ConfigurationMonitor` , içinde `Startup.ConfigureServices`bir hizmet olarak kaydedilir:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Dizin sayfası, yapılandırma izleme üzerinde Kullanıcı denetimi sağlar. Örneği öğesine `IConfigurationMonitor` eklenir `IndexModel`.

*Pages/Index. cshtml. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Yapılandırma İzleyicisi (`_monitor`), izlemeyi etkinleştirmek veya devre dışı BıRAKMAK ve UI geri bildirimi için geçerli durumu ayarlamak üzere kullanılır:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

`OnPostStartMonitoring` Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir. `OnPostStopMonitoring` Tetiklendiğinde, izleme devre dışıdır ve durum, izlemenin gerçekleşmediğinden emin olmak üzere ayarlanır.

Kullanıcı arabirimindeki düğmeler izlemeyi etkinleştirir ve devre dışı bırakır.

*Sayfa/dizin. cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Önbelleğe alınmış dosya değişikliklerini izle

Dosya içeriği, kullanarak <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>bellek içinde önbelleğe alınabilir. Bellek içi önbelleğe alma, [bellek Içi önbellek](xref:performance/caching/memory) konusunda açıklanmaktadır. Aşağıda açıklanan uygulama gibi ek adımlar uygulamadan, kaynak veriler değişirse önbellekten *eski* (eski) veriler döndürülür.

Örneğin, bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) dönemini yenilerken önbelleğe alınmış bir kaynak dosyanın durumu, eski önbelleğe alınmış dosya verilerine yol açar. Verilerin her isteği, Kayan süre sonu süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez. Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, büyük olasılıkla eski içerik alınmasına tabidir.

Bir dosya önbelleğe alma senaryosunda değişiklik belirteçlerini kullanmak önbellekte eski dosya içeriğinin varlığını engeller. Örnek uygulama, yaklaşımın bir uygulamasını gösterir.

Örnek şu amaçlarla `GetFileContent` kullanılır:

* Dosya içeriğini döndürür.
* Bir dosya kilidinin geçici olarak bir dosya okumayı engellediği durumları kapsamak için üstel geri ile yeniden deneme algoritması uygulayın.

*Yardımcı programlar/yardımcı programlar. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

`FileService` Önbelleğe alınmış dosya aramalarını işlemek için oluşturulur. Hizmetin `GetFileContent` Yöntem çağrısı, bellek içi önbellekten dosya içeriğini almaya çalışır ve bunu çağırana (*Services/FileService. cs*) döndürebilir.

Önbellek anahtarı kullanılarak önbelleğe alınmış içerik bulunamazsa, aşağıdaki eylemler gerçekleştirilir:

1. Dosya içeriği kullanılarak `GetFileContent`elde edilir.
1. Dosya sağlayıcısından [ıfileproviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile bir değişiklik belirteci elde edilir. Dosya değiştirildiğinde belirtecin geri çağırması tetiklenir.
1. Dosya içeriği bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alınır. Değişiklik belirteci, önbelleğe alınmış durumdayken dosya değişirse önbellek girdisini çıkarmak için [Memorycacheentryextensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte eklenir.

Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)saklanır. [Ihostingenvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) , uygulamanın üzerine gelindiğinde bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> işaret elde etmek için kullanılır <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>. , `filePath` [Ifıleınfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

, `FileService` Hizmet kapsayıcısına bellek önbelleği hizmeti ile birlikte kaydedilir.

`Startup.ConfigureServices` içinde:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.

Dizin sayfasının `OnGet` yönteminde (*Pages/Index. cshtml. cs*):

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken sınıfı

Tek bir nesnedeki bir veya `IChangeToken` daha fazla örneği temsil etmek için <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> sınıfını kullanın.

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

`HasChanged`herhangi bir temsil edilen belirteç `true` `HasChanged` varsa bileşik belirteç raporlarında `true`. `ActiveChangeCallbacks`herhangi bir temsil edilen belirteç `true` `ActiveChangeCallbacks` varsa bileşik belirteç raporlarında `true`. Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri çağırması bir kez çağrılır.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
