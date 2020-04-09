---
title: ASP.NET Blazor Çekirdek bağımlılık enjeksiyonu
author: guardrex
description: Uygulamaların Blazor hizmetleri bileşenlere nasıl enjekte edebileceğine bakın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658076"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Çekirdek Blazor bağımlılık enjeksiyonu

Yazar: [Rainer Stropek](https://www.timecockpit.com) ve [Mike Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor [bağımlılık enjeksiyonu destekler (DI)](xref:fundamentals/dependency-injection). Uygulamalar yerleşik hizmetleri bileşenlere enjekte ederek kullanabilir. Uygulamalar ayrıca özel hizmetleri tanımlayabilir ve kaydedebilir ve bunları DI aracılığıyla uygulama boyunca kullanılabilir hale getirebilir.

DI, merkezi bir konumda yapılandırılan hizmetlere erişmek için bir tekniktir. Bu, Blazor uygulamalarında aşağıdakiler için yararlı olabilir:

* *Tek ton* hizmeti olarak bilinen birçok bileşen arasında hizmet sınıfının tek bir örneğini paylaşın.
* Referans soyutlamaları kullanarak bileşenleri somut hizmet sınıflarından ayırın. Örneğin, uygulamadaki `IDataAccess` verilere erişmek için bir arabirim düşünün. Arabirim, somut `DataAccess` bir sınıf tarafından uygulanır ve uygulamanın servis konteynerinde hizmet olarak kaydedilir. Bir bileşen bir `IDataAccess` uygulama almak için DI kullandığında, bileşen beton türüyle birleştiğinde olmaz. Uygulama, belki birim testlerinde sahte bir uygulama için değiştirilebilir.

## <a name="default-services"></a>Varsayılan hizmetler

Varsayılan hizmetler otomatik olarak uygulamanın hizmet koleksiyonuna eklenir.

| Hizmet | Ömür | Açıklama |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | BIR URI tarafından tanımlanan bir kaynaktan HTTP istekleri göndermek ve HTTP yanıtları almak için yöntemler sağlar.<br><br>Blazor `HttpClient` WebAssembly uygulamasındaki örnek, arka planda http trafiğini işlemek için tarayıcıyı kullanır.<br><br>Blazor Server uygulamaları varsayılan `HttpClient` olarak bir hizmet olarak yapılandırılmış içermez. Bir `HttpClient` Blazor Server uygulaması sağlayın.<br><br>Daha fazla bilgi için bkz. <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor WebAssembly)<br>Scoped (Blazor Server) | JavaScript çağrılarının gönderildiği bir JavaScript çalışma zamanı örneğini temsil eder. Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (Blazor WebAssembly)<br>Scoped (Blazor Server) | URI'ler ve navigasyon durumuyla çalışmak için yardımcılar içerir. Daha fazla bilgi için [URI ve navigasyon durumu yardımcıları](xref:blazor/routing#uri-and-navigation-state-helpers)bakın. |

Özel bir hizmet sağlayıcısı, tabloda listelenen varsayılan hizmetleri otomatik olarak sağlamaz. Özel bir hizmet sağlayıcısı kullanıyorsanız ve tabloda gösterilen hizmetlerden herhangi birini gerektiriyorsanız, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.

## <a name="add-services-to-an-app"></a>Uygulamaya hizmet ekleme

### <a name="blazor-webassembly"></a>Blazor WebAssembly

`Main` *Uygulamanın*hizmet toplama hizmetlerini Program.cs yöntemiyle yapılandırın. Aşağıdaki örnekte, `MyDependency` uygulama için `IMyDependency`kaydedilir:

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

Ana bilgisayar oluşturuluntan sonra, hizmetlere herhangi bir bileşen işlenmeden önce kök DI kapsamından erişilebilir. Bu, içeriği oluşturmadan önce başlatma mantığını çalıştırmak için yararlı olabilir:

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

Ana bilgisayar, uygulama için merkezi bir yapılandırma örneği de sağlar. Önceki örnekte bina, hava durumu hizmetinin URL'si varsayılan bir yapılandırma kaynağından (örneğin, *appsettings.json)* `InitializeWeatherAsync`geçirilir:

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

Yeni bir uygulama oluşturduktan `Startup.ConfigureServices` sonra yöntemi inceleyin:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Yöntem, `ConfigureServices` hizmet <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>tanımlayıcı nesnelerin listesi olan bir ()<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>geçirilir. Hizmetler, hizmet koleksiyonuna hizmet tanımlayıcıları sağlanarak eklenir. Aşağıdaki `IDataAccess` örnek, arayüz ve somut uygulama `DataAccess`ile kavramı göstermektedir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Hizmet ömrü

Hizmetler, aşağıdaki tabloda gösterilen yaşam süreleriyle yapılandırılabilir.

