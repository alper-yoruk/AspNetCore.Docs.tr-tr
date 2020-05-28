---
<span data-ttu-id="3063c-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-102">'Blazor'</span></span>
- <span data-ttu-id="3063c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-103">'Identity'</span></span>
- <span data-ttu-id="3063c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-105">'Razor'</span></span>
- <span data-ttu-id="3063c-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="3063c-107">ASP.NET Core'da bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="3063c-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="3063c-108">, [Steve Smith](https://ardalis.com/) ve [Scott Ade](https://scottaddie.com) tarafından</span><span class="sxs-lookup"><span data-stu-id="3063c-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3063c-109">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="3063c-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="3063c-110">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="3063c-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="3063c-111">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3063c-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="3063c-112">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="3063c-112">Overview of dependency injection</span></span>

<span data-ttu-id="3063c-113">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="3063c-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="3063c-114">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3063c-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="3063c-115">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="3063c-116">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="3063c-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="3063c-117">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="3063c-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="3063c-118">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="3063c-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="3063c-119">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="3063c-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="3063c-120">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3063c-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="3063c-121">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="3063c-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="3063c-122">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="3063c-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="3063c-123">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3063c-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="3063c-124">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="3063c-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="3063c-125">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="3063c-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="3063c-126">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="3063c-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="3063c-127">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="3063c-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="3063c-128">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="3063c-129">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="3063c-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="3063c-130">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="3063c-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="3063c-131">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="3063c-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="3063c-132">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="3063c-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="3063c-133">`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister.</span><span class="sxs-lookup"><span data-stu-id="3063c-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="3063c-134">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="3063c-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="3063c-135">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="3063c-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="3063c-136">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="3063c-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="3063c-137">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="3063c-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="3063c-138">`IMyDependency`ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="3063c-139">`IMyDependency`kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3063c-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3063c-140">`ILogger<TCategoryName>`günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="3063c-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="3063c-141">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="3063c-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="3063c-142">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="3063c-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="3063c-143">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="3063c-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="3063c-144">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3063c-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="3063c-145">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="3063c-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="3063c-146">Örneğin, `services.AddMvc()` hizmet Razor SAYFALARıNı ve MVC gerektir öğesini ekler.</span><span class="sxs-lookup"><span data-stu-id="3063c-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="3063c-147">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="3063c-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="3063c-148">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="3063c-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="3063c-149">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="3063c-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="3063c-150">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="3063c-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="3063c-151">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3063c-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="3063c-152">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="3063c-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="3063c-153">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="3063c-153">Services injected into Startup</span></span>

<span data-ttu-id="3063c-154">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="3063c-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="3063c-155">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3063c-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="3063c-156">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="3063c-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="3063c-157">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="3063c-157">Framework-provided services</span></span>

<span data-ttu-id="3063c-158">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="3063c-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="3063c-159">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3063c-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="3063c-160">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="3063c-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="3063c-161">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="3063c-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="3063c-162">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="3063c-162">Service Type</span></span> | <span data-ttu-id="3063c-163">Ömür</span><span class="sxs-lookup"><span data-stu-id="3063c-163">Lifetime</span></span> |
| ---
<span data-ttu-id="3063c-164">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-165">'Blazor'</span></span>
- <span data-ttu-id="3063c-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-166">'Identity'</span></span>
- <span data-ttu-id="3063c-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-168">'Razor'</span></span>
- <span data-ttu-id="3063c-169">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-170">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-171">'Blazor'</span></span>
- <span data-ttu-id="3063c-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-172">'Identity'</span></span>
- <span data-ttu-id="3063c-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-174">'Razor'</span></span>
- <span data-ttu-id="3063c-175">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-176">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-177">'Blazor'</span></span>
- <span data-ttu-id="3063c-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-178">'Identity'</span></span>
- <span data-ttu-id="3063c-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-180">'Razor'</span></span>
- <span data-ttu-id="3063c-181">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-182">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-183">'Blazor'</span></span>
- <span data-ttu-id="3063c-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-184">'Identity'</span></span>
- <span data-ttu-id="3063c-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-186">'Razor'</span></span>
- <span data-ttu-id="3063c-187">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-187">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-188">------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-189">'Blazor'</span></span>
- <span data-ttu-id="3063c-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-190">'Identity'</span></span>
- <span data-ttu-id="3063c-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-192">'Razor'</span></span>
- <span data-ttu-id="3063c-193">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-194">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-195">'Blazor'</span></span>
- <span data-ttu-id="3063c-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-196">'Identity'</span></span>
- <span data-ttu-id="3063c-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-198">'Razor'</span></span>
- <span data-ttu-id="3063c-199">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-199">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Geçici | | `IHostApplicationLifetime` | Tek | | `IWebHostEnvironment` | Tek | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Tek | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Geçici | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Tek | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Geçici | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Tek | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Tek | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Tek | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Geçici | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Tek | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Tek | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Tek |</span><span class="sxs-lookup"><span data-stu-id="3063c-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="3063c-201">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="3063c-201">Register additional services with extension methods</span></span>

<span data-ttu-id="3063c-202">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="3063c-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="3063c-203">Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="3063c-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="3063c-204">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="3063c-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="3063c-205">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="3063c-205">Service lifetimes</span></span>

<span data-ttu-id="3063c-206">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="3063c-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="3063c-207">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="3063c-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="3063c-208">Larsa</span><span class="sxs-lookup"><span data-stu-id="3063c-208">Transient</span></span>

<span data-ttu-id="3063c-209">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3063c-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="3063c-210">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3063c-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="3063c-211">Yayıl</span><span class="sxs-lookup"><span data-stu-id="3063c-211">Scoped</span></span>

<span data-ttu-id="3063c-212">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="3063c-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="3063c-213">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3063c-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="3063c-214">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="3063c-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="3063c-215">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="3063c-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="3063c-216">Adet</span><span class="sxs-lookup"><span data-stu-id="3063c-216">Singleton</span></span>

<span data-ttu-id="3063c-217">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="3063c-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="3063c-218">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="3063c-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="3063c-219">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="3063c-220">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="3063c-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="3063c-221">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="3063c-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="3063c-222">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="3063c-223">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="3063c-223">Service registration methods</span></span>

<span data-ttu-id="3063c-224">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="3063c-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="3063c-225">Yöntem</span><span class="sxs-lookup"><span data-stu-id="3063c-225">Method</span></span> | <span data-ttu-id="3063c-226">Automatic</span><span class="sxs-lookup"><span data-stu-id="3063c-226">Automatic</span></span><br><span data-ttu-id="3063c-227">nesne</span><span class="sxs-lookup"><span data-stu-id="3063c-227">object</span></span><br><span data-ttu-id="3063c-228">elden</span><span class="sxs-lookup"><span data-stu-id="3063c-228">disposal</span></span> | <span data-ttu-id="3063c-229">Birden çok</span><span class="sxs-lookup"><span data-stu-id="3063c-229">Multiple</span></span><br><span data-ttu-id="3063c-230">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="3063c-230">implementations</span></span> | <span data-ttu-id="3063c-231">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3063c-231">Pass args</span></span> |
| ---
<span data-ttu-id="3063c-232">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-233">'Blazor'</span></span>
- <span data-ttu-id="3063c-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-234">'Identity'</span></span>
- <span data-ttu-id="3063c-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-236">'Razor'</span></span>
- <span data-ttu-id="3063c-237">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-237">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-238">--- | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-239">'Blazor'</span></span>
- <span data-ttu-id="3063c-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-240">'Identity'</span></span>
- <span data-ttu-id="3063c-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-242">'Razor'</span></span>
- <span data-ttu-id="3063c-243">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-244">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-245">'Blazor'</span></span>
- <span data-ttu-id="3063c-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-246">'Identity'</span></span>
- <span data-ttu-id="3063c-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-248">'Razor'</span></span>
- <span data-ttu-id="3063c-249">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-250">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-251">'Blazor'</span></span>
- <span data-ttu-id="3063c-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-252">'Identity'</span></span>
- <span data-ttu-id="3063c-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-254">'Razor'</span></span>
- <span data-ttu-id="3063c-255">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-256">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-257">'Blazor'</span></span>
- <span data-ttu-id="3063c-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-258">'Identity'</span></span>
- <span data-ttu-id="3063c-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-260">'Razor'</span></span>
- <span data-ttu-id="3063c-261">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-262">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-263">'Blazor'</span></span>
- <span data-ttu-id="3063c-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-264">'Identity'</span></span>
- <span data-ttu-id="3063c-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-266">'Razor'</span></span>
- <span data-ttu-id="3063c-267">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-268">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-269">'Blazor'</span></span>
- <span data-ttu-id="3063c-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-270">'Identity'</span></span>
- <span data-ttu-id="3063c-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-272">'Razor'</span></span>
- <span data-ttu-id="3063c-273">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-274">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-275">'Blazor'</span></span>
- <span data-ttu-id="3063c-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-276">'Identity'</span></span>
- <span data-ttu-id="3063c-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-278">'Razor'</span></span>
- <span data-ttu-id="3063c-279">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-280">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-281">'Blazor'</span></span>
- <span data-ttu-id="3063c-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-282">'Identity'</span></span>
- <span data-ttu-id="3063c-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-284">'Razor'</span></span>
- <span data-ttu-id="3063c-285">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-286">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-287">'Blazor'</span></span>
- <span data-ttu-id="3063c-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-288">'Identity'</span></span>
- <span data-ttu-id="3063c-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-290">'Razor'</span></span>
- <span data-ttu-id="3063c-291">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-292">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-293">'Blazor'</span></span>
- <span data-ttu-id="3063c-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-294">'Identity'</span></span>
- <span data-ttu-id="3063c-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-296">'Razor'</span></span>
- <span data-ttu-id="3063c-297">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-298">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-299">'Blazor'</span></span>
- <span data-ttu-id="3063c-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-300">'Identity'</span></span>
- <span data-ttu-id="3063c-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-302">'Razor'</span></span>
- <span data-ttu-id="3063c-303">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-304">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-305">'Blazor'</span></span>
- <span data-ttu-id="3063c-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-306">'Identity'</span></span>
- <span data-ttu-id="3063c-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-308">'Razor'</span></span>
- <span data-ttu-id="3063c-309">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-309">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-310">---------------: | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-311">'Blazor'</span></span>
- <span data-ttu-id="3063c-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-312">'Identity'</span></span>
- <span data-ttu-id="3063c-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-314">'Razor'</span></span>
- <span data-ttu-id="3063c-315">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-316">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-317">'Blazor'</span></span>
- <span data-ttu-id="3063c-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-318">'Identity'</span></span>
- <span data-ttu-id="3063c-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-320">'Razor'</span></span>
- <span data-ttu-id="3063c-321">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-322">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-323">'Blazor'</span></span>
- <span data-ttu-id="3063c-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-324">'Identity'</span></span>
- <span data-ttu-id="3063c-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-326">'Razor'</span></span>
- <span data-ttu-id="3063c-327">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-328">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-329">'Blazor'</span></span>
- <span data-ttu-id="3063c-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-330">'Identity'</span></span>
- <span data-ttu-id="3063c-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-332">'Razor'</span></span>
- <span data-ttu-id="3063c-333">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-334">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-335">'Blazor'</span></span>
- <span data-ttu-id="3063c-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-336">'Identity'</span></span>
- <span data-ttu-id="3063c-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-338">'Razor'</span></span>
- <span data-ttu-id="3063c-339">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-340">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-341">'Blazor'</span></span>
- <span data-ttu-id="3063c-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-342">'Identity'</span></span>
- <span data-ttu-id="3063c-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-344">'Razor'</span></span>
- <span data-ttu-id="3063c-345">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-346">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-347">'Blazor'</span></span>
- <span data-ttu-id="3063c-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-348">'Identity'</span></span>
- <span data-ttu-id="3063c-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-350">'Razor'</span></span>
- <span data-ttu-id="3063c-351">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-352">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-353">'Blazor'</span></span>
- <span data-ttu-id="3063c-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-354">'Identity'</span></span>
- <span data-ttu-id="3063c-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-356">'Razor'</span></span>
- <span data-ttu-id="3063c-357">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-358">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-359">'Blazor'</span></span>
- <span data-ttu-id="3063c-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-360">'Identity'</span></span>
- <span data-ttu-id="3063c-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-362">'Razor'</span></span>
- <span data-ttu-id="3063c-363">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-364">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-365">'Blazor'</span></span>
- <span data-ttu-id="3063c-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-366">'Identity'</span></span>
- <span data-ttu-id="3063c-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-368">'Razor'</span></span>
- <span data-ttu-id="3063c-369">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-369">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-370">-------------: | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-371">'Blazor'</span></span>
- <span data-ttu-id="3063c-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-372">'Identity'</span></span>
- <span data-ttu-id="3063c-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-374">'Razor'</span></span>
- <span data-ttu-id="3063c-375">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-375">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="3063c-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="3063c-377">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3063c-377">Example:</span></span><br><span data-ttu-id="3063c-378">`services.AddSingleton<IMyDep, MyDep>();`| Evet | Evet | Hayır | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="3063c-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="3063c-379">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="3063c-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="3063c-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Evet | Evet | Evet | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="3063c-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="3063c-381">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3063c-381">Example:</span></span><br><span data-ttu-id="3063c-382">`services.AddSingleton<MyDep>();`| Evet | Hayır | Hayır | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="3063c-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="3063c-383">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="3063c-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="3063c-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Hayır | Evet | Evet | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="3063c-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="3063c-385">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="3063c-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="3063c-386">`services.AddSingleton(new MyDep("A string!"));`| Hayır | Hayır | Evet |</span><span class="sxs-lookup"><span data-stu-id="3063c-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="3063c-387">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3063c-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="3063c-388">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="3063c-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="3063c-389">`TryAdd{LIFETIME}`Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="3063c-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="3063c-390">Aşağıdaki örnekte, ilk satır `MyDependency` için kaydedilir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="3063c-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="3063c-391">`IMyDependency`Zaten kayıtlı bir uygulamaya sahip olduğundan ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="3063c-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="3063c-392">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="3063c-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="3063c-393">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="3063c-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="3063c-394">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="3063c-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="3063c-395">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="3063c-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="3063c-396">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3063c-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="3063c-397">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="3063c-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="3063c-398">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="3063c-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="3063c-399">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="3063c-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="3063c-400">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="3063c-400">Constructor injection behavior</span></span>

<span data-ttu-id="3063c-401">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="3063c-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="3063c-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="3063c-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="3063c-403">`ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3063c-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="3063c-404">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3063c-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="3063c-405">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3063c-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="3063c-406">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3063c-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="3063c-407">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="3063c-408">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="3063c-408">Entity Framework contexts</span></span>

<span data-ttu-id="3063c-409">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="3063c-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="3063c-410">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="3063c-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="3063c-411">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="3063c-412">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="3063c-412">Lifetime and registration options</span></span>

<span data-ttu-id="3063c-413">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="3063c-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="3063c-414">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="3063c-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="3063c-415">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="3063c-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="3063c-416">`Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="3063c-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="3063c-417">, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` .</span><span class="sxs-lookup"><span data-stu-id="3063c-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="3063c-418">`OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="3063c-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="3063c-419">Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="3063c-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="3063c-420">`OperationService`sınıfının yeni bir örneğini alır `IOperationTransient` .</span><span class="sxs-lookup"><span data-stu-id="3063c-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="3063c-421">Yeni örnek farklı bir şekilde oluşturur `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="3063c-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="3063c-422">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="3063c-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="3063c-423">İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="3063c-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="3063c-424">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="3063c-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="3063c-425">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="3063c-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="3063c-426">`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="3063c-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="3063c-427">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="3063c-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="3063c-428">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="3063c-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="3063c-429">Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="3063c-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="3063c-430">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3063c-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="3063c-431">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="3063c-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="3063c-432">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="3063c-432">**First request:**</span></span>

<span data-ttu-id="3063c-433">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="3063c-433">Controller operations:</span></span>

<span data-ttu-id="3063c-434">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="3063c-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="3063c-435">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="3063c-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="3063c-436">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3063c-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3063c-437">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3063c-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3063c-438">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="3063c-438">`OperationService` operations:</span></span>

<span data-ttu-id="3063c-439">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="3063c-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="3063c-440">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="3063c-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="3063c-441">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3063c-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3063c-442">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3063c-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3063c-443">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="3063c-443">**Second request:**</span></span>

<span data-ttu-id="3063c-444">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="3063c-444">Controller operations:</span></span>

<span data-ttu-id="3063c-445">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="3063c-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="3063c-446">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="3063c-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="3063c-447">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3063c-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3063c-448">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3063c-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3063c-449">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="3063c-449">`OperationService` operations:</span></span>

<span data-ttu-id="3063c-450">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="3063c-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="3063c-451">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="3063c-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="3063c-452">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3063c-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3063c-453">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3063c-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3063c-454">`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="3063c-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="3063c-455">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-455">*Transient* objects are always different.</span></span> <span data-ttu-id="3063c-456">Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="3063c-457">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3063c-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="3063c-458">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="3063c-459">*Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3063c-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="3063c-460">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="3063c-460">Call services from main</span></span>

<span data-ttu-id="3063c-461"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3063c-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="3063c-462">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="3063c-463">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="3063c-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="3063c-464">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="3063c-464">Scope validation</span></span>

<span data-ttu-id="3063c-465">Uygulama geliştirme ortamında çalışırken ve Konağı derlemek için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) çağırdığında, varsayılan hizmet sağlayıcı aşağıdakileri doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="3063c-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="3063c-466">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="3063c-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="3063c-467">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="3063c-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="3063c-468">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3063c-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="3063c-469">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="3063c-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="3063c-470">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="3063c-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="3063c-471">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="3063c-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="3063c-472">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="3063c-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="3063c-473">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="3063c-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="3063c-474">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="3063c-474">Request Services</span></span>

