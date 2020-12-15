---
title: ASP.NET Core Blazor bağımlılığı ekleme
author: guardrex
description: BlazorUygulamaların bileşenlere nasıl hizmet ekleyebileceğinizi öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
ms.openlocfilehash: af6b645fc3c398414c85c78e1cfeb213e538c2a6
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506805"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="8a19b-103">ASP.NET Core Blazor bağımlılığı ekleme</span><span class="sxs-lookup"><span data-stu-id="8a19b-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="8a19b-104">Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Mike rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="8a19b-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="8a19b-105">[Bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) , merkezi bir konumda yapılandırılmış hizmetlere erişmek için bir tekniktir:</span><span class="sxs-lookup"><span data-stu-id="8a19b-105">[Dependency injection (DI)](xref:fundamentals/dependency-injection) is a technique for accessing services configured in a central location:</span></span>

* <span data-ttu-id="8a19b-106">Çerçeve kayıtlı Hizmetleri doğrudan uygulama bileşenlerine eklenebilir Blazor .</span><span class="sxs-lookup"><span data-stu-id="8a19b-106">Framework-registered services can be injected directly into components of Blazor apps.</span></span>
* <span data-ttu-id="8a19b-107">Blazor uygulamalar özel hizmetleri tanımlar ve kaydeder ve bunu uygulama genelinde DI aracılığıyla kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-107">Blazor apps define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="default-services"></a><span data-ttu-id="8a19b-108">Varsayılan hizmetler</span><span class="sxs-lookup"><span data-stu-id="8a19b-108">Default services</span></span>

<span data-ttu-id="8a19b-109">Aşağıdaki tabloda gösterilen hizmetler genellikle Blazor uygulamalarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-109">The services shown the following table are commonly used in Blazor apps.</span></span>

