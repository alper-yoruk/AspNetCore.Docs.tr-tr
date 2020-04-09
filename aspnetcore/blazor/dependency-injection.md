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
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="51ade-103">ASP.NET Çekirdek Blazor bağımlılık enjeksiyonu</span><span class="sxs-lookup"><span data-stu-id="51ade-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="51ade-104">Yazar: [Rainer Stropek](https://www.timecockpit.com) ve [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="51ade-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="51ade-105">Blazor [bağımlılık enjeksiyonu destekler (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="51ade-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="51ade-106">Uygulamalar yerleşik hizmetleri bileşenlere enjekte ederek kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="51ade-107">Uygulamalar ayrıca özel hizmetleri tanımlayabilir ve kaydedebilir ve bunları DI aracılığıyla uygulama boyunca kullanılabilir hale getirebilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="51ade-108">DI, merkezi bir konumda yapılandırılan hizmetlere erişmek için bir tekniktir.</span><span class="sxs-lookup"><span data-stu-id="51ade-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="51ade-109">Bu, Blazor uygulamalarında aşağıdakiler için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="51ade-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="51ade-110">*Tek ton* hizmeti olarak bilinen birçok bileşen arasında hizmet sınıfının tek bir örneğini paylaşın.</span><span class="sxs-lookup"><span data-stu-id="51ade-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="51ade-111">Referans soyutlamaları kullanarak bileşenleri somut hizmet sınıflarından ayırın.</span><span class="sxs-lookup"><span data-stu-id="51ade-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="51ade-112">Örneğin, uygulamadaki `IDataAccess` verilere erişmek için bir arabirim düşünün.</span><span class="sxs-lookup"><span data-stu-id="51ade-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="51ade-113">Arabirim, somut `DataAccess` bir sınıf tarafından uygulanır ve uygulamanın servis konteynerinde hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="51ade-114">Bir bileşen bir `IDataAccess` uygulama almak için DI kullandığında, bileşen beton türüyle birleştiğinde olmaz.</span><span class="sxs-lookup"><span data-stu-id="51ade-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="51ade-115">Uygulama, belki birim testlerinde sahte bir uygulama için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="51ade-116">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="51ade-116">Default services</span></span>

<span data-ttu-id="51ade-117">Varsayılan hizmetler otomatik olarak uygulamanın hizmet koleksiyonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="51ade-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="51ade-118">Hizmet</span><span class="sxs-lookup"><span data-stu-id="51ade-118">Service</span></span> | <span data-ttu-id="51ade-119">Ömür</span><span class="sxs-lookup"><span data-stu-id="51ade-119">Lifetime</span></span> | <span data-ttu-id="51ade-120">Açıklama</span><span class="sxs-lookup"><span data-stu-id="51ade-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="51ade-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="51ade-121">Singleton</span></span> | <span data-ttu-id="51ade-122">BIR URI tarafından tanımlanan bir kaynaktan HTTP istekleri göndermek ve HTTP yanıtları almak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ade-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="51ade-123">Blazor `HttpClient` WebAssembly uygulamasındaki örnek, arka planda http trafiğini işlemek için tarayıcıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="51ade-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="51ade-124">Blazor Server uygulamaları varsayılan `HttpClient` olarak bir hizmet olarak yapılandırılmış içermez.</span><span class="sxs-lookup"><span data-stu-id="51ade-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="51ade-125">Bir `HttpClient` Blazor Server uygulaması sağlayın.</span><span class="sxs-lookup"><span data-stu-id="51ade-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="51ade-126">Daha fazla bilgi için bkz. <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="51ade-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="51ade-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="51ade-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="51ade-128">Scoped (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="51ade-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="51ade-129">JavaScript çağrılarının gönderildiği bir JavaScript çalışma zamanı örneğini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="51ade-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="51ade-130">Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="51ade-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="51ade-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="51ade-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="51ade-132">Scoped (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="51ade-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="51ade-133">URI'ler ve navigasyon durumuyla çalışmak için yardımcılar içerir.</span><span class="sxs-lookup"><span data-stu-id="51ade-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="51ade-134">Daha fazla bilgi için [URI ve navigasyon durumu yardımcıları](xref:blazor/routing#uri-and-navigation-state-helpers)bakın.</span><span class="sxs-lookup"><span data-stu-id="51ade-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="51ade-135">Özel bir hizmet sağlayıcısı, tabloda listelenen varsayılan hizmetleri otomatik olarak sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="51ade-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="51ade-136">Özel bir hizmet sağlayıcısı kullanıyorsanız ve tabloda gösterilen hizmetlerden herhangi birini gerektiriyorsanız, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="51ade-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="51ade-137">Uygulamaya hizmet ekleme</span><span class="sxs-lookup"><span data-stu-id="51ade-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="51ade-138">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="51ade-138">Blazor WebAssembly</span></span>

<span data-ttu-id="51ade-139">`Main` *Uygulamanın*hizmet toplama hizmetlerini Program.cs yöntemiyle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="51ade-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="51ade-140">Aşağıdaki örnekte, `MyDependency` uygulama için `IMyDependency`kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="51ade-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="51ade-141">Ana bilgisayar oluşturuluntan sonra, hizmetlere herhangi bir bileşen işlenmeden önce kök DI kapsamından erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="51ade-142">Bu, içeriği oluşturmadan önce başlatma mantığını çalıştırmak için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="51ade-142">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="51ade-143">Ana bilgisayar, uygulama için merkezi bir yapılandırma örneği de sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ade-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="51ade-144">Önceki örnekte bina, hava durumu hizmetinin URL'si varsayılan bir yapılandırma kaynağından (örneğin, *appsettings.json)* `InitializeWeatherAsync`geçirilir:</span><span class="sxs-lookup"><span data-stu-id="51ade-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="51ade-145">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="51ade-145">Blazor Server</span></span>

<span data-ttu-id="51ade-146">Yeni bir uygulama oluşturduktan `Startup.ConfigureServices` sonra yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="51ade-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="51ade-147">Yöntem, `ConfigureServices` hizmet <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>tanımlayıcı nesnelerin listesi olan bir ()<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>geçirilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="51ade-148">Hizmetler, hizmet koleksiyonuna hizmet tanımlayıcıları sağlanarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="51ade-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="51ade-149">Aşağıdaki `IDataAccess` örnek, arayüz ve somut uygulama `DataAccess`ile kavramı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="51ade-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="51ade-150">Hizmet ömrü</span><span class="sxs-lookup"><span data-stu-id="51ade-150">Service lifetime</span></span>

<span data-ttu-id="51ade-151">Hizmetler, aşağıdaki tabloda gösterilen yaşam süreleriyle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="51ade-152">Ömür</span><span class="sxs-lookup"><span data-stu-id="51ade-152">Lifetime</span></span> | <span data-ttu-id="51ade-153">Açıklama</span><span class="sxs-lookup"><span data-stu-id="51ade-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="51ade-154">WebAssembly uygulamaları şu anda DI kapsamları kavramı yok.</span><span class="sxs-lookup"><span data-stu-id="51ade-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="51ade-155">`Scoped`-kayıtlı hizmetler hizmet `Singleton` gibi olur.</span><span class="sxs-lookup"><span data-stu-id="51ade-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="51ade-156">Ancak, Blazor Sunucu barındırma modeli `Scoped` ömür boyu destekler.</span><span class="sxs-lookup"><span data-stu-id="51ade-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="51ade-157">Sunucu Blazor uygulamalarında, kapsamlı bir hizmet kaydı *bağlantıya*kapsamlıdır.</span><span class="sxs-lookup"><span data-stu-id="51ade-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="51ade-158">Bu nedenle, geçerli amaç istemci tarafında tarayıcıda çalıştırmak olsa bile, geçerli kullanıcıya kapsamlı olması gereken hizmetler için kapsamlı hizmetlerin kullanılması tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="51ade-159">DI, hizmetin tek bir *örneğini* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="51ade-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="51ade-160">Hizmet `Singleton` gerektiren tüm bileşenler aynı hizmetin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="51ade-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="51ade-161">Bir bileşen hizmet kapsayıcısından bir `Transient` hizmet örneği aldığında, hizmetin yeni bir *örneğini* alır.</span><span class="sxs-lookup"><span data-stu-id="51ade-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="51ade-162">DI sistemi ASP.NET Core'daki DI sistemine dayanır.</span><span class="sxs-lookup"><span data-stu-id="51ade-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="51ade-163">Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="51ade-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="51ade-164">Bileşende hizmet isteme</span><span class="sxs-lookup"><span data-stu-id="51ade-164">Request a service in a component</span></span>

<span data-ttu-id="51ade-165">Hizmetler hizmet koleksiyonuna eklendikten sonra, razor [ \@direktifini kullanarak](xref:mvc/views/razor#inject) hizmetleri bileşenlere enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="51ade-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="51ade-166">`@inject`iki parametrevardır:</span><span class="sxs-lookup"><span data-stu-id="51ade-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="51ade-167">Enjekte &ndash; etmek için hizmet türünü yazın.</span><span class="sxs-lookup"><span data-stu-id="51ade-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="51ade-168">Özellik &ndash; Enjekte edilen uygulama hizmetini alan özelliğin adı.</span><span class="sxs-lookup"><span data-stu-id="51ade-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="51ade-169">Özellik manuel oluşturma gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="51ade-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="51ade-170">Derleyici özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="51ade-170">The compiler creates the property.</span></span>

<span data-ttu-id="51ade-171">Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="51ade-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="51ade-172">Farklı `@inject` hizmetler enjekte etmek için birden çok deyim kullanın.</span><span class="sxs-lookup"><span data-stu-id="51ade-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="51ade-173">Aşağıdaki örnekte nasıl `@inject`kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="51ade-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="51ade-174">Hizmet uygulaması `Services.IDataAccess` bileşenin özelliğine `DataRepository`enjekte edilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="51ade-175">Kodun yalnızca soyutlamayı `IDataAccess` nasıl kullandığına dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="51ade-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="51ade-176">Dahili olarak, oluşturulan`DataRepository`özellik `InjectAttribute` ( ) özniteliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="51ade-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="51ade-177">Genellikle, bu öznitelik doğrudan kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="51ade-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="51ade-178">Bileşenler için bir taban sınıf gerekiyorsa ve taban sınıf için enjekte edilen `InjectAttribute`özellikler de gerekliyse, el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="51ade-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="51ade-179">Taban sınıftan türetilen bileşenlerde `@inject` yönerge gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="51ade-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="51ade-180">`InjectAttribute` Taban sınıfın yeterlidir:</span><span class="sxs-lookup"><span data-stu-id="51ade-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="51ade-181">Hizmetlerde DI'yi kullanma</span><span class="sxs-lookup"><span data-stu-id="51ade-181">Use DI in services</span></span>

<span data-ttu-id="51ade-182">Karmaşık hizmetler ek hizmetler gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-182">Complex services might require additional services.</span></span> <span data-ttu-id="51ade-183">Önceki örnekte, `DataAccess` `HttpClient` varsayılan hizmet gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="51ade-184">`@inject``InjectAttribute`(veya) hizmetlerde kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="51ade-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="51ade-185">*Bunun* yerine yapıcı enjeksiyon kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="51ade-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="51ade-186">Gerekli hizmetler, hizmetin oluşturucuya parametreler eklenerek eklenir.</span><span class="sxs-lookup"><span data-stu-id="51ade-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="51ade-187">DI hizmeti oluşturduğunda, oluşturucuda ihtiyaç duyduğu hizmetleri tanır ve buna göre sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ade-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="51ade-188">Konstrüktör enjeksiyonu için ön koşullar:</span><span class="sxs-lookup"><span data-stu-id="51ade-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="51ade-189">Bir oluşturucu olan argümanlar tüm DI tarafından yerine getirilebilir var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="51ade-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="51ade-190">Varsayılan değerleri belirtirlerse, DI tarafından kapsanmayan ek parametrelere izin verilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="51ade-191">İlgili yapıcı *genel*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="51ade-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="51ade-192">Uygulanabilir bir yapıcı var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="51ade-192">One applicable constructor must exist.</span></span> <span data-ttu-id="51ade-193">Bir belirsizlik durumunda, DI bir özel durum atar.</span><span class="sxs-lookup"><span data-stu-id="51ade-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="51ade-194">Bir DI kapsamını yönetmek için yardımcı program temel bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="51ade-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="51ade-195">ASP.NET Core uygulamalarında, kapsamlı hizmetler genellikle geçerli isteğe göre kapsamlıdır.</span><span class="sxs-lookup"><span data-stu-id="51ade-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="51ade-196">İstek tamamlandıktan sonra, kapsamlı veya geçici hizmetler DI sistemi tarafından bertaraf edilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="51ade-197">Sunucu Blazor uygulamalarında, istek kapsamı istemci bağlantısı süresince devam eder ve bu da geçici ve kapsamlı hizmetlerin beklenenden çok daha uzun yaşamalarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="51ade-198">WebAssembly uygulamalarında, Blazor kapsamlı bir kullanım ömrüne sahip hizmetler singletons olarak kabul edilir, bu nedenle tipik ASP.NET Core uygulamalarındaki kapsamlı hizmetlerden daha uzun yaşarlar.</span><span class="sxs-lookup"><span data-stu-id="51ade-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="51ade-199">Uygulamalarda Blazor hizmet ömrünü sınırlayan bir yaklaşım, `OwningComponentBase` bu tür de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51ade-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="51ade-200">`OwningComponentBase`bileşenin `ComponentBase` ömrüne karşılık gelen bir DI kapsamı oluşturan soyut bir türdür.</span><span class="sxs-lookup"><span data-stu-id="51ade-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="51ade-201">Bu kapsamı kullanarak, DI hizmetlerini kapsamlı bir ömür boyu kullanmak ve bileşen kadar uzun süre yaşamalarını sağlamak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="51ade-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="51ade-202">Bileşen yok edildiğinde, bileşenin kapsamlı servis sağlayıcısının hizmetleri de bertaraf edilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="51ade-203">Bu, aşağıdaki hizmetler için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="51ade-203">This can be useful for services that:</span></span>

* <span data-ttu-id="51ade-204">Geçici ömür uygun olmadığından, bir bileşen içinde yeniden kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="51ade-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="51ade-205">Singleton ömrü uygun olmadığından, bileşenler arasında paylaşılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="51ade-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="51ade-206">`OwningComponentBase` Türünün iki sürümü mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="51ade-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="51ade-207">`OwningComponentBase``ComponentBase` türünde `ScopedServices` `IServiceProvider`korumalı bir özelliğe sahip türün soyut, tek kullanımlık bir çocuktur.</span><span class="sxs-lookup"><span data-stu-id="51ade-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="51ade-208">Bu sağlayıcı, bileşenin ömrüne göre kapsamda olan hizmetleri çözümlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="51ade-209">Bileşene enjekte `@inject` edilen DI hizmetleri `InjectAttribute` `[Inject]`veya ( ) bileşenin kapsamında oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="51ade-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="51ade-210">Bileşenin kapsamını kullanmak için, hizmetlerin kullanılarak `ScopedServices.GetRequiredService` çözülmesi `ScopedServices.GetService`veya .</span><span class="sxs-lookup"><span data-stu-id="51ade-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="51ade-211">`ScopedServices` Sağlayıcı kullanılarak çözüme kavuşturulan tüm hizmetlerin bağımlılıkları aynı kapsamdan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="51ade-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="51ade-212">`OwningComponentBase<T>`kapsamına giren `OwningComponentBase` DI sağlayıcısından bir örneğini `Service` `T` döndüren bir özellikten türetilmiştir ve ekler.</span><span class="sxs-lookup"><span data-stu-id="51ade-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="51ade-213">Bu tür, uygulamanın bileşenin kapsamını kullanarak DI `IServiceProvider` kapsayıcısından gerektirdiği bir birincil hizmetin ne zaman olduğunu kullanmadan kapsamlı hizmetlere erişmek için kullanışlı bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="51ade-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="51ade-214">`ScopedServices` Özellik kullanılabilir, böylece uygulama gerekirse diğer türde hizmetler alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51ade-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="51ade-215">DI'den Varlık Çerçevesi DbContext Kullanımı</span><span class="sxs-lookup"><span data-stu-id="51ade-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="51ade-216">Web uygulamalarında DI'den alınacak yaygın bir hizmet `DbContext` türü de Entity Framework (EF) nesneleridir.</span><span class="sxs-lookup"><span data-stu-id="51ade-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="51ade-217">EF hizmetlerini kullanarak `IServiceCollection.AddDbContext` kaydetmek `DbContext` varsayılan olarak kapsamlı bir hizmet olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="51ade-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="51ade-218">Kapsamlı bir hizmet olarak kaydolmak, Blazor örneklerin `DbContext` uzun ömürlü olmasına ve uygulama genelinde paylaşılmasına neden olduğundan uygulamalarda sorunlara yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="51ade-219">`DbContext`iş parçacığı güvenli değildir ve aynı anda kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="51ade-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="51ade-220">Uygulamaya bağlı olarak, `OwningComponentBase` a'nın `DbContext` kapsamını tek bir bileşenle sınırlamak için kullanmak sorunu *çözebilir.*</span><span class="sxs-lookup"><span data-stu-id="51ade-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="51ade-221">Bir bileşen paralel olarak `DbContext` bir bileşen kullanmıyorsa, `OwningComponentBase` bileşeni elde etmek `DbContext` ve `ScopedServices` ondan almak yeterlidir, çünkü aşağıdakileri sağlar:</span><span class="sxs-lookup"><span data-stu-id="51ade-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="51ade-222">Ayrı bileşenler bir `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="51ade-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="51ade-223">Sadece `DbContext` bileşen bağlı olduğu sürece yaşıyor.</span><span class="sxs-lookup"><span data-stu-id="51ade-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="51ade-224">Tek bir bileşen aynı `DbContext` anda bir bileşen kullanıyorsa (örneğin, bir `OwningComponentBase` kullanıcı her düğmeyi seçtiğinde), kullanmak bile eşzamanlı EF işlemleriyle ilgili sorunları önlemez.</span><span class="sxs-lookup"><span data-stu-id="51ade-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="51ade-225">Bu durumda, her `DbContext` mantıksal EF işlemi için farklı bir kullanın.</span><span class="sxs-lookup"><span data-stu-id="51ade-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="51ade-226">Aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="51ade-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="51ade-227">DI'den alınabilir ve iş parçacığı güvenli bir bağımsız değişken olarak `DbContext` doğrudan kullanarak `DbContextOptions<TContext>` oluşturun.</span><span class="sxs-lookup"><span data-stu-id="51ade-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="51ade-228">Servis `DbContext` konteynerini geçici bir kullanım ömrüyle kaydedin:</span><span class="sxs-lookup"><span data-stu-id="51ade-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="51ade-229">Bağlamı kaydederken, `ServiceLifetime.Transient`.'</span><span class="sxs-lookup"><span data-stu-id="51ade-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="51ade-230">Uzantı `AddDbContext` yöntemi, türünün `ServiceLifetime`iki isteğe bağlı parametresini alır.</span><span class="sxs-lookup"><span data-stu-id="51ade-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="51ade-231">Bu yaklaşımı kullanmak için `contextLifetime` yalnızca parametrenin . `ServiceLifetime.Transient`</span><span class="sxs-lookup"><span data-stu-id="51ade-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="51ade-232">`optionsLifetime`varsayılan değerini `ServiceLifetime.Scoped`koruyabilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="51ade-233">Geçici, `DbContext` paralel olarak birden fazla `@inject`EF işlemini yürütmeyecek bileşenlere normal (using) olarak enjekte edilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ade-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="51ade-234">Aynı anda birden çok EF işlemi `DbContext` gerçekleştirebilenler, `IServiceProvider.GetRequiredService`her paralel işlem için ayrı nesneler isteyebilirler.</span><span class="sxs-lookup"><span data-stu-id="51ade-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="51ade-235">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="51ade-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
