---
title: ASP.NET Core Blazor bağımlılığı ekleme
author: guardrex
description: BlazorUygulamaların bileşenlere nasıl hizmet ekleyebileceğinizi öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
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
ms.openlocfilehash: 3dc15f5efcc8f48a809bf9132588fb38732a7b35
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628293"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a>ASP.NET Core Blazor bağımlılığı ekleme

Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Mike rousos](https://github.com/mjrousos)

Blazor[bağımlılık ekleme işlemini (dı)](xref:fundamentals/dependency-injection)destekler. Uygulamalar, yerleşik hizmetleri ekleme tarafından bileşenlere kullanabilir. Uygulamalar Ayrıca özel hizmetleri tanımlayabilir ve kaydedebilir ve bu Hizmetleri uygulama genelinde DI aracılığıyla kullanılabilir hale getirebilirsiniz.

DI, merkezi bir konumda yapılandırılmış hizmetlere erişmek için bir tekniktir. Bu, Blazor uygulamalarda şunları yapmak için yararlı olabilir:

* Hizmet sınıfının tek bir *örneğini tek bir hizmet olarak* bilinen birçok bileşen arasında paylaşabilirsiniz.
* Başvuru soyutlamalarını kullanarak somut hizmet sınıflarından bileşenleri ayırın. Örneğin, `IDataAccess` uygulamadaki verilere erişim için bir arabirim düşünün. Arabirim somut bir sınıf tarafından uygulanır `DataAccess` ve uygulamanın hizmet kapsayıcısında bir hizmet olarak kaydedilir. Bir bileşen bir uygulama almak için DI kullandığında `IDataAccess` , bileşen somut tür ile eşleştirilmez. Uygulama, büyük olasılıkla birim testlerinde bir sahte uygulama için değiştirilebilir.

## <a name="default-services"></a>Varsayılan hizmetler

Varsayılan hizmetler, uygulamanın hizmet koleksiyonuna otomatik olarak eklenir.

| Hizmet | Ömür | Açıklama |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Yayıl | HTTP istekleri göndermek ve bir URI tarafından tanımlanan bir kaynaktan HTTP yanıtlarını almak için yöntemler sağlar.<br><br><xref:System.Net.Http.HttpClient>Bir uygulamadaki örneği, Blazor WebAssembly arka planda HTTP trafiğini işlemek için tarayıcıyı kullanır.<br><br>Blazor Server uygulamalar <xref:System.Net.Http.HttpClient> Varsayılan olarak yapılandırılmış bir hizmet olarak yapılandırılmamış. Bir <xref:System.Net.Http.HttpClient> Blazor Server uygulamaya bir uygulama sağlayın.<br><br>Daha fazla bilgi için bkz. <xref:blazor/call-web-api>. |
| <xref:Microsoft.JSInterop.IJSRuntime> | Singleton ( Blazor WebAssembly )<br>Kapsamlı ( Blazor Server ) | JavaScript çağrılarının dağıtıldığı bir JavaScript çalışma zamanının örneğini temsil eder. Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton ( Blazor WebAssembly )<br>Kapsamlı ( Blazor Server ) | URI 'Ler ve gezinme durumu ile çalışmaya yönelik yardımcıları içerir. Daha fazla bilgi için bkz. [URI ve gezinti durumu yardımcıları](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Özel bir hizmet sağlayıcı, tabloda listelenen varsayılan Hizmetleri otomatik olarak sağlamaz. Özel bir hizmet sağlayıcısı kullanır ve tabloda gösterilen hizmetlerden herhangi birini gerekliyse, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.

## <a name="add-services-to-an-app"></a>Uygulamaya hizmet ekleme

### Blazor WebAssembly

Uygulamasındaki uygulamasının hizmet koleksiyonu için Hizmetleri yapılandırın `Main` `Program.cs` . Aşağıdaki örnekte, `MyDependency` uygulama için kaydedilir `IMyDependency` :

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        await builder.Build().RunAsync();
    }
}
```

Konak oluşturulduktan sonra, herhangi bir bileşen işlenmeden önce kök dı kapsamından hizmetlere erişilebilir. Bu, içerik işlemeden önce başlatma mantığını çalıştırmak için yararlı olabilir:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

Konak, uygulama için bir merkezi yapılandırma örneği de sağlar. Yukarıdaki örnekte derleme yaparken, hava durumu hizmetinin URL 'SI varsayılan bir yapılandırma kaynağından geçirilir (örneğin, `appsettings.json` ) `InitializeWeatherAsync` :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

Yeni bir uygulama oluşturduktan sonra, yöntemini inceleyin `Startup.ConfigureServices` :

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Yöntemi <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , hizmet açıklayıcı nesnelerinin bir listesi olan bir ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ) iletilir. Hizmetler, `ConfigureServices` hizmet koleksiyonuna hizmet tanımlayıcıları sağlayarak yöntemine eklenir. Aşağıdaki örnek, `IDataAccess` arabirimini ve somut uygulamasını içeren kavramı gösterir `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Hizmet ömrü

