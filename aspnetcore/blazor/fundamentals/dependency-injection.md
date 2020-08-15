---
title: ASP.NET Core Blazor bağımlılığı ekleme
author: guardrex
description: BlazorUygulamaların bileşenlere nasıl hizmet ekleyebileceğinizi öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
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
ms.openlocfilehash: 40c47213021bf82150be2b41201b6af3228e4485
ms.sourcegitcommit: 4df445e7d49a99f81625430f728c28e5d6bf2107
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88253570"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="6ab98-103">ASP.NET Core Blazor bağımlılığı ekleme</span><span class="sxs-lookup"><span data-stu-id="6ab98-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="6ab98-104">Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Mike rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="6ab98-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="6ab98-105">Blazor[bağımlılık ekleme işlemini (dı)](xref:fundamentals/dependency-injection)destekler.</span><span class="sxs-lookup"><span data-stu-id="6ab98-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6ab98-106">Uygulamalar, yerleşik hizmetleri ekleme tarafından bileşenlere kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="6ab98-107">Uygulamalar Ayrıca özel hizmetleri tanımlayabilir ve kaydedebilir ve bu Hizmetleri uygulama genelinde DI aracılığıyla kullanılabilir hale getirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6ab98-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="6ab98-108">DI, merkezi bir konumda yapılandırılmış hizmetlere erişmek için bir tekniktir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="6ab98-109">Bu, Blazor uygulamalarda şunları yapmak için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="6ab98-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="6ab98-110">Hizmet sınıfının tek bir *örneğini tek bir hizmet olarak* bilinen birçok bileşen arasında paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6ab98-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="6ab98-111">Başvuru soyutlamalarını kullanarak somut hizmet sınıflarından bileşenleri ayırın.</span><span class="sxs-lookup"><span data-stu-id="6ab98-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="6ab98-112">Örneğin, `IDataAccess` uygulamadaki verilere erişim için bir arabirim düşünün.</span><span class="sxs-lookup"><span data-stu-id="6ab98-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="6ab98-113">Arabirim somut bir sınıf tarafından uygulanır `DataAccess` ve uygulamanın hizmet kapsayıcısında bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="6ab98-114">Bir bileşen bir uygulama almak için DI kullandığında `IDataAccess` , bileşen somut tür ile eşleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="6ab98-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="6ab98-115">Uygulama, büyük olasılıkla birim testlerinde bir sahte uygulama için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="6ab98-116">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="6ab98-116">Default services</span></span>

<span data-ttu-id="6ab98-117">Varsayılan hizmetler, uygulamanın hizmet koleksiyonuna otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="6ab98-118">Hizmet</span><span class="sxs-lookup"><span data-stu-id="6ab98-118">Service</span></span> | <span data-ttu-id="6ab98-119">Ömür</span><span class="sxs-lookup"><span data-stu-id="6ab98-119">Lifetime</span></span> | <span data-ttu-id="6ab98-120">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6ab98-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="6ab98-121">Yayıl</span><span class="sxs-lookup"><span data-stu-id="6ab98-121">Scoped</span></span> | <span data-ttu-id="6ab98-122">HTTP istekleri göndermek ve bir URI tarafından tanımlanan bir kaynaktan HTTP yanıtlarını almak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="6ab98-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="6ab98-123"><xref:System.Net.Http.HttpClient>Bir uygulamadaki örneği, Blazor WebAssembly arka planda HTTP trafiğini işlemek için tarayıcıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="6ab98-124">Blazor Server uygulamalar <xref:System.Net.Http.HttpClient> Varsayılan olarak yapılandırılmış bir hizmet olarak yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="6ab98-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="6ab98-125">Bir <xref:System.Net.Http.HttpClient> Blazor Server uygulamaya bir uygulama sağlayın.</span><span class="sxs-lookup"><span data-stu-id="6ab98-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="6ab98-126">Daha fazla bilgi için bkz. <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="6ab98-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="6ab98-127">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="6ab98-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="6ab98-128">Kapsamlı ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="6ab98-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="6ab98-129">JavaScript çağrılarının dağıtıldığı bir JavaScript çalışma zamanının örneğini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6ab98-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="6ab98-130">Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="6ab98-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="6ab98-131">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="6ab98-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="6ab98-132">Kapsamlı ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="6ab98-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="6ab98-133">URI 'Ler ve gezinme durumu ile çalışmaya yönelik yardımcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="6ab98-134">Daha fazla bilgi için bkz. [URI ve gezinti durumu yardımcıları](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="6ab98-134">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="6ab98-135">Özel bir hizmet sağlayıcı, tabloda listelenen varsayılan Hizmetleri otomatik olarak sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="6ab98-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="6ab98-136">Özel bir hizmet sağlayıcısı kullanır ve tabloda gösterilen hizmetlerden herhangi birini gerekliyse, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6ab98-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="6ab98-137">Uygulamaya hizmet ekleme</span><span class="sxs-lookup"><span data-stu-id="6ab98-137">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="6ab98-138">Uygulamasındaki uygulamasının hizmet koleksiyonu için Hizmetleri yapılandırın `Main` `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="6ab98-138">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="6ab98-139">Aşağıdaki örnekte, `MyDependency` uygulama için kaydedilir `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="6ab98-139">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="6ab98-140">Konak oluşturulduktan sonra, herhangi bir bileşen işlenmeden önce kök dı kapsamından hizmetlere erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-140">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="6ab98-141">Bu, içerik işlemeden önce başlatma mantığını çalıştırmak için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="6ab98-141">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="6ab98-142">Konak, uygulama için bir merkezi yapılandırma örneği de sağlar.</span><span class="sxs-lookup"><span data-stu-id="6ab98-142">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="6ab98-143">Yukarıdaki örnekte derleme yaparken, hava durumu hizmetinin URL 'SI varsayılan bir yapılandırma kaynağından geçirilir (örneğin, `appsettings.json` ) `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="6ab98-143">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

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

