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
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="040a9-103">ASP.NET Core Blazor bağımlılığı ekleme</span><span class="sxs-lookup"><span data-stu-id="040a9-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="040a9-104">Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Mike rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="040a9-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="040a9-105">Blazor, [bağımlılık ekleme işlemini (dı)](xref:fundamentals/dependency-injection)destekler.</span><span class="sxs-lookup"><span data-stu-id="040a9-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="040a9-106">Uygulamalar, yerleşik hizmetleri ekleme tarafından bileşenlere kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="040a9-107">Uygulamalar Ayrıca özel hizmetleri tanımlayabilir ve kaydedebilir ve bu Hizmetleri uygulama genelinde DI aracılığıyla kullanılabilir hale getirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="040a9-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="040a9-108">DI, merkezi bir konumda yapılandırılmış hizmetlere erişmek için bir tekniktir.</span><span class="sxs-lookup"><span data-stu-id="040a9-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="040a9-109">Bu, Blazor uygulamalarında şu şekilde yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="040a9-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="040a9-110">Hizmet sınıfının tek bir *örneğini tek bir hizmet olarak* bilinen birçok bileşen arasında paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="040a9-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="040a9-111">Başvuru soyutlamalarını kullanarak somut hizmet sınıflarından bileşenleri ayırın.</span><span class="sxs-lookup"><span data-stu-id="040a9-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="040a9-112">Örneğin, uygulamadaki verilere erişim için `IDataAccess` bir arabirim düşünün.</span><span class="sxs-lookup"><span data-stu-id="040a9-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="040a9-113">Arabirim somut `DataAccess` bir sınıf tarafından uygulanır ve uygulamanın hizmet kapsayıcısında bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="040a9-114">Bir bileşen bir `IDataAccess` uygulama almak için dı kullandığında, bileşen somut tür ile eşleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="040a9-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="040a9-115">Uygulama, büyük olasılıkla birim testlerinde bir sahte uygulama için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="040a9-116">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="040a9-116">Default services</span></span>