| <span data-ttu-id="8a19b-110">Hizmet</span><span class="sxs-lookup"><span data-stu-id="8a19b-110">Service</span></span> | <span data-ttu-id="8a19b-111">Ömür</span><span class="sxs-lookup"><span data-stu-id="8a19b-111">Lifetime</span></span> | <span data-ttu-id="8a19b-112">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8a19b-112">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="8a19b-113">Yayıl</span><span class="sxs-lookup"><span data-stu-id="8a19b-113">Scoped</span></span> | <p><span data-ttu-id="8a19b-114">HTTP istekleri göndermek ve bir URI tarafından tanımlanan bir kaynaktan HTTP yanıtlarını almak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="8a19b-114">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span></p><p><span data-ttu-id="8a19b-115"><xref:System.Net.Http.HttpClient>Bir uygulamadaki örneği, Blazor WebAssembly arka planda HTTP trafiğini işlemek için tarayıcıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-115">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span></p><p><span data-ttu-id="8a19b-116">Blazor Server uygulamalar <xref:System.Net.Http.HttpClient> Varsayılan olarak yapılandırılmış bir hizmet olarak yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="8a19b-116">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="8a19b-117">Bir <xref:System.Net.Http.HttpClient> Blazor Server uygulamaya bir uygulama sağlayın.</span><span class="sxs-lookup"><span data-stu-id="8a19b-117">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span></p><p><span data-ttu-id="8a19b-118">Daha fazla bilgi için bkz. <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="8a19b-118">For more information, see <xref:blazor/call-web-api>.</span></span></p><p><span data-ttu-id="8a19b-119"><xref:System.Net.Http.HttpClient>Tek değil, kapsamlı bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-119">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="8a19b-120">Daha fazla bilgi için [hizmet ömrü](#service-lifetime) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8a19b-120">For more information, see the [Service lifetime](#service-lifetime) section.</span></span></p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p><span data-ttu-id="8a19b-121">**Blazor WebAssembly**: Singleton</span><span class="sxs-lookup"><span data-stu-id="8a19b-121">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="8a19b-122">**Blazor Server**: Kapsamlı</span><span class="sxs-lookup"><span data-stu-id="8a19b-122">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="8a19b-123">JavaScript çağrılarının dağıtıldığı bir JavaScript çalışma zamanının örneğini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="8a19b-123">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="8a19b-124">Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="8a19b-124">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p><span data-ttu-id="8a19b-125">**Blazor WebAssembly**: Singleton</span><span class="sxs-lookup"><span data-stu-id="8a19b-125">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="8a19b-126">**Blazor Server**: Kapsamlı</span><span class="sxs-lookup"><span data-stu-id="8a19b-126">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="8a19b-127">URI 'Ler ve gezinme durumu ile çalışmaya yönelik yardımcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-127">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="8a19b-128">Daha fazla bilgi için bkz. [URI ve gezinti durumu yardımcıları](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="8a19b-128">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="8a19b-129">Özel bir hizmet sağlayıcı, tabloda listelenen varsayılan Hizmetleri otomatik olarak sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="8a19b-129">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="8a19b-130">Özel bir hizmet sağlayıcısı kullanır ve tabloda gösterilen hizmetlerden herhangi birini gerekliyse, gerekli hizmetleri yeni hizmet sağlayıcısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8a19b-130">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="8a19b-131">Uygulamaya hizmet ekleme</span><span class="sxs-lookup"><span data-stu-id="8a19b-131">Add services to an app</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="8a19b-132">Uygulamasındaki uygulamasının hizmet koleksiyonu için Hizmetleri yapılandırın `Program.Main` `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="8a19b-132">Configure services for the app's service collection in the `Program.Main` method of `Program.cs`.</span></span> <span data-ttu-id="8a19b-133">Aşağıdaki örnekte, `MyDependency` uygulama için kaydedilir `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="8a19b-133">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

<span data-ttu-id="8a19b-134">Konak oluşturulduktan sonra, herhangi bir bileşen işlenmeden önce kök dı kapsamından hizmetler kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-134">After the host is built, services are available from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="8a19b-135">Bu, içerik işlemeden önce başlatma mantığını çalıştırmak için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="8a19b-135">This can be useful for running initialization logic before rendering content:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

<span data-ttu-id="8a19b-136">Ana bilgisayar, uygulama için merkezi bir yapılandırma örneği sağlar.</span><span class="sxs-lookup"><span data-stu-id="8a19b-136">The host provides a central configuration instance for the app.</span></span> <span data-ttu-id="8a19b-137">Yukarıdaki örnekte derleme yaparken, hava durumu hizmetinin URL 'SI varsayılan bir yapılandırma kaynağından geçirilir (örneğin, `appsettings.json` ) `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="8a19b-137">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

<span data-ttu-id="8a19b-138">Yeni bir uygulama oluşturduktan sonra, `Startup.ConfigureServices` içindeki yöntemini inceleyin `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="8a19b-138">After creating a new app, examine the `Startup.ConfigureServices` method in `Startup.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="8a19b-139"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Yöntemi <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , [hizmet tanımlayıcısı](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) nesnelerinin bir listesi olan bir geçirilir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-139">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of [service descriptor](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) objects.</span></span> <span data-ttu-id="8a19b-140">Hizmetler, `ConfigureServices` hizmet koleksiyonuna hizmet tanımlayıcıları sağlayarak yöntemine eklenir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-140">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="8a19b-141">Aşağıdaki örnek, `IDataAccess` arabirimini ve somut uygulamasını içeren kavramı gösterir `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="8a19b-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a><span data-ttu-id="8a19b-142">Hizmet ömrü</span><span class="sxs-lookup"><span data-stu-id="8a19b-142">Service lifetime</span></span>

<span data-ttu-id="8a19b-143">Hizmetler, aşağıdaki tabloda gösterilen ömürlerle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-143">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="8a19b-144">Ömür</span><span class="sxs-lookup"><span data-stu-id="8a19b-144">Lifetime</span></span> | <span data-ttu-id="8a19b-145">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8a19b-145">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="8a19b-146">Blazor WebAssembly uygulamalar şu anda bir dı kapsamları kavramı içermez.</span><span class="sxs-lookup"><span data-stu-id="8a19b-146">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="8a19b-147">`Scoped`-kayıtlı hizmetler hizmetler gibi davranır `Singleton` .</span><span class="sxs-lookup"><span data-stu-id="8a19b-147">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="8a19b-148">Blazor ServerBarındırma modeli, `Scoped` http istekleri genelinde yaşam süresini destekler, ancak istemciye yüklenen bileşenler arasında Maalr bağlantısı/devre iletileri arasında değildir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-148">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SingalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="8a19b-149">RazorUygulamanın sayfaları veya MVC bölümü, kapsamlı hizmetleri normal şekilde ele alır ve sayfalar veya görünümler veya bir sayfa ya da bir bileşen görünümü arasında gezinilirken *her bir http isteğindeki* hizmetleri yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8a19b-149">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="8a19b-150">Kapsamdaki hizmetler, istemci üzerindeki bileşenler arasında gezinilirken, sunucu SignalR BAĞLANTıSıNıN http istekleri aracılığıyla değil Kullanıcı devresi bağlantısı üzerinden gerçekleştiği sırada yeniden yapılandırılmadı.</span><span class="sxs-lookup"><span data-stu-id="8a19b-150">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="8a19b-151">İstemci üzerindeki aşağıdaki bileşen senaryolarında, Kullanıcı için yeni bir devre oluşturulması nedeniyle kapsamlı hizmetler yeniden yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="8a19b-151">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="8a19b-152">Kullanıcı tarayıcının penceresini kapatır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-152">The user closes the browser's window.</span></span> <span data-ttu-id="8a19b-153">Kullanıcı yeni bir pencere açar ve uygulamaya geri gider.</span><span class="sxs-lookup"><span data-stu-id="8a19b-153">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="8a19b-154">Kullanıcı, bir tarayıcı penceresinde uygulamanın son sekmesini kapatır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-154">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="8a19b-155">Kullanıcı yeni bir sekme açar ve uygulamaya geri gider.</span><span class="sxs-lookup"><span data-stu-id="8a19b-155">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="8a19b-156">Kullanıcı tarayıcının yeniden yükleme/yenileme düğmesini seçer.</span><span class="sxs-lookup"><span data-stu-id="8a19b-156">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="8a19b-157">Uygulamalarda kapsamlı hizmetler genelinde Kullanıcı durumunu koruma hakkında daha fazla bilgi için Blazor Server bkz <xref:blazor/hosting-models?pivots=server> ..</span><span class="sxs-lookup"><span data-stu-id="8a19b-157">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="8a19b-158">Dı, hizmetin *tek bir örneğini* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8a19b-158">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="8a19b-159">Hizmet gerektiren tüm bileşenler `Singleton` aynı hizmetin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-159">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="8a19b-160">Bir bileşen hizmet kapsayıcısından bir hizmetin örneğini edindiğinde `Transient` , hizmetin *Yeni bir örneğini* alır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-160">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="8a19b-161">Dı sistemi ASP.NET Core içindeki DI sistemini temel alır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-161">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="8a19b-162">Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8a19b-162">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="8a19b-163">Bir bileşende hizmet isteme</span><span class="sxs-lookup"><span data-stu-id="8a19b-163">Request a service in a component</span></span>

<span data-ttu-id="8a19b-164">Hizmetler hizmet koleksiyonuna eklendikten sonra, [ \@ ekleme](xref:mvc/views/razor#inject) yönergesini kullanarak hizmetleri bileşenlere ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="8a19b-164">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="8a19b-165">[`@inject`](xref:mvc/views/razor#inject) iki parametreye sahiptir:</span><span class="sxs-lookup"><span data-stu-id="8a19b-165">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="8a19b-166">Tür: eklenecek hizmetin türü.</span><span class="sxs-lookup"><span data-stu-id="8a19b-166">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="8a19b-167">Özellik: eklenen App Service 'i alan özelliğin adı.</span><span class="sxs-lookup"><span data-stu-id="8a19b-167">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="8a19b-168">Özelliği el ile oluşturma gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="8a19b-168">The property doesn't require manual creation.</span></span> <span data-ttu-id="8a19b-169">Derleyici özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8a19b-169">The compiler creates the property.</span></span>

<span data-ttu-id="8a19b-170">Daha fazla bilgi için bkz. <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8a19b-170">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="8a19b-171">[`@inject`](xref:mvc/views/razor#inject)Farklı hizmetler eklemek için birden çok deyim kullanın.</span><span class="sxs-lookup"><span data-stu-id="8a19b-171">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="8a19b-172">Aşağıdaki örnek nasıl kullanılacağını göstermektedir [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="8a19b-172">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="8a19b-173">Uygulayan hizmet `Services.IDataAccess` bileşenin özelliğine eklenir `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="8a19b-173">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="8a19b-174">Kodun yalnızca soyutlamayı nasıl kullandığını aklınızda yapın `IDataAccess` :</span><span class="sxs-lookup"><span data-stu-id="8a19b-174">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="8a19b-175">Dahili olarak, oluşturulan Özellik ( `DataRepository` ) özniteliğini kullanır [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) .</span><span class="sxs-lookup"><span data-stu-id="8a19b-175">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="8a19b-176">Genellikle, bu öznitelik doğrudan kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="8a19b-176">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="8a19b-177">Bileşenler için bir temel sınıf gerekliyse ve temel sınıf için eklenen özellikler de gerekliyse, özniteliği el ile ekleyin [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) :</span><span class="sxs-lookup"><span data-stu-id="8a19b-177">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="8a19b-178">Temel sınıftan türetilmiş bileşenlerde, [`@inject`](xref:mvc/views/razor#inject) yönergesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-178">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="8a19b-179"><xref:Microsoft.AspNetCore.Components.InjectAttribute>Taban sınıfının yeterli olması yeterlidir:</span><span class="sxs-lookup"><span data-stu-id="8a19b-179">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="8a19b-180">Hizmetler 'de dı kullanma</span><span class="sxs-lookup"><span data-stu-id="8a19b-180">Use DI in services</span></span>

<span data-ttu-id="8a19b-181">Karmaşık hizmetler için ek hizmetler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-181">Complex services might require additional services.</span></span> <span data-ttu-id="8a19b-182">Aşağıdaki örnekte, `DataAccess` <xref:System.Net.Http.HttpClient> varsayılan hizmeti gerektirir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-182">In the following example, `DataAccess` requires the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="8a19b-183">[`@inject`](xref:mvc/views/razor#inject) (veya [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) özniteliği) hizmetlerde kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="8a19b-183">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="8a19b-184">Bunun yerine *Oluşturucu Ekleme* kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-184">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="8a19b-185">Gerekli hizmetler, hizmetin oluşturucusuna parametreler eklenerek eklenir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-185">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="8a19b-186">Dı hizmeti oluşturduğunda, oluşturucuda gereken hizmetleri algılar ve bunlara göre sağlar.</span><span class="sxs-lookup"><span data-stu-id="8a19b-186">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="8a19b-187">Aşağıdaki örnekte, Oluşturucu bir ile bir ile alır <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="8a19b-187">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="8a19b-188"><xref:System.Net.Http.HttpClient> Varsayılan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-188"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

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

<span data-ttu-id="8a19b-189">Oluşturucu Ekleme önkoşulları:</span><span class="sxs-lookup"><span data-stu-id="8a19b-189">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="8a19b-190">Bağımsız değişkenlerinin tümü DI tarafından yerine getirilme için tek bir Oluşturucu bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-190">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="8a19b-191">Varsayılan değerleri belirttiklerinde, DI tarafından kapsanmayan ek parametrelere izin verilir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-191">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="8a19b-192">Uygulanabilir Oluşturucu olmalıdır `public` .</span><span class="sxs-lookup"><span data-stu-id="8a19b-192">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="8a19b-193">Uygulanabilir bir Oluşturucu var olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-193">One applicable constructor must exist.</span></span> <span data-ttu-id="8a19b-194">Belirsizlik söz konusu olduğunda, bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8a19b-194">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="8a19b-195">Bir dı kapsamını yönetmek için yardımcı program temel bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="8a19b-195">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="8a19b-196">ASP.NET Core uygulamalarda, kapsamlı hizmetler genellikle geçerli isteğin kapsamlandırılır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-196">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="8a19b-197">İstek tamamlandıktan sonra, tüm kapsamlı veya geçici hizmetler dı sistemi tarafından silinir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-197">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="8a19b-198">Blazor ServerUygulamalarda, istek kapsamı istemci bağlantısı süresince sürer. Bu, geçici ve kapsamlı hizmetlerin beklenenden çok daha uzun süreli olarak oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-198">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="8a19b-199">Blazor WebAssemblyUygulamalarda, kapsamlı bir ömürle kaydedilen hizmetler tekton olarak değerlendirilir, bu nedenle tipik ASP.NET Core uygulamalarında kapsamlı hizmetlerden daha uzun bir süre yaşarlar.</span><span class="sxs-lookup"><span data-stu-id="8a19b-199">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="8a19b-200">Bir uygulamada atılabilir geçici Hizmetleri algılamak için [geçici disposaı 'Yi Algıla](#detect-transient-disposables) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8a19b-200">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="8a19b-201">Uygulamalarda bir hizmet ömrünü sınırlayan bir yaklaşım Blazor , <xref:Microsoft.AspNetCore.Components.OwningComponentBase> türü kullanır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-201">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="8a19b-202"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> , öğesinden türetilmiş soyut bir türdür <xref:Microsoft.AspNetCore.Components.ComponentBase> ve bileşenin ömrüne karşılık gelen BIR dı kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8a19b-202"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="8a19b-203">Bu kapsamı kullanarak, dı hizmetlerini kapsamlı bir ömür ile kullanmak mümkündür ve bileşen olarak bu uygulamaları canlı hale gelir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-203">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="8a19b-204">Bileşen yok edildiğinde, bileşenin kapsamlı hizmet sağlayıcısından gelen hizmetler de silinir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-204">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="8a19b-205">Bu, şu hizmetler için yararlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="8a19b-205">This can be useful for services that:</span></span>

* <span data-ttu-id="8a19b-206">Geçici ömür uygun olmadığından, bir bileşen içinde yeniden kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-206">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="8a19b-207">Tek yaşam süresi uygun olmadığından, bileşenler arasında paylaşılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-207">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="8a19b-208">Türün iki sürümü <xref:Microsoft.AspNetCore.Components.OwningComponentBase> kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="8a19b-208">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="8a19b-209"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> , türünde <xref:Microsoft.AspNetCore.Components.ComponentBase> Protected özelliği olan bir abstract, atılabilir alt öğesidir <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="8a19b-209"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="8a19b-210">Bu sağlayıcı, bileşenin kullanım ömrü kapsamındaki Hizmetleri çözümlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-210">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="8a19b-211">Ya da özniteliği kullanılarak bileşene eklenen dı Hizmetleri, [`@inject`](xref:mvc/views/razor#inject) [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) bileşen kapsamında oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="8a19b-211">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="8a19b-212">Bileşenin kapsamını kullanmak için, hizmetler veya kullanılarak çözümlenmelidir <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="8a19b-212">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="8a19b-213">Sağlayıcı kullanılarak çözümlenen hizmetlerin, <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> aynı kapsamdaki bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="8a19b-213">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <span data-ttu-id="8a19b-214"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> öğesinden türetilir <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ve KAPSAMDAKI <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> dı sağlayıcısından bir örneğini döndüren bir özellik ekler `T` .</span><span class="sxs-lookup"><span data-stu-id="8a19b-214"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="8a19b-215">Bu tür, <xref:System.IServiceProvider> uygulamanın, bileşenin kapsamını kullanarak dı kapsayıcısından gerektirdiği bir birincil hizmet olduğunda bir örneği kullanmadan kapsamlı hizmetlere erişmenin kolay bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="8a19b-215">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="8a19b-216"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>Özelliği kullanılabilir, bu sayede uygulama, gerekirse diğer tür hizmetleri alabilir.</span><span class="sxs-lookup"><span data-stu-id="8a19b-216">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="8a19b-217">DI Entity Framework Core (EF Core) DbContext kullanımı</span><span class="sxs-lookup"><span data-stu-id="8a19b-217">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="8a19b-218">Daha fazla bilgi için bkz. <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="8a19b-218">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

::: moniker range="< aspnetcore-5.0"

## <a name="detect-transient-disposables"></a><span data-ttu-id="8a19b-219">Geçici disposleri Algıla</span><span class="sxs-lookup"><span data-stu-id="8a19b-219">Detect transient disposables</span></span>

<span data-ttu-id="8a19b-220">Aşağıdaki örneklerde, kullanması gereken bir uygulamada atılabilir geçici hizmetlerinin nasıl algılanacağı gösterilmektedir <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="8a19b-220">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="8a19b-221">Daha fazla bilgi için bkz. [BIR dı kapsamı bölümünü yönetmek Için yardımcı program temel bileşen sınıfları](#utility-base-component-classes-to-manage-a-di-scope) .</span><span class="sxs-lookup"><span data-stu-id="8a19b-221">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="8a19b-222">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="8a19b-222">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="8a19b-223">`TransientDisposable`Aşağıdaki örnekte algılandı ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="8a19b-223">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

<!-- moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

moniker-end 

moniker range="< aspnetcore-5.0" -->

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

<!-- moniker-end -->

::: zone-end

::: zone pivot="server"

<span data-ttu-id="8a19b-224">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="8a19b-224">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="8a19b-225">Ad alanını şu <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> şekilde ekleyin `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="8a19b-225">Add the namespace for <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="8a19b-226">İçinde `Program.CreateHostBuilder` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="8a19b-226">In `Program.CreateHostBuilder` of `Program.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

<span data-ttu-id="8a19b-227">`TransientDependency`Aşağıdaki örnekte algılandı ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="8a19b-227">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8a19b-228">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8a19b-228">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="8a19b-229">`IDisposable` Geçici ve paylaşılan örnekler için rehberlik</span><span class="sxs-lookup"><span data-stu-id="8a19b-229">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
