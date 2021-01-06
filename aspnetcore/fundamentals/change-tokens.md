---
title: ASP.NET Core değişiklik belirteçleriyle değişiklikleri Algıla
author: rick-anderson
description: Değişiklikleri izlemek için değişiklik belirteçlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
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
uid: fundamentals/change-tokens
ms.openlocfilehash: f20d44c7767b284f727ce19a46224dae0cf6a5e1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053780"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>ASP.NET Core değişiklik belirteçleriyle değişiklikleri Algıla

::: moniker range=">= aspnetcore-3.0"

*Değişiklik belirteci* , durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzey bir yapı taşıdır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Ichangetoken arabirimi

<xref:Microsoft.Extensions.Primitives.IChangeToken> bir değişikliğin gerçekleştiği bildirimleri yayar. `IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur. [Microsoft. Extensions. Ilkel öğeler](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi, ASP.NET Core uygulamalarına örtük olarak sağlanır.

`IChangeToken` iki özelliğe sahiptir:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> belirtecin etkin olup geri çağırmaları harekete geçirmediğini belirtir. `ActiveChangedCallbacks`Olarak ayarlanırsa `false` , bir geri çağırma hiçbir şekilde çağrılmaz ve uygulamanın değişiklikleri yoklamalıdır `HasChanged` . Hiçbir değişiklik gerçekleşmüyorsa veya temeldeki değişiklik dinleyicisi atıldığı veya devre dışı bırakıldığında belirtecin hiçbir şekilde iptal edilmemesi de mümkündür.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> bir değişikliğin oluşup gerçekleşmediğini gösteren bir değer alır.

`IChangeToken`Arabirim, belirteç değiştirildiğinde çağrılan bir geri aramayı kaydeden [Registerchangecallback (eylem \<Object> , nesne)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir. `HasChanged` geri çağırma çağrılmadan önce ayarlanmalıdır.

## <a name="changetoken-class"></a>ChangeToken sınıfı

<xref:Microsoft.Extensions.Primitives.ChangeToken> , bir değişikliğin gerçekleştiği bildirimleri yaymak için kullanılan statik bir sınıftır. `ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur. [Microsoft. Extensions. Ilkel öğeler](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi, ASP.NET Core uygulamalarına örtük olarak sağlanır.

[ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi, `Action` belirteç her değiştiğinde bir çağrı yapar:

* `Func<IChangeToken>` belirteci üretir.
* `Action` belirteç değiştiğinde çağrılır.

[ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yüklemesi, `TState` belirteç tüketicisine geçirilen ek bir parametreyi alır `Action` .

`OnChange` döndürür <xref:System.IDisposable> . Çağırma <xref:System.IDisposable.Dispose*> , belirteci daha fazla değişiklik için dinlemeyi durdurup belirtecin kaynaklarını serbest bırakır.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>ASP.NET Core değişiklik belirteçlerinin örnek kullanımları

Değişiklik belirteçleri, nesnelerde yapılan değişiklikleri izlemek için ASP.NET Core belirgin alanlarında kullanılır:

* Dosyalarda yapılan değişiklikleri izlemek için, <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> yöntemi `IChangeToken` belirtilen dosya veya klasör için bir oluşturur.
* `IChangeToken` değişiklik üzerine önbellek çıkarmaları tetiklemek için, belirteç önbellek girişlerine eklenebilir.
* `TOptions`Değişiklikler için, varsayılan uygulamasının <xref:Microsoft.Extensions.Options.OptionsMonitor`1> <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> bir veya daha fazla örnek kabul eden bir aşırı yüklemesi vardır <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> . Her örnek, `IChangeToken` izleme seçenekleri değişiklikleri için değişiklik bildirimi geri aramasını kaydetmek üzere bir döndürür.

## <a name="monitor-for-configuration-changes"></a>Yapılandırma değişikliklerini izle

Varsayılan olarak ASP.NET Core şablonlar, uygulama yapılandırma ayarlarını yüklemek için [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.jsaçık* ve *appsettings.Production.js*) kullanır.

Bu dosyalar, üzerinde bir parametre kabul eden [Addjsonfile (IController, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) genişletme yöntemi kullanılarak yapılandırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> `reloadOnChange` . `reloadOnChange` yapılandırmanın dosya değişikliklerinde yeniden yüklenmesi gerekip gerekmediğini gösterir. Bu ayar <xref:Microsoft.Extensions.Hosting.Host> kolaylık yönteminde görünür <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Dosya tabanlı yapılandırma tarafından temsil edilir <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> . `FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>dosyalarını izlemek için kullanır.

Varsayılan olarak, `IFileMonitor` <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> <xref:System.IO.FileSystemWatcher> yapılandırma dosyası değişikliklerini izlemek için kullanılan bir tarafından sağlanır.

Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir. *AppSettings* dosyalarından herhangi biri değiştiğinde, dosya izleme uygulamalarının her ikisi de özel kod yürütür &mdash; örnek uygulama konsola bir ileti yazar.

Bir yapılandırma dosyası `FileSystemWatcher` , tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri çağırmaları tetikleyebilir. Özel kodun, birden fazla belirteç geri çağırma işlemi tetiklendiğinde yalnızca bir kez çalıştığından emin olmak için, örnek uygulama dosya karmalarını denetler. Örnek, SHA1 dosya karma kullanır. Bir yeniden deneme, üstel geri dönme ile uygulanır. Dosya kilitlemesi, geçici olarak bir dosyada yeni bir karma işlem yapılmasını önleyen dosya kilitleme gerçekleşebileceğinden, yeniden deneme vardır.

*Yardımcı programlar/yardımcı programlar. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Basit başlangıç değiştirme belirteci

`Action`Değişiklik bildirimleri için bir belirteç tüketicisi geri aramasını yapılandırma yeniden yükleme belirtecine kaydedin.

`Startup.Configure` içinde:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` belirteci sağlar. Geri çağırma `InvokeChanged` yöntemi:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

`state`Geri aramanın, `IWebHostEnvironment` izlemek için doğru *appSettings* yapılandırma dosyasını (örneğin, geliştirme ortamında olduğu zaman *appsettings.Development.js* ) belirtmek için yararlı olan ' a geçmek için kullanılır. Dosya karmaları, `WriteConsole` bir yapılandırma dosyası yalnızca bir kez değiştirildiğinde, daha fazla belirteç geri çağırmaları nedeniyle deyimin birden çok kez çalışmasını engellemek için kullanılır.

Uygulama çalıştığı sürece bu sistem çalışır ve Kullanıcı tarafından devre dışı bırakılamaz.

### <a name="monitor-configuration-changes-as-a-service"></a>Yapılandırma değişikliklerini hizmet olarak izle

Örnek şunları uygular:

* Temel başlangıç belirteci izleme.
* Hizmet olarak izleme.
* İzlemeyi etkinleştirme ve devre dışı bırakma mekanizması.

Örnek bir arabirim oluşturur `IConfigurationMonitor` .

*Uzantılar/ConfigurationMonitor. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Uygulanan sınıfın Oluşturucusu, `ConfigurationMonitor` değişiklik bildirimleri için bir geri çağırma kaydeder:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` belirteci sağlar. `InvokeChanged` geri çağırma yöntemidir. `state`Bu örnekte, `IConfigurationMonitor` izleme durumuna erişmek için kullanılan örneğe bir başvuru vardır. İki özellik kullanılır:

* `MonitoringEnabled`: Geri aramanın özel kodunu çalıştırması gerekip gerekmediğini gösterir.
* `CurrentState`: Kullanıcı arabiriminde kullanım için geçerli izleme durumunu açıklar.

`InvokeChanged`Yöntemi önceki yaklaşımla benzerdir, bunun dışında:

* , Olmadığı müddetçe kodunu çalıştırmaz `MonitoringEnabled` `true` .
* `state`Çıktıda geçerli olan çıktıyı verir `WriteConsole` .

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Bir örnek `ConfigurationMonitor` , içinde bir hizmet olarak kaydedilir `Startup.ConfigureServices` :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Dizin sayfası, yapılandırma izleme üzerinde Kullanıcı denetimi sağlar. Örneği `IConfigurationMonitor` öğesine eklenir `IndexModel` .

*Pages/Index. cshtml. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Yapılandırma İzleyicisi ( `_monitor` ), izlemeyi etkinleştirmek veya devre dışı bırakmak ve UI geri bildirimi için geçerli durumu ayarlamak üzere kullanılır:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

`OnPostStartMonitoring`Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir. `OnPostStopMonitoring`Tetiklendiğinde, izleme devre dışıdır ve durum, izlemenin gerçekleşmediğinden emin olmak üzere ayarlanır.

Kullanıcı arabirimindeki düğmeler izlemeyi etkinleştirir ve devre dışı bırakır.

*Sayfa/dizin. cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Önbelleğe alınmış dosya değişikliklerini izle

Dosya içeriği, kullanarak bellek içinde önbelleğe alınabilir <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> . Bellek içi önbelleğe alma, [bellek Içi önbellek](xref:performance/caching/memory) konusunda açıklanmaktadır. Aşağıda açıklanan uygulama gibi ek adımlar uygulamadan, kaynak veriler değişirse önbellekten *eski* (eski) veriler döndürülür.

Örneğin, bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) dönemini yenilerken önbelleğe alınmış bir kaynak dosyanın durumu, eski önbelleğe alınmış dosya verilerine yol açar. Verilerin her isteği, Kayan süre sonu süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez. Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, büyük olasılıkla eski içerik alınmasına tabidir.

Bir dosya önbelleğe alma senaryosunda değişiklik belirteçlerini kullanmak önbellekte eski dosya içeriğinin varlığını engeller. Örnek uygulama, yaklaşımın bir uygulamasını gösterir.

Örnek şu amaçlarla kullanılır `GetFileContent` :

* Dosya içeriğini döndürür.
* Bir dosya kilidinin geçici olarak bir dosya okumayı engellediği durumları kapsamak için üstel geri ile yeniden deneme algoritması uygulayın.

*Yardımcı programlar/yardımcı programlar. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

`FileService`Önbelleğe alınmış dosya aramalarını işlemek için oluşturulur. `GetFileContent`Hizmetin Yöntem çağrısı, bellek içi önbellekten dosya içeriğini almaya çalışır ve bunu çağırana (*Services/FileService. cs*) döndürebilir.

Önbellek anahtarı kullanılarak önbelleğe alınmış içerik bulunamazsa, aşağıdaki eylemler gerçekleştirilir:

1. Dosya içeriği kullanılarak elde edilir `GetFileContent` .
1. Dosya sağlayıcısından [ıfileproviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile bir değişiklik belirteci elde edilir. Dosya değiştirildiğinde belirtecin geri çağırması tetiklenir.
1. Dosya içeriği bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alınır. Değişiklik belirteci, önbelleğe alınmış durumdayken dosya değişirse önbellek girdisini çıkarmak için [Memorycacheentryextensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte eklenir.

Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)saklanır. `IWebHostEnvironment.ContentRootFileProvider` , <xref:Microsoft.Extensions.FileProviders.IFileProvider> uygulamanın üzerine gelindiğinde bir işaret elde etmek için kullanılır `IWebHostEnvironment.ContentRootPath` . , `filePath` [Ifıleınfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

, `FileService` Hizmet kapsayıcısına bellek önbelleği hizmeti ile birlikte kaydedilir.

`Startup.ConfigureServices` içinde:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.

Dizin sayfasının `OnGet` yönteminde (*Pages/Index. cshtml. cs*):

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken sınıfı

Tek bir nesnedeki bir veya daha fazla örneği temsil etmek için `IChangeToken` sınıfını kullanın <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> .

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

`HasChanged``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `HasChanged` `true` . `ActiveChangeCallbacks``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `ActiveChangeCallbacks` `true` . Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri çağırması bir kez çağrılır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Değişiklik belirteci* , durum değişikliklerini izlemek için kullanılan genel amaçlı, düşük düzey bir yapı taşıdır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Ichangetoken arabirimi

<xref:Microsoft.Extensions.Primitives.IChangeToken> bir değişikliğin gerçekleştiği bildirimleri yayar. `IChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur. [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmayan uygulamalar Için, [Microsoft. Extensions. ilkel](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.

`IChangeToken` iki özelliğe sahiptir:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> belirtecin etkin olup geri çağırmaları harekete geçirmediğini belirtir. `ActiveChangedCallbacks`Olarak ayarlanırsa `false` , bir geri çağırma hiçbir şekilde çağrılmaz ve uygulamanın değişiklikleri yoklamalıdır `HasChanged` . Hiçbir değişiklik gerçekleşmüyorsa veya temeldeki değişiklik dinleyicisi atıldığı veya devre dışı bırakıldığında belirtecin hiçbir şekilde iptal edilmemesi de mümkündür.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> bir değişikliğin oluşup gerçekleşmediğini gösteren bir değer alır.

`IChangeToken`Arabirim, belirteç değiştirildiğinde çağrılan bir geri aramayı kaydeden [Registerchangecallback (eylem \<Object> , nesne)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) yöntemini içerir. `HasChanged` geri çağırma çağrılmadan önce ayarlanmalıdır.

## <a name="changetoken-class"></a>ChangeToken sınıfı

<xref:Microsoft.Extensions.Primitives.ChangeToken> , bir değişikliğin gerçekleştiği bildirimleri yaymak için kullanılan statik bir sınıftır. `ChangeToken`<xref:Microsoft.Extensions.Primitives?displayProperty=fullName>ad alanında bulunur. [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmayan uygulamalar Için, [Microsoft. Extensions. ilkel](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet paketi için bir paket başvurusu oluşturun.

[ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) yöntemi, `Action` belirteç her değiştiğinde bir çağrı yapar:

* `Func<IChangeToken>` belirteci üretir.
* `Action` belirteç değiştiğinde çağrılır.

[ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) aşırı yüklemesi, `TState` belirteç tüketicisine geçirilen ek bir parametreyi alır `Action` .

`OnChange` döndürür <xref:System.IDisposable> . Çağırma <xref:System.IDisposable.Dispose*> , belirteci daha fazla değişiklik için dinlemeyi durdurup belirtecin kaynaklarını serbest bırakır.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>ASP.NET Core değişiklik belirteçlerinin örnek kullanımları

Değişiklik belirteçleri, nesnelerde yapılan değişiklikleri izlemek için ASP.NET Core belirgin alanlarında kullanılır:

* Dosyalarda yapılan değişiklikleri izlemek için, <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> yöntemi `IChangeToken` belirtilen dosya veya klasör için bir oluşturur.
* `IChangeToken` değişiklik üzerine önbellek çıkarmaları tetiklemek için, belirteç önbellek girişlerine eklenebilir.
* `TOptions`Değişiklikler için, varsayılan uygulamasının <xref:Microsoft.Extensions.Options.OptionsMonitor`1> <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> bir veya daha fazla örnek kabul eden bir aşırı yüklemesi vardır <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> . Her örnek, `IChangeToken` izleme seçenekleri değişiklikleri için değişiklik bildirimi geri aramasını kaydetmek üzere bir döndürür.

## <a name="monitor-for-configuration-changes"></a>Yapılandırma değişikliklerini izle

Varsayılan olarak ASP.NET Core şablonlar, uygulama yapılandırma ayarlarını yüklemek için [JSON yapılandırma dosyalarını](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.jsaçık* ve *appsettings.Production.js*) kullanır.

Bu dosyalar, üzerinde bir parametre kabul eden [Addjsonfile (IController, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) genişletme yöntemi kullanılarak yapılandırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> `reloadOnChange` . `reloadOnChange` yapılandırmanın dosya değişikliklerinde yeniden yüklenmesi gerekip gerekmediğini gösterir. Bu ayar <xref:Microsoft.AspNetCore.WebHost> kolaylık yönteminde görünür <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Dosya tabanlı yapılandırma tarafından temsil edilir <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> . `FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>dosyalarını izlemek için kullanır.

Varsayılan olarak, `IFileMonitor` <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> <xref:System.IO.FileSystemWatcher> yapılandırma dosyası değişikliklerini izlemek için kullanılan bir tarafından sağlanır.

Örnek uygulama, yapılandırma değişikliklerini izlemek için iki uygulama gösterir. *AppSettings* dosyalarından herhangi biri değiştiğinde, dosya izleme uygulamalarının her ikisi de özel kod yürütür &mdash; örnek uygulama konsola bir ileti yazar.

Bir yapılandırma dosyası `FileSystemWatcher` , tek bir yapılandırma dosyası değişikliği için birden çok belirteç geri çağırmaları tetikleyebilir. Özel kodun, birden fazla belirteç geri çağırma işlemi tetiklendiğinde yalnızca bir kez çalıştığından emin olmak için, örnek uygulama dosya karmalarını denetler. Örnek, SHA1 dosya karma kullanır. Bir yeniden deneme, üstel geri dönme ile uygulanır. Dosya kilitlemesi, geçici olarak bir dosyada yeni bir karma işlem yapılmasını önleyen dosya kilitleme gerçekleşebileceğinden, yeniden deneme vardır.

*Yardımcı programlar/yardımcı programlar. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Basit başlangıç değiştirme belirteci

`Action`Değişiklik bildirimleri için bir belirteç tüketicisi geri aramasını yapılandırma yeniden yükleme belirtecine kaydedin.

`Startup.Configure` içinde:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` belirteci sağlar. Geri çağırma `InvokeChanged` yöntemi:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

`state`Geri aramanın, `IHostingEnvironment` izlemek için doğru *appSettings* yapılandırma dosyasını (örneğin, geliştirme ortamında olduğu zaman *appsettings.Development.js* ) belirtmek için yararlı olan ' a geçmek için kullanılır. Dosya karmaları, `WriteConsole` bir yapılandırma dosyası yalnızca bir kez değiştirildiğinde, daha fazla belirteç geri çağırmaları nedeniyle deyimin birden çok kez çalışmasını engellemek için kullanılır.

Uygulama çalıştığı sürece bu sistem çalışır ve Kullanıcı tarafından devre dışı bırakılamaz.

### <a name="monitor-configuration-changes-as-a-service"></a>Yapılandırma değişikliklerini hizmet olarak izle

Örnek şunları uygular:

* Temel başlangıç belirteci izleme.
* Hizmet olarak izleme.
* İzlemeyi etkinleştirme ve devre dışı bırakma mekanizması.

Örnek bir arabirim oluşturur `IConfigurationMonitor` .

*Uzantılar/ConfigurationMonitor. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Uygulanan sınıfın Oluşturucusu, `ConfigurationMonitor` değişiklik bildirimleri için bir geri çağırma kaydeder:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` belirteci sağlar. `InvokeChanged` geri çağırma yöntemidir. `state`Bu örnekte, `IConfigurationMonitor` izleme durumuna erişmek için kullanılan örneğe bir başvuru vardır. İki özellik kullanılır:

* `MonitoringEnabled`: Geri aramanın özel kodunu çalıştırması gerekip gerekmediğini gösterir.
* `CurrentState`: Kullanıcı arabiriminde kullanım için geçerli izleme durumunu açıklar.

`InvokeChanged`Yöntemi önceki yaklaşımla benzerdir, bunun dışında:

* , Olmadığı müddetçe kodunu çalıştırmaz `MonitoringEnabled` `true` .
* `state`Çıktıda geçerli olan çıktıyı verir `WriteConsole` .

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Bir örnek `ConfigurationMonitor` , içinde bir hizmet olarak kaydedilir `Startup.ConfigureServices` :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Dizin sayfası, yapılandırma izleme üzerinde Kullanıcı denetimi sağlar. Örneği `IConfigurationMonitor` öğesine eklenir `IndexModel` .

*Pages/Index. cshtml. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Yapılandırma İzleyicisi ( `_monitor` ), izlemeyi etkinleştirmek veya devre dışı bırakmak ve UI geri bildirimi için geçerli durumu ayarlamak üzere kullanılır:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

`OnPostStartMonitoring`Tetiklendiğinde, izleme etkinleştirilir ve geçerli durum temizlenir. `OnPostStopMonitoring`Tetiklendiğinde, izleme devre dışıdır ve durum, izlemenin gerçekleşmediğinden emin olmak üzere ayarlanır.

Kullanıcı arabirimindeki düğmeler izlemeyi etkinleştirir ve devre dışı bırakır.

*Sayfa/dizin. cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Önbelleğe alınmış dosya değişikliklerini izle

Dosya içeriği, kullanarak bellek içinde önbelleğe alınabilir <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> . Bellek içi önbelleğe alma, [bellek Içi önbellek](xref:performance/caching/memory) konusunda açıklanmaktadır. Aşağıda açıklanan uygulama gibi ek adımlar uygulamadan, kaynak veriler değişirse önbellekten *eski* (eski) veriler döndürülür.

Örneğin, bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) dönemini yenilerken önbelleğe alınmış bir kaynak dosyanın durumu, eski önbelleğe alınmış dosya verilerine yol açar. Verilerin her isteği, Kayan süre sonu süresini yeniler, ancak dosya hiçbir zaman önbelleğe yeniden yüklenmez. Dosyanın önbelleğe alınmış içeriğini kullanan tüm uygulama özellikleri, büyük olasılıkla eski içerik alınmasına tabidir.

Bir dosya önbelleğe alma senaryosunda değişiklik belirteçlerini kullanmak önbellekte eski dosya içeriğinin varlığını engeller. Örnek uygulama, yaklaşımın bir uygulamasını gösterir.

Örnek şu amaçlarla kullanılır `GetFileContent` :

* Dosya içeriğini döndürür.
* Bir dosya kilidinin geçici olarak bir dosya okumayı engellediği durumları kapsamak için üstel geri ile yeniden deneme algoritması uygulayın.

*Yardımcı programlar/yardımcı programlar. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

`FileService`Önbelleğe alınmış dosya aramalarını işlemek için oluşturulur. `GetFileContent`Hizmetin Yöntem çağrısı, bellek içi önbellekten dosya içeriğini almaya çalışır ve bunu çağırana (*Services/FileService. cs*) döndürebilir.

Önbellek anahtarı kullanılarak önbelleğe alınmış içerik bulunamazsa, aşağıdaki eylemler gerçekleştirilir:

1. Dosya içeriği kullanılarak elde edilir `GetFileContent` .
1. Dosya sağlayıcısından [ıfileproviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)ile bir değişiklik belirteci elde edilir. Dosya değiştirildiğinde belirtecin geri çağırması tetiklenir.
1. Dosya içeriği bir [kayan süre sonu](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) süresiyle önbelleğe alınır. Değişiklik belirteci, önbelleğe alınmış durumdayken dosya değişirse önbellek girdisini çıkarmak için [Memorycacheentryextensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) ile birlikte eklenir.

Aşağıdaki örnekte, dosyalar uygulamanın [içerik kökünde](xref:fundamentals/index#content-root)saklanır. [Ihostingenvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) <xref:Microsoft.Extensions.FileProviders.IFileProvider> , uygulamanın üzerine gelindiğinde bir işaret elde etmek için kullanılır <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> . , `filePath` [Ifıleınfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)ile elde edilir.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

, `FileService` Hizmet kapsayıcısına bellek önbelleği hizmeti ile birlikte kaydedilir.

`Startup.ConfigureServices` içinde:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Sayfa modeli, hizmeti kullanarak dosyanın içeriğini yükler.

Dizin sayfasının `OnGet` yönteminde (*Pages/Index. cshtml. cs*):

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken sınıfı

Tek bir nesnedeki bir veya daha fazla örneği temsil etmek için `IChangeToken` sınıfını kullanın <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> .

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

`HasChanged``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `HasChanged` `true` . `ActiveChangeCallbacks``true`herhangi bir temsil edilen belirteç varsa bileşik belirteç raporlarında `ActiveChangeCallbacks` `true` . Birden çok eşzamanlı değişiklik olayı oluşursa, bileşik değişiklik geri çağırması bir kez çağrılır.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