<span data-ttu-id="6ab98-144">Yeni bir uygulama oluşturduktan sonra, yöntemini inceleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6ab98-144">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="6ab98-145"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Yöntemi <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , hizmet açıklayıcı nesnelerinin bir listesi olan bir ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ) iletilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-145">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="6ab98-146">Hizmetler, `ConfigureServices` hizmet koleksiyonuna hizmet tanımlayıcıları sağlayarak yöntemine eklenir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-146">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="6ab98-147">Aşağıdaki örnek, `IDataAccess` arabirimini ve somut uygulamasını içeren kavramı gösterir `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="6ab98-147">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="6ab98-148">Hizmet ömrü</span><span class="sxs-lookup"><span data-stu-id="6ab98-148">Service lifetime</span></span>

<span data-ttu-id="6ab98-149">Hizmetler, aşağıdaki tabloda gösterilen ömürlerle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-149">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="6ab98-150">Ömür</span><span class="sxs-lookup"><span data-stu-id="6ab98-150">Lifetime</span></span> | <span data-ttu-id="6ab98-151">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6ab98-151">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="6ab98-152">Blazor WebAssembly uygulamalar şu anda bir dı kapsamları kavramı içermez.</span><span class="sxs-lookup"><span data-stu-id="6ab98-152">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="6ab98-153">`Scoped`-kayıtlı hizmetler hizmetler gibi davranır `Singleton` .</span><span class="sxs-lookup"><span data-stu-id="6ab98-153">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="6ab98-154">Ancak, Blazor Server barındırma modeli `Scoped` yaşam süresini destekler.</span><span class="sxs-lookup"><span data-stu-id="6ab98-154">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="6ab98-155">Blazor ServerUygulamalarda, kapsamlı bir hizmet kaydı *bağlantının*kapsamına alınır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-155">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="6ab98-156">Bu nedenle, geçerli amaç tarayıcıda istemci tarafı çalıştırmak olsa bile, kapsama alınmış hizmetlerin kullanılması geçerli kullanıcı kapsamında olması gereken hizmetler için tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-156">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="6ab98-157">Dı, hizmetin *tek bir örneğini* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6ab98-157">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="6ab98-158">Hizmet gerektiren tüm bileşenler `Singleton` aynı hizmetin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-158">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="6ab98-159">Bir bileşen hizmet kapsayıcısından bir hizmetin örneğini edindiğinde `Transient` , hizmetin *Yeni bir örneğini* alır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-159">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="6ab98-160">Dı sistemi ASP.NET Core içindeki DI sistemini temel alır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-160">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="6ab98-161">Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="6ab98-161">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="6ab98-162">Bir bileşende hizmet isteme</span><span class="sxs-lookup"><span data-stu-id="6ab98-162">Request a service in a component</span></span>

