---
Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core Blazor bağımlılığı ekleme

Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Mike rousos](https://github.com/mjrousos)

Blazor[bağımlılık ekleme işlemini (dı)](xref:fundamentals/dependency-injection)destekler. Uygulamalar, yerleşik hizmetleri ekleme tarafından bileşenlere kullanabilir. Uygulamalar Ayrıca özel hizmetleri tanımlayabilir ve kaydedebilir ve bu Hizmetleri uygulama genelinde DI aracılığıyla kullanılabilir hale getirebilirsiniz.

DI, merkezi bir konumda yapılandırılmış hizmetlere erişmek için bir tekniktir. Bu, Blazor uygulamalarda şunları yapmak için yararlı olabilir:

* Hizmet sınıfının tek bir *örneğini tek bir hizmet olarak* bilinen birçok bileşen arasında paylaşabilirsiniz.
* Başvuru soyutlamalarını kullanarak somut hizmet sınıflarından bileşenleri ayırın. Örneğin, `IDataAccess` uygulamadaki verilere erişim için bir arabirim düşünün. Arabirim somut bir sınıf tarafından uygulanır `DataAccess` ve uygulamanın hizmet kapsayıcısında bir hizmet olarak kaydedilir. Bir bileşen bir uygulama almak için DI kullandığında `IDataAccess` , bileşen somut tür ile eşleştirilmez. Uygulama, büyük olasılıkla birim testlerinde bir sahte uygulama için değiştirilebilir.

## <a name="default-services"></a>Varsayılan hizmetler

Varsayılan hizmetler, uygulamanın hizmet koleksiyonuna otomatik olarak eklenir.

| Hizmet | Ömür | Açıklama |
| ---
Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---- | ---başlığı: ' ASP.NET Core Blazor bağımlılığı ekleme ' Yazar: Açıklama: ' Blazor uygulamaların bileşenlere nasıl hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---- | ---başlığı: ' ASP.NET Core Blazor bağımlılığı ekleme ' Yazar: Açıklama: ' Blazor uygulamaların bileşenlere nasıl hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | | <xref:System.Net.Http.HttpClient> | Geçici | HTTP istekleri göndermek ve bir URI tarafından tanımlanan bir kaynaktan HTTP yanıtlarını almak için yöntemler sağlar.<br><br><xref:System.Net.Http.HttpClient> Blazor Webassembly uygulamasındaki örneği, arka planda HTTP trafiğini işlemek için tarayıcıyı kullanır.<br><br>BlazorSunucu uygulamaları <xref:System.Net.Http.HttpClient> Varsayılan olarak yapılandırılmış bir hizmet olarak yapılandırma içermez. Bir <xref:System.Net.Http.HttpClient> Blazor sunucu uygulamasına bir sağlar.<br><br>Daha fazla bilgi için bkz. <xref:blazor/call-web-api>. | | <xref:Microsoft.JSInterop.IJSRuntime> | Singleton ( Blazor webassembly)<br>Kapsamlı ( Blazor sunucu) | JavaScript çağrılarının dağıtıldığı bir JavaScript çalışma zamanının örneğini temsil eder. Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>. | | <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton ( Blazor webassembly)<br>Kapsamlı ( Blazor sunucu) | URI 'Ler ve gezinme durumu ile çalışmaya yönelik yardımcıları içerir. Daha fazla bilgi için bkz. [URI ve gezinti durumu yardımcıları](xref:blazor/routing#uri-and-navigation-state-helpers). |

Özel bir hizmet sağlayıcı, tabloda listelenen varsayılan Hizmetleri otomatik olarak sağlamaz. Özel bir hizmet sağlayıcısı kullanır ve tabloda gösterilen hizmetlerden herhangi birini gerekliyse, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.

## <a name="add-services-to-an-app"></a>Uygulamaya hizmet ekleme

### <a name="blazor-webassembly"></a>BlazorWebAssembly

Program.cs yönteminde uygulamanın hizmet koleksiyonu için Hizmetleri yapılandırın `Main` . *Program.cs* Aşağıdaki örnekte, `MyDependency` uygulama için kaydedilir `IMyDependency` :

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

Konak, uygulama için bir merkezi yapılandırma örneği de sağlar. Yukarıdaki örnekte derleme yaparken, hava durumu hizmetinin URL 'SI varsayılan bir yapılandırma kaynağından (örneğin, *appSettings. JSON*) geçirilir `InitializeWeatherAsync` :

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

### <a name="blazor-server"></a>BlazorServer

Yeni bir uygulama oluşturduktan sonra, yöntemini inceleyin `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Yöntemi <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , hizmet açıklayıcı nesnelerinin bir listesi olan bir ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ) iletilir. Hizmetler, hizmet koleksiyonuna hizmet tanımlayıcıları sağlayarak eklenir. Aşağıdaki örnek, `IDataAccess` arabirimini ve somut uygulamasını içeren kavramı gösterir `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Hizmet ömrü

Hizmetler, aşağıdaki tabloda gösterilen ömürlerle yapılandırılabilir.

| Ömür | Açıklama |
| ---
Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---- | ---başlığı: ' ASP.NET Core Blazor bağımlılığı ekleme ' Yazar: Açıklama: ' Blazor uygulamaların bileşenlere nasıl hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly uygulamalarında Şu anda bir dı kapsamları kavramı yoktur. `Scoped`-kayıtlı hizmetler hizmetler gibi davranır `Singleton` . Ancak, Blazor sunucu barındırma modeli `Scoped` yaşam süresini destekler. BlazorSunucu uygulamalarında, kapsamlı bir hizmet kaydı *bağlantının*kapsamına alınır. Bu nedenle, geçerli amaç tarayıcıda istemci tarafı çalıştırmak olsa bile, kapsama alınmış hizmetlerin kullanılması geçerli kullanıcı kapsamında olması gereken hizmetler için tercih edilir. | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Dı, hizmetin *tek bir örneğini* oluşturur. Hizmet gerektiren tüm bileşenler `Singleton` aynı hizmetin bir örneğini alır. | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Bir bileşen hizmet kapsayıcısından bir hizmetin örneğini edindiğinde `Transient` , hizmetin *Yeni bir örneğini* alır. |

Dı sistemi ASP.NET Core içindeki DI sistemini temel alır. Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Bir bileşende hizmet isteme

Hizmetler hizmet koleksiyonuna eklendikten sonra, [ \@ ekleme](xref:mvc/views/razor#inject) yönergesini kullanarak hizmetleri bileşenlere ekleyin Razor . [`@inject`](xref:mvc/views/razor#inject)iki parametreye sahiptir:

* &ndash;Eklenecek hizmetin türünü yazın.
* Özelliği &ndash; eklenen App Service 'i alan özelliğin adı. Özelliği el ile oluşturma gerektirmez. Derleyici özelliği oluşturur.

Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.

[`@inject`](xref:mvc/views/razor#inject)Farklı hizmetler eklemek için birden çok deyim kullanın.

Aşağıdaki örnek nasıl kullanılacağını göstermektedir [`@inject`](xref:mvc/views/razor#inject) . Uygulayan hizmet `Services.IDataAccess` bileşenin özelliğine eklenir `DataRepository` . Kodun yalnızca soyutlamayı nasıl kullandığını aklınızda yapın `IDataAccess` :

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

Dahili olarak, oluşturulan Özellik ( `DataRepository` ) özniteliğini kullanır [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) . Genellikle, bu öznitelik doğrudan kullanılmaz. Bileşenler için bir temel sınıf gerekliyse ve temel sınıf için eklenen özellikler de gerekliyse, özniteliği el ile ekleyin [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) :

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
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

Karmaşık hizmetler için ek hizmetler gerekebilir. Önceki örnekte, `DataAccess` <xref:System.Net.Http.HttpClient> varsayılan hizmet gerekebilir. [`@inject`](xref:mvc/views/razor#inject)(veya [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) özniteliği) hizmetlerde kullanılamaz. Bunun yerine *Oluşturucu Ekleme* kullanılmalıdır. Gerekli hizmetler, hizmetin oluşturucusuna parametreler eklenerek eklenir. Dı hizmeti oluşturduğunda, oluşturucuda gereken hizmetleri algılar ve bunlara göre sağlar.

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

ASP.NET Core uygulamalarda, kapsamlı hizmetler genellikle geçerli isteğin kapsamlandırılır. İstek tamamlandıktan sonra, tüm kapsamlı veya geçici hizmetler dı sistemi tarafından silinir. BlazorSunucu uygulamalarında istek kapsamı, istemci bağlantısı süresince sürer. Bu, geçici ve kapsamlı hizmetlerin beklenenden çok daha uzun süreli olarak oluşmasına neden olabilir. BlazorWebassembly uygulamalarında, kapsamlı bir ömürle kaydedilen hizmetler tekton olarak değerlendirilir, bu nedenle tipik ASP.NET Core uygulamalarında kapsamlı hizmetlerden daha uzun bir süre yaşarlar.

Uygulamalarda bir hizmet ömrünü sınırlayan bir yaklaşım Blazor , <xref:Microsoft.AspNetCore.Components.OwningComponentBase> türü kullanır. <xref:Microsoft.AspNetCore.Components.OwningComponentBase>, öğesinden türetilmiş soyut bir türdür <xref:Microsoft.AspNetCore.Components.ComponentBase> ve bileşenin ömrüne karşılık gelen BIR dı kapsamı oluşturur. Bu kapsamı kullanarak, dı hizmetlerini kapsamlı bir ömür ile kullanmak mümkündür ve bileşen olarak bu uygulamaları canlı hale gelir. Bileşen yok edildiğinde, bileşenin kapsamlı hizmet sağlayıcısından gelen hizmetler de silinir. Bu, şu hizmetler için yararlı olabilir:

* Geçici ömür uygun olmadığından, bir bileşen içinde yeniden kullanılmalıdır.
* Tek yaşam süresi uygun olmadığından, bileşenler arasında paylaşılmamalıdır.

Türün iki sürümü <xref:Microsoft.AspNetCore.Components.OwningComponentBase> kullanılabilir:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase>, türünde <xref:Microsoft.AspNetCore.Components.ComponentBase> Protected özelliği olan bir abstract, atılabilir alt öğesidir <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> <xref:System.IServiceProvider> . Bu sağlayıcı, bileşenin kullanım ömrü kapsamındaki Hizmetleri çözümlemek için kullanılabilir.

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

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>öğesinden türetilir <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ve KAPSAMDAKI <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> dı sağlayıcısından bir örneğini döndüren bir özellik ekler `T` . Bu tür, <xref:System.IServiceProvider> uygulamanın, bileşenin kapsamını kullanarak dı kapsayıcısından gerektirdiği bir birincil hizmet olduğunda bir örneği kullanmadan kapsamlı hizmetlere erişmenin kolay bir yoludur. <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>Özelliği kullanılabilir, bu sayede uygulama, gerekirse diğer tür hizmetleri alabilir.

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

Web Apps 'in kaynağından alınacak bir ortak hizmet türü Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> nesneleridir. Kullanarak EF Services <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 'in kaydı, <xref:Microsoft.EntityFrameworkCore.DbContext> Varsayılan olarak kapsamlı bir hizmet olarak ekler. Kapsamlı bir hizmet olarak kaydetme Blazor <xref:Microsoft.EntityFrameworkCore.DbContext> , örneklerin uygulama genelinde uzun süreli ve paylaşılan olmasına neden olduğu için uygulamalardaki sorunlara yol açabilir. <xref:Microsoft.EntityFrameworkCore.DbContext>iş parçacığı açısından güvenli değildir ve aynı anda kullanılmamalıdır.

Uygulamaya bağlı olarak, <xref:Microsoft.AspNetCore.Components.OwningComponentBase> bir öğesinin kapsamını tek bir bileşen ile sınırlamak için kullanılması <xref:Microsoft.EntityFrameworkCore.DbContext> sorunu çözebilir *may* . Bir bileşen <xref:Microsoft.EntityFrameworkCore.DbContext> paralel olarak kullanmıyorsa, bileşeni öğesinden kaynağından türeterek <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ve <xref:Microsoft.EntityFrameworkCore.DbContext> kaynağından alma <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> yeterlidir çünkü şunları sağlar:

* Ayrı bileşenler bir paylaşmaz <xref:Microsoft.EntityFrameworkCore.DbContext> .
* <xref:Microsoft.EntityFrameworkCore.DbContext>Yalnızca bileşen bu bileşene bağlı olarak sürer.

Tek bir bileşen eşzamanlı olarak kullanılabilir <xref:Microsoft.EntityFrameworkCore.DbContext> (örneğin, Kullanıcı her bir düğme seçtiğinde), kullanılması de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> eşzamanlı EF işlemlerinde sorunları önetmez. Bu durumda, <xref:Microsoft.EntityFrameworkCore.DbContext> her MANTıKSAL EF işlemi için farklı bir kullanın. Aşağıdaki yaklaşımlardan birini kullanın:

* <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> Bir bağımsız değişken olarak kullanarak doğrudan oluşturun, bu, dı 'den alınabilir ve iş parçacığı güvenlidir.

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
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

* <xref:Microsoft.EntityFrameworkCore.DbContext>Hizmeti kapsayıcısına geçici bir yaşam süresi ile kaydedin:
  * Bağlamı kaydederken kullanın <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>Genişletme yöntemi, türünde iki isteğe bağlı parametre alır <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> . Bu yaklaşımı kullanmak için yalnızca `contextLifetime` parametresinin olması gerekir <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . `optionsLifetime`varsayılan değerini tutabilir <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Geçici, <xref:Microsoft.EntityFrameworkCore.DbContext> [`@inject`](xref:mvc/views/razor#inject) paralel olarak bırden çok EF işlemi yürütemeyecek bileşenlere normal (kullanılarak) eklenebilir. Aynı anda birden çok EF işlemi gerçekleştirebilecek olanlar, <xref:Microsoft.EntityFrameworkCore.DbContext> kullanarak her paralel işlem için ayrı nesneler isteyebilir <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
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
