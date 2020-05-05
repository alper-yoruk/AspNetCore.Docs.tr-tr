---
title: ASP.NET Core Blazor bağımlılığı ekleme
author: guardrex
description: Uygulamaların bileşenlere Blazor nasıl hizmet ekleyebilmesi bölümüne bakın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 742f3c5ea26fab5e168f162a0e133da05fd74a74
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767128"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core Blazor bağımlılığı ekleme

Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Mike rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor, [bağımlılık ekleme işlemini (dı)](xref:fundamentals/dependency-injection)destekler. Uygulamalar, yerleşik hizmetleri ekleme tarafından bileşenlere kullanabilir. Uygulamalar Ayrıca özel hizmetleri tanımlayabilir ve kaydedebilir ve bu Hizmetleri uygulama genelinde DI aracılığıyla kullanılabilir hale getirebilirsiniz.

DI, merkezi bir konumda yapılandırılmış hizmetlere erişmek için bir tekniktir. Bu, Blazor uygulamalarında şu şekilde yararlı olabilir:

* Hizmet sınıfının tek bir *örneğini tek bir hizmet olarak* bilinen birçok bileşen arasında paylaşabilirsiniz.
* Başvuru soyutlamalarını kullanarak somut hizmet sınıflarından bileşenleri ayırın. Örneğin, uygulamadaki verilere erişim için `IDataAccess` bir arabirim düşünün. Arabirim somut `DataAccess` bir sınıf tarafından uygulanır ve uygulamanın hizmet kapsayıcısında bir hizmet olarak kaydedilir. Bir bileşen bir `IDataAccess` uygulama almak için dı kullandığında, bileşen somut tür ile eşleştirilmez. Uygulama, büyük olasılıkla birim testlerinde bir sahte uygulama için değiştirilebilir.

## <a name="default-services"></a>Varsayılan hizmetler

Varsayılan hizmetler, uygulamanın hizmet koleksiyonuna otomatik olarak eklenir.

| Hizmet | Ömür | Açıklama |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Larsa | HTTP istekleri göndermek ve bir URI tarafından tanımlanan bir kaynaktan HTTP yanıtlarını almak için yöntemler sağlar.<br><br>Bir Blazor WebAssembly uygulamasındaki örneği `HttpClient` , arka planda HTTP trafiğini işlemek için tarayıcıyı kullanır.<br><br>Blazor Server uygulamaları varsayılan olarak `HttpClient` yapılandırılmış bir hizmet olarak yapılandırma içermez. Bir Blazor `HttpClient` sunucu uygulamasına bir sağlar.<br><br>Daha fazla bilgi için bkz. <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor WebAssembly)<br>Kapsamlı (Blazor sunucusu) | JavaScript çağrılarının dağıtıldığı bir JavaScript çalışma zamanının örneğini temsil eder. Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (Blazor WebAssembly)<br>Kapsamlı (Blazor sunucusu) | URI 'Ler ve gezinme durumu ile çalışmaya yönelik yardımcıları içerir. Daha fazla bilgi için bkz. [URI ve gezinti durumu yardımcıları](xref:blazor/routing#uri-and-navigation-state-helpers). |

Özel bir hizmet sağlayıcı, tabloda listelenen varsayılan Hizmetleri otomatik olarak sağlamaz. Özel bir hizmet sağlayıcısı kullanır ve tabloda gösterilen hizmetlerden herhangi birini gerekliyse, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.

## <a name="add-services-to-an-app"></a>Uygulamaya hizmet ekleme

### <a name="blazor-webassembly"></a>Blazor WebAssembly

`Main` *Program.cs*yönteminde uygulamanın hizmet koleksiyonu için Hizmetleri yapılandırın. Aşağıdaki örnekte, `MyDependency` uygulama için `IMyDependency`kaydedilir:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

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

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

Konak, uygulama için bir merkezi yapılandırma örneği de sağlar. Yukarıdaki örnekte derleme yaparken, hava durumu hizmetinin URL 'SI varsayılan bir yapılandırma kaynağından (örneğin, *appSettings. JSON*) geçirilir `InitializeWeatherAsync`:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a>Blazor Server

Yeni bir uygulama oluşturduktan sonra, `Startup.ConfigureServices` yöntemini inceleyin:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

