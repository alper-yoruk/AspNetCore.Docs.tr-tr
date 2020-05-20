---
<span data-ttu-id="14143-101">Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-101">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-103">'Blazor'</span></span>
- <span data-ttu-id="14143-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-104">'Identity'</span></span>
- <span data-ttu-id="14143-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-106">'Razor'</span></span>
- <span data-ttu-id="14143-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="14143-108">ASP.NET Core Blazor bağımlılığı ekleme</span><span class="sxs-lookup"><span data-stu-id="14143-108">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="14143-109">Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Mike rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="14143-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="14143-110">[bağımlılık ekleme işlemini (dı)](xref:fundamentals/dependency-injection)destekler.</span><span class="sxs-lookup"><span data-stu-id="14143-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="14143-111">Uygulamalar, yerleşik hizmetleri ekleme tarafından bileşenlere kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="14143-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="14143-112">Uygulamalar Ayrıca özel hizmetleri tanımlayabilir ve kaydedebilir ve bu Hizmetleri uygulama genelinde DI aracılığıyla kullanılabilir hale getirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14143-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="14143-113">DI, merkezi bir konumda yapılandırılmış hizmetlere erişmek için bir tekniktir.</span><span class="sxs-lookup"><span data-stu-id="14143-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="14143-114">Bu, Blazor uygulamalarda şunları yapmak için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="14143-114">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="14143-115">Hizmet sınıfının tek bir *örneğini tek bir hizmet olarak* bilinen birçok bileşen arasında paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14143-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="14143-116">Başvuru soyutlamalarını kullanarak somut hizmet sınıflarından bileşenleri ayırın.</span><span class="sxs-lookup"><span data-stu-id="14143-116">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="14143-117">Örneğin, `IDataAccess` uygulamadaki verilere erişim için bir arabirim düşünün.</span><span class="sxs-lookup"><span data-stu-id="14143-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="14143-118">Arabirim somut bir sınıf tarafından uygulanır `DataAccess` ve uygulamanın hizmet kapsayıcısında bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="14143-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="14143-119">Bir bileşen bir uygulama almak için DI kullandığında `IDataAccess` , bileşen somut tür ile eşleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="14143-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="14143-120">Uygulama, büyük olasılıkla birim testlerinde bir sahte uygulama için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="14143-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="14143-121">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="14143-121">Default services</span></span>

<span data-ttu-id="14143-122">Varsayılan hizmetler, uygulamanın hizmet koleksiyonuna otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="14143-122">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="14143-123">Hizmet</span><span class="sxs-lookup"><span data-stu-id="14143-123">Service</span></span> | <span data-ttu-id="14143-124">Ömür</span><span class="sxs-lookup"><span data-stu-id="14143-124">Lifetime</span></span> | <span data-ttu-id="14143-125">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14143-125">Description</span></span> |
| ---
<span data-ttu-id="14143-126">Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-126">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-127">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-127">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-128">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-128">'Blazor'</span></span>
- <span data-ttu-id="14143-129">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-129">'Identity'</span></span>
- <span data-ttu-id="14143-130">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-130">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-131">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-131">'Razor'</span></span>
- <span data-ttu-id="14143-132">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-132">'SignalR' uid:</span></span> 

<span data-ttu-id="14143-133">---- | ---başlığı: ' ASP.NET Core Blazor bağımlılığı ekleme ' Yazar: Açıklama: ' Blazor uygulamaların bileşenlere nasıl hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-133">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-134">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-134">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-135">'Blazor'</span></span>
- <span data-ttu-id="14143-136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-136">'Identity'</span></span>
- <span data-ttu-id="14143-137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-137">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-138">'Razor'</span></span>
- <span data-ttu-id="14143-139">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="14143-140">Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-140">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-141">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-141">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-142">'Blazor'</span></span>
- <span data-ttu-id="14143-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-143">'Identity'</span></span>
- <span data-ttu-id="14143-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-145">'Razor'</span></span>
- <span data-ttu-id="14143-146">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-146">'SignalR' uid:</span></span> 

