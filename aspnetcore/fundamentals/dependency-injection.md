---
title: ASP.NET Core'da bağımlılık ekleme
author: rick-anderson
description: ASP.NET Core'un bağımlılık enjeksiyonunu nasıl uyguladığını ve nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 943ea30c2e4887638f69b6dcdb7e323bcee40240
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405975"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="34b71-103">ASP.NET Core'da bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="34b71-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="34b71-104">Yazar: [Steve Smith](https://ardalis.com/) ve [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="34b71-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="34b71-105">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [Kontrol Inversion (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik tir bağımlılık enjeksiyon (DI) yazılım tasarım deseni destekler.</span><span class="sxs-lookup"><span data-stu-id="34b71-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="34b71-106">MVC denetleyicileri içinde bağımlılık enjeksiyonuna özgü <xref:mvc/controllers/dependency-injection>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="34b71-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="34b71-107">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34b71-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="34b71-108">Bağımlılık enjeksiyonuna genel bakış</span><span class="sxs-lookup"><span data-stu-id="34b71-108">Overview of dependency injection</span></span>

<span data-ttu-id="34b71-109">*Bağımlılık,* başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="34b71-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="34b71-110">Bir uygulamadaki diğer `WriteMessage` sınıfların bağlı olduğu bir yöntemle aşağıdaki `MyDependency` sınıfı inceleyin:</span><span class="sxs-lookup"><span data-stu-id="34b71-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="34b71-111">Yöntemi bir `MyDependency` sınıf için kullanılabilir hale getirmek için sınıfın bir örneği oluşturulabilir. `WriteMessage`</span><span class="sxs-lookup"><span data-stu-id="34b71-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="34b71-112">Sınıf `MyDependency` sınıfın bir bağımlılıktır: `IndexModel`</span><span class="sxs-lookup"><span data-stu-id="34b71-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="34b71-113">Sınıf oluşturur ve doğrudan örneğin `MyDependency` bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="34b71-114">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunludur ve aşağıdaki nedenlerden dolayı kaçınılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="34b71-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="34b71-115">Farklı `MyDependency` bir uygulamayla değiştirmek için sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="34b71-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="34b71-116">Bağımlılıkları `MyDependency` varsa, sınıf tarafından yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="34b71-117">Bağlı olarak birden fazla sınıfa `MyDependency`sahip büyük bir projede, yapılandırma kodu uygulamaya dağılır.</span><span class="sxs-lookup"><span data-stu-id="34b71-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="34b71-118">Bu uygulamayı birleştirmek zordur.</span><span class="sxs-lookup"><span data-stu-id="34b71-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="34b71-119">Uygulama, bu yaklaşımla mümkün `MyDependency` olmayan bir sahte veya saplama sınıfı kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="34b71-120">Bağımlılık enjeksiyonu bu sorunları şu yollarla giderir:</span><span class="sxs-lookup"><span data-stu-id="34b71-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="34b71-121">Bağımlılık uygulamasını soyutlamak için arabirim veya taban sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="34b71-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="34b71-122">Bir hizmet kapsayıcısında ki bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="34b71-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="34b71-123">ASP.NET Core dahili servis konteyneri sağlar. <xref:System.IServiceProvider></span><span class="sxs-lookup"><span data-stu-id="34b71-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="34b71-124">Hizmetler uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="34b71-125">Hizmetin kullanıldığı sınıfın yapıcısına *enjeksiyonu.*</span><span class="sxs-lookup"><span data-stu-id="34b71-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="34b71-126">Çerçeve, artık ihtiyaç duyulmadığında bağımlılığın bir örneğini oluşturma ve ortadan çıkarma sorumluluğunu üstlenir.</span><span class="sxs-lookup"><span data-stu-id="34b71-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="34b71-127">Örnek [uygulamada,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="34b71-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="34b71-128">Bu arayüz, somut bir tür `MyDependency`tarafından uygulanır:</span><span class="sxs-lookup"><span data-stu-id="34b71-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="34b71-129">`MyDependency`onun <xref:Microsoft.Extensions.Logging.ILogger`1> yapıcı bir talep.</span><span class="sxs-lookup"><span data-stu-id="34b71-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="34b71-130">Bağımlılık enjeksiyonunu zincirli bir şekilde kullanmak alışılmadık bir şey değil.</span><span class="sxs-lookup"><span data-stu-id="34b71-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="34b71-131">İstenen her bağımlılık sırayla kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="34b71-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="34b71-132">Kapsayıcı grafikteki bağımlılıkları giderir ve tam olarak çözülmüş hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="34b71-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="34b71-133">Çözülmesi gereken toplu bağımlılıkkümesi genellikle *bağımlılık ağacı,* *bağımlılık grafiği*veya nesne *grafiği*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="34b71-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="34b71-134">`IMyDependency`ve `ILogger<TCategoryName>` servis konteynerine kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="34b71-135">`IMyDependency``Startup.ConfigureServices`kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34b71-136">`ILogger<TCategoryName>`günlük soyutlamaaltyapısı tarafından kaydedilir, bu nedenle çerçeve tarafından varsayılan olarak kaydedilmiş bir [çerçeve tarafından sağlanan](#framework-provided-services) bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="34b71-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="34b71-137">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türleri](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak giderir , her [(genel) inşa türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kayıt ihtiyacını ortadan kaldırarak:</span><span class="sxs-lookup"><span data-stu-id="34b71-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="34b71-138">Örnek uygulamada, `IMyDependency` hizmet beton türüne `MyDependency`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="34b71-139">Kayıt, hizmet ömrünü tek bir isteğin ömrüne kadar kapsamlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="34b71-140">[Hizmet ömürleri](#service-lifetimes) bu konuda daha sonra açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="34b71-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="34b71-141">Her `services.Add{SERVICE_NAME}` uzantı yöntemi hizmetleri ekler (ve büyük ölçüde yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="34b71-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="34b71-142">Örneğin, `services.AddMvc()` Razor Pages ve MVC'nin gerektirdiği hizmetleri ekler.</span><span class="sxs-lookup"><span data-stu-id="34b71-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="34b71-143">Uygulamaların bu kuralı izlemelerini tavsiye ettik.</span><span class="sxs-lookup"><span data-stu-id="34b71-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="34b71-144">Hizmet kayıtları gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="34b71-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="34b71-145">Hizmetin oluşturucusu, yapılandırma [veya](xref:fundamentals/configuration/index) [seçenekler deseni](xref:fundamentals/configuration/options)kullanılarak enjekte edilebilir, bir , bir `string`gibi yerleşik bir [tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektirir:</span><span class="sxs-lookup"><span data-stu-id="34b71-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="34b71-146">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="34b71-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="34b71-147">Alan, sınıf boyunca gerektiğinde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="34b71-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="34b71-148">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin `WriteMessage` yöntemini aramak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="34b71-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="34b71-149">Başlangıç'a enjekte edilen hizmetler</span><span class="sxs-lookup"><span data-stu-id="34b71-149">Services injected into Startup</span></span>

<span data-ttu-id="34b71-150">Genel Ana Bilgisayar kullanırken `Startup` oluşturucuya yalnızca aşağıdaki hizmet<xref:Microsoft.Extensions.Hosting.IHostBuilder>türleri enjekte edilebilir ( ):</span><span class="sxs-lookup"><span data-stu-id="34b71-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="34b71-151">Hizmetler enjekte `Startup.Configure`edilebilir:</span><span class="sxs-lookup"><span data-stu-id="34b71-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="34b71-152">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="34b71-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="34b71-153">Çerçeve tarafından sağlanan hizmetler</span><span class="sxs-lookup"><span data-stu-id="34b71-153">Framework-provided services</span></span>

<span data-ttu-id="34b71-154">Yöntem, `Startup.ConfigureServices` Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="34b71-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="34b71-155">Başlangıçta, `IServiceCollection` sağlanan `ConfigureServices` hizmetler [ana bilgisayar yapılandırılmıştır nasıl](xref:fundamentals/index#host)bağlı olarak çerçeve tarafından tanımlanan vardır.</span><span class="sxs-lookup"><span data-stu-id="34b71-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="34b71-156">ASP.NET Core şablonuna dayalı bir uygulamanın çerçeve tarafından yüzlerce hizmetin kaydolması nadir değildir.</span><span class="sxs-lookup"><span data-stu-id="34b71-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="34b71-157">Çerçeveye kayıtlı hizmetlerin küçük bir örneği aşağıdaki tabloda listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="34b71-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="34b71-158">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="34b71-158">Service Type</span></span> | <span data-ttu-id="34b71-159">Ömür</span><span class="sxs-lookup"><span data-stu-id="34b71-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="34b71-160">Geçi -ci</span><span class="sxs-lookup"><span data-stu-id="34b71-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="34b71-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="34b71-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="34b71-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="34b71-164">Geçi -ci</span><span class="sxs-lookup"><span data-stu-id="34b71-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="34b71-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="34b71-166">Geçi -ci</span><span class="sxs-lookup"><span data-stu-id="34b71-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="34b71-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="34b71-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="34b71-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="34b71-170">Geçi -ci</span><span class="sxs-lookup"><span data-stu-id="34b71-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="34b71-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="34b71-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="34b71-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="34b71-174">Ek hizmetleri genişletme yöntemleriyle kaydetme</span><span class="sxs-lookup"><span data-stu-id="34b71-174">Register additional services with extension methods</span></span>

<span data-ttu-id="34b71-175">Bir hizmeti kaydetmek için bir hizmet toplama uzantısı yöntemi (ve gerekirse bağımlı hizmetleri) `Add{SERVICE_NAME}` kullanılabilir olduğunda, sözleşme, söz le ilgili hizmetin gerektirdiği tüm hizmetleri kaydetmek için tek bir uzantı yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="34b71-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="34b71-176">Aşağıdaki kod, [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ve aşağıdaki uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetlerin nasıl ekleyeceğinize bir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>örnektir:</span><span class="sxs-lookup"><span data-stu-id="34b71-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="34b71-177">Daha fazla bilgi <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> için API belgelerindeki sınıfa bakın.</span><span class="sxs-lookup"><span data-stu-id="34b71-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="34b71-178">Hizmet ömürleri</span><span class="sxs-lookup"><span data-stu-id="34b71-178">Service lifetimes</span></span>

<span data-ttu-id="34b71-179">Her kayıtlı hizmet için uygun bir ömür seçin.</span><span class="sxs-lookup"><span data-stu-id="34b71-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="34b71-180">ASP.NET Core hizmetleri aşağıdaki yaşam süreleriyle yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="34b71-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="34b71-181">Geçi -ci</span><span class="sxs-lookup"><span data-stu-id="34b71-181">Transient</span></span>

<span data-ttu-id="34b71-182">Geçici yaşam süresi<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>hizmetleri ( ) hizmet kapsayıcısından her istendiğinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="34b71-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="34b71-183">Bu ömür, hafif ve devletsiz hizmetler için en iyi şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="34b71-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="34b71-184">Kapsamlı</span><span class="sxs-lookup"><span data-stu-id="34b71-184">Scoped</span></span>

<span data-ttu-id="34b71-185">Kapsamlı yaşam<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>süresi hizmetleri ( ) istemci isteği (bağlantı) başına bir kez oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="34b71-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="34b71-186">Bir ara yazılımda kapsamlı bir hizmet kullanırken, `Invoke` `InvokeAsync` hizmeti veya yöntemini enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="34b71-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="34b71-187">Yapıcı enjeksiyon yoluyla enjekte etmeyin çünkü hizmeti tek ton gibi olmaya zorlar.</span><span class="sxs-lookup"><span data-stu-id="34b71-187">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="34b71-188">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="34b71-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="34b71-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-189">Singleton</span></span>

<span data-ttu-id="34b71-190">Singleton yaşam<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>boyu hizmetleri ( ) ilk kez istendiğinde `Startup.ConfigureServices` oluşturulur (veya çalıştırıldığında ve hizmet kaydıyla bir örnek belirtilir).</span><span class="sxs-lookup"><span data-stu-id="34b71-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="34b71-191">Sonraki her istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="34b71-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="34b71-192">Uygulama singleton davranışı gerektiriyorsa, hizmet kapsayıcısının hizmetin kullanım ömrünü yönetmesine izin vermek önerilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="34b71-193">Singleton tasarım deseni uygulamayın ve nesnenin sınıftaki ömrünü yönetmek için kullanıcı kodu sağlamayın.</span><span class="sxs-lookup"><span data-stu-id="34b71-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="34b71-194">Tek tonlu bir hizmeti çözmek tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="34b71-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="34b71-195">Sonraki istekleri işlerken hizmetin yanlış duruma sahip olmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="34b71-196">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="34b71-196">Service registration methods</span></span>

<span data-ttu-id="34b71-197">Hizmet kaydı uzatma yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="34b71-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="34b71-198">Yöntem</span><span class="sxs-lookup"><span data-stu-id="34b71-198">Method</span></span> | <span data-ttu-id="34b71-199">Automatic</span><span class="sxs-lookup"><span data-stu-id="34b71-199">Automatic</span></span><br><span data-ttu-id="34b71-200">object</span><span class="sxs-lookup"><span data-stu-id="34b71-200">object</span></span><br><span data-ttu-id="34b71-201">Elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="34b71-201">disposal</span></span> | <span data-ttu-id="34b71-202">Birden çok</span><span class="sxs-lookup"><span data-stu-id="34b71-202">Multiple</span></span><br><span data-ttu-id="34b71-203">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="34b71-203">implementations</span></span> | <span data-ttu-id="34b71-204">Pass args</span><span class="sxs-lookup"><span data-stu-id="34b71-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="34b71-205">Örnek:</span><span class="sxs-lookup"><span data-stu-id="34b71-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="34b71-206">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-206">Yes</span></span> | <span data-ttu-id="34b71-207">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-207">Yes</span></span> | <span data-ttu-id="34b71-208">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="34b71-209">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="34b71-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="34b71-210">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-210">Yes</span></span> | <span data-ttu-id="34b71-211">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-211">Yes</span></span> | <span data-ttu-id="34b71-212">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="34b71-213">Örnek:</span><span class="sxs-lookup"><span data-stu-id="34b71-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="34b71-214">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-214">Yes</span></span> | <span data-ttu-id="34b71-215">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-215">No</span></span> | <span data-ttu-id="34b71-216">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="34b71-217">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="34b71-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="34b71-218">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-218">No</span></span> | <span data-ttu-id="34b71-219">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-219">Yes</span></span> | <span data-ttu-id="34b71-220">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="34b71-221">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="34b71-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="34b71-222">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-222">No</span></span> | <span data-ttu-id="34b71-223">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-223">No</span></span> | <span data-ttu-id="34b71-224">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-224">Yes</span></span> |

<span data-ttu-id="34b71-225">Tür elden çıkarma hakkında daha fazla bilgi [için, hizmetlerin Elden Çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="34b71-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="34b71-226">Birden çok uygulama için ortak bir [senaryo, sınama türleri ile alay ediyor.](xref:test/integration-tests#inject-mock-services)</span><span class="sxs-lookup"><span data-stu-id="34b71-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="34b71-227">`TryAdd{LIFETIME}`yöntemler yalnızca kayıtlı bir uygulama yoksa hizmeti kaydedin.</span><span class="sxs-lookup"><span data-stu-id="34b71-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="34b71-228">Aşağıdaki örnekte, ilk satır `MyDependency` için `IMyDependency`kaydeder.</span><span class="sxs-lookup"><span data-stu-id="34b71-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="34b71-229">`IMyDependency` Zaten kayıtlı bir uygulama olduğundan ikinci satırın hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="34b71-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="34b71-230">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="34b71-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="34b71-231">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="34b71-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="34b71-232">Birden çok hizmet `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="34b71-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="34b71-233">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri henüz eklenmemişse bir örnek eklemek ister.</span><span class="sxs-lookup"><span data-stu-id="34b71-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="34b71-234">Genellikle, bu yöntem kapsayıcıda bir örneğin iki kopyasını kaydetmeyi önlemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="34b71-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="34b71-235">Aşağıdaki örnekte, ilk satır `MyDep` için `IMyDep1`kaydeder.</span><span class="sxs-lookup"><span data-stu-id="34b71-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="34b71-236">İkinci satır `MyDep` için `IMyDep2`kaydeder.</span><span class="sxs-lookup"><span data-stu-id="34b71-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="34b71-237">Zaten kayıtlı bir uygulama `IMyDep1` vardır, çünkü `MyDep`üçüncü satırın hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="34b71-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="34b71-238">Yapıcı enjeksiyon davranışı</span><span class="sxs-lookup"><span data-stu-id="34b71-238">Constructor injection behavior</span></span>

<span data-ttu-id="34b71-239">Hizmetler iki mekanizma yla çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="34b71-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="34b71-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Bağımlılık enjeksiyon konteynerinde hizmet kaydı olmadan nesne oluşturmaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="34b71-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="34b71-241">`ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model bağlayıcıları gibi kullanıcıya yönelik soyutlamalarla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="34b71-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="34b71-242">Oluşturucular bağımlılık enjeksiyonu tarafından sağlanmadı bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenler varsayılan değerleri atamalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="34b71-243">Hizmetler tarafından `IServiceProvider` çözüldüğünde `ActivatorUtilities`veya , yapıcı enjeksiyon bir *kamu* yapıcı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="34b71-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="34b71-244">Hizmetler tarafından `ActivatorUtilities`çözüldüğünde, yapıcı enjeksiyon sadece bir uygulanabilir yapıcı var gerektirir.</span><span class="sxs-lookup"><span data-stu-id="34b71-244">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="34b71-245">Yapıcı aşırı yükler desteklenir, ancak bağımsız değişkenleri bağımlılık enjeksiyonu yla yerine getirilebilen yalnızca bir aşırı yükleme olabilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="34b71-246">Varlık Çerçevesi bağlamları</span><span class="sxs-lookup"><span data-stu-id="34b71-246">Entity Framework contexts</span></span>

<span data-ttu-id="34b71-247">Varlık Çerçevesi bağlamları genellikle [kapsamlı yaşam süresi](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir, çünkü web uygulaması veritabanı işlemleri normalde istemci isteğine yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="34b71-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="34b71-248">Veritabanı bağlamını kaydederken bir [adddbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklenme tarafından bir ömür belirtilmemişse varsayılan yaşam süresi kapsamlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="34b71-249">Belirli bir yaşam süresinin hizmetleri, hizmetten daha kısa bir kullanım ömrüne sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="34b71-250">Ömür boyu ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="34b71-250">Lifetime and registration options</span></span>

<span data-ttu-id="34b71-251">Kullanım ömrü ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir `OperationId`işlem olarak temsil eden aşağıdaki arabirimleri düşünün.</span><span class="sxs-lookup"><span data-stu-id="34b71-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="34b71-252">Bir işlem hizmetinin kullanım ömrünün aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak, kapsayıcı bir sınıf tarafından istendiğinde hizmetin aynı veya farklı bir örneğini sağlar:</span><span class="sxs-lookup"><span data-stu-id="34b71-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="34b71-253">Arabirimler `Operation` sınıfta uygulanır.</span><span class="sxs-lookup"><span data-stu-id="34b71-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="34b71-254">Oluşturucu, `Operation` sağlanmazsa bir GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="34b71-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="34b71-255">Diğer `OperationService` `Operation` türlerin her birine bağlı olarak bir kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="34b71-256">Bağımlılık `OperationService` enjeksiyonu yoluyla istendiğinde, bağımlı hizmetin kullanım ömrüne bağlı olarak her hizmetin yeni bir örneğini veya varolan bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="34b71-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="34b71-257">Kapsayıcıdan istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmetin .'den `OperationId` farkı `OperationService`vardır.</span><span class="sxs-lookup"><span data-stu-id="34b71-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="34b71-258">`OperationService`sınıfın yeni bir `IOperationTransient` örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="34b71-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="34b71-259">Yeni örnek farklı `OperationId`bir verim verir.</span><span class="sxs-lookup"><span data-stu-id="34b71-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="34b71-260">Kapsamlı hizmetler istemci isteği başına `OperationId` oluşturulduğunda, `IOperationScoped` hizmetin istemci `OperationService` isteği içindekiyle aynıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="34b71-261">İstemci istekleri arasında, `OperationId` her iki hizmet de farklı bir değeri paylaşır.</span><span class="sxs-lookup"><span data-stu-id="34b71-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="34b71-262">Singleton ve singleton-instance hizmetleri bir kez oluşturulduğunda ve tüm `OperationId` istemci istekleri ve tüm hizmetler arasında kullanıldığında, tüm hizmet istekleri arasında sabittir.</span><span class="sxs-lookup"><span data-stu-id="34b71-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="34b71-263">Her `Startup.ConfigureServices`tür, kapta adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="34b71-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="34b71-264">Hizmet, `IOperationSingletonInstance` bilinen bir kimliği olan belirli `Guid.Empty`bir örneği kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="34b71-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="34b71-265">Bu tür kullanımda olduğunda açıktır (ONUN GUID tüm sıfırlar).</span><span class="sxs-lookup"><span data-stu-id="34b71-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="34b71-266">Örnek uygulama, tek tek istekler içinde ve arasında nesne yaşam ömürlerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="34b71-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="34b71-267">Örnek uygulamanın `IndexModel` her `IOperation` tür ve `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="34b71-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="34b71-268">Sayfa daha sonra özellik atamaları aracılığıyla sayfa modeli `OperationId` sınıfının ve hizmet değerlerinin tümlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="34b71-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="34b71-269">Aşağıdaki iki çıktı iki isteğin sonuçlarını gösterir:</span><span class="sxs-lookup"><span data-stu-id="34b71-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="34b71-270">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="34b71-270">**First request:**</span></span>

<span data-ttu-id="34b71-271">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="34b71-271">Controller operations:</span></span>

<span data-ttu-id="34b71-272">Geçici: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="34b71-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="34b71-273">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="34b71-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="34b71-274">Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="34b71-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="34b71-275">Örnek: 00000000-0000-0000-0000-0000000000000</span><span class="sxs-lookup"><span data-stu-id="34b71-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="34b71-276">`OperationService`Işlem:</span><span class="sxs-lookup"><span data-stu-id="34b71-276">`OperationService` operations:</span></span>

<span data-ttu-id="34b71-277">Geçici: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="34b71-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="34b71-278">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="34b71-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="34b71-279">Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="34b71-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="34b71-280">Örnek: 00000000-0000-0000-0000-0000000000000</span><span class="sxs-lookup"><span data-stu-id="34b71-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="34b71-281">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="34b71-281">**Second request:**</span></span>

<span data-ttu-id="34b71-282">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="34b71-282">Controller operations:</span></span>

<span data-ttu-id="34b71-283">Geçici: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="34b71-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="34b71-284">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="34b71-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="34b71-285">Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="34b71-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="34b71-286">Örnek: 00000000-0000-0000-0000-0000000000000</span><span class="sxs-lookup"><span data-stu-id="34b71-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="34b71-287">`OperationService`Işlem:</span><span class="sxs-lookup"><span data-stu-id="34b71-287">`OperationService` operations:</span></span>

<span data-ttu-id="34b71-288">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="34b71-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="34b71-289">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="34b71-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="34b71-290">Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="34b71-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="34b71-291">Örnek: 00000000-0000-0000-0000-0000000000000</span><span class="sxs-lookup"><span data-stu-id="34b71-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="34b71-292">Değerlerden hangisinin `OperationId` istek içinde ve istekler arasında farklılık gösterdiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="34b71-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="34b71-293">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-293">*Transient* objects are always different.</span></span> <span data-ttu-id="34b71-294">Hem birinci `OperationId` hem de ikinci istemci istekleri için `OperationService` geçici değer hem işlemler hem de istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="34b71-295">Her hizmet isteği ve istemci isteği için yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="34b71-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="34b71-296">*Kapsamlı* nesneler istemci isteği içinde aynıdır, ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="34b71-297">*Singleton* nesneleri, bir `Operation` örneğin sağlanıp sağlanmadığına bakılmaksızın her `Startup.ConfigureServices`nesne ve her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="34b71-298">Ana hizmetten çağrı hizmetleri</span><span class="sxs-lookup"><span data-stu-id="34b71-298">Call services from main</span></span>

<span data-ttu-id="34b71-299">Uygulama <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> kapsamındaki kapsamlı bir hizmeti çözmek için [iServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="34b71-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="34b71-300">Bu yaklaşım, başlatma görevlerini çalıştırmak için başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="34b71-301">Aşağıdaki örnek, aşağıdaki `MyScopedService` `Program.Main`için bir bağlam nasıl elde edilebildiğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="34b71-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="34b71-302">Kapsam doğrulama</span><span class="sxs-lookup"><span data-stu-id="34b71-302">Scope validation</span></span>

<span data-ttu-id="34b71-303">Uygulama Geliştirme ortamında çalışırken ve ana bilgisayarı oluşturmak için [CreateDefaultBuilder'ı](xref:fundamentals/host/generic-host#default-builder-settings) aradığında, varsayılan hizmet sağlayıcısı bunu doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="34b71-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="34b71-304">Kapsamlı hizmetler, kök hizmet sağlayıcısından doğrudan veya dolaylı olarak çözülmez.</span><span class="sxs-lookup"><span data-stu-id="34b71-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="34b71-305">Kapsamlı hizmetler doğrudan veya dolaylı olarak singleton içine enjekte değildir.</span><span class="sxs-lookup"><span data-stu-id="34b71-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="34b71-306">Kök hizmet sağlayıcısı çağrıldığında <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="34b71-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="34b71-307">Kök servis sağlayıcısının ömrü, sağlayıcı nın uygulamayla birlikte işe başlaması ve uygulama kapandığında imha edilmesiyle uygulamanın/sunucunun ömrüne karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="34b71-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="34b71-308">Kapsamlı hizmetler, bunları oluşturan kapsayıcı tarafından bertaraf edilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="34b71-309">Kök kapsayıcıda kapsamlı bir hizmet oluşturulursa, yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından bertaraf edildiğinden, hizmetin ömrü etkin bir şekilde singleton'a yükseltilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="34b71-310">Hizmet kapsamlarını doğrulama çağrıldığında `BuildServiceProvider` bu durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="34b71-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="34b71-311">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="34b71-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="34b71-312">Talep Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="34b71-312">Request Services</span></span>

<span data-ttu-id="34b71-313">ASP.NET Core isteği içinde sunulan `HttpContext` [hizmetler, HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla açıklanır.</span><span class="sxs-lookup"><span data-stu-id="34b71-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="34b71-314">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil ediyor.</span><span class="sxs-lookup"><span data-stu-id="34b71-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="34b71-315">Nesneler bağımlılıkları belirttiğinde, bunlar , `RequestServices` `ApplicationServices`değil' de bulunan türlerle karşılanır.</span><span class="sxs-lookup"><span data-stu-id="34b71-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="34b71-316">Genellikle, uygulama bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="34b71-317">Bunun yerine, sınıf oluşturucular aracılığıyla sınıfların gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları enjekte etmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="34b71-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="34b71-318">Bu, sınaması daha kolay olan sınıflar verir.</span><span class="sxs-lookup"><span data-stu-id="34b71-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="34b71-319">Koleksiyona erişmek için kurucu parametreleri olarak `RequestServices` bağımlılıkları talep etmeyi tercih edin.</span><span class="sxs-lookup"><span data-stu-id="34b71-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="34b71-320">Bağımlılık enjeksiyonu için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="34b71-320">Design services for dependency injection</span></span>

<span data-ttu-id="34b71-321">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="34b71-321">Best practices are to:</span></span>

* <span data-ttu-id="34b71-322">Bağımlılıklarını elde etmek için bağımlılık enjeksiyonu kullanmak için tasarım hizmetleri.</span><span class="sxs-lookup"><span data-stu-id="34b71-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="34b71-323">Durum lu, statik sınıflardan ve üyelerden kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="34b71-324">Bunun yerine, küresel durum oluşturmaktan kaçınan singleton hizmetlerini kullanmak için uygulamalar tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="34b71-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="34b71-325">Hizmetler içindeki bağımlı sınıfların doğrudan anında anlanmasından kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="34b71-326">Kodu belirli bir uygulamaya yönlendirin anlık çiftler.</span><span class="sxs-lookup"><span data-stu-id="34b71-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="34b71-327">Uygulama sınıflarını küçük, iyi faktörlü ve kolayca test edilebilen hale getirin.</span><span class="sxs-lookup"><span data-stu-id="34b71-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="34b71-328">Bir sınıfın çok fazla enjekte edilmiş bağımlılığı varsa, bu genellikle sınıfın çok fazla sorumluluğu olduğunun ve [Tek Sorumluluk İlkesi'ni (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal ettiğini gösteren bir işarettir.</span><span class="sxs-lookup"><span data-stu-id="34b71-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="34b71-329">Bazı sorumluluklarını yeni bir sınıfa taşıyarak sınıfı yeniden düzenlemeyi dene.</span><span class="sxs-lookup"><span data-stu-id="34b71-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="34b71-330">Razor Pages sayfa modeli sınıflarının ve MVC denetleyici sınıflarının UI endişelerine odaklanması gerektiğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="34b71-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="34b71-331">İş kuralları ve veri erişimi uygulama ayrıntıları bu [ayrı endişelere](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="34b71-332">Hizmetlerin bertaraf edilmesi</span><span class="sxs-lookup"><span data-stu-id="34b71-332">Disposal of services</span></span>

<span data-ttu-id="34b71-333">Kapsayıcı, <xref:System.IDisposable.Dispose*> oluşturduğu <xref:System.IDisposable> türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="34b71-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="34b71-334">Kullanıcı koduyla kapsayıcıya bir örnek eklenirse, otomatik olarak atılmaz.</span><span class="sxs-lookup"><span data-stu-id="34b71-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="34b71-335">Aşağıdaki örnekte, hizmetler servis kapsayıcısı tarafından oluşturulur ve otomatik olarak atılır:</span><span class="sxs-lookup"><span data-stu-id="34b71-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="34b71-336">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="34b71-336">In the following example:</span></span>

* <span data-ttu-id="34b71-337">Hizmet örnekleri servis kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="34b71-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="34b71-338">Amaçlanan hizmet ömürleri çerçeve tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="34b71-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="34b71-339">Çerçeve hizmetleri otomatik olarak elden çıkarmaz.</span><span class="sxs-lookup"><span data-stu-id="34b71-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="34b71-340">Hizmetler geliştirici kodunda açıkça belirtinmezse, uygulama kapanana kadar devam eder.</span><span class="sxs-lookup"><span data-stu-id="34b71-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="34b71-341">Hizmet imha seçeneklerinin tartışılması [için, IDisposables'i ASP.NET Core'da elden çıkarmanın dört yolunu](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)görün.</span><span class="sxs-lookup"><span data-stu-id="34b71-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="34b71-342">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="34b71-342">Default service container replacement</span></span>

<span data-ttu-id="34b71-343">Dahili servis konteyneri, çerçevenin ve çoğu tüketici uygulamasının ihtiyaçlarına hizmet etmek üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="34b71-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="34b71-344">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız olmadığı sürece yerleşik kapsayıcıyı kullanmanızı öneririz:</span><span class="sxs-lookup"><span data-stu-id="34b71-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="34b71-345">Özellik enjeksiyonu</span><span class="sxs-lookup"><span data-stu-id="34b71-345">Property injection</span></span>
* <span data-ttu-id="34b71-346">İsme göre enjeksiyon</span><span class="sxs-lookup"><span data-stu-id="34b71-346">Injection based on name</span></span>
* <span data-ttu-id="34b71-347">Çocuk kapları</span><span class="sxs-lookup"><span data-stu-id="34b71-347">Child containers</span></span>
* <span data-ttu-id="34b71-348">Özel yaşam boyu yönetimi</span><span class="sxs-lookup"><span data-stu-id="34b71-348">Custom lifetime management</span></span>
* <span data-ttu-id="34b71-349">`Func<T>`tembel başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="34b71-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="34b71-350">Sözleşme tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="34b71-350">Convention-based registration</span></span>

<span data-ttu-id="34b71-351">Aşağıdaki 3 ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="34b71-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="34b71-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="34b71-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="34b71-353">Dryioc</span><span class="sxs-lookup"><span data-stu-id="34b71-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="34b71-354">Zarafet</span><span class="sxs-lookup"><span data-stu-id="34b71-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="34b71-355">LightInject</span><span class="sxs-lookup"><span data-stu-id="34b71-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="34b71-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="34b71-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="34b71-357">Zula kutusu</span><span class="sxs-lookup"><span data-stu-id="34b71-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="34b71-358">Unity</span><span class="sxs-lookup"><span data-stu-id="34b71-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="34b71-359">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="34b71-359">Thread safety</span></span>

<span data-ttu-id="34b71-360">İş parçacığı güvenli singleton hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="34b71-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="34b71-361">Bir singleton hizmetigeçici bir hizmete bağımlıysa, geçici hizmet, singleton tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="34b71-362">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)ikinci bağımsız değişkeni gibi tek hizmet fabrika yöntemi, iş parçacığı güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="34b71-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="34b71-363">Bir tür`static`gibi ( ) oluşturucu, tek bir iş parçacığı tarafından bir kez çağrılması garanti.</span><span class="sxs-lookup"><span data-stu-id="34b71-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="34b71-364">Öneriler</span><span class="sxs-lookup"><span data-stu-id="34b71-364">Recommendations</span></span>

* <span data-ttu-id="34b71-365">`async/await`ve `Task` tabanlı hizmet çözünürlüğü desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="34b71-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="34b71-366">C# asynchronous yapıcıları desteklemez; bu nedenle, önerilen desen eşitolarak hizmeti çözdükten sonra eşzamanlı yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="34b71-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="34b71-367">Verileri ve yapılandırmayı doğrudan servis kapsayıcısında depolamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="34b71-368">Örneğin, bir kullanıcının alışveriş sepeti genellikle servis kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="34b71-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="34b71-369">Yapılandırma seçenekleri [deseni](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="34b71-370">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için var olan "veri tutucu" nesnelerden kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="34b71-371">Bu DI üzerinden gerçek öğeyi istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="34b71-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="34b71-372">Hizmetlere statik erişimden kaçının (örneğin, başka bir yerde kullanmak için statik olarak [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) yazarak).</span><span class="sxs-lookup"><span data-stu-id="34b71-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="34b71-373">*Servis bulucu deseni*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="34b71-374">Örneğin, di yerine <xref:System.IServiceProvider.GetService*> kullanabileceğiniz bir hizmet örneği almak için çağrıda almayın:</span><span class="sxs-lookup"><span data-stu-id="34b71-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="34b71-375">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="34b71-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="34b71-376">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="34b71-376">**Correct**:</span></span>

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

* <span data-ttu-id="34b71-377">Önlemek için başka bir hizmet bulucu varyasyon çalışma zamanında bağımlılıkları çözen bir fabrika enjekte etmektir.</span><span class="sxs-lookup"><span data-stu-id="34b71-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="34b71-378">Bu uygulamaların her ikisi de [Kontrol stratejilerinin Ters Çevrilmesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) karıştırır.</span><span class="sxs-lookup"><span data-stu-id="34b71-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="34b71-379">Statik erişimden `HttpContext` kaçının (örneğin, [IHttpContextAccessor.HttpContext).](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)</span><span class="sxs-lookup"><span data-stu-id="34b71-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="34b71-380">Tüm öneri kümeleri gibi, bir öneriyi yoksaymanın gerekli olduğu durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="34b71-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="34b71-381">İstisnalar, çerçevenin kendisi içinde nadir görülen&mdash;özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="34b71-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="34b71-382">DI statik /küresel nesne erişim desenleri için bir *alternatiftir.*</span><span class="sxs-lookup"><span data-stu-id="34b71-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="34b71-383">Statik nesne erişimiyle karıştırırsanız DI'nin faydalarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="34b71-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34b71-384">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="34b71-384">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="34b71-385">Bağımlılık Enjeksiyonu (MSDN) ile ASP.NET Çekirdekte Temiz Kod Yazma</span><span class="sxs-lookup"><span data-stu-id="34b71-385">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="34b71-386">Açık Bağımlılıklar İlkesi</span><span class="sxs-lookup"><span data-stu-id="34b71-386">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="34b71-387">Kontrol Kapları ve Bağımlılık Enjeksiyon Deseni (Martin Fowler) Ters</span><span class="sxs-lookup"><span data-stu-id="34b71-387">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="34b71-388">Core DI'de birden çok arabirimi olan bir hizmet ASP.NET</span><span class="sxs-lookup"><span data-stu-id="34b71-388">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34b71-389">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [Kontrol Inversion (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik tir bağımlılık enjeksiyon (DI) yazılım tasarım deseni destekler.</span><span class="sxs-lookup"><span data-stu-id="34b71-389">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="34b71-390">MVC denetleyicileri içinde bağımlılık enjeksiyonuna özgü <xref:mvc/controllers/dependency-injection>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="34b71-390">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="34b71-391">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34b71-391">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="34b71-392">Bağımlılık enjeksiyonuna genel bakış</span><span class="sxs-lookup"><span data-stu-id="34b71-392">Overview of dependency injection</span></span>

<span data-ttu-id="34b71-393">*Bağımlılık,* başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="34b71-393">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="34b71-394">Bir uygulamadaki diğer `WriteMessage` sınıfların bağlı olduğu bir yöntemle aşağıdaki `MyDependency` sınıfı inceleyin:</span><span class="sxs-lookup"><span data-stu-id="34b71-394">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="34b71-395">Yöntemi bir `MyDependency` sınıf için kullanılabilir hale getirmek için sınıfın bir örneği oluşturulabilir. `WriteMessage`</span><span class="sxs-lookup"><span data-stu-id="34b71-395">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="34b71-396">Sınıf `MyDependency` sınıfın bir bağımlılıktır: `IndexModel`</span><span class="sxs-lookup"><span data-stu-id="34b71-396">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="34b71-397">Sınıf oluşturur ve doğrudan örneğin `MyDependency` bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-397">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="34b71-398">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunludur ve aşağıdaki nedenlerden dolayı kaçınılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="34b71-398">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="34b71-399">Farklı `MyDependency` bir uygulamayla değiştirmek için sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="34b71-399">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="34b71-400">Bağımlılıkları `MyDependency` varsa, sınıf tarafından yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-400">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="34b71-401">Bağlı olarak birden fazla sınıfa `MyDependency`sahip büyük bir projede, yapılandırma kodu uygulamaya dağılır.</span><span class="sxs-lookup"><span data-stu-id="34b71-401">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="34b71-402">Bu uygulamayı birleştirmek zordur.</span><span class="sxs-lookup"><span data-stu-id="34b71-402">This implementation is difficult to unit test.</span></span> <span data-ttu-id="34b71-403">Uygulama, bu yaklaşımla mümkün `MyDependency` olmayan bir sahte veya saplama sınıfı kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-403">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="34b71-404">Bağımlılık enjeksiyonu bu sorunları şu yollarla giderir:</span><span class="sxs-lookup"><span data-stu-id="34b71-404">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="34b71-405">Bağımlılık uygulamasını soyutlamak için arabirim veya taban sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="34b71-405">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="34b71-406">Bir hizmet kapsayıcısında ki bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="34b71-406">Registration of the dependency in a service container.</span></span> <span data-ttu-id="34b71-407">ASP.NET Core dahili servis konteyneri sağlar. <xref:System.IServiceProvider></span><span class="sxs-lookup"><span data-stu-id="34b71-407">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="34b71-408">Hizmetler uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-408">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="34b71-409">Hizmetin kullanıldığı sınıfın yapıcısına *enjeksiyonu.*</span><span class="sxs-lookup"><span data-stu-id="34b71-409">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="34b71-410">Çerçeve, artık ihtiyaç duyulmadığında bağımlılığın bir örneğini oluşturma ve ortadan çıkarma sorumluluğunu üstlenir.</span><span class="sxs-lookup"><span data-stu-id="34b71-410">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="34b71-411">Örnek [uygulamada,](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="34b71-411">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="34b71-412">Bu arayüz, somut bir tür `MyDependency`tarafından uygulanır:</span><span class="sxs-lookup"><span data-stu-id="34b71-412">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="34b71-413">`MyDependency`onun <xref:Microsoft.Extensions.Logging.ILogger`1> yapıcı bir talep.</span><span class="sxs-lookup"><span data-stu-id="34b71-413">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="34b71-414">Bağımlılık enjeksiyonunu zincirli bir şekilde kullanmak alışılmadık bir şey değil.</span><span class="sxs-lookup"><span data-stu-id="34b71-414">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="34b71-415">İstenen her bağımlılık sırayla kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="34b71-415">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="34b71-416">Kapsayıcı grafikteki bağımlılıkları giderir ve tam olarak çözülmüş hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="34b71-416">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="34b71-417">Çözülmesi gereken toplu bağımlılıkkümesi genellikle *bağımlılık ağacı,* *bağımlılık grafiği*veya nesne *grafiği*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="34b71-417">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="34b71-418">`IMyDependency`ve `ILogger<TCategoryName>` servis konteynerine kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-418">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="34b71-419">`IMyDependency``Startup.ConfigureServices`kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-419">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34b71-420">`ILogger<TCategoryName>`günlük soyutlamaaltyapısı tarafından kaydedilir, bu nedenle çerçeve tarafından varsayılan olarak kaydedilmiş bir [çerçeve tarafından sağlanan](#framework-provided-services) bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="34b71-420">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="34b71-421">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türleri](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak giderir , her [(genel) inşa türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kayıt ihtiyacını ortadan kaldırarak:</span><span class="sxs-lookup"><span data-stu-id="34b71-421">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="34b71-422">Örnek uygulamada, `IMyDependency` hizmet beton türüne `MyDependency`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-422">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="34b71-423">Kayıt, hizmet ömrünü tek bir isteğin ömrüne kadar kapsamlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-423">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="34b71-424">[Hizmet ömürleri](#service-lifetimes) bu konuda daha sonra açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="34b71-424">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="34b71-425">Her `services.Add{SERVICE_NAME}` uzantı yöntemi hizmetleri ekler (ve büyük ölçüde yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="34b71-425">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="34b71-426">Örneğin, `services.AddMvc()` Razor Pages ve MVC'nin gerektirdiği hizmetleri ekler.</span><span class="sxs-lookup"><span data-stu-id="34b71-426">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="34b71-427">Uygulamaların bu kuralı izlemelerini tavsiye ettik.</span><span class="sxs-lookup"><span data-stu-id="34b71-427">We recommended that apps follow this convention.</span></span> <span data-ttu-id="34b71-428">Hizmet kayıtları gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="34b71-428">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="34b71-429">Hizmetin oluşturucusu, yapılandırma [veya](xref:fundamentals/configuration/index) [seçenekler deseni](xref:fundamentals/configuration/options)kullanılarak enjekte edilebilir, bir , bir `string`gibi yerleşik bir [tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektirir:</span><span class="sxs-lookup"><span data-stu-id="34b71-429">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="34b71-430">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="34b71-430">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="34b71-431">Alan, sınıf boyunca gerektiğinde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="34b71-431">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="34b71-432">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin `WriteMessage` yöntemini aramak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="34b71-432">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="34b71-433">Başlangıç'a enjekte edilen hizmetler</span><span class="sxs-lookup"><span data-stu-id="34b71-433">Services injected into Startup</span></span>

<span data-ttu-id="34b71-434">Genel Ana Bilgisayar kullanırken `Startup` oluşturucuya yalnızca aşağıdaki hizmet<xref:Microsoft.Extensions.Hosting.IHostBuilder>türleri enjekte edilebilir ( ):</span><span class="sxs-lookup"><span data-stu-id="34b71-434">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="34b71-435">Hizmetler enjekte `Startup.Configure`edilebilir:</span><span class="sxs-lookup"><span data-stu-id="34b71-435">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="34b71-436">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="34b71-436">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="34b71-437">Çerçeve tarafından sağlanan hizmetler</span><span class="sxs-lookup"><span data-stu-id="34b71-437">Framework-provided services</span></span>

<span data-ttu-id="34b71-438">Yöntem, `Startup.ConfigureServices` Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="34b71-438">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="34b71-439">Başlangıçta, `IServiceCollection` sağlanan `ConfigureServices` hizmetler [ana bilgisayar yapılandırılmıştır nasıl](xref:fundamentals/index#host)bağlı olarak çerçeve tarafından tanımlanan vardır.</span><span class="sxs-lookup"><span data-stu-id="34b71-439">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="34b71-440">ASP.NET Core şablonuna dayalı bir uygulamanın çerçeve tarafından yüzlerce hizmetin kaydolması nadir değildir.</span><span class="sxs-lookup"><span data-stu-id="34b71-440">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="34b71-441">Çerçeveye kayıtlı hizmetlerin küçük bir örneği aşağıdaki tabloda listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="34b71-441">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="34b71-442">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="34b71-442">Service Type</span></span> | <span data-ttu-id="34b71-443">Ömür</span><span class="sxs-lookup"><span data-stu-id="34b71-443">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="34b71-444">Geçi -ci</span><span class="sxs-lookup"><span data-stu-id="34b71-444">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="34b71-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-445">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="34b71-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="34b71-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-447">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="34b71-448">Geçi -ci</span><span class="sxs-lookup"><span data-stu-id="34b71-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="34b71-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="34b71-450">Geçi -ci</span><span class="sxs-lookup"><span data-stu-id="34b71-450">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="34b71-451">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-451">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="34b71-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-452">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="34b71-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-453">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="34b71-454">Geçi -ci</span><span class="sxs-lookup"><span data-stu-id="34b71-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="34b71-455">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-455">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="34b71-456">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-456">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="34b71-457">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-457">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="34b71-458">Ek hizmetleri genişletme yöntemleriyle kaydetme</span><span class="sxs-lookup"><span data-stu-id="34b71-458">Register additional services with extension methods</span></span>

<span data-ttu-id="34b71-459">Bir hizmeti kaydetmek için bir hizmet toplama uzantısı yöntemi (ve gerekirse bağımlı hizmetleri) `Add{SERVICE_NAME}` kullanılabilir olduğunda, sözleşme, söz le ilgili hizmetin gerektirdiği tüm hizmetleri kaydetmek için tek bir uzantı yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="34b71-459">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="34b71-460">Aşağıdaki kod, [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ve aşağıdaki uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetlerin nasıl ekleyeceğinize bir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>örnektir:</span><span class="sxs-lookup"><span data-stu-id="34b71-460">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="34b71-461">Daha fazla bilgi <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> için API belgelerindeki sınıfa bakın.</span><span class="sxs-lookup"><span data-stu-id="34b71-461">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="34b71-462">Hizmet ömürleri</span><span class="sxs-lookup"><span data-stu-id="34b71-462">Service lifetimes</span></span>

<span data-ttu-id="34b71-463">Her kayıtlı hizmet için uygun bir ömür seçin.</span><span class="sxs-lookup"><span data-stu-id="34b71-463">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="34b71-464">ASP.NET Core hizmetleri aşağıdaki yaşam süreleriyle yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="34b71-464">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="34b71-465">Geçi -ci</span><span class="sxs-lookup"><span data-stu-id="34b71-465">Transient</span></span>

<span data-ttu-id="34b71-466">Geçici yaşam süresi<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>hizmetleri ( ) hizmet kapsayıcısından her istendiğinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="34b71-466">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="34b71-467">Bu ömür, hafif ve devletsiz hizmetler için en iyi şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="34b71-467">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="34b71-468">Kapsamlı</span><span class="sxs-lookup"><span data-stu-id="34b71-468">Scoped</span></span>

<span data-ttu-id="34b71-469">Kapsamlı yaşam<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>süresi hizmetleri ( ) istemci isteği (bağlantı) başına bir kez oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="34b71-469">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="34b71-470">Bir ara yazılımda kapsamlı bir hizmet kullanırken, `Invoke` `InvokeAsync` hizmeti veya yöntemini enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="34b71-470">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="34b71-471">Yapıcı enjeksiyon yoluyla enjekte etmeyin çünkü hizmeti tek ton gibi olmaya zorlar.</span><span class="sxs-lookup"><span data-stu-id="34b71-471">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="34b71-472">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="34b71-472">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="34b71-473">Singleton</span><span class="sxs-lookup"><span data-stu-id="34b71-473">Singleton</span></span>

<span data-ttu-id="34b71-474">Singleton yaşam<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>boyu hizmetleri ( ) ilk kez istendiğinde `Startup.ConfigureServices` oluşturulur (veya çalıştırıldığında ve hizmet kaydıyla bir örnek belirtilir).</span><span class="sxs-lookup"><span data-stu-id="34b71-474">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="34b71-475">Sonraki her istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="34b71-475">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="34b71-476">Uygulama singleton davranışı gerektiriyorsa, hizmet kapsayıcısının hizmetin kullanım ömrünü yönetmesine izin vermek önerilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-476">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="34b71-477">Singleton tasarım deseni uygulamayın ve nesnenin sınıftaki ömrünü yönetmek için kullanıcı kodu sağlamayın.</span><span class="sxs-lookup"><span data-stu-id="34b71-477">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="34b71-478">Tek tonlu bir hizmeti çözmek tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="34b71-478">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="34b71-479">Sonraki istekleri işlerken hizmetin yanlış duruma sahip olmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-479">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="34b71-480">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="34b71-480">Service registration methods</span></span>

<span data-ttu-id="34b71-481">Hizmet kaydı uzatma yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="34b71-481">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="34b71-482">Yöntem</span><span class="sxs-lookup"><span data-stu-id="34b71-482">Method</span></span> | <span data-ttu-id="34b71-483">Automatic</span><span class="sxs-lookup"><span data-stu-id="34b71-483">Automatic</span></span><br><span data-ttu-id="34b71-484">object</span><span class="sxs-lookup"><span data-stu-id="34b71-484">object</span></span><br><span data-ttu-id="34b71-485">Elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="34b71-485">disposal</span></span> | <span data-ttu-id="34b71-486">Birden çok</span><span class="sxs-lookup"><span data-stu-id="34b71-486">Multiple</span></span><br><span data-ttu-id="34b71-487">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="34b71-487">implementations</span></span> | <span data-ttu-id="34b71-488">Pass args</span><span class="sxs-lookup"><span data-stu-id="34b71-488">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="34b71-489">Örnek:</span><span class="sxs-lookup"><span data-stu-id="34b71-489">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="34b71-490">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-490">Yes</span></span> | <span data-ttu-id="34b71-491">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-491">Yes</span></span> | <span data-ttu-id="34b71-492">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-492">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="34b71-493">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="34b71-493">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="34b71-494">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-494">Yes</span></span> | <span data-ttu-id="34b71-495">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-495">Yes</span></span> | <span data-ttu-id="34b71-496">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-496">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="34b71-497">Örnek:</span><span class="sxs-lookup"><span data-stu-id="34b71-497">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="34b71-498">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-498">Yes</span></span> | <span data-ttu-id="34b71-499">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-499">No</span></span> | <span data-ttu-id="34b71-500">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-500">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="34b71-501">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="34b71-501">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="34b71-502">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-502">No</span></span> | <span data-ttu-id="34b71-503">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-503">Yes</span></span> | <span data-ttu-id="34b71-504">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-504">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="34b71-505">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="34b71-505">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="34b71-506">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-506">No</span></span> | <span data-ttu-id="34b71-507">Hayır</span><span class="sxs-lookup"><span data-stu-id="34b71-507">No</span></span> | <span data-ttu-id="34b71-508">Evet</span><span class="sxs-lookup"><span data-stu-id="34b71-508">Yes</span></span> |

<span data-ttu-id="34b71-509">Tür elden çıkarma hakkında daha fazla bilgi [için, hizmetlerin Elden Çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="34b71-509">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="34b71-510">Birden çok uygulama için ortak bir [senaryo, sınama türleri ile alay ediyor.](xref:test/integration-tests#inject-mock-services)</span><span class="sxs-lookup"><span data-stu-id="34b71-510">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="34b71-511">`TryAdd{LIFETIME}`yöntemler yalnızca kayıtlı bir uygulama yoksa hizmeti kaydedin.</span><span class="sxs-lookup"><span data-stu-id="34b71-511">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="34b71-512">Aşağıdaki örnekte, ilk satır `MyDependency` için `IMyDependency`kaydeder.</span><span class="sxs-lookup"><span data-stu-id="34b71-512">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="34b71-513">`IMyDependency` Zaten kayıtlı bir uygulama olduğundan ikinci satırın hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="34b71-513">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="34b71-514">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="34b71-514">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="34b71-515">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="34b71-515">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="34b71-516">Birden çok hizmet `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="34b71-516">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="34b71-517">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri henüz eklenmemişse bir örnek eklemek ister.</span><span class="sxs-lookup"><span data-stu-id="34b71-517">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="34b71-518">Genellikle, bu yöntem kapsayıcıda bir örneğin iki kopyasını kaydetmeyi önlemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="34b71-518">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="34b71-519">Aşağıdaki örnekte, ilk satır `MyDep` için `IMyDep1`kaydeder.</span><span class="sxs-lookup"><span data-stu-id="34b71-519">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="34b71-520">İkinci satır `MyDep` için `IMyDep2`kaydeder.</span><span class="sxs-lookup"><span data-stu-id="34b71-520">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="34b71-521">Zaten kayıtlı bir uygulama `IMyDep1` vardır, çünkü `MyDep`üçüncü satırın hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="34b71-521">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="34b71-522">Yapıcı enjeksiyon davranışı</span><span class="sxs-lookup"><span data-stu-id="34b71-522">Constructor injection behavior</span></span>

<span data-ttu-id="34b71-523">Hizmetler iki mekanizma yla çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="34b71-523">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="34b71-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Bağımlılık enjeksiyon konteynerinde hizmet kaydı olmadan nesne oluşturmaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="34b71-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="34b71-525">`ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model bağlayıcıları gibi kullanıcıya yönelik soyutlamalarla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="34b71-525">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="34b71-526">Oluşturucular bağımlılık enjeksiyonu tarafından sağlanmadı bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenler varsayılan değerleri atamalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-526">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="34b71-527">Hizmetler tarafından `IServiceProvider` çözüldüğünde `ActivatorUtilities`veya , yapıcı enjeksiyon bir *kamu* yapıcı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="34b71-527">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="34b71-528">Hizmetler tarafından `ActivatorUtilities`çözüldüğünde, yapıcı enjeksiyon sadece bir uygulanabilir yapıcı var gerektirir.</span><span class="sxs-lookup"><span data-stu-id="34b71-528">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="34b71-529">Yapıcı aşırı yükler desteklenir, ancak bağımsız değişkenleri bağımlılık enjeksiyonu yla yerine getirilebilen yalnızca bir aşırı yükleme olabilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-529">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="34b71-530">Varlık Çerçevesi bağlamları</span><span class="sxs-lookup"><span data-stu-id="34b71-530">Entity Framework contexts</span></span>

<span data-ttu-id="34b71-531">Varlık Çerçevesi bağlamları genellikle [kapsamlı yaşam süresi](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir, çünkü web uygulaması veritabanı işlemleri normalde istemci isteğine yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="34b71-531">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="34b71-532">Veritabanı bağlamını kaydederken bir [adddbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklenme tarafından bir ömür belirtilmemişse varsayılan yaşam süresi kapsamlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-532">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="34b71-533">Belirli bir yaşam süresinin hizmetleri, hizmetten daha kısa bir kullanım ömrüne sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-533">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="34b71-534">Ömür boyu ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="34b71-534">Lifetime and registration options</span></span>

<span data-ttu-id="34b71-535">Kullanım ömrü ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir `OperationId`işlem olarak temsil eden aşağıdaki arabirimleri düşünün.</span><span class="sxs-lookup"><span data-stu-id="34b71-535">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="34b71-536">Bir işlem hizmetinin kullanım ömrünün aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak, kapsayıcı bir sınıf tarafından istendiğinde hizmetin aynı veya farklı bir örneğini sağlar:</span><span class="sxs-lookup"><span data-stu-id="34b71-536">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="34b71-537">Arabirimler `Operation` sınıfta uygulanır.</span><span class="sxs-lookup"><span data-stu-id="34b71-537">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="34b71-538">Oluşturucu, `Operation` sağlanmazsa bir GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="34b71-538">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="34b71-539">Diğer `OperationService` `Operation` türlerin her birine bağlı olarak bir kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-539">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="34b71-540">Bağımlılık `OperationService` enjeksiyonu yoluyla istendiğinde, bağımlı hizmetin kullanım ömrüne bağlı olarak her hizmetin yeni bir örneğini veya varolan bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="34b71-540">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="34b71-541">Kapsayıcıdan istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmetin .'den `OperationId` farkı `OperationService`vardır.</span><span class="sxs-lookup"><span data-stu-id="34b71-541">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="34b71-542">`OperationService`sınıfın yeni bir `IOperationTransient` örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="34b71-542">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="34b71-543">Yeni örnek farklı `OperationId`bir verim verir.</span><span class="sxs-lookup"><span data-stu-id="34b71-543">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="34b71-544">Kapsamlı hizmetler istemci isteği başına `OperationId` oluşturulduğunda, `IOperationScoped` hizmetin istemci `OperationService` isteği içindekiyle aynıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-544">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="34b71-545">İstemci istekleri arasında, `OperationId` her iki hizmet de farklı bir değeri paylaşır.</span><span class="sxs-lookup"><span data-stu-id="34b71-545">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="34b71-546">Singleton ve singleton-instance hizmetleri bir kez oluşturulduğunda ve tüm `OperationId` istemci istekleri ve tüm hizmetler arasında kullanıldığında, tüm hizmet istekleri arasında sabittir.</span><span class="sxs-lookup"><span data-stu-id="34b71-546">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="34b71-547">Her `Startup.ConfigureServices`tür, kapta adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="34b71-547">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="34b71-548">Hizmet, `IOperationSingletonInstance` bilinen bir kimliği olan belirli `Guid.Empty`bir örneği kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="34b71-548">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="34b71-549">Bu tür kullanımda olduğunda açıktır (ONUN GUID tüm sıfırlar).</span><span class="sxs-lookup"><span data-stu-id="34b71-549">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="34b71-550">Örnek uygulama, tek tek istekler içinde ve arasında nesne yaşam ömürlerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="34b71-550">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="34b71-551">Örnek uygulamanın `IndexModel` her `IOperation` tür ve `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="34b71-551">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="34b71-552">Sayfa daha sonra özellik atamaları aracılığıyla sayfa modeli `OperationId` sınıfının ve hizmet değerlerinin tümlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="34b71-552">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="34b71-553">Aşağıdaki iki çıktı iki isteğin sonuçlarını gösterir:</span><span class="sxs-lookup"><span data-stu-id="34b71-553">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="34b71-554">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="34b71-554">**First request:**</span></span>

<span data-ttu-id="34b71-555">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="34b71-555">Controller operations:</span></span>

<span data-ttu-id="34b71-556">Geçici: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="34b71-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="34b71-557">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="34b71-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="34b71-558">Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="34b71-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="34b71-559">Örnek: 00000000-0000-0000-0000-0000000000000</span><span class="sxs-lookup"><span data-stu-id="34b71-559">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="34b71-560">`OperationService`Işlem:</span><span class="sxs-lookup"><span data-stu-id="34b71-560">`OperationService` operations:</span></span>

<span data-ttu-id="34b71-561">Geçici: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="34b71-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="34b71-562">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="34b71-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="34b71-563">Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="34b71-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="34b71-564">Örnek: 00000000-0000-0000-0000-0000000000000</span><span class="sxs-lookup"><span data-stu-id="34b71-564">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="34b71-565">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="34b71-565">**Second request:**</span></span>

<span data-ttu-id="34b71-566">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="34b71-566">Controller operations:</span></span>

<span data-ttu-id="34b71-567">Geçici: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="34b71-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="34b71-568">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="34b71-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="34b71-569">Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="34b71-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="34b71-570">Örnek: 00000000-0000-0000-0000-0000000000000</span><span class="sxs-lookup"><span data-stu-id="34b71-570">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="34b71-571">`OperationService`Işlem:</span><span class="sxs-lookup"><span data-stu-id="34b71-571">`OperationService` operations:</span></span>

<span data-ttu-id="34b71-572">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="34b71-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="34b71-573">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="34b71-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="34b71-574">Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="34b71-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="34b71-575">Örnek: 00000000-0000-0000-0000-0000000000000</span><span class="sxs-lookup"><span data-stu-id="34b71-575">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="34b71-576">Değerlerden hangisinin `OperationId` istek içinde ve istekler arasında farklılık gösterdiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="34b71-576">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="34b71-577">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-577">*Transient* objects are always different.</span></span> <span data-ttu-id="34b71-578">Hem birinci `OperationId` hem de ikinci istemci istekleri için `OperationService` geçici değer hem işlemler hem de istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-578">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="34b71-579">Her hizmet isteği ve istemci isteği için yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="34b71-579">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="34b71-580">*Kapsamlı* nesneler istemci isteği içinde aynıdır, ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-580">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="34b71-581">*Singleton* nesneleri, bir `Operation` örneğin sağlanıp sağlanmadığına bakılmaksızın her `Startup.ConfigureServices`nesne ve her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-581">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="34b71-582">Ana hizmetten çağrı hizmetleri</span><span class="sxs-lookup"><span data-stu-id="34b71-582">Call services from main</span></span>

<span data-ttu-id="34b71-583">Uygulama <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> kapsamındaki kapsamlı bir hizmeti çözmek için [iServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="34b71-583">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="34b71-584">Bu yaklaşım, başlatma görevlerini çalıştırmak için başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-584">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="34b71-585">Aşağıdaki örnek, aşağıdaki `MyScopedService` `Program.Main`için bir bağlam nasıl elde edilebildiğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="34b71-585">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="34b71-586">Kapsam doğrulama</span><span class="sxs-lookup"><span data-stu-id="34b71-586">Scope validation</span></span>

<span data-ttu-id="34b71-587">Uygulama Geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı aşağıdakileri doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="34b71-587">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="34b71-588">Kapsamlı hizmetler, kök hizmet sağlayıcısından doğrudan veya dolaylı olarak çözülmez.</span><span class="sxs-lookup"><span data-stu-id="34b71-588">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="34b71-589">Kapsamlı hizmetler doğrudan veya dolaylı olarak singleton içine enjekte değildir.</span><span class="sxs-lookup"><span data-stu-id="34b71-589">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="34b71-590">Kök hizmet sağlayıcısı çağrıldığında <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="34b71-590">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="34b71-591">Kök servis sağlayıcısının ömrü, sağlayıcı nın uygulamayla birlikte işe başlaması ve uygulama kapandığında imha edilmesiyle uygulamanın/sunucunun ömrüne karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="34b71-591">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="34b71-592">Kapsamlı hizmetler, bunları oluşturan kapsayıcı tarafından bertaraf edilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-592">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="34b71-593">Kök kapsayıcıda kapsamlı bir hizmet oluşturulursa, yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından bertaraf edildiğinden, hizmetin ömrü etkin bir şekilde singleton'a yükseltilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-593">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="34b71-594">Hizmet kapsamlarını doğrulama çağrıldığında `BuildServiceProvider` bu durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="34b71-594">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="34b71-595">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="34b71-595">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="34b71-596">Talep Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="34b71-596">Request Services</span></span>

<span data-ttu-id="34b71-597">ASP.NET Core isteği içinde sunulan `HttpContext` [hizmetler, HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla açıklanır.</span><span class="sxs-lookup"><span data-stu-id="34b71-597">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="34b71-598">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil ediyor.</span><span class="sxs-lookup"><span data-stu-id="34b71-598">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="34b71-599">Nesneler bağımlılıkları belirttiğinde, bunlar , `RequestServices` `ApplicationServices`değil' de bulunan türlerle karşılanır.</span><span class="sxs-lookup"><span data-stu-id="34b71-599">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="34b71-600">Genellikle, uygulama bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-600">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="34b71-601">Bunun yerine, sınıf oluşturucular aracılığıyla sınıfların gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları enjekte etmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="34b71-601">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="34b71-602">Bu, sınaması daha kolay olan sınıflar verir.</span><span class="sxs-lookup"><span data-stu-id="34b71-602">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="34b71-603">Koleksiyona erişmek için kurucu parametreleri olarak `RequestServices` bağımlılıkları talep etmeyi tercih edin.</span><span class="sxs-lookup"><span data-stu-id="34b71-603">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="34b71-604">Bağımlılık enjeksiyonu için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="34b71-604">Design services for dependency injection</span></span>

<span data-ttu-id="34b71-605">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="34b71-605">Best practices are to:</span></span>

* <span data-ttu-id="34b71-606">Bağımlılıklarını elde etmek için bağımlılık enjeksiyonu kullanmak için tasarım hizmetleri.</span><span class="sxs-lookup"><span data-stu-id="34b71-606">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="34b71-607">Durum lu, statik sınıflardan ve üyelerden kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-607">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="34b71-608">Bunun yerine, küresel durum oluşturmaktan kaçınan singleton hizmetlerini kullanmak için uygulamalar tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="34b71-608">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="34b71-609">Hizmetler içindeki bağımlı sınıfların doğrudan anında anlanmasından kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-609">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="34b71-610">Kodu belirli bir uygulamaya yönlendirin anlık çiftler.</span><span class="sxs-lookup"><span data-stu-id="34b71-610">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="34b71-611">Uygulama sınıflarını küçük, iyi faktörlü ve kolayca test edilebilen hale getirin.</span><span class="sxs-lookup"><span data-stu-id="34b71-611">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="34b71-612">Bir sınıfın çok fazla enjekte edilmiş bağımlılığı varsa, bu genellikle sınıfın çok fazla sorumluluğu olduğunun ve [Tek Sorumluluk İlkesi'ni (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal ettiğini gösteren bir işarettir.</span><span class="sxs-lookup"><span data-stu-id="34b71-612">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="34b71-613">Bazı sorumluluklarını yeni bir sınıfa taşıyarak sınıfı yeniden düzenlemeyi dene.</span><span class="sxs-lookup"><span data-stu-id="34b71-613">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="34b71-614">Razor Pages sayfa modeli sınıflarının ve MVC denetleyici sınıflarının UI endişelerine odaklanması gerektiğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="34b71-614">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="34b71-615">İş kuralları ve veri erişimi uygulama ayrıntıları bu [ayrı endişelere](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-615">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="34b71-616">Hizmetlerin bertaraf edilmesi</span><span class="sxs-lookup"><span data-stu-id="34b71-616">Disposal of services</span></span>

<span data-ttu-id="34b71-617">Kapsayıcı, <xref:System.IDisposable.Dispose*> oluşturduğu <xref:System.IDisposable> türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="34b71-617">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="34b71-618">Kullanıcı koduyla kapsayıcıya bir örnek eklenirse, otomatik olarak atılmaz.</span><span class="sxs-lookup"><span data-stu-id="34b71-618">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="34b71-619">Aşağıdaki örnekte, hizmetler servis kapsayıcısı tarafından oluşturulur ve otomatik olarak atılır:</span><span class="sxs-lookup"><span data-stu-id="34b71-619">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="34b71-620">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="34b71-620">In the following example:</span></span>

* <span data-ttu-id="34b71-621">Hizmet örnekleri servis kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="34b71-621">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="34b71-622">Amaçlanan hizmet ömürleri çerçeve tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="34b71-622">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="34b71-623">Çerçeve hizmetleri otomatik olarak elden çıkarmaz.</span><span class="sxs-lookup"><span data-stu-id="34b71-623">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="34b71-624">Hizmetler geliştirici kodunda açıkça belirtinmezse, uygulama kapanana kadar devam eder.</span><span class="sxs-lookup"><span data-stu-id="34b71-624">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="34b71-625">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="34b71-625">Default service container replacement</span></span>

<span data-ttu-id="34b71-626">Dahili servis konteyneri, çerçevenin ve çoğu tüketici uygulamasının ihtiyaçlarına hizmet etmek üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="34b71-626">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="34b71-627">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız olmadığı sürece yerleşik kapsayıcıyı kullanmanızı öneririz:</span><span class="sxs-lookup"><span data-stu-id="34b71-627">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="34b71-628">Özellik enjeksiyonu</span><span class="sxs-lookup"><span data-stu-id="34b71-628">Property injection</span></span>
* <span data-ttu-id="34b71-629">İsme göre enjeksiyon</span><span class="sxs-lookup"><span data-stu-id="34b71-629">Injection based on name</span></span>
* <span data-ttu-id="34b71-630">Çocuk kapları</span><span class="sxs-lookup"><span data-stu-id="34b71-630">Child containers</span></span>
* <span data-ttu-id="34b71-631">Özel yaşam boyu yönetimi</span><span class="sxs-lookup"><span data-stu-id="34b71-631">Custom lifetime management</span></span>
* <span data-ttu-id="34b71-632">`Func<T>`tembel başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="34b71-632">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="34b71-633">Sözleşme tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="34b71-633">Convention-based registration</span></span>

<span data-ttu-id="34b71-634">Aşağıdaki 3 ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="34b71-634">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="34b71-635">Autofac</span><span class="sxs-lookup"><span data-stu-id="34b71-635">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="34b71-636">Dryioc</span><span class="sxs-lookup"><span data-stu-id="34b71-636">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="34b71-637">Zarafet</span><span class="sxs-lookup"><span data-stu-id="34b71-637">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="34b71-638">LightInject</span><span class="sxs-lookup"><span data-stu-id="34b71-638">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="34b71-639">Lamar</span><span class="sxs-lookup"><span data-stu-id="34b71-639">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="34b71-640">Zula kutusu</span><span class="sxs-lookup"><span data-stu-id="34b71-640">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="34b71-641">Unity</span><span class="sxs-lookup"><span data-stu-id="34b71-641">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="34b71-642">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="34b71-642">Thread safety</span></span>

<span data-ttu-id="34b71-643">İş parçacığı güvenli singleton hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="34b71-643">Create thread-safe singleton services.</span></span> <span data-ttu-id="34b71-644">Bir singleton hizmetigeçici bir hizmete bağımlıysa, geçici hizmet, singleton tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="34b71-644">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="34b71-645">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)ikinci bağımsız değişkeni gibi tek hizmet fabrika yöntemi, iş parçacığı güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="34b71-645">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="34b71-646">Bir tür`static`gibi ( ) oluşturucu, tek bir iş parçacığı tarafından bir kez çağrılması garanti.</span><span class="sxs-lookup"><span data-stu-id="34b71-646">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="34b71-647">Öneriler</span><span class="sxs-lookup"><span data-stu-id="34b71-647">Recommendations</span></span>

* <span data-ttu-id="34b71-648">`async/await`ve `Task` tabanlı hizmet çözünürlüğü desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="34b71-648">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="34b71-649">C# asynchronous yapıcıları desteklemez; bu nedenle, önerilen desen eşitolarak hizmeti çözdükten sonra eşzamanlı yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="34b71-649">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="34b71-650">Verileri ve yapılandırmayı doğrudan servis kapsayıcısında depolamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-650">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="34b71-651">Örneğin, bir kullanıcının alışveriş sepeti genellikle servis kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="34b71-651">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="34b71-652">Yapılandırma seçenekleri [deseni](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="34b71-652">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="34b71-653">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için var olan "veri tutucu" nesnelerden kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-653">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="34b71-654">Bu DI üzerinden gerçek öğeyi istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="34b71-654">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="34b71-655">Hizmetlere statik erişimden kaçının (örneğin, başka bir yerde kullanmak için statik olarak [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) yazarak).</span><span class="sxs-lookup"><span data-stu-id="34b71-655">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="34b71-656">Denetim stratejilerinin [Ters Çevrilmesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) karıştıran *hizmet bulucu deseni*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-656">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="34b71-657">Bunun yerine <xref:System.IServiceProvider.GetService*> DI'yi kullanabileceğiniz bir hizmet örneği almak için çağrıda almayın:</span><span class="sxs-lookup"><span data-stu-id="34b71-657">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="34b71-658">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="34b71-658">**Incorrect:**</span></span>

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

    <span data-ttu-id="34b71-659">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="34b71-659">**Correct**:</span></span>

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

  * <span data-ttu-id="34b71-660">Kullanarak çalışma zamanında bağımlılıkları çözen bir <xref:System.IServiceProvider.GetService*>fabrika enjekte kaçının.</span><span class="sxs-lookup"><span data-stu-id="34b71-660">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="34b71-661">Statik erişimden `HttpContext` kaçının (örneğin, [IHttpContextAccessor.HttpContext).](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)</span><span class="sxs-lookup"><span data-stu-id="34b71-661">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="34b71-662">Tüm öneri kümeleri gibi, bir öneriyi yoksaymanın gerekli olduğu durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="34b71-662">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="34b71-663">İstisnalar, çerçevenin kendisi içinde nadir görülen&mdash;özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="34b71-663">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="34b71-664">DI statik /küresel nesne erişim desenleri için bir *alternatiftir.*</span><span class="sxs-lookup"><span data-stu-id="34b71-664">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="34b71-665">Statik nesne erişimiyle karıştırırsanız DI'nin faydalarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="34b71-665">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34b71-666">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="34b71-666">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="34b71-667">Bağımlılık Enjeksiyonu (MSDN) ile ASP.NET Çekirdekte Temiz Kod Yazma</span><span class="sxs-lookup"><span data-stu-id="34b71-667">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="34b71-668">Açık Bağımlılıklar İlkesi</span><span class="sxs-lookup"><span data-stu-id="34b71-668">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="34b71-669">Kontrol Kapları ve Bağımlılık Enjeksiyon Deseni (Martin Fowler) Ters</span><span class="sxs-lookup"><span data-stu-id="34b71-669">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="34b71-670">Core DI'de birden çok arabirimi olan bir hizmet ASP.NET</span><span class="sxs-lookup"><span data-stu-id="34b71-670">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