<span data-ttu-id="3063c-475">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="3063c-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="3063c-476">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3063c-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="3063c-477">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="3063c-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="3063c-478">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="3063c-479">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="3063c-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="3063c-480">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3063c-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="3063c-481">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="3063c-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="3063c-482">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="3063c-482">Design services for dependency injection</span></span>

<span data-ttu-id="3063c-483">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3063c-483">Best practices are to:</span></span>

* <span data-ttu-id="3063c-484">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="3063c-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="3063c-485">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="3063c-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="3063c-486">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="3063c-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="3063c-487">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="3063c-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="3063c-488">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="3063c-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="3063c-489">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="3063c-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="3063c-490">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="3063c-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="3063c-491">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="3063c-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="3063c-492">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="3063c-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="3063c-493">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="3063c-494">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="3063c-494">Disposal of services</span></span>

<span data-ttu-id="3063c-495">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="3063c-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="3063c-496">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="3063c-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="3063c-497">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="3063c-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="3063c-498">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="3063c-498">In the following example:</span></span>

* <span data-ttu-id="3063c-499">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="3063c-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="3063c-500">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="3063c-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="3063c-501">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="3063c-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="3063c-502">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="3063c-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="3063c-503">Hizmet verme seçeneklerinin bir tartışması için bkz. [ASP.NET Core ' de, Ndisposaveya ' ı atılamayan dört yol](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="3063c-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="3063c-504">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="3063c-504">Default service container replacement</span></span>

<span data-ttu-id="3063c-505">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3063c-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="3063c-506">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="3063c-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="3063c-507">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="3063c-507">Property injection</span></span>
* <span data-ttu-id="3063c-508">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="3063c-508">Injection based on name</span></span>
* <span data-ttu-id="3063c-509">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="3063c-509">Child containers</span></span>
* <span data-ttu-id="3063c-510">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="3063c-510">Custom lifetime management</span></span>
* <span data-ttu-id="3063c-511">`Func<T>`yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="3063c-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="3063c-512">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="3063c-512">Convention-based registration</span></span>

<span data-ttu-id="3063c-513">Aşağıdaki 3. taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3063c-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="3063c-514">Autofac</span><span class="sxs-lookup"><span data-stu-id="3063c-514">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="3063c-515">Drıioc</span><span class="sxs-lookup"><span data-stu-id="3063c-515">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="3063c-516">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="3063c-516">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="3063c-517">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="3063c-517">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="3063c-518">E</span><span class="sxs-lookup"><span data-stu-id="3063c-518">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="3063c-519">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="3063c-519">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="3063c-520">Unity</span><span class="sxs-lookup"><span data-stu-id="3063c-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="3063c-521">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="3063c-521">Thread safety</span></span>

<span data-ttu-id="3063c-522">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3063c-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="3063c-523">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="3063c-524">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="3063c-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="3063c-525">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="3063c-526">Öneriler</span><span class="sxs-lookup"><span data-stu-id="3063c-526">Recommendations</span></span>

* <span data-ttu-id="3063c-527">`async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="3063c-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="3063c-528">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="3063c-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="3063c-529">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="3063c-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="3063c-530">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="3063c-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="3063c-531">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="3063c-532">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="3063c-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="3063c-533">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="3063c-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="3063c-534">Hizmetlere statik erişimi önleyin (örneğin, başka bir yerde kullanmak üzere, statik olarak yazılan [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ).</span><span class="sxs-lookup"><span data-stu-id="3063c-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="3063c-535">*Hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="3063c-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="3063c-536">Örneğin, <xref:System.IServiceProvider.GetService*> yerine şunu kullandığınızda bir hizmet örneği elde etme çağrısı yapmayın:</span><span class="sxs-lookup"><span data-stu-id="3063c-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="3063c-537">**Olmayan**</span><span class="sxs-lookup"><span data-stu-id="3063c-537">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="3063c-538">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="3063c-538">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="3063c-539">Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme.</span><span class="sxs-lookup"><span data-stu-id="3063c-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="3063c-540">Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="3063c-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="3063c-541">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="3063c-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="3063c-542">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3063c-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="3063c-543">Özel durumlar &mdash; genellikle çerçevenin kendisi içinde özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="3063c-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="3063c-544">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="3063c-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="3063c-545">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3063c-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="3063c-546">DI 'de çok kiracılı için önerilen desenler</span><span class="sxs-lookup"><span data-stu-id="3063c-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="3063c-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , çok kiracılı bir uygulama sağlar.</span><span class="sxs-lookup"><span data-stu-id="3063c-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="3063c-548">Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="3063c-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="3063c-549">https://github.com/OrchardCMS/OrchardCore.SamplesCMS 'e özgü özelliklerden herhangi biri olmadan yalnızca Orchard Core çerçevesini kullanarak modüler ve çok kiracılı uygulamalar oluşturmaya yönelik örnekler için adresindeki örnek uygulamalar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3063c-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3063c-550">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3063c-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="3063c-551">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="3063c-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="3063c-552">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="3063c-552">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="3063c-553">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="3063c-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="3063c-554">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="3063c-554">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3063c-555">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="3063c-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="3063c-556">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="3063c-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="3063c-557">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3063c-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="3063c-558">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="3063c-558">Overview of dependency injection</span></span>

<span data-ttu-id="3063c-559">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="3063c-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="3063c-560">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3063c-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="3063c-561">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="3063c-562">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="3063c-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="3063c-563">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="3063c-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="3063c-564">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="3063c-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="3063c-565">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="3063c-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="3063c-566">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3063c-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="3063c-567">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="3063c-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="3063c-568">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="3063c-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="3063c-569">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3063c-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="3063c-570">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="3063c-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="3063c-571">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="3063c-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="3063c-572">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="3063c-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="3063c-573">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="3063c-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="3063c-574">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="3063c-575">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="3063c-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="3063c-576">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="3063c-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="3063c-577">[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="3063c-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="3063c-578">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="3063c-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="3063c-579">`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister.</span><span class="sxs-lookup"><span data-stu-id="3063c-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="3063c-580">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="3063c-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="3063c-581">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="3063c-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="3063c-582">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="3063c-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="3063c-583">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="3063c-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="3063c-584">`IMyDependency`ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="3063c-585">`IMyDependency`kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3063c-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3063c-586">`ILogger<TCategoryName>`günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="3063c-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="3063c-587">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="3063c-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="3063c-588">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="3063c-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="3063c-589">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="3063c-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="3063c-590">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3063c-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="3063c-591">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="3063c-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="3063c-592">Örneğin, `services.AddMvc()` hizmet Razor SAYFALARıNı ve MVC gerektir öğesini ekler.</span><span class="sxs-lookup"><span data-stu-id="3063c-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="3063c-593">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="3063c-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="3063c-594">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="3063c-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="3063c-595">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="3063c-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="3063c-596">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="3063c-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="3063c-597">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3063c-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="3063c-598">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="3063c-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="3063c-599">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="3063c-599">Services injected into Startup</span></span>

<span data-ttu-id="3063c-600">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="3063c-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="3063c-601">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3063c-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="3063c-602">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="3063c-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="3063c-603">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="3063c-603">Framework-provided services</span></span>

<span data-ttu-id="3063c-604">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="3063c-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="3063c-605">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3063c-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="3063c-606">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="3063c-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="3063c-607">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="3063c-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="3063c-608">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="3063c-608">Service Type</span></span> | <span data-ttu-id="3063c-609">Ömür</span><span class="sxs-lookup"><span data-stu-id="3063c-609">Lifetime</span></span> |
| ---
<span data-ttu-id="3063c-610">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-611">'Blazor'</span></span>
- <span data-ttu-id="3063c-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-612">'Identity'</span></span>
- <span data-ttu-id="3063c-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-614">'Razor'</span></span>
- <span data-ttu-id="3063c-615">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-616">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-617">'Blazor'</span></span>
- <span data-ttu-id="3063c-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-618">'Identity'</span></span>
- <span data-ttu-id="3063c-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-620">'Razor'</span></span>
- <span data-ttu-id="3063c-621">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-622">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-623">'Blazor'</span></span>
- <span data-ttu-id="3063c-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-624">'Identity'</span></span>
- <span data-ttu-id="3063c-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-626">'Razor'</span></span>
- <span data-ttu-id="3063c-627">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-628">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-629">'Blazor'</span></span>
- <span data-ttu-id="3063c-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-630">'Identity'</span></span>
- <span data-ttu-id="3063c-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-632">'Razor'</span></span>
- <span data-ttu-id="3063c-633">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-633">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-634">------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-635">'Blazor'</span></span>
- <span data-ttu-id="3063c-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-636">'Identity'</span></span>
- <span data-ttu-id="3063c-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-638">'Razor'</span></span>
- <span data-ttu-id="3063c-639">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-640">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-641">'Blazor'</span></span>
- <span data-ttu-id="3063c-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-642">'Identity'</span></span>
- <span data-ttu-id="3063c-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-644">'Razor'</span></span>
- <span data-ttu-id="3063c-645">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-645">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Geçici | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Tek | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Tek | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Tek | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Geçici | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Tek | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Geçici | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Tek | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Tek | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Tek | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Geçici | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Tek | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Tek | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Tek |</span><span class="sxs-lookup"><span data-stu-id="3063c-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="3063c-647">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="3063c-647">Register additional services with extension methods</span></span>

<span data-ttu-id="3063c-648">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="3063c-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="3063c-649">Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="3063c-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="3063c-650">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="3063c-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="3063c-651">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="3063c-651">Service lifetimes</span></span>

<span data-ttu-id="3063c-652">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="3063c-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="3063c-653">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="3063c-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="3063c-654">Larsa</span><span class="sxs-lookup"><span data-stu-id="3063c-654">Transient</span></span>

<span data-ttu-id="3063c-655">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3063c-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="3063c-656">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3063c-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="3063c-657">Yayıl</span><span class="sxs-lookup"><span data-stu-id="3063c-657">Scoped</span></span>

<span data-ttu-id="3063c-658">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="3063c-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="3063c-659">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3063c-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="3063c-660">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="3063c-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="3063c-661">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="3063c-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="3063c-662">Adet</span><span class="sxs-lookup"><span data-stu-id="3063c-662">Singleton</span></span>

<span data-ttu-id="3063c-663">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="3063c-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="3063c-664">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="3063c-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="3063c-665">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="3063c-666">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="3063c-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="3063c-667">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="3063c-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="3063c-668">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="3063c-669">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="3063c-669">Service registration methods</span></span>

<span data-ttu-id="3063c-670">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="3063c-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="3063c-671">Yöntem</span><span class="sxs-lookup"><span data-stu-id="3063c-671">Method</span></span> | <span data-ttu-id="3063c-672">Automatic</span><span class="sxs-lookup"><span data-stu-id="3063c-672">Automatic</span></span><br><span data-ttu-id="3063c-673">nesne</span><span class="sxs-lookup"><span data-stu-id="3063c-673">object</span></span><br><span data-ttu-id="3063c-674">elden</span><span class="sxs-lookup"><span data-stu-id="3063c-674">disposal</span></span> | <span data-ttu-id="3063c-675">Birden çok</span><span class="sxs-lookup"><span data-stu-id="3063c-675">Multiple</span></span><br><span data-ttu-id="3063c-676">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="3063c-676">implementations</span></span> | <span data-ttu-id="3063c-677">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3063c-677">Pass args</span></span> |
| ---
<span data-ttu-id="3063c-678">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-679">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-679">'Blazor'</span></span>
- <span data-ttu-id="3063c-680">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-680">'Identity'</span></span>
- <span data-ttu-id="3063c-681">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-682">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-682">'Razor'</span></span>
- <span data-ttu-id="3063c-683">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-683">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-684">--- | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-685">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-685">'Blazor'</span></span>
- <span data-ttu-id="3063c-686">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-686">'Identity'</span></span>
- <span data-ttu-id="3063c-687">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-688">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-688">'Razor'</span></span>
- <span data-ttu-id="3063c-689">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-690">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-691">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-691">'Blazor'</span></span>
- <span data-ttu-id="3063c-692">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-692">'Identity'</span></span>
- <span data-ttu-id="3063c-693">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-694">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-694">'Razor'</span></span>
- <span data-ttu-id="3063c-695">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-696">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-697">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-697">'Blazor'</span></span>
- <span data-ttu-id="3063c-698">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-698">'Identity'</span></span>
- <span data-ttu-id="3063c-699">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-700">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-700">'Razor'</span></span>
- <span data-ttu-id="3063c-701">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-702">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-703">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-703">'Blazor'</span></span>
- <span data-ttu-id="3063c-704">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-704">'Identity'</span></span>
- <span data-ttu-id="3063c-705">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-706">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-706">'Razor'</span></span>
- <span data-ttu-id="3063c-707">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-708">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-709">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-709">'Blazor'</span></span>
- <span data-ttu-id="3063c-710">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-710">'Identity'</span></span>
- <span data-ttu-id="3063c-711">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-712">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-712">'Razor'</span></span>
- <span data-ttu-id="3063c-713">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-714">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-715">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-715">'Blazor'</span></span>
- <span data-ttu-id="3063c-716">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-716">'Identity'</span></span>
- <span data-ttu-id="3063c-717">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-718">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-718">'Razor'</span></span>
- <span data-ttu-id="3063c-719">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-720">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-721">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-721">'Blazor'</span></span>
- <span data-ttu-id="3063c-722">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-722">'Identity'</span></span>
- <span data-ttu-id="3063c-723">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-724">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-724">'Razor'</span></span>
- <span data-ttu-id="3063c-725">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-726">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-727">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-727">'Blazor'</span></span>
- <span data-ttu-id="3063c-728">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-728">'Identity'</span></span>
- <span data-ttu-id="3063c-729">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-730">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-730">'Razor'</span></span>
- <span data-ttu-id="3063c-731">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-732">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-733">'Blazor'</span></span>
- <span data-ttu-id="3063c-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-734">'Identity'</span></span>
- <span data-ttu-id="3063c-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-736">'Razor'</span></span>
- <span data-ttu-id="3063c-737">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-738">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-739">'Blazor'</span></span>
- <span data-ttu-id="3063c-740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-740">'Identity'</span></span>
- <span data-ttu-id="3063c-741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-742">'Razor'</span></span>
- <span data-ttu-id="3063c-743">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-744">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-745">'Blazor'</span></span>
- <span data-ttu-id="3063c-746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-746">'Identity'</span></span>
- <span data-ttu-id="3063c-747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-748">'Razor'</span></span>
- <span data-ttu-id="3063c-749">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-750">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-751">'Blazor'</span></span>
- <span data-ttu-id="3063c-752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-752">'Identity'</span></span>
- <span data-ttu-id="3063c-753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-754">'Razor'</span></span>
- <span data-ttu-id="3063c-755">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-755">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-756">---------------: | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-757">'Blazor'</span></span>
- <span data-ttu-id="3063c-758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-758">'Identity'</span></span>
- <span data-ttu-id="3063c-759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-760">'Razor'</span></span>
- <span data-ttu-id="3063c-761">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-762">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-763">'Blazor'</span></span>
- <span data-ttu-id="3063c-764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-764">'Identity'</span></span>
- <span data-ttu-id="3063c-765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-766">'Razor'</span></span>
- <span data-ttu-id="3063c-767">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-768">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-769">'Blazor'</span></span>
- <span data-ttu-id="3063c-770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-770">'Identity'</span></span>
- <span data-ttu-id="3063c-771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-772">'Razor'</span></span>
- <span data-ttu-id="3063c-773">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-774">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-775">'Blazor'</span></span>
- <span data-ttu-id="3063c-776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-776">'Identity'</span></span>
- <span data-ttu-id="3063c-777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-778">'Razor'</span></span>
- <span data-ttu-id="3063c-779">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-780">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-781">'Blazor'</span></span>
- <span data-ttu-id="3063c-782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-782">'Identity'</span></span>
- <span data-ttu-id="3063c-783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-784">'Razor'</span></span>
- <span data-ttu-id="3063c-785">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-786">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-787">'Blazor'</span></span>
- <span data-ttu-id="3063c-788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-788">'Identity'</span></span>
- <span data-ttu-id="3063c-789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-790">'Razor'</span></span>
- <span data-ttu-id="3063c-791">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-792">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-793">'Blazor'</span></span>
- <span data-ttu-id="3063c-794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-794">'Identity'</span></span>
- <span data-ttu-id="3063c-795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-796">'Razor'</span></span>
- <span data-ttu-id="3063c-797">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-798">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-799">'Blazor'</span></span>
- <span data-ttu-id="3063c-800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-800">'Identity'</span></span>
- <span data-ttu-id="3063c-801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-802">'Razor'</span></span>
- <span data-ttu-id="3063c-803">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-804">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-805">'Blazor'</span></span>
- <span data-ttu-id="3063c-806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-806">'Identity'</span></span>
- <span data-ttu-id="3063c-807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-808">'Razor'</span></span>
- <span data-ttu-id="3063c-809">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3063c-810">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-811">'Blazor'</span></span>
- <span data-ttu-id="3063c-812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-812">'Identity'</span></span>
- <span data-ttu-id="3063c-813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-814">'Razor'</span></span>
- <span data-ttu-id="3063c-815">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-815">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-816">-------------: | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3063c-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3063c-817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3063c-817">'Blazor'</span></span>
- <span data-ttu-id="3063c-818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3063c-818">'Identity'</span></span>
- <span data-ttu-id="3063c-819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3063c-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="3063c-820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3063c-820">'Razor'</span></span>
- <span data-ttu-id="3063c-821">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3063c-821">'SignalR' uid:</span></span> 

<span data-ttu-id="3063c-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="3063c-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="3063c-823">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3063c-823">Example:</span></span><br><span data-ttu-id="3063c-824">`services.AddSingleton<IMyDep, MyDep>();`| Evet | Evet | Hayır | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="3063c-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="3063c-825">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="3063c-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="3063c-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Evet | Evet | Evet | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="3063c-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="3063c-827">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3063c-827">Example:</span></span><br><span data-ttu-id="3063c-828">`services.AddSingleton<MyDep>();`| Evet | Hayır | Hayır | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="3063c-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="3063c-829">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="3063c-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="3063c-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Hayır | Evet | Evet | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="3063c-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="3063c-831">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="3063c-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="3063c-832">`services.AddSingleton(new MyDep("A string!"));`| Hayır | Hayır | Evet |</span><span class="sxs-lookup"><span data-stu-id="3063c-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="3063c-833">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3063c-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="3063c-834">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="3063c-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="3063c-835">`TryAdd{LIFETIME}`Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="3063c-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="3063c-836">Aşağıdaki örnekte, ilk satır `MyDependency` için kaydedilir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="3063c-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="3063c-837">`IMyDependency`Zaten kayıtlı bir uygulamaya sahip olduğundan ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="3063c-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="3063c-838">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="3063c-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="3063c-839">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="3063c-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="3063c-840">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="3063c-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="3063c-841">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="3063c-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="3063c-842">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3063c-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="3063c-843">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="3063c-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="3063c-844">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="3063c-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="3063c-845">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="3063c-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="3063c-846">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="3063c-846">Constructor injection behavior</span></span>

<span data-ttu-id="3063c-847">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="3063c-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="3063c-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="3063c-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="3063c-849">`ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3063c-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="3063c-850">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3063c-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="3063c-851">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3063c-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="3063c-852">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3063c-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="3063c-853">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="3063c-854">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="3063c-854">Entity Framework contexts</span></span>

<span data-ttu-id="3063c-855">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="3063c-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="3063c-856">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="3063c-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="3063c-857">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="3063c-858">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="3063c-858">Lifetime and registration options</span></span>

<span data-ttu-id="3063c-859">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="3063c-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="3063c-860">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="3063c-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="3063c-861">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="3063c-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="3063c-862">`Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="3063c-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="3063c-863">, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` .</span><span class="sxs-lookup"><span data-stu-id="3063c-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="3063c-864">`OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="3063c-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="3063c-865">Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="3063c-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="3063c-866">`OperationService`sınıfının yeni bir örneğini alır `IOperationTransient` .</span><span class="sxs-lookup"><span data-stu-id="3063c-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="3063c-867">Yeni örnek farklı bir şekilde oluşturur `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="3063c-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="3063c-868">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="3063c-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="3063c-869">İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="3063c-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="3063c-870">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="3063c-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="3063c-871">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="3063c-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="3063c-872">`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="3063c-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="3063c-873">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="3063c-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="3063c-874">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="3063c-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="3063c-875">Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="3063c-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="3063c-876">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3063c-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="3063c-877">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="3063c-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="3063c-878">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="3063c-878">**First request:**</span></span>

<span data-ttu-id="3063c-879">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="3063c-879">Controller operations:</span></span>

<span data-ttu-id="3063c-880">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="3063c-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="3063c-881">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="3063c-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="3063c-882">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3063c-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3063c-883">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3063c-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3063c-884">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="3063c-884">`OperationService` operations:</span></span>

<span data-ttu-id="3063c-885">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="3063c-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="3063c-886">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="3063c-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="3063c-887">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3063c-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3063c-888">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3063c-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3063c-889">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="3063c-889">**Second request:**</span></span>

<span data-ttu-id="3063c-890">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="3063c-890">Controller operations:</span></span>

<span data-ttu-id="3063c-891">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="3063c-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="3063c-892">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="3063c-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="3063c-893">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3063c-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3063c-894">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3063c-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3063c-895">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="3063c-895">`OperationService` operations:</span></span>

<span data-ttu-id="3063c-896">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="3063c-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="3063c-897">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="3063c-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="3063c-898">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3063c-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3063c-899">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3063c-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3063c-900">`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="3063c-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="3063c-901">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-901">*Transient* objects are always different.</span></span> <span data-ttu-id="3063c-902">Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="3063c-903">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3063c-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="3063c-904">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="3063c-905">*Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3063c-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="3063c-906">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="3063c-906">Call services from main</span></span>

<span data-ttu-id="3063c-907"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3063c-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="3063c-908">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="3063c-909">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="3063c-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="3063c-910">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="3063c-910">Scope validation</span></span>

<span data-ttu-id="3063c-911">Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="3063c-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="3063c-912">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="3063c-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="3063c-913">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="3063c-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="3063c-914">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3063c-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="3063c-915">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="3063c-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="3063c-916">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="3063c-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="3063c-917">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="3063c-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="3063c-918">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="3063c-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="3063c-919">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="3063c-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="3063c-920">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="3063c-920">Request Services</span></span>

<span data-ttu-id="3063c-921">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="3063c-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="3063c-922">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3063c-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="3063c-923">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="3063c-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="3063c-924">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="3063c-925">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="3063c-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="3063c-926">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3063c-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="3063c-927">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="3063c-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="3063c-928">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="3063c-928">Design services for dependency injection</span></span>

<span data-ttu-id="3063c-929">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3063c-929">Best practices are to:</span></span>

* <span data-ttu-id="3063c-930">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="3063c-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="3063c-931">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="3063c-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="3063c-932">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="3063c-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="3063c-933">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="3063c-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="3063c-934">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="3063c-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="3063c-935">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="3063c-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="3063c-936">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="3063c-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="3063c-937">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="3063c-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="3063c-938">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="3063c-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="3063c-939">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="3063c-940">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="3063c-940">Disposal of services</span></span>

<span data-ttu-id="3063c-941">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="3063c-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="3063c-942">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="3063c-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="3063c-943">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="3063c-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="3063c-944">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="3063c-944">In the following example:</span></span>

* <span data-ttu-id="3063c-945">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="3063c-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="3063c-946">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="3063c-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="3063c-947">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="3063c-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="3063c-948">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="3063c-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="3063c-949">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="3063c-949">Default service container replacement</span></span>

<span data-ttu-id="3063c-950">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3063c-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="3063c-951">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="3063c-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="3063c-952">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="3063c-952">Property injection</span></span>
* <span data-ttu-id="3063c-953">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="3063c-953">Injection based on name</span></span>
* <span data-ttu-id="3063c-954">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="3063c-954">Child containers</span></span>
* <span data-ttu-id="3063c-955">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="3063c-955">Custom lifetime management</span></span>
* <span data-ttu-id="3063c-956">`Func<T>`yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="3063c-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="3063c-957">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="3063c-957">Convention-based registration</span></span>

<span data-ttu-id="3063c-958">Aşağıdaki 3. taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3063c-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="3063c-959">Autofac</span><span class="sxs-lookup"><span data-stu-id="3063c-959">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="3063c-960">Drıioc</span><span class="sxs-lookup"><span data-stu-id="3063c-960">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="3063c-961">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="3063c-961">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="3063c-962">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="3063c-962">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="3063c-963">E</span><span class="sxs-lookup"><span data-stu-id="3063c-963">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="3063c-964">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="3063c-964">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="3063c-965">Unity</span><span class="sxs-lookup"><span data-stu-id="3063c-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="3063c-966">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="3063c-966">Thread safety</span></span>

<span data-ttu-id="3063c-967">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3063c-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="3063c-968">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="3063c-969">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="3063c-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="3063c-970">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="3063c-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="3063c-971">Öneriler</span><span class="sxs-lookup"><span data-stu-id="3063c-971">Recommendations</span></span>

* <span data-ttu-id="3063c-972">`async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="3063c-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="3063c-973">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="3063c-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="3063c-974">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="3063c-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="3063c-975">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="3063c-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="3063c-976">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3063c-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="3063c-977">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="3063c-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="3063c-978">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="3063c-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="3063c-979">Hizmetlere statik erişimi önleyin (örneğin, başka bir yerde kullanmak üzere, statik olarak yazılan [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ).</span><span class="sxs-lookup"><span data-stu-id="3063c-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="3063c-980">[Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="3063c-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="3063c-981"><xref:System.IServiceProvider.GetService*>Bunun yerine, şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:</span><span class="sxs-lookup"><span data-stu-id="3063c-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="3063c-982">**Olmayan**</span><span class="sxs-lookup"><span data-stu-id="3063c-982">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="3063c-983">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="3063c-983">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * <span data-ttu-id="3063c-984">Kullanarak çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="3063c-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="3063c-985">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="3063c-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="3063c-986">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3063c-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="3063c-987">Özel durumlar &mdash; genellikle çerçevenin kendisi içinde özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="3063c-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="3063c-988">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="3063c-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="3063c-989">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3063c-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3063c-990">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3063c-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="3063c-991">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="3063c-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="3063c-992">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="3063c-992">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="3063c-993">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="3063c-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="3063c-994">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="3063c-994">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