<span data-ttu-id="040a9-117">Varsayılan hizmetler, uygulamanın hizmet koleksiyonuna otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="040a9-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="040a9-118">Hizmet</span><span class="sxs-lookup"><span data-stu-id="040a9-118">Service</span></span> | <span data-ttu-id="040a9-119">Ömür</span><span class="sxs-lookup"><span data-stu-id="040a9-119">Lifetime</span></span> | <span data-ttu-id="040a9-120">Açıklama</span><span class="sxs-lookup"><span data-stu-id="040a9-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="040a9-121">Larsa</span><span class="sxs-lookup"><span data-stu-id="040a9-121">Transient</span></span> | <span data-ttu-id="040a9-122">HTTP istekleri göndermek ve bir URI tarafından tanımlanan bir kaynaktan HTTP yanıtlarını almak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="040a9-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="040a9-123">Bir Blazor WebAssembly uygulamasındaki örneği `HttpClient` , arka planda HTTP trafiğini işlemek için tarayıcıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="040a9-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="040a9-124">Blazor Server uygulamaları varsayılan olarak `HttpClient` yapılandırılmış bir hizmet olarak yapılandırma içermez.</span><span class="sxs-lookup"><span data-stu-id="040a9-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="040a9-125">Bir Blazor `HttpClient` sunucu uygulamasına bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="040a9-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="040a9-126">Daha fazla bilgi için bkz. <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="040a9-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="040a9-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="040a9-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="040a9-128">Kapsamlı (Blazor sunucusu)</span><span class="sxs-lookup"><span data-stu-id="040a9-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="040a9-129">JavaScript çağrılarının dağıtıldığı bir JavaScript çalışma zamanının örneğini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="040a9-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="040a9-130">Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="040a9-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="040a9-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="040a9-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="040a9-132">Kapsamlı (Blazor sunucusu)</span><span class="sxs-lookup"><span data-stu-id="040a9-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="040a9-133">URI 'Ler ve gezinme durumu ile çalışmaya yönelik yardımcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="040a9-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="040a9-134">Daha fazla bilgi için bkz. [URI ve gezinti durumu yardımcıları](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="040a9-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="040a9-135">Özel bir hizmet sağlayıcı, tabloda listelenen varsayılan Hizmetleri otomatik olarak sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="040a9-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="040a9-136">Özel bir hizmet sağlayıcısı kullanır ve tabloda gösterilen hizmetlerden herhangi birini gerekliyse, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="040a9-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="040a9-137">Uygulamaya hizmet ekleme</span><span class="sxs-lookup"><span data-stu-id="040a9-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="040a9-138">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="040a9-138">Blazor WebAssembly</span></span>

<span data-ttu-id="040a9-139">`Main` *Program.cs*yönteminde uygulamanın hizmet koleksiyonu için Hizmetleri yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="040a9-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="040a9-140">Aşağıdaki örnekte, `MyDependency` uygulama için `IMyDependency`kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="040a9-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="040a9-141">Konak oluşturulduktan sonra, herhangi bir bileşen işlenmeden önce kök dı kapsamından hizmetlere erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="040a9-142">Bu, içerik işlemeden önce başlatma mantığını çalıştırmak için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="040a9-142">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="040a9-143">Konak, uygulama için bir merkezi yapılandırma örneği de sağlar.</span><span class="sxs-lookup"><span data-stu-id="040a9-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="040a9-144">Yukarıdaki örnekte derleme yaparken, hava durumu hizmetinin URL 'SI varsayılan bir yapılandırma kaynağından (örneğin, *appSettings. JSON*) geçirilir `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="040a9-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="040a9-145">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="040a9-145">Blazor Server</span></span>

<span data-ttu-id="040a9-146">Yeni bir uygulama oluşturduktan sonra, `Startup.ConfigureServices` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="040a9-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="040a9-147">`ConfigureServices` Yöntemi, hizmet açıklayıcı nesnelerinin <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>bir listesi olan bir (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) iletilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="040a9-148">Hizmetler, hizmet koleksiyonuna hizmet tanımlayıcıları sağlayarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="040a9-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="040a9-149">Aşağıdaki örnek, `IDataAccess` arabirimini ve somut uygulamasını `DataAccess`içeren kavramı gösterir:</span><span class="sxs-lookup"><span data-stu-id="040a9-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="040a9-150">Hizmet ömrü</span><span class="sxs-lookup"><span data-stu-id="040a9-150">Service lifetime</span></span>

<span data-ttu-id="040a9-151">Hizmetler, aşağıdaki tabloda gösterilen ömürlerle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="040a9-152">Ömür</span><span class="sxs-lookup"><span data-stu-id="040a9-152">Lifetime</span></span> | <span data-ttu-id="040a9-153">Açıklama</span><span class="sxs-lookup"><span data-stu-id="040a9-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="040a9-154">WebAssembly uygulamalarında Şu anda bir dı kapsamları kavramı yoktur.</span><span class="sxs-lookup"><span data-stu-id="040a9-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="040a9-155">`Scoped`-kayıtlı hizmetler hizmetler gibi `Singleton` davranır.</span><span class="sxs-lookup"><span data-stu-id="040a9-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="040a9-156">Ancak, Blazor sunucu barındırma modeli `Scoped` yaşam süresini destekler.</span><span class="sxs-lookup"><span data-stu-id="040a9-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="040a9-157">Blazor Sunucu uygulamalarında, kapsamlı bir hizmet kaydı *bağlantının*kapsamına alınır.</span><span class="sxs-lookup"><span data-stu-id="040a9-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="040a9-158">Bu nedenle, geçerli amaç tarayıcıda istemci tarafı çalıştırmak olsa bile, kapsama alınmış hizmetlerin kullanılması geçerli kullanıcı kapsamında olması gereken hizmetler için tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="040a9-159">Dı, hizmetin *tek bir örneğini* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="040a9-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="040a9-160">`Singleton` Hizmet gerektiren tüm bileşenler aynı hizmetin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="040a9-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="040a9-161">Bir bileşen hizmet kapsayıcısından bir `Transient` hizmetin örneğini edindiğinde, hizmetin yeni bir *örneğini* alır.</span><span class="sxs-lookup"><span data-stu-id="040a9-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="040a9-162">Dı sistemi ASP.NET Core içindeki DI sistemini temel alır.</span><span class="sxs-lookup"><span data-stu-id="040a9-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="040a9-163">Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="040a9-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="040a9-164">Bir bileşende hizmet isteme</span><span class="sxs-lookup"><span data-stu-id="040a9-164">Request a service in a component</span></span>

<span data-ttu-id="040a9-165">Hizmetler hizmet koleksiyonuna eklendikten sonra, [ \@ekleme](xref:mvc/views/razor#inject) Razor yönergesini kullanarak hizmetleri bileşenlere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="040a9-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="040a9-166">`@inject`iki parametreye sahiptir:</span><span class="sxs-lookup"><span data-stu-id="040a9-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="040a9-167">Eklenecek &ndash; hizmetin türünü yazın.</span><span class="sxs-lookup"><span data-stu-id="040a9-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="040a9-168">Özelliği &ndash; eklenen App Service 'i alan özelliğin adı.</span><span class="sxs-lookup"><span data-stu-id="040a9-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="040a9-169">Özelliği el ile oluşturma gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="040a9-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="040a9-170">Derleyici özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="040a9-170">The compiler creates the property.</span></span>

<span data-ttu-id="040a9-171">Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="040a9-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="040a9-172">Farklı hizmetler `@inject` eklemek için birden çok deyim kullanın.</span><span class="sxs-lookup"><span data-stu-id="040a9-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="040a9-173">Aşağıdaki örnek nasıl kullanılacağını `@inject`göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="040a9-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="040a9-174">Uygulayan `Services.IDataAccess` hizmet bileşenin özelliğine `DataRepository`eklenir.</span><span class="sxs-lookup"><span data-stu-id="040a9-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="040a9-175">Kodun yalnızca `IDataAccess` soyutlamayı nasıl kullandığını aklınızda yapın:</span><span class="sxs-lookup"><span data-stu-id="040a9-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="040a9-176">Dahili olarak, oluşturulan Özellik (`DataRepository`) `InjectAttribute` özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="040a9-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="040a9-177">Genellikle, bu öznitelik doğrudan kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="040a9-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="040a9-178">Bileşenler için bir temel sınıf gerekliyse ve temel sınıf için de eklenen özellikler gerekliyse, şunu el ile ekleyin `InjectAttribute`:</span><span class="sxs-lookup"><span data-stu-id="040a9-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="040a9-179">Temel sınıftan türetilmiş bileşenlerde, `@inject` yönergesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="040a9-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="040a9-180">`InjectAttribute` Taban sınıfının yeterli olması yeterlidir:</span><span class="sxs-lookup"><span data-stu-id="040a9-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="040a9-181">Hizmetler 'de dı kullanma</span><span class="sxs-lookup"><span data-stu-id="040a9-181">Use DI in services</span></span>

<span data-ttu-id="040a9-182">Karmaşık hizmetler için ek hizmetler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-182">Complex services might require additional services.</span></span> <span data-ttu-id="040a9-183">Önceki örnekte, `DataAccess` `HttpClient` varsayılan hizmet gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="040a9-184">`@inject`(veya) `InjectAttribute`, hizmetlerde kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="040a9-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="040a9-185">Bunun yerine *Oluşturucu Ekleme* kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="040a9-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="040a9-186">Gerekli hizmetler, hizmetin oluşturucusuna parametreler eklenerek eklenir.</span><span class="sxs-lookup"><span data-stu-id="040a9-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="040a9-187">Dı hizmeti oluşturduğunda, oluşturucuda gereken hizmetleri algılar ve bunlara göre sağlar.</span><span class="sxs-lookup"><span data-stu-id="040a9-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="040a9-188">Oluşturucu Ekleme önkoşulları:</span><span class="sxs-lookup"><span data-stu-id="040a9-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="040a9-189">Bağımsız değişkenlerinin tümü DI tarafından yerine getirilme için tek bir Oluşturucu bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="040a9-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="040a9-190">Varsayılan değerleri belirttiklerinde, DI tarafından kapsanmayan ek parametrelere izin verilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="040a9-191">Uygulanabilir Oluşturucu *ortak*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="040a9-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="040a9-192">Uygulanabilir bir Oluşturucu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="040a9-192">One applicable constructor must exist.</span></span> <span data-ttu-id="040a9-193">Belirsizlik söz konusu olduğunda, bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="040a9-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="040a9-194">Bir dı kapsamını yönetmek için yardımcı program temel bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="040a9-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="040a9-195">ASP.NET Core uygulamalarda, kapsamlı hizmetler genellikle geçerli isteğin kapsamlandırılır.</span><span class="sxs-lookup"><span data-stu-id="040a9-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="040a9-196">İstek tamamlandıktan sonra, tüm kapsamlı veya geçici hizmetler dı sistemi tarafından silinir.</span><span class="sxs-lookup"><span data-stu-id="040a9-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="040a9-197">Blazor Sunucu uygulamalarında istek kapsamı, istemci bağlantısı süresince sürer. Bu, geçici ve kapsamlı hizmetlerin beklenenden çok daha uzun süreli olarak oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="040a9-198">Blazor Webassembly uygulamalarında, kapsamlı bir ömürle kaydedilen hizmetler tekton olarak değerlendirilir, bu nedenle tipik ASP.NET Core uygulamalarında kapsamlı hizmetlerden daha uzun bir süre yaşarlar.</span><span class="sxs-lookup"><span data-stu-id="040a9-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="040a9-199">Blazor Uygulamalarda bir hizmet ömrünü sınırlayan bir yaklaşım, `OwningComponentBase` türü kullanır.</span><span class="sxs-lookup"><span data-stu-id="040a9-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="040a9-200">`OwningComponentBase`, öğesinden `ComponentBase` türetilmiş soyut bir türdür ve bileşenin ömrüne karşılık gelen bir dı kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="040a9-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="040a9-201">Bu kapsamı kullanarak, dı hizmetlerini kapsamlı bir ömür ile kullanmak mümkündür ve bileşen olarak bu uygulamaları canlı hale gelir.</span><span class="sxs-lookup"><span data-stu-id="040a9-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="040a9-202">Bileşen yok edildiğinde, bileşenin kapsamlı hizmet sağlayıcısından gelen hizmetler de silinir.</span><span class="sxs-lookup"><span data-stu-id="040a9-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="040a9-203">Bu, şu hizmetler için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="040a9-203">This can be useful for services that:</span></span>

* <span data-ttu-id="040a9-204">Geçici ömür uygun olmadığından, bir bileşen içinde yeniden kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="040a9-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="040a9-205">Tek yaşam süresi uygun olmadığından, bileşenler arasında paylaşılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="040a9-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="040a9-206">`OwningComponentBase` Türün iki sürümü kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="040a9-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="040a9-207">`OwningComponentBase`, türünde Protected `ComponentBase` `ScopedServices` özelliği olan bir abstract, atılabilir alt öğesidir `IServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="040a9-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="040a9-208">Bu sağlayıcı, bileşenin kullanım ömrü kapsamındaki Hizmetleri çözümlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="040a9-209">Veya `@inject` `InjectAttribute` (`[Inject]`) kullanılarak bileşene eklenen dı Hizmetleri bileşen kapsamında oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="040a9-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="040a9-210">Bileşenin kapsamını kullanmak için, hizmetler veya `ScopedServices.GetRequiredService` `ScopedServices.GetService`kullanılarak çözümlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="040a9-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="040a9-211">`ScopedServices` Sağlayıcı kullanılarak çözümlenen hizmetlerin, aynı kapsamdaki bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="040a9-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="040a9-212">`OwningComponentBase<T>`öğesinden `OwningComponentBase` türetilir ve kapsamdaki dı sağlayıcısından `Service` bir örneğini `T` döndüren bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="040a9-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="040a9-213">Bu tür, uygulamanın, bileşenin kapsamını kullanarak dı kapsayıcısından gerektirdiği bir birincil hizmet `IServiceProvider` olduğunda bir örneği kullanmadan kapsamlı hizmetlere erişmenin kolay bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="040a9-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="040a9-214">`ScopedServices` Özelliği kullanılabilir, bu sayede uygulama, gerekirse diğer tür hizmetleri alabilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="040a9-215">Dı Entity Framework DbContext kullanımı</span><span class="sxs-lookup"><span data-stu-id="040a9-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="040a9-216">Web Apps 'in kaynağından alınacak bir ortak hizmet türü Entity Framework (EF) `DbContext` nesneleridir.</span><span class="sxs-lookup"><span data-stu-id="040a9-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="040a9-217">Kullanarak `IServiceCollection.AddDbContext` EF Services 'in kaydı, `DbContext` varsayılan olarak kapsamlı bir hizmet olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="040a9-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="040a9-218">Kapsamlı bir hizmet olarak kaydetme, örneklerin uygulama genelinde uzun Blazor süreli ve paylaşılan olmasına `DbContext` neden olduğu için uygulamalardaki sorunlara yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="040a9-219">`DbContext`iş parçacığı açısından güvenli değildir ve aynı anda kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="040a9-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="040a9-220">Uygulamaya bağlı olarak, bir `OwningComponentBase` `DbContext` öğesinin kapsamını tek bir bileşen ile sınırlamak için kullanılması sorunu çözebilir *.*</span><span class="sxs-lookup"><span data-stu-id="040a9-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="040a9-221">Bir `DbContext` bileşen paralel olarak kullanmıyorsa, bileşeni öğesinden kaynağından türeterek `OwningComponentBase` ve `DbContext` kaynağından `ScopedServices` alma yeterlidir çünkü şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="040a9-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="040a9-222">Ayrı bileşenler bir `DbContext`paylaşmaz.</span><span class="sxs-lookup"><span data-stu-id="040a9-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="040a9-223">`DbContext` Yalnızca bileşen bu bileşene bağlı olarak sürer.</span><span class="sxs-lookup"><span data-stu-id="040a9-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="040a9-224">Tek bir bileşen `DbContext` eşzamanlı olarak kullanılabilir (örneğin, Kullanıcı her bir düğme seçtiğinde), kullanılması `OwningComponentBase` de eşzamanlı EF işlemlerinde sorunları önetmez.</span><span class="sxs-lookup"><span data-stu-id="040a9-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="040a9-225">Bu durumda, her mantıksal EF işlemi `DbContext` için farklı bir kullanın.</span><span class="sxs-lookup"><span data-stu-id="040a9-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="040a9-226">Aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="040a9-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="040a9-227">Bir bağımsız `DbContext` değişken olarak `DbContextOptions<TContext>` kullanarak doğrudan oluşturun, bu, dı 'den alınabilir ve iş parçacığı güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="040a9-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="040a9-228">`DbContext` Hizmeti kapsayıcısına geçici bir yaşam süresi ile kaydedin:</span><span class="sxs-lookup"><span data-stu-id="040a9-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="040a9-229">Bağlamı kaydederken kullanın `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="040a9-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="040a9-230">Genişletme `AddDbContext` yöntemi, türünde `ServiceLifetime`iki isteğe bağlı parametre alır.</span><span class="sxs-lookup"><span data-stu-id="040a9-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="040a9-231">Bu yaklaşımı kullanmak için yalnızca `contextLifetime` parametresinin olması `ServiceLifetime.Transient`gerekir.</span><span class="sxs-lookup"><span data-stu-id="040a9-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="040a9-232">`optionsLifetime`varsayılan değerini tutabilir `ServiceLifetime.Scoped`.</span><span class="sxs-lookup"><span data-stu-id="040a9-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="040a9-233">Geçici `DbContext` , paralel olarak bırden çok EF işlemi yürütemeyecek bileşenlere normal (kullanılarak `@inject`) eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="040a9-234">Aynı anda birden çok EF işlemi gerçekleştirebilecek olanlar, kullanarak `DbContext` `IServiceProvider.GetRequiredService`her paralel işlem için ayrı nesneler isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="040a9-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="040a9-235">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="040a9-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