<span data-ttu-id="14143-147">---- | ---başlığı: ' ASP.NET Core Blazor bağımlılığı ekleme ' Yazar: Açıklama: ' Blazor uygulamaların bileşenlere nasıl hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-147">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-148">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-148">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-149">'Blazor'</span></span>
- <span data-ttu-id="14143-150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-150">'Identity'</span></span>
- <span data-ttu-id="14143-151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-151">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-152">'Razor'</span></span>
- <span data-ttu-id="14143-153">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="14143-154">Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-154">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-155">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-155">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-156">'Blazor'</span></span>
- <span data-ttu-id="14143-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-157">'Identity'</span></span>
- <span data-ttu-id="14143-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-159">'Razor'</span></span>
- <span data-ttu-id="14143-160">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="14143-161">Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-161">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-162">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-162">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-163">'Blazor'</span></span>
- <span data-ttu-id="14143-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-164">'Identity'</span></span>
- <span data-ttu-id="14143-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-166">'Razor'</span></span>
- <span data-ttu-id="14143-167">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-167">'SignalR' uid:</span></span> 

<span data-ttu-id="14143-168">------ | | <xref:System.Net.Http.HttpClient> | Geçici | HTTP istekleri göndermek ve bir URI tarafından tanımlanan bir kaynaktan HTTP yanıtlarını almak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="14143-168">------ | | <xref:System.Net.Http.HttpClient> | Transient | Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="14143-169">`HttpClient` Blazor Webassembly uygulamasındaki örneği, arka planda HTTP trafiğini işlemek için tarayıcıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="14143-169">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="14143-170">Sunucu uygulamaları `HttpClient` Varsayılan olarak yapılandırılmış bir hizmet olarak yapılandırma içermez.</span><span class="sxs-lookup"><span data-stu-id="14143-170"> Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="14143-171">Bir `HttpClient` Blazor sunucu uygulamasına bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="14143-171">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="14143-172">Daha fazla bilgi için bkz. <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="14143-172">For more information, see <xref:blazor/call-web-api>.</span></span> <span data-ttu-id="14143-173">| | `IJSRuntime` | Singleton ( Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="14143-173">| | `IJSRuntime` | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="14143-174">Kapsamlı ( Blazor sunucu) | JavaScript çağrılarının dağıtıldığı bir JavaScript çalışma zamanının örneğini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="14143-174">Scoped (Blazor Server) | Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="14143-175">Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="14143-175">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="14143-176">| | `NavigationManager` | Singleton ( Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="14143-176">| | `NavigationManager` | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="14143-177">Kapsamlı ( Blazor sunucu) | URI 'Ler ve gezinme durumu ile çalışmaya yönelik yardımcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="14143-177">Scoped (Blazor Server) | Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="14143-178">Daha fazla bilgi için bkz. [URI ve gezinti durumu yardımcıları](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="14143-178">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="14143-179">Özel bir hizmet sağlayıcı, tabloda listelenen varsayılan Hizmetleri otomatik olarak sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="14143-179">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="14143-180">Özel bir hizmet sağlayıcısı kullanır ve tabloda gösterilen hizmetlerden herhangi birini gerekliyse, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="14143-180">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="14143-181">Uygulamaya hizmet ekleme</span><span class="sxs-lookup"><span data-stu-id="14143-181">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="14143-182">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="14143-182"> WebAssembly</span></span>

<span data-ttu-id="14143-183">Program.cs yönteminde uygulamanın hizmet koleksiyonu için Hizmetleri yapılandırın `Main` . *Program.cs*</span><span class="sxs-lookup"><span data-stu-id="14143-183">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="14143-184">Aşağıdaki örnekte, `MyDependency` uygulama için kaydedilir `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="14143-184">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="14143-185">Konak oluşturulduktan sonra, herhangi bir bileşen işlenmeden önce kök dı kapsamından hizmetlere erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="14143-185">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="14143-186">Bu, içerik işlemeden önce başlatma mantığını çalıştırmak için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="14143-186">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="14143-187">Konak, uygulama için bir merkezi yapılandırma örneği de sağlar.</span><span class="sxs-lookup"><span data-stu-id="14143-187">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="14143-188">Yukarıdaki örnekte derleme yaparken, hava durumu hizmetinin URL 'SI varsayılan bir yapılandırma kaynağından (örneğin, *appSettings. JSON*) geçirilir `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="14143-188">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="14143-189">Server</span><span class="sxs-lookup"><span data-stu-id="14143-189"> Server</span></span>

<span data-ttu-id="14143-190">Yeni bir uygulama oluşturduktan sonra, yöntemini inceleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="14143-190">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="14143-191">`ConfigureServices`Yöntemi <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , hizmet açıklayıcı nesnelerinin bir listesi olan bir ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ) iletilir.</span><span class="sxs-lookup"><span data-stu-id="14143-191">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="14143-192">Hizmetler, hizmet koleksiyonuna hizmet tanımlayıcıları sağlayarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="14143-192">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="14143-193">Aşağıdaki örnek, `IDataAccess` arabirimini ve somut uygulamasını içeren kavramı gösterir `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="14143-193">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="14143-194">Hizmet ömrü</span><span class="sxs-lookup"><span data-stu-id="14143-194">Service lifetime</span></span>

<span data-ttu-id="14143-195">Hizmetler, aşağıdaki tabloda gösterilen ömürlerle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14143-195">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="14143-196">Ömür</span><span class="sxs-lookup"><span data-stu-id="14143-196">Lifetime</span></span> | <span data-ttu-id="14143-197">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14143-197">Description</span></span> |
| ---
<span data-ttu-id="14143-198">Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-198">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-199">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-199">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-200">'Blazor'</span></span>
- <span data-ttu-id="14143-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-201">'Identity'</span></span>
- <span data-ttu-id="14143-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-203">'Razor'</span></span>
- <span data-ttu-id="14143-204">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="14143-205">Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-205">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-206">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-206">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-207">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-207">'Blazor'</span></span>
- <span data-ttu-id="14143-208">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-208">'Identity'</span></span>
- <span data-ttu-id="14143-209">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-209">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-210">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-210">'Razor'</span></span>
- <span data-ttu-id="14143-211">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-211">'SignalR' uid:</span></span> 

<span data-ttu-id="14143-212">---- | ---başlığı: ' ASP.NET Core Blazor bağımlılığı ekleme ' Yazar: Açıklama: ' Blazor uygulamaların bileşenlere nasıl hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-212">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-213">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-213">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-214">'Blazor'</span></span>
- <span data-ttu-id="14143-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-215">'Identity'</span></span>
- <span data-ttu-id="14143-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-217">'Razor'</span></span>
- <span data-ttu-id="14143-218">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="14143-219">Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-219">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-220">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-220">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-221">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-221">'Blazor'</span></span>
- <span data-ttu-id="14143-222">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-222">'Identity'</span></span>
- <span data-ttu-id="14143-223">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-223">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-224">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-224">'Razor'</span></span>
- <span data-ttu-id="14143-225">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-225">'SignalR' uid:</span></span> 

-
<span data-ttu-id="14143-226">Başlık: ' ASP.NET Core Blazor bağımlılık ekleme ' Yazar: Açıklama: ' uygulamaların nasıl Blazor hizmet ekleyebilmesi gerektiğini görün. '</span><span class="sxs-lookup"><span data-stu-id="14143-226">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="14143-227">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="14143-227">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14143-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14143-228">'Blazor'</span></span>
- <span data-ttu-id="14143-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14143-229">'Identity'</span></span>
- <span data-ttu-id="14143-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14143-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="14143-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14143-231">'Razor'</span></span>
- <span data-ttu-id="14143-232">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="14143-232">'SignalR' uid:</span></span> 

<span data-ttu-id="14143-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly uygulamalarında Şu anda bir dı kapsamları kavramı yoktur.</span><span class="sxs-lookup"><span data-stu-id="14143-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="14143-234">`Scoped`-kayıtlı hizmetler hizmetler gibi davranır `Singleton` .</span><span class="sxs-lookup"><span data-stu-id="14143-234">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="14143-235">Ancak, Blazor sunucu barındırma modeli `Scoped` yaşam süresini destekler.</span><span class="sxs-lookup"><span data-stu-id="14143-235">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="14143-236">BlazorSunucu uygulamalarında, kapsamlı bir hizmet kaydı *bağlantının*kapsamına alınır.</span><span class="sxs-lookup"><span data-stu-id="14143-236">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="14143-237">Bu nedenle, geçerli amaç tarayıcıda istemci tarafı çalıştırmak olsa bile, kapsama alınmış hizmetlerin kullanılması geçerli kullanıcı kapsamında olması gereken hizmetler için tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="14143-237">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> <span data-ttu-id="14143-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Dı, hizmetin *tek bir örneğini* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="14143-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI creates a *single instance* of the service.</span></span> <span data-ttu-id="14143-239">Hizmet gerektiren tüm bileşenler `Singleton` aynı hizmetin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="14143-239">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="14143-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Bir bileşen hizmet kapsayıcısından bir hizmetin örneğini edindiğinde `Transient` , hizmetin *Yeni bir örneğini* alır.</span><span class="sxs-lookup"><span data-stu-id="14143-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="14143-241">Dı sistemi ASP.NET Core içindeki DI sistemini temel alır.</span><span class="sxs-lookup"><span data-stu-id="14143-241">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="14143-242">Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="14143-242">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="14143-243">Bir bileşende hizmet isteme</span><span class="sxs-lookup"><span data-stu-id="14143-243">Request a service in a component</span></span>

<span data-ttu-id="14143-244">Hizmetler hizmet koleksiyonuna eklendikten sonra, [ \@ ekleme](xref:mvc/views/razor#inject) yönergesini kullanarak hizmetleri bileşenlere ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="14143-244">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="14143-245">`@inject`iki parametreye sahiptir:</span><span class="sxs-lookup"><span data-stu-id="14143-245">`@inject` has two parameters:</span></span>

* <span data-ttu-id="14143-246">&ndash;Eklenecek hizmetin türünü yazın.</span><span class="sxs-lookup"><span data-stu-id="14143-246">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="14143-247">Özelliği &ndash; eklenen App Service 'i alan özelliğin adı.</span><span class="sxs-lookup"><span data-stu-id="14143-247">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="14143-248">Özelliği el ile oluşturma gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="14143-248">The property doesn't require manual creation.</span></span> <span data-ttu-id="14143-249">Derleyici özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="14143-249">The compiler creates the property.</span></span>

<span data-ttu-id="14143-250">Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="14143-250">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="14143-251">`@inject`Farklı hizmetler eklemek için birden çok deyim kullanın.</span><span class="sxs-lookup"><span data-stu-id="14143-251">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="14143-252">Aşağıdaki örnek nasıl kullanılacağını göstermektedir `@inject` .</span><span class="sxs-lookup"><span data-stu-id="14143-252">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="14143-253">Uygulayan hizmet `Services.IDataAccess` bileşenin özelliğine eklenir `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="14143-253">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="14143-254">Kodun yalnızca soyutlamayı nasıl kullandığını aklınızda yapın `IDataAccess` :</span><span class="sxs-lookup"><span data-stu-id="14143-254">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="14143-255">Dahili olarak, oluşturulan Özellik ( `DataRepository` ) özniteliğini kullanır `InjectAttribute` .</span><span class="sxs-lookup"><span data-stu-id="14143-255">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="14143-256">Genellikle, bu öznitelik doğrudan kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="14143-256">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="14143-257">Bileşenler için bir temel sınıf gerekliyse ve temel sınıf için de eklenen özellikler gerekliyse, şunu el ile ekleyin `InjectAttribute` :</span><span class="sxs-lookup"><span data-stu-id="14143-257">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="14143-258">Temel sınıftan türetilmiş bileşenlerde, `@inject` yönergesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="14143-258">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="14143-259">`InjectAttribute`Taban sınıfının yeterli olması yeterlidir:</span><span class="sxs-lookup"><span data-stu-id="14143-259">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="14143-260">Hizmetler 'de dı kullanma</span><span class="sxs-lookup"><span data-stu-id="14143-260">Use DI in services</span></span>

<span data-ttu-id="14143-261">Karmaşık hizmetler için ek hizmetler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="14143-261">Complex services might require additional services.</span></span> <span data-ttu-id="14143-262">Önceki örnekte, `DataAccess` `HttpClient` varsayılan hizmet gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="14143-262">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="14143-263">`@inject`(veya `InjectAttribute` ) Hizmetler 'de kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="14143-263">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="14143-264">Bunun yerine *Oluşturucu Ekleme* kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14143-264">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="14143-265">Gerekli hizmetler, hizmetin oluşturucusuna parametreler eklenerek eklenir.</span><span class="sxs-lookup"><span data-stu-id="14143-265">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="14143-266">Dı hizmeti oluşturduğunda, oluşturucuda gereken hizmetleri algılar ve bunlara göre sağlar.</span><span class="sxs-lookup"><span data-stu-id="14143-266">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="14143-267">Oluşturucu Ekleme önkoşulları:</span><span class="sxs-lookup"><span data-stu-id="14143-267">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="14143-268">Bağımsız değişkenlerinin tümü DI tarafından yerine getirilme için tek bir Oluşturucu bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14143-268">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="14143-269">Varsayılan değerleri belirttiklerinde, DI tarafından kapsanmayan ek parametrelere izin verilir.</span><span class="sxs-lookup"><span data-stu-id="14143-269">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="14143-270">Uygulanabilir Oluşturucu *ortak*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14143-270">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="14143-271">Uygulanabilir bir Oluşturucu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14143-271">One applicable constructor must exist.</span></span> <span data-ttu-id="14143-272">Belirsizlik söz konusu olduğunda, bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="14143-272">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="14143-273">Bir dı kapsamını yönetmek için yardımcı program temel bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="14143-273">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="14143-274">ASP.NET Core uygulamalarda, kapsamlı hizmetler genellikle geçerli isteğin kapsamlandırılır.</span><span class="sxs-lookup"><span data-stu-id="14143-274">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="14143-275">İstek tamamlandıktan sonra, tüm kapsamlı veya geçici hizmetler dı sistemi tarafından silinir.</span><span class="sxs-lookup"><span data-stu-id="14143-275">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="14143-276">BlazorSunucu uygulamalarında istek kapsamı, istemci bağlantısı süresince sürer. Bu, geçici ve kapsamlı hizmetlerin beklenenden çok daha uzun süreli olarak oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="14143-276">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="14143-277">BlazorWebassembly uygulamalarında, kapsamlı bir ömürle kaydedilen hizmetler tekton olarak değerlendirilir, bu nedenle tipik ASP.NET Core uygulamalarında kapsamlı hizmetlerden daha uzun bir süre yaşarlar.</span><span class="sxs-lookup"><span data-stu-id="14143-277">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="14143-278">Uygulamalarda bir hizmet ömrünü sınırlayan bir yaklaşım Blazor , `OwningComponentBase` türü kullanır.</span><span class="sxs-lookup"><span data-stu-id="14143-278">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="14143-279">`OwningComponentBase`, öğesinden türetilmiş soyut bir türdür `ComponentBase` ve bileşenin ömrüne karşılık gelen BIR dı kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="14143-279">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="14143-280">Bu kapsamı kullanarak, dı hizmetlerini kapsamlı bir ömür ile kullanmak mümkündür ve bileşen olarak bu uygulamaları canlı hale gelir.</span><span class="sxs-lookup"><span data-stu-id="14143-280">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="14143-281">Bileşen yok edildiğinde, bileşenin kapsamlı hizmet sağlayıcısından gelen hizmetler de silinir.</span><span class="sxs-lookup"><span data-stu-id="14143-281">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="14143-282">Bu, şu hizmetler için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="14143-282">This can be useful for services that:</span></span>

* <span data-ttu-id="14143-283">Geçici ömür uygun olmadığından, bir bileşen içinde yeniden kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14143-283">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="14143-284">Tek yaşam süresi uygun olmadığından, bileşenler arasında paylaşılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="14143-284">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="14143-285">Türün iki sürümü `OwningComponentBase` kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="14143-285">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="14143-286">`OwningComponentBase`, türünde `ComponentBase` Protected özelliği olan bir abstract, atılabilir alt öğesidir `ScopedServices` `IServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="14143-286">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="14143-287">Bu sağlayıcı, bileşenin kullanım ömrü kapsamındaki Hizmetleri çözümlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="14143-287">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="14143-288">Veya () kullanılarak bileşene eklenen dı Hizmetleri `@inject` `InjectAttribute` `[Inject]` bileşen kapsamında oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="14143-288">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="14143-289">Bileşenin kapsamını kullanmak için, hizmetler veya kullanılarak çözümlenmelidir `ScopedServices.GetRequiredService` `ScopedServices.GetService` .</span><span class="sxs-lookup"><span data-stu-id="14143-289">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="14143-290">Sağlayıcı kullanılarak çözümlenen hizmetlerin, `ScopedServices` aynı kapsamdaki bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="14143-290">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="14143-291">`OwningComponentBase<T>`öğesinden türetilir `OwningComponentBase` ve KAPSAMDAKI `Service` dı sağlayıcısından bir örneğini döndüren bir özellik ekler `T` .</span><span class="sxs-lookup"><span data-stu-id="14143-291">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="14143-292">Bu tür, `IServiceProvider` uygulamanın, bileşenin kapsamını kullanarak dı kapsayıcısından gerektirdiği bir birincil hizmet olduğunda bir örneği kullanmadan kapsamlı hizmetlere erişmenin kolay bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="14143-292">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="14143-293">`ScopedServices`Özelliği kullanılabilir, bu sayede uygulama, gerekirse diğer tür hizmetleri alabilir.</span><span class="sxs-lookup"><span data-stu-id="14143-293">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="14143-294">Dı Entity Framework DbContext kullanımı</span><span class="sxs-lookup"><span data-stu-id="14143-294">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="14143-295">Web Apps 'in kaynağından alınacak bir ortak hizmet türü Entity Framework (EF) `DbContext` nesneleridir.</span><span class="sxs-lookup"><span data-stu-id="14143-295">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="14143-296">Kullanarak EF Services `IServiceCollection.AddDbContext` 'in kaydı, `DbContext` Varsayılan olarak kapsamlı bir hizmet olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="14143-296">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="14143-297">Kapsamlı bir hizmet olarak kaydetme Blazor `DbContext` , örneklerin uygulama genelinde uzun süreli ve paylaşılan olmasına neden olduğu için uygulamalardaki sorunlara yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="14143-297">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="14143-298">`DbContext`iş parçacığı açısından güvenli değildir ve aynı anda kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="14143-298">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="14143-299">Uygulamaya bağlı olarak, `OwningComponentBase` bir öğesinin kapsamını tek bir bileşen ile sınırlamak için kullanılması `DbContext` sorunu çözebilir *may* .</span><span class="sxs-lookup"><span data-stu-id="14143-299">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="14143-300">Bir bileşen `DbContext` paralel olarak kullanmıyorsa, bileşeni öğesinden kaynağından türeterek `OwningComponentBase` ve `DbContext` kaynağından alma `ScopedServices` yeterlidir çünkü şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="14143-300">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="14143-301">Ayrı bileşenler bir paylaşmaz `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="14143-301">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="14143-302">`DbContext`Yalnızca bileşen bu bileşene bağlı olarak sürer.</span><span class="sxs-lookup"><span data-stu-id="14143-302">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="14143-303">Tek bir bileşen eşzamanlı olarak kullanılabilir `DbContext` (örneğin, Kullanıcı her bir düğme seçtiğinde), kullanılması de `OwningComponentBase` eşzamanlı EF işlemlerinde sorunları önetmez.</span><span class="sxs-lookup"><span data-stu-id="14143-303">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="14143-304">Bu durumda, `DbContext` her MANTıKSAL EF işlemi için farklı bir kullanın.</span><span class="sxs-lookup"><span data-stu-id="14143-304">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="14143-305">Aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="14143-305">Use either of the following approaches:</span></span>

* <span data-ttu-id="14143-306">`DbContext` `DbContextOptions<TContext>` Bir bağımsız değişken olarak kullanarak doğrudan oluşturun, bu, dı 'den alınabilir ve iş parçacığı güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="14143-306">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="14143-307">`DbContext`Hizmeti kapsayıcısına geçici bir yaşam süresi ile kaydedin:</span><span class="sxs-lookup"><span data-stu-id="14143-307">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="14143-308">Bağlamı kaydederken kullanın `ServiceLifetime.Transient` .</span><span class="sxs-lookup"><span data-stu-id="14143-308">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="14143-309">`AddDbContext`Genişletme yöntemi, türünde iki isteğe bağlı parametre alır `ServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="14143-309">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="14143-310">Bu yaklaşımı kullanmak için yalnızca `contextLifetime` parametresinin olması gerekir `ServiceLifetime.Transient` .</span><span class="sxs-lookup"><span data-stu-id="14143-310">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="14143-311">`optionsLifetime`varsayılan değerini tutabilir `ServiceLifetime.Scoped` .</span><span class="sxs-lookup"><span data-stu-id="14143-311">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="14143-312">Geçici, `DbContext` `@inject` paralel olarak bırden çok EF işlemi yürütemeyecek bileşenlere normal (kullanılarak) eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="14143-312">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="14143-313">Aynı anda birden çok EF işlemi gerçekleştirebilecek olanlar, `DbContext` kullanarak her paralel işlem için ayrı nesneler isteyebilir `IServiceProvider.GetRequiredService` .</span><span class="sxs-lookup"><span data-stu-id="14143-313">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="14143-314">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="14143-314">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