<span data-ttu-id="6ab98-163">Hizmetler hizmet koleksiyonuna eklendikten sonra, [ \@ ekleme](xref:mvc/views/razor#inject) yönergesini kullanarak hizmetleri bileşenlere ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="6ab98-163">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="6ab98-164">[`@inject`](xref:mvc/views/razor#inject) iki parametreye sahiptir:</span><span class="sxs-lookup"><span data-stu-id="6ab98-164">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="6ab98-165">Tür: eklenecek hizmetin türü.</span><span class="sxs-lookup"><span data-stu-id="6ab98-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="6ab98-166">Özellik: eklenen App Service 'i alan özelliğin adı.</span><span class="sxs-lookup"><span data-stu-id="6ab98-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="6ab98-167">Özelliği el ile oluşturma gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="6ab98-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="6ab98-168">Derleyici özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6ab98-168">The compiler creates the property.</span></span>

<span data-ttu-id="6ab98-169">Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="6ab98-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="6ab98-170">[`@inject`](xref:mvc/views/razor#inject)Farklı hizmetler eklemek için birden çok deyim kullanın.</span><span class="sxs-lookup"><span data-stu-id="6ab98-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="6ab98-171">Aşağıdaki örnek nasıl kullanılacağını göstermektedir [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="6ab98-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="6ab98-172">Uygulayan hizmet `Services.IDataAccess` bileşenin özelliğine eklenir `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="6ab98-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="6ab98-173">Kodun yalnızca soyutlamayı nasıl kullandığını aklınızda yapın `IDataAccess` :</span><span class="sxs-lookup"><span data-stu-id="6ab98-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="6ab98-174">Dahili olarak, oluşturulan Özellik ( `DataRepository` ) özniteliğini kullanır [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) .</span><span class="sxs-lookup"><span data-stu-id="6ab98-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="6ab98-175">Genellikle, bu öznitelik doğrudan kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="6ab98-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="6ab98-176">Bileşenler için bir temel sınıf gerekliyse ve temel sınıf için eklenen özellikler de gerekliyse, özniteliği el ile ekleyin [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) :</span><span class="sxs-lookup"><span data-stu-id="6ab98-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="6ab98-177">Temel sınıftan türetilmiş bileşenlerde, [`@inject`](xref:mvc/views/razor#inject) yönergesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="6ab98-178"><xref:Microsoft.AspNetCore.Components.InjectAttribute>Taban sınıfının yeterli olması yeterlidir:</span><span class="sxs-lookup"><span data-stu-id="6ab98-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="6ab98-179">Hizmetler 'de dı kullanma</span><span class="sxs-lookup"><span data-stu-id="6ab98-179">Use DI in services</span></span>

<span data-ttu-id="6ab98-180">Karmaşık hizmetler için ek hizmetler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-180">Complex services might require additional services.</span></span> <span data-ttu-id="6ab98-181">Önceki örnekte, `DataAccess` <xref:System.Net.Http.HttpClient> varsayılan hizmet gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-181">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="6ab98-182">[`@inject`](xref:mvc/views/razor#inject) (veya [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) özniteliği) hizmetlerde kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="6ab98-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="6ab98-183">Bunun yerine *Oluşturucu Ekleme* kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="6ab98-184">Gerekli hizmetler, hizmetin oluşturucusuna parametreler eklenerek eklenir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="6ab98-185">Dı hizmeti oluşturduğunda, oluşturucuda gereken hizmetleri algılar ve bunlara göre sağlar.</span><span class="sxs-lookup"><span data-stu-id="6ab98-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="6ab98-186">Aşağıdaki örnekte, Oluşturucu bir ile bir ile alır <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="6ab98-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="6ab98-187"><xref:System.Net.Http.HttpClient> Varsayılan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="6ab98-188">Oluşturucu Ekleme önkoşulları:</span><span class="sxs-lookup"><span data-stu-id="6ab98-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="6ab98-189">Bağımsız değişkenlerinin tümü DI tarafından yerine getirilme için tek bir Oluşturucu bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="6ab98-190">Varsayılan değerleri belirttiklerinde, DI tarafından kapsanmayan ek parametrelere izin verilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="6ab98-191">Uygulanabilir Oluşturucu olmalıdır `public` .</span><span class="sxs-lookup"><span data-stu-id="6ab98-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="6ab98-192">Uygulanabilir bir Oluşturucu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-192">One applicable constructor must exist.</span></span> <span data-ttu-id="6ab98-193">Belirsizlik söz konusu olduğunda, bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6ab98-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="6ab98-194">Bir dı kapsamını yönetmek için yardımcı program temel bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="6ab98-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="6ab98-195">ASP.NET Core uygulamalarda, kapsamlı hizmetler genellikle geçerli isteğin kapsamlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="6ab98-196">İstek tamamlandıktan sonra, tüm kapsamlı veya geçici hizmetler dı sistemi tarafından silinir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="6ab98-197">Blazor ServerUygulamalarda, istek kapsamı istemci bağlantısı süresince sürer. Bu, geçici ve kapsamlı hizmetlerin beklenenden çok daha uzun süreli olarak oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="6ab98-198">Blazor WebAssemblyUygulamalarda, kapsamlı bir ömürle kaydedilen hizmetler tekton olarak değerlendirilir, bu nedenle tipik ASP.NET Core uygulamalarında kapsamlı hizmetlerden daha uzun bir süre yaşarlar.</span><span class="sxs-lookup"><span data-stu-id="6ab98-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="6ab98-199">Bir uygulamada atılabilir geçici Hizmetleri algılamak için [geçici disposaı 'Yi Algıla](#detect-transient-disposables) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6ab98-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="6ab98-200">Uygulamalarda bir hizmet ömrünü sınırlayan bir yaklaşım Blazor , <xref:Microsoft.AspNetCore.Components.OwningComponentBase> türü kullanır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="6ab98-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> , öğesinden türetilmiş soyut bir türdür <xref:Microsoft.AspNetCore.Components.ComponentBase> ve bileşenin ömrüne karşılık gelen BIR dı kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6ab98-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="6ab98-202">Bu kapsamı kullanarak, dı hizmetlerini kapsamlı bir ömür ile kullanmak mümkündür ve bileşen olarak bu uygulamaları canlı hale gelir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="6ab98-203">Bileşen yok edildiğinde, bileşenin kapsamlı hizmet sağlayıcısından gelen hizmetler de silinir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="6ab98-204">Bu, şu hizmetler için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="6ab98-204">This can be useful for services that:</span></span>

* <span data-ttu-id="6ab98-205">Geçici ömür uygun olmadığından, bir bileşen içinde yeniden kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="6ab98-206">Tek yaşam süresi uygun olmadığından, bileşenler arasında paylaşılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="6ab98-207">Türün iki sürümü <xref:Microsoft.AspNetCore.Components.OwningComponentBase> kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="6ab98-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="6ab98-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> , türünde <xref:Microsoft.AspNetCore.Components.ComponentBase> Protected özelliği olan bir abstract, atılabilir alt öğesidir <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="6ab98-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="6ab98-209">Bu sağlayıcı, bileşenin kullanım ömrü kapsamındaki Hizmetleri çözümlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="6ab98-210">Ya da özniteliği kullanılarak bileşene eklenen dı Hizmetleri, [`@inject`](xref:mvc/views/razor#inject) [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) bileşen kapsamında oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="6ab98-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="6ab98-211">Bileşenin kapsamını kullanmak için, hizmetler veya kullanılarak çözümlenmelidir <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="6ab98-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="6ab98-212">Sağlayıcı kullanılarak çözümlenen hizmetlerin, <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> aynı kapsamdaki bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="6ab98-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="6ab98-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> öğesinden türetilir <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ve KAPSAMDAKI <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> dı sağlayıcısından bir örneğini döndüren bir özellik ekler `T` .</span><span class="sxs-lookup"><span data-stu-id="6ab98-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="6ab98-214">Bu tür, <xref:System.IServiceProvider> uygulamanın, bileşenin kapsamını kullanarak dı kapsayıcısından gerektirdiği bir birincil hizmet olduğunda bir örneği kullanmadan kapsamlı hizmetlere erişmenin kolay bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="6ab98-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="6ab98-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>Özelliği kullanılabilir, bu sayede uygulama, gerekirse diğer tür hizmetleri alabilir.</span><span class="sxs-lookup"><span data-stu-id="6ab98-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="6ab98-216">DI Entity Framework Core (EF Core) DbContext kullanımı</span><span class="sxs-lookup"><span data-stu-id="6ab98-216">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="6ab98-217">Daha fazla bilgi için bkz. <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="6ab98-217">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="6ab98-218">Geçici disposleri Algıla</span><span class="sxs-lookup"><span data-stu-id="6ab98-218">Detect transient disposables</span></span>

<span data-ttu-id="6ab98-219">Aşağıdaki örneklerde, kullanması gereken bir uygulamada atılabilir geçici hizmetlerinin nasıl algılanacağı gösterilmektedir <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="6ab98-219">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="6ab98-220">Daha fazla bilgi için bkz. [BIR dı kapsamı bölümünü yönetmek Için yardımcı program temel bileşen sınıfları](#utility-base-component-classes-to-manage-a-di-scope) .</span><span class="sxs-lookup"><span data-stu-id="6ab98-220">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="6ab98-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="6ab98-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="6ab98-222">`TransientDisposable`Aşağıdaki örnekte algılandı ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="6ab98-222">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

<span data-ttu-id="6ab98-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="6ab98-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="6ab98-224">`Program`:</span><span class="sxs-lookup"><span data-stu-id="6ab98-224">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="6ab98-225">`TransientDependency`Aşağıdaki örnekte algılandı ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="6ab98-225">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="6ab98-226">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6ab98-226">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="6ab98-227">`IDisposable` Geçici ve paylaşılan örnekler için rehberlik</span><span class="sxs-lookup"><span data-stu-id="6ab98-227">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