`ConfigureServices` Yöntemi, hizmet açıklayıcı nesnelerinin <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>bir listesi olan bir (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) iletilir. Hizmetler, hizmet koleksiyonuna hizmet tanımlayıcıları sağlayarak eklenir. Aşağıdaki örnek, `IDataAccess` arabirimini ve somut uygulamasını `DataAccess`içeren kavramı gösterir:

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
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | BlazorWebAssembly uygulamalarında Şu anda bir dı kapsamları kavramı yoktur. `Scoped`-kayıtlı hizmetler hizmetler gibi `Singleton` davranır. Ancak, Blazor sunucu barındırma modeli `Scoped` yaşam süresini destekler. Blazor Sunucu uygulamalarında, kapsamlı bir hizmet kaydı *bağlantının*kapsamına alınır. Bu nedenle, geçerli amaç tarayıcıda istemci tarafı çalıştırmak olsa bile, kapsama alınmış hizmetlerin kullanılması geçerli kullanıcı kapsamında olması gereken hizmetler için tercih edilir. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | Dı, hizmetin *tek bir örneğini* oluşturur. `Singleton` Hizmet gerektiren tüm bileşenler aynı hizmetin bir örneğini alır. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Bir bileşen hizmet kapsayıcısından bir `Transient` hizmetin örneğini edindiğinde, hizmetin yeni bir *örneğini* alır. |

Dı sistemi ASP.NET Core içindeki DI sistemini temel alır. Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Bir bileşende hizmet isteme

