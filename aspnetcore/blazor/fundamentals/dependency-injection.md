---
title: ASP.NET Core Blazor bağımlılığı ekleme
author: guardrex
description: BlazorUygulamaların bileşenlere nasıl hizmet ekleyebileceğinizi öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3f2b4eff5422acbec80b2fd9b801101271cc3f75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97808731"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a>ASP.NET Core Blazor bağımlılığı ekleme

Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Mike rousos](https://github.com/mjrousos)

[Bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) , merkezi bir konumda yapılandırılmış hizmetlere erişmek için bir tekniktir:

* Çerçeve kayıtlı Hizmetleri doğrudan uygulama bileşenlerine eklenebilir Blazor .
* Blazor uygulamalar özel hizmetleri tanımlar ve kaydeder ve bunu uygulama genelinde DI aracılığıyla kullanılabilir hale getirir.

## <a name="default-services"></a>Varsayılan hizmetler

Aşağıdaki tabloda gösterilen hizmetler genellikle Blazor uygulamalarda kullanılır.

| Hizmet | Ömür | Açıklama |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Yayıl | <p>HTTP istekleri göndermek ve bir URI tarafından tanımlanan bir kaynaktan HTTP yanıtlarını almak için yöntemler sağlar.</p><p><xref:System.Net.Http.HttpClient>Bir uygulamadaki örneği, Blazor WebAssembly arka planda HTTP trafiğini işlemek için tarayıcıyı kullanır.</p><p>Blazor Server uygulamalar <xref:System.Net.Http.HttpClient> Varsayılan olarak yapılandırılmış bir hizmet olarak yapılandırılmamış. Bir <xref:System.Net.Http.HttpClient> Blazor Server uygulamaya bir uygulama sağlayın.</p><p>Daha fazla bilgi için bkz. <xref:blazor/call-web-api>.</p><p><xref:System.Net.Http.HttpClient>Tek değil, kapsamlı bir hizmet olarak kaydedilir. Daha fazla bilgi için [hizmet ömrü](#service-lifetime) bölümüne bakın.</p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p>**Blazor WebAssembly**: Singleton</p><p>**Blazor Server**: Kapsamlı</p> | JavaScript çağrılarının dağıtıldığı bir JavaScript çalışma zamanının örneğini temsil eder. Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p>**Blazor WebAssembly**: Singleton</p><p>**Blazor Server**: Kapsamlı</p> | URI 'Ler ve gezinme durumu ile çalışmaya yönelik yardımcıları içerir. Daha fazla bilgi için bkz. [URI ve gezinti durumu yardımcıları](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Özel bir hizmet sağlayıcı, tabloda listelenen varsayılan Hizmetleri otomatik olarak sağlamaz. Özel bir hizmet sağlayıcısı kullanır ve tabloda gösterilen hizmetlerden herhangi birini gerekliyse, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.

## <a name="add-services-to-an-app"></a>Uygulamaya hizmet ekleme

::: zone pivot="webassembly"

Uygulamasındaki uygulamasının hizmet koleksiyonu için Hizmetleri yapılandırın `Program.Main` `Program.cs` . Aşağıdaki örnekte, `MyDependency` uygulama için kaydedilir `IMyDependency` :

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

Konak oluşturulduktan sonra, herhangi bir bileşen işlenmeden önce kök dı kapsamından hizmetler kullanılabilir. Bu, içerik işlemeden önce başlatma mantığını çalıştırmak için yararlı olabilir:

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

Ana bilgisayar, uygulama için merkezi bir yapılandırma örneği sağlar. Yukarıdaki örnekte derleme yaparken, hava durumu hizmetinin URL 'SI varsayılan bir yapılandırma kaynağından geçirilir (örneğin, `appsettings.json` ) `InitializeWeatherAsync` :

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

Yeni bir uygulama oluşturduktan sonra, `Startup.ConfigureServices` içindeki yöntemini inceleyin `Startup.cs` :

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Yöntemi <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , [hizmet tanımlayıcısı](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) nesnelerinin bir listesi olan bir geçirilir. Hizmetler, `ConfigureServices` hizmet koleksiyonuna hizmet tanımlayıcıları sağlayarak yöntemine eklenir. Aşağıdaki örnek, `IDataAccess` arabirimini ve somut uygulamasını içeren kavramı gösterir `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a>Hizmet ömrü

Hizmetler, aşağıdaki tabloda gösterilen ömürlerle yapılandırılabilir.

| Ömür | Açıklama |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p>Blazor WebAssembly uygulamalar şu anda bir dı kapsamları kavramı içermez. `Scoped`-kayıtlı hizmetler hizmetler gibi davranır `Singleton` .</p><p>Blazor ServerBarındırma modeli, `Scoped` http istekleri genelinde yaşam süresini destekler, ancak SignalR istemciye yüklenen bileşenler arasında bağlantı/devre iletileri arasında değildir. RazorUygulamanın sayfaları veya MVC bölümü, kapsamlı hizmetleri normal şekilde ele alır ve sayfalar veya görünümler veya bir sayfa ya da bir bileşen görünümü arasında gezinilirken *her bir http isteğindeki* hizmetleri yeniden oluşturur. Kapsamdaki hizmetler, istemci üzerindeki bileşenler arasında gezinilirken, sunucu SignalR BAĞLANTıSıNıN http istekleri aracılığıyla değil Kullanıcı devresi bağlantısı üzerinden gerçekleştiği sırada yeniden yapılandırılmadı. İstemci üzerindeki aşağıdaki bileşen senaryolarında, Kullanıcı için yeni bir devre oluşturulması nedeniyle kapsamlı hizmetler yeniden yapılandırılır:</p><ul><li>Kullanıcı tarayıcının penceresini kapatır. Kullanıcı yeni bir pencere açar ve uygulamaya geri gider.</li><li>Kullanıcı, bir tarayıcı penceresinde uygulamanın son sekmesini kapatır. Kullanıcı yeni bir sekme açar ve uygulamaya geri gider.</li><li>Kullanıcı tarayıcının yeniden yükleme/yenileme düğmesini seçer.</li></ul><p>Uygulamalarda kapsamlı hizmetler genelinde Kullanıcı durumunu koruma hakkında daha fazla bilgi için Blazor Server bkz <xref:blazor/hosting-models?pivots=server> ..</p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Dı, hizmetin *tek bir örneğini* oluşturur. Hizmet gerektiren tüm bileşenler `Singleton` aynı hizmetin bir örneğini alır. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Bir bileşen hizmet kapsayıcısından bir hizmetin örneğini edindiğinde `Transient` , hizmetin *Yeni bir örneğini* alır. |

Dı sistemi ASP.NET Core içindeki DI sistemini temel alır. Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Bir bileşende hizmet isteme

Hizmetler hizmet koleksiyonuna eklendikten sonra, [`@inject`](xref:mvc/views/razor#inject) Razor iki parametreye sahip olan yönergesini kullanarak hizmetleri bileşenlere ekleyin:

* Tür: eklenecek hizmetin türü.
* Özellik: eklenen App Service 'i alan özelliğin adı. Özelliği el ile oluşturma gerektirmez. Derleyici özelliği oluşturur.

Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.

[`@inject`](xref:mvc/views/razor#inject)Farklı hizmetler eklemek için birden çok deyim kullanın.

Aşağıdaki örnek nasıl kullanılacağını göstermektedir [`@inject`](xref:mvc/views/razor#inject) . Uygulayan hizmet `Services.IDataAccess` bileşenin özelliğine eklenir `DataRepository` . Kodun yalnızca soyutlamayı nasıl kullandığını aklınızda yapın `IDataAccess` :

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

Dahili olarak, oluşturulan Özellik ( `DataRepository` ) özniteliğini kullanır [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) . Genellikle, bu öznitelik doğrudan kullanılmaz. Bileşenler için bir temel sınıf gerekliyse ve temel sınıf için eklenen özellikler de gerekliyse, özniteliği el ile ekleyin [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) :

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

Temel sınıftan türetilmiş bileşenlerde, [`@inject`](xref:mvc/views/razor#inject) yönergesi gerekli değildir. <xref:Microsoft.AspNetCore.Components.InjectAttribute>Taban sınıfının yeterli olması yeterlidir:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Hizmetler 'de dı kullanma

Karmaşık hizmetler için ek hizmetler gerekebilir. Aşağıdaki örnekte, `DataAccess` <xref:System.Net.Http.HttpClient> varsayılan hizmeti gerektirir. [`@inject`](xref:mvc/views/razor#inject) (veya [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) özniteliği) hizmetlerde kullanılamaz. Bunun yerine *Oluşturucu Ekleme* kullanılmalıdır. Gerekli hizmetler, hizmetin oluşturucusuna parametreler eklenerek eklenir. Dı hizmeti oluşturduğunda, oluşturucuda gereken hizmetleri algılar ve bunlara göre sağlar. Aşağıdaki örnekte, Oluşturucu bir ile bir ile alır <xref:System.Net.Http.HttpClient> . <xref:System.Net.Http.HttpClient> Varsayılan bir hizmettir.

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

Oluşturucu Ekleme önkoşulları:

* Bağımsız değişkenlerinin tümü DI tarafından yerine getirilme için tek bir Oluşturucu bulunmalıdır. Varsayılan değerleri belirttiklerinde, DI tarafından kapsanmayan ek parametrelere izin verilir.
* Uygulanabilir Oluşturucu olmalıdır `public` .
* Uygulanabilir bir Oluşturucu var olmalıdır. Belirsizlik söz konusu olduğunda, bir özel durum oluşturur.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Bir dı kapsamını yönetmek için yardımcı program temel bileşen sınıfları

ASP.NET Core uygulamalarda, kapsamlı hizmetler genellikle geçerli isteğin kapsamlandırılır. İstek tamamlandıktan sonra, tüm kapsamlı veya geçici hizmetler dı sistemi tarafından silinir. Blazor ServerUygulamalarda, istek kapsamı istemci bağlantısı süresince sürer. Bu, geçici ve kapsamlı hizmetlerin beklenenden çok daha uzun süreli olarak oluşmasına neden olabilir. Blazor WebAssemblyUygulamalarda, kapsamlı bir ömürle kaydedilen hizmetler tekton olarak değerlendirilir, bu nedenle tipik ASP.NET Core uygulamalarında kapsamlı hizmetlerden daha uzun bir süre yaşarlar.

> [!NOTE]
> Bir uygulamada atılabilir geçici Hizmetleri algılamak için [geçici disposaı 'Yi Algıla](#detect-transient-disposables) bölümüne bakın.

Uygulamalarda bir hizmet ömrünü sınırlayan bir yaklaşım Blazor , <xref:Microsoft.AspNetCore.Components.OwningComponentBase> türü kullanır. <xref:Microsoft.AspNetCore.Components.OwningComponentBase> , öğesinden türetilmiş soyut bir türdür <xref:Microsoft.AspNetCore.Components.ComponentBase> ve bileşenin ömrüne karşılık gelen BIR dı kapsamı oluşturur. Bu kapsamı kullanarak, dı hizmetlerini kapsamlı bir ömür ile kullanmak mümkündür ve bileşen olarak bu uygulamaları canlı hale gelir. Bileşen yok edildiğinde, bileşenin kapsamlı hizmet sağlayıcısından gelen hizmetler de silinir. Bu, şu hizmetler için yararlı olabilir:

* Geçici ömür uygun olmadığından, bir bileşen içinde yeniden kullanılmalıdır.
* Tek yaşam süresi uygun olmadığından, bileşenler arasında paylaşılmamalıdır.

Türün iki sürümü <xref:Microsoft.AspNetCore.Components.OwningComponentBase> kullanılabilir:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> , türünde <xref:Microsoft.AspNetCore.Components.ComponentBase> Protected özelliği olan bir abstract, atılabilir alt öğesidir <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> <xref:System.IServiceProvider> . Bu sağlayıcı, bileşenin kullanım ömrü kapsamındaki Hizmetleri çözümlemek için kullanılabilir.

  Ya da özniteliği kullanılarak bileşene eklenen dı Hizmetleri, [`@inject`](xref:mvc/views/razor#inject) [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) bileşen kapsamında oluşturulmaz. Bileşenin kapsamını kullanmak için, hizmetler veya kullanılarak çözümlenmelidir <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> <xref:System.IServiceProvider.GetService%2A> . Sağlayıcı kullanılarak çözümlenen hizmetlerin, <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> aynı kapsamdaki bağımlılıkları vardır.

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> öğesinden türetilir <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ve KAPSAMDAKI <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> dı sağlayıcısından bir örneğini döndüren bir özellik ekler `T` . Bu tür, <xref:System.IServiceProvider> uygulamanın, bileşenin kapsamını kullanarak dı kapsayıcısından gerektirdiği bir birincil hizmet olduğunda bir örneği kullanmadan kapsamlı hizmetlere erişmenin kolay bir yoludur. <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>Özelliği kullanılabilir, bu sayede uygulama, gerekirse diğer tür hizmetleri alabilir.

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>DI Entity Framework Core (EF Core) DbContext kullanımı

Daha fazla bilgi için bkz. <xref:blazor/blazor-server-ef-core>.

## <a name="detect-transient-disposables"></a>Geçici disposleri Algıla

Aşağıdaki örneklerde, kullanması gereken bir uygulamada atılabilir geçici hizmetlerinin nasıl algılanacağı gösterilmektedir <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . Daha fazla bilgi için bkz. [BIR dı kapsamı bölümünü yönetmek Için yardımcı program temel bileşen sınıfları](#utility-base-component-classes-to-manage-a-di-scope) .

::: zone pivot="webassembly"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

`TransientDisposable`Aşağıdaki örnekte algılandı ( `Program.cs` ):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end 

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: zone-end

::: zone pivot="server"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

Ad alanını şu <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> şekilde ekleyin `Program.cs` :

```csharp
using Microsoft.Extensions.DependencyInjection;
```

İçinde `Program.CreateHostBuilder` `Program.cs` :

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

`TransientDependency`Aşağıdaki örnekte algılandı ( `Startup.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

Uygulama, özel durum oluşturmadan geçici disposana kaydedebilir. Ancak, aşağıdaki örnekte gösterildiği gibi geçici bir atılabilir, bir olarak çözümlenmeye çalışıldığında bir <xref:System.InvalidOperationException> ile sonuçlanır.

`Pages/TransientDisposable.razor`:

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

İçindeki bileşene gidin `TransientDisposable` `/transient-disposable` ve <xref:System.InvalidOperationException> Framework bir örneğini oluşturmaya çalıştığında oluşur `TransientDisposable` :

> System. InvalidOperationException: yanlış kapsamdaki geçici atılabilir hizmeti TransientDisposable çözümlenmeye çalışılıyor. \<T>Çözmeye çalıştığınız ' 't ' hizmeti için ' OwningComponentBase ' bileşen temel sınıfını kullanın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/dependency-injection>
* [`IDisposable` Geçici ve paylaşılan örnekler için rehberlik](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