Hizmetler, aşağıdaki tabloda gösterilen ömürlerle yapılandırılabilir.

| Ömür | Açıklama |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly uygulamalar şu anda bir dı kapsamları kavramı içermez. `Scoped`-kayıtlı hizmetler hizmetler gibi davranır `Singleton` . Ancak, Blazor Server barındırma modeli `Scoped` yaşam süresini destekler. Blazor ServerUygulamalarda, kapsamlı bir hizmet kaydı *bağlantının*kapsamına alınır. Bu nedenle, geçerli amaç tarayıcıda istemci tarafı çalıştırmak olsa bile, kapsama alınmış hizmetlerin kullanılması geçerli kullanıcı kapsamında olması gereken hizmetler için tercih edilir. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Dı, hizmetin *tek bir örneğini* oluşturur. Hizmet gerektiren tüm bileşenler `Singleton` aynı hizmetin bir örneğini alır. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Bir bileşen hizmet kapsayıcısından bir hizmetin örneğini edindiğinde `Transient` , hizmetin *Yeni bir örneğini* alır. |

Dı sistemi ASP.NET Core içindeki DI sistemini temel alır. Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Bir bileşende hizmet isteme

Hizmetler hizmet koleksiyonuna eklendikten sonra, [ \@ ekleme](xref:mvc/views/razor#inject) yönergesini kullanarak hizmetleri bileşenlere ekleyin Razor . [`@inject`](xref:mvc/views/razor#inject) iki parametreye sahiptir:

* Tür: eklenecek hizmetin türü.
* Özellik: eklenen App Service 'i alan özelliğin adı. Özelliği el ile oluşturma gerektirmez. Derleyici özelliği oluşturur.

Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.

[`@inject`](xref:mvc/views/razor#inject)Farklı hizmetler eklemek için birden çok deyim kullanın.

Aşağıdaki örnek nasıl kullanılacağını göstermektedir [`@inject`](xref:mvc/views/razor#inject) . Uygulayan hizmet `Services.IDataAccess` bileşenin özelliğine eklenir `DataRepository` . Kodun yalnızca soyutlamayı nasıl kullandığını aklınızda yapın `IDataAccess` :

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

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

Karmaşık hizmetler için ek hizmetler gerekebilir. Önceki örnekte, `DataAccess` <xref:System.Net.Http.HttpClient> varsayılan hizmet gerekebilir. [`@inject`](xref:mvc/views/razor#inject) (veya [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) özniteliği) hizmetlerde kullanılamaz. Bunun yerine *Oluşturucu Ekleme* kullanılmalıdır. Gerekli hizmetler, hizmetin oluşturucusuna parametreler eklenerek eklenir. Dı hizmeti oluşturduğunda, oluşturucuda gereken hizmetleri algılar ve bunlara göre sağlar. Aşağıdaki örnekte, Oluşturucu bir ile bir ile alır <xref:System.Net.Http.HttpClient> . <xref:System.Net.Http.HttpClient> Varsayılan bir hizmettir.

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
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

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> öğesinden türetilir <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ve KAPSAMDAKI <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> dı sağlayıcısından bir örneğini döndüren bir özellik ekler `T` . Bu tür, <xref:System.IServiceProvider> uygulamanın, bileşenin kapsamını kullanarak dı kapsayıcısından gerektirdiği bir birincil hizmet olduğunda bir örneği kullanmadan kapsamlı hizmetlere erişmenin kolay bir yoludur. <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>Özelliği kullanılabilir, bu sayede uygulama, gerekirse diğer tür hizmetleri alabilir.

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>DI Entity Framework Core (EF Core) DbContext kullanımı

Daha fazla bilgi için bkz. <xref:blazor/blazor-server-ef-core>.

## <a name="detect-transient-disposables"></a>Geçici disposleri Algıla

Aşağıdaki örneklerde, kullanması gereken bir uygulamada atılabilir geçici hizmetlerinin nasıl algılanacağı gösterilmektedir <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . Daha fazla bilgi için bkz. [BIR dı kapsamı bölümünü yönetmek Için yardımcı program temel bileşen sınıfları](#utility-base-component-classes-to-manage-a-di-scope) .

### Blazor WebAssembly

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

`TransientDisposable`Aşağıdaki örnekte algılandı ( `Program.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

`Program`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

`TransientDependency`Aşağıdaki örnekte algılandı ( `Startup.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/dependency-injection>
* [`IDisposable` Geçici ve paylaşılan örnekler için rehberlik](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