Hizmetler hizmet koleksiyonuna eklendikten sonra, [ \@ekleme](xref:mvc/views/razor#inject) Razor yönergesini kullanarak hizmetleri bileşenlere ekleyin. `@inject`iki parametreye sahiptir:

* Eklenecek &ndash; hizmetin türünü yazın.
* Özelliği &ndash; eklenen App Service 'i alan özelliğin adı. Özelliği el ile oluşturma gerektirmez. Derleyici özelliği oluşturur.

Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.

Farklı hizmetler `@inject` eklemek için birden çok deyim kullanın.

Aşağıdaki örnek nasıl kullanılacağını `@inject`göstermektedir. Uygulayan `Services.IDataAccess` hizmet bileşenin özelliğine `DataRepository`eklenir. Kodun yalnızca `IDataAccess` soyutlamayı nasıl kullandığını aklınızda yapın:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Dahili olarak, oluşturulan Özellik (`DataRepository`) `InjectAttribute` özniteliğini kullanır. Genellikle, bu öznitelik doğrudan kullanılmaz. Bileşenler için bir temel sınıf gerekliyse ve temel sınıf için de eklenen özellikler gerekliyse, şunu el ile ekleyin `InjectAttribute`:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Temel sınıftan türetilmiş bileşenlerde, `@inject` yönergesi gerekli değildir. `InjectAttribute` Taban sınıfının yeterli olması yeterlidir:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Hizmetler 'de dı kullanma

Karmaşık hizmetler için ek hizmetler gerekebilir. Önceki örnekte, `DataAccess` `HttpClient` varsayılan hizmet gerekebilir. `@inject`(veya) `InjectAttribute`, hizmetlerde kullanılamaz. Bunun yerine *Oluşturucu Ekleme* kullanılmalıdır. Gerekli hizmetler, hizmetin oluşturucusuna parametreler eklenerek eklenir. Dı hizmeti oluşturduğunda, oluşturucuda gereken hizmetleri algılar ve bunlara göre sağlar.

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

Oluşturucu Ekleme önkoşulları:

* Bağımsız değişkenlerinin tümü DI tarafından yerine getirilme için tek bir Oluşturucu bulunmalıdır. Varsayılan değerleri belirttiklerinde, DI tarafından kapsanmayan ek parametrelere izin verilir.
* Uygulanabilir Oluşturucu *ortak*olmalıdır.
* Uygulanabilir bir Oluşturucu var olmalıdır. Belirsizlik söz konusu olduğunda, bir özel durum oluşturur.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Bir dı kapsamını yönetmek için yardımcı program temel bileşen sınıfları

ASP.NET Core uygulamalarda, kapsamlı hizmetler genellikle geçerli isteğin kapsamlandırılır. İstek tamamlandıktan sonra, tüm kapsamlı veya geçici hizmetler dı sistemi tarafından silinir. Blazor Sunucu uygulamalarında istek kapsamı, istemci bağlantısı süresince sürer. Bu, geçici ve kapsamlı hizmetlerin beklenenden çok daha uzun süreli olarak oluşmasına neden olabilir. Blazor Webassembly uygulamalarında, kapsamlı bir ömürle kaydedilen hizmetler tekton olarak değerlendirilir, bu nedenle tipik ASP.NET Core uygulamalarında kapsamlı hizmetlerden daha uzun bir süre yaşarlar.

Blazor Uygulamalarda bir hizmet ömrünü sınırlayan bir yaklaşım, `OwningComponentBase` türü kullanır. `OwningComponentBase`, öğesinden `ComponentBase` türetilmiş soyut bir türdür ve bileşenin ömrüne karşılık gelen bir dı kapsamı oluşturur. Bu kapsamı kullanarak, dı hizmetlerini kapsamlı bir ömür ile kullanmak mümkündür ve bileşen olarak bu uygulamaları canlı hale gelir. Bileşen yok edildiğinde, bileşenin kapsamlı hizmet sağlayıcısından gelen hizmetler de silinir. Bu, şu hizmetler için yararlı olabilir:

* Geçici ömür uygun olmadığından, bir bileşen içinde yeniden kullanılmalıdır.
* Tek yaşam süresi uygun olmadığından, bileşenler arasında paylaşılmamalıdır.

`OwningComponentBase` Türün iki sürümü kullanılabilir:

* `OwningComponentBase`, türünde Protected `ComponentBase` `ScopedServices` özelliği olan bir abstract, atılabilir alt öğesidir `IServiceProvider`. Bu sağlayıcı, bileşenin kullanım ömrü kapsamındaki Hizmetleri çözümlemek için kullanılabilir.

  Veya `@inject` `InjectAttribute` (`[Inject]`) kullanılarak bileşene eklenen dı Hizmetleri bileşen kapsamında oluşturulmaz. Bileşenin kapsamını kullanmak için, hizmetler veya `ScopedServices.GetRequiredService` `ScopedServices.GetService`kullanılarak çözümlenmelidir. `ScopedServices` Sağlayıcı kullanılarak çözümlenen hizmetlerin, aynı kapsamdaki bağımlılıkları vardır.

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

* `OwningComponentBase<T>`öğesinden `OwningComponentBase` türetilir ve kapsamdaki dı sağlayıcısından `Service` bir örneğini `T` döndüren bir özellik ekler. Bu tür, uygulamanın, bileşenin kapsamını kullanarak dı kapsayıcısından gerektirdiği bir birincil hizmet `IServiceProvider` olduğunda bir örneği kullanmadan kapsamlı hizmetlere erişmenin kolay bir yoludur. `ScopedServices` Özelliği kullanılabilir, bu sayede uygulama, gerekirse diğer tür hizmetleri alabilir.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Dı Entity Framework DbContext kullanımı

Web Apps 'in kaynağından alınacak bir ortak hizmet türü Entity Framework (EF) `DbContext` nesneleridir. Kullanarak `IServiceCollection.AddDbContext` EF Services 'in kaydı, `DbContext` varsayılan olarak kapsamlı bir hizmet olarak ekler. Kapsamlı bir hizmet olarak kaydetme, örneklerin uygulama genelinde uzun Blazor süreli ve paylaşılan olmasına `DbContext` neden olduğu için uygulamalardaki sorunlara yol açabilir. `DbContext`iş parçacığı açısından güvenli değildir ve aynı anda kullanılmamalıdır.

Uygulamaya bağlı olarak, bir `OwningComponentBase` `DbContext` öğesinin kapsamını tek bir bileşen ile sınırlamak için kullanılması sorunu çözebilir *.* Bir `DbContext` bileşen paralel olarak kullanmıyorsa, bileşeni öğesinden kaynağından türeterek `OwningComponentBase` ve `DbContext` kaynağından `ScopedServices` alma yeterlidir çünkü şunları sağlar:

* Ayrı bileşenler bir `DbContext`paylaşmaz.
* `DbContext` Yalnızca bileşen bu bileşene bağlı olarak sürer.

Tek bir bileşen `DbContext` eşzamanlı olarak kullanılabilir (örneğin, Kullanıcı her bir düğme seçtiğinde), kullanılması `OwningComponentBase` de eşzamanlı EF işlemlerinde sorunları önetmez. Bu durumda, her mantıksal EF işlemi `DbContext` için farklı bir kullanın. Aşağıdaki yaklaşımlardan birini kullanın:

* Bir bağımsız `DbContext` değişken olarak `DbContextOptions<TContext>` kullanarak doğrudan oluşturun, bu, dı 'den alınabilir ve iş parçacığı güvenlidir.

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* `DbContext` Hizmeti kapsayıcısına geçici bir yaşam süresi ile kaydedin:
  * Bağlamı kaydederken kullanın `ServiceLifetime.Transient`. Genişletme `AddDbContext` yöntemi, türünde `ServiceLifetime`iki isteğe bağlı parametre alır. Bu yaklaşımı kullanmak için yalnızca `contextLifetime` parametresinin olması `ServiceLifetime.Transient`gerekir. `optionsLifetime`varsayılan değerini tutabilir `ServiceLifetime.Scoped`.

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Geçici `DbContext` , paralel olarak bırden çok EF işlemi yürütemeyecek bileşenlere normal (kullanılarak `@inject`) eklenebilir. Aynı anda birden çok EF işlemi gerçekleştirebilecek olanlar, kullanarak `DbContext` `IServiceProvider.GetRequiredService`her paralel işlem için ayrı nesneler isteyebilir.

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