| Ömür | Açıklama |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | BlazorWebAssembly uygulamaları şu anda DI kapsamları kavramı yok. `Scoped`-kayıtlı hizmetler hizmet `Singleton` gibi olur. Ancak, Blazor Sunucu barındırma modeli `Scoped` ömür boyu destekler. Sunucu Blazor uygulamalarında, kapsamlı bir hizmet kaydı *bağlantıya*kapsamlıdır. Bu nedenle, geçerli amaç istemci tarafında tarayıcıda çalıştırmak olsa bile, geçerli kullanıcıya kapsamlı olması gereken hizmetler için kapsamlı hizmetlerin kullanılması tercih edilir. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI, hizmetin tek bir *örneğini* oluşturur. Hizmet `Singleton` gerektiren tüm bileşenler aynı hizmetin bir örneğini alır. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Bir bileşen hizmet kapsayıcısından bir `Transient` hizmet örneği aldığında, hizmetin yeni bir *örneğini* alır. |

DI sistemi ASP.NET Core'daki DI sistemine dayanır. Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Bileşende hizmet isteme

Hizmetler hizmet koleksiyonuna eklendikten sonra, razor [ \@direktifini kullanarak](xref:mvc/views/razor#inject) hizmetleri bileşenlere enjekte edin. `@inject`iki parametrevardır:

* Enjekte &ndash; etmek için hizmet türünü yazın.
* Özellik &ndash; Enjekte edilen uygulama hizmetini alan özelliğin adı. Özellik manuel oluşturma gerektirmez. Derleyici özelliği oluşturur.

Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.

Farklı `@inject` hizmetler enjekte etmek için birden çok deyim kullanın.

Aşağıdaki örnekte nasıl `@inject`kullanılacağı gösterilmektedir. Hizmet uygulaması `Services.IDataAccess` bileşenin özelliğine `DataRepository`enjekte edilir. Kodun yalnızca soyutlamayı `IDataAccess` nasıl kullandığına dikkat edin:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Dahili olarak, oluşturulan`DataRepository`özellik `InjectAttribute` ( ) özniteliği kullanır. Genellikle, bu öznitelik doğrudan kullanılmaz. Bileşenler için bir taban sınıf gerekiyorsa ve taban sınıf için enjekte edilen `InjectAttribute`özellikler de gerekliyse, el ile ekleyin:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Taban sınıftan türetilen bileşenlerde `@inject` yönerge gerekli değildir. `InjectAttribute` Taban sınıfın yeterlidir:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Hizmetlerde DI'yi kullanma

Karmaşık hizmetler ek hizmetler gerektirebilir. Önceki örnekte, `DataAccess` `HttpClient` varsayılan hizmet gerektirebilir. `@inject``InjectAttribute`(veya) hizmetlerde kullanılamaz. *Bunun* yerine yapıcı enjeksiyon kullanılmalıdır. Gerekli hizmetler, hizmetin oluşturucuya parametreler eklenerek eklenir. DI hizmeti oluşturduğunda, oluşturucuda ihtiyaç duyduğu hizmetleri tanır ve buna göre sağlar.

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

Konstrüktör enjeksiyonu için ön koşullar:

* Bir oluşturucu olan argümanlar tüm DI tarafından yerine getirilebilir var olmalıdır. Varsayılan değerleri belirtirlerse, DI tarafından kapsanmayan ek parametrelere izin verilir.
* İlgili yapıcı *genel*olmalıdır.
* Uygulanabilir bir yapıcı var olmalıdır. Bir belirsizlik durumunda, DI bir özel durum atar.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Bir DI kapsamını yönetmek için yardımcı program temel bileşen sınıfları

ASP.NET Core uygulamalarında, kapsamlı hizmetler genellikle geçerli isteğe göre kapsamlıdır. İstek tamamlandıktan sonra, kapsamlı veya geçici hizmetler DI sistemi tarafından bertaraf edilir. Sunucu Blazor uygulamalarında, istek kapsamı istemci bağlantısı süresince devam eder ve bu da geçici ve kapsamlı hizmetlerin beklenenden çok daha uzun yaşamalarına neden olabilir. WebAssembly uygulamalarında, Blazor kapsamlı bir kullanım ömrüne sahip hizmetler singletons olarak kabul edilir, bu nedenle tipik ASP.NET Core uygulamalarındaki kapsamlı hizmetlerden daha uzun yaşarlar.

Uygulamalarda Blazor hizmet ömrünü sınırlayan bir yaklaşım, `OwningComponentBase` bu tür de kullanılır. `OwningComponentBase`bileşenin `ComponentBase` ömrüne karşılık gelen bir DI kapsamı oluşturan soyut bir türdür. Bu kapsamı kullanarak, DI hizmetlerini kapsamlı bir ömür boyu kullanmak ve bileşen kadar uzun süre yaşamalarını sağlamak mümkündür. Bileşen yok edildiğinde, bileşenin kapsamlı servis sağlayıcısının hizmetleri de bertaraf edilir. Bu, aşağıdaki hizmetler için yararlı olabilir:

* Geçici ömür uygun olmadığından, bir bileşen içinde yeniden kullanılmalıdır.
* Singleton ömrü uygun olmadığından, bileşenler arasında paylaşılmamalıdır.

`OwningComponentBase` Türünün iki sürümü mevcuttur:

* `OwningComponentBase``ComponentBase` türünde `ScopedServices` `IServiceProvider`korumalı bir özelliğe sahip türün soyut, tek kullanımlık bir çocuktur. Bu sağlayıcı, bileşenin ömrüne göre kapsamda olan hizmetleri çözümlemek için kullanılabilir.

  Bileşene enjekte `@inject` edilen DI hizmetleri `InjectAttribute` `[Inject]`veya ( ) bileşenin kapsamında oluşturulmaz. Bileşenin kapsamını kullanmak için, hizmetlerin kullanılarak `ScopedServices.GetRequiredService` çözülmesi `ScopedServices.GetService`veya . `ScopedServices` Sağlayıcı kullanılarak çözüme kavuşturulan tüm hizmetlerin bağımlılıkları aynı kapsamdan sağlanır.

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

* `OwningComponentBase<T>`kapsamına giren `OwningComponentBase` DI sağlayıcısından bir örneğini `Service` `T` döndüren bir özellikten türetilmiştir ve ekler. Bu tür, uygulamanın bileşenin kapsamını kullanarak DI `IServiceProvider` kapsayıcısından gerektirdiği bir birincil hizmetin ne zaman olduğunu kullanmadan kapsamlı hizmetlere erişmek için kullanışlı bir yoldur. `ScopedServices` Özellik kullanılabilir, böylece uygulama gerekirse diğer türde hizmetler alabilirsiniz.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>DI'den Varlık Çerçevesi DbContext Kullanımı

Web uygulamalarında DI'den alınacak yaygın bir hizmet `DbContext` türü de Entity Framework (EF) nesneleridir. EF hizmetlerini kullanarak `IServiceCollection.AddDbContext` kaydetmek `DbContext` varsayılan olarak kapsamlı bir hizmet olarak ekler. Kapsamlı bir hizmet olarak kaydolmak, Blazor örneklerin `DbContext` uzun ömürlü olmasına ve uygulama genelinde paylaşılmasına neden olduğundan uygulamalarda sorunlara yol açabilir. `DbContext`iş parçacığı güvenli değildir ve aynı anda kullanılmamalıdır.

Uygulamaya bağlı olarak, `OwningComponentBase` a'nın `DbContext` kapsamını tek bir bileşenle sınırlamak için kullanmak sorunu *çözebilir.* Bir bileşen paralel olarak `DbContext` bir bileşen kullanmıyorsa, `OwningComponentBase` bileşeni elde etmek `DbContext` ve `ScopedServices` ondan almak yeterlidir, çünkü aşağıdakileri sağlar:

* Ayrı bileşenler bir `DbContext`.
* Sadece `DbContext` bileşen bağlı olduğu sürece yaşıyor.

Tek bir bileşen aynı `DbContext` anda bir bileşen kullanıyorsa (örneğin, bir `OwningComponentBase` kullanıcı her düğmeyi seçtiğinde), kullanmak bile eşzamanlı EF işlemleriyle ilgili sorunları önlemez. Bu durumda, her `DbContext` mantıksal EF işlemi için farklı bir kullanın. Aşağıdaki yaklaşımlardan birini kullanın:

* DI'den alınabilir ve iş parçacığı güvenli bir bağımsız değişken olarak `DbContext` doğrudan kullanarak `DbContextOptions<TContext>` oluşturun.

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

* Servis `DbContext` konteynerini geçici bir kullanım ömrüyle kaydedin:
  * Bağlamı kaydederken, `ServiceLifetime.Transient`.' Uzantı `AddDbContext` yöntemi, türünün `ServiceLifetime`iki isteğe bağlı parametresini alır. Bu yaklaşımı kullanmak için `contextLifetime` yalnızca parametrenin . `ServiceLifetime.Transient` `optionsLifetime`varsayılan değerini `ServiceLifetime.Scoped`koruyabilir.

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Geçici, `DbContext` paralel olarak birden fazla `@inject`EF işlemini yürütmeyecek bileşenlere normal (using) olarak enjekte edilebilir. Aynı anda birden çok EF işlemi `DbContext` gerçekleştirebilenler, `IServiceProvider.GetRequiredService`her paralel işlem için ayrı nesneler isteyebilirler.

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
