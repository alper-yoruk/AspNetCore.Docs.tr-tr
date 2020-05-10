---
title: ASP.NET Core'da bağımlılık ekleme
author: rick-anderson
description: ASP.NET Core bağımlılık ekleme ve nasıl kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 3e31be02f21f8c28c1d98d47d9a744b3a8502253
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003176"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="b93e8-103">ASP.NET Core'da bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="b93e8-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="b93e8-104">, [Steve Smith](https://ardalis.com/) ve [Scott Ade](https://scottaddie.com) tarafından</span><span class="sxs-lookup"><span data-stu-id="b93e8-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b93e8-105">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="b93e8-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="b93e8-106">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection>..</span><span class="sxs-lookup"><span data-stu-id="b93e8-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="b93e8-107">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b93e8-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="b93e8-108">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="b93e8-108">Overview of dependency injection</span></span>

<span data-ttu-id="b93e8-109">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="b93e8-110">Aşağıdaki `MyDependency` sınıfı, bir uygulamadaki diğer `WriteMessage` sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="b93e8-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="b93e8-111">Yöntemi bir sınıf için `MyDependency` kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir. `WriteMessage`</span><span class="sxs-lookup"><span data-stu-id="b93e8-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="b93e8-112">`MyDependency` Sınıfı, `IndexModel` sınıfının bir bağımlılığı olur:</span><span class="sxs-lookup"><span data-stu-id="b93e8-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="b93e8-113">Sınıf oluşturur ve `MyDependency` örneğe doğrudan bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="b93e8-114">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="b93e8-115">Farklı bir `MyDependency` uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="b93e8-116">`MyDependency` Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="b93e8-117">Uygulamasına bağlı olarak `MyDependency`, birden çok sınıfı olan büyük bir projede yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="b93e8-118">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="b93e8-119">Uygulamanın bu yaklaşım ile mümkün olmayan bir sahte `MyDependency` veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="b93e8-120">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="b93e8-121">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="b93e8-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="b93e8-122">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="b93e8-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="b93e8-123">ASP.NET Core, <xref:System.IServiceProvider>yerleşik bir hizmet kapsayıcısı sağlar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="b93e8-124">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="b93e8-125">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="b93e8-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="b93e8-126">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="b93e8-127">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="b93e8-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="b93e8-128">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="b93e8-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="b93e8-129">`MyDependency`kendi oluşturucusunda <xref:Microsoft.Extensions.Logging.ILogger`1> bir ister.</span><span class="sxs-lookup"><span data-stu-id="b93e8-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="b93e8-130">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="b93e8-131">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="b93e8-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="b93e8-132">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="b93e8-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="b93e8-133">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="b93e8-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="b93e8-134">`IMyDependency`ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="b93e8-135">`IMyDependency`kaydedilir `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b93e8-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b93e8-136">`ILogger<TCategoryName>`günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="b93e8-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="b93e8-137">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="b93e8-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="b93e8-138">Örnek uygulamada, `IMyDependency` hizmet somut tür `MyDependency`ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="b93e8-139">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="b93e8-140">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="b93e8-141">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="b93e8-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="b93e8-142">Örneğin, `services.AddMvc()` Razor Pages ve MVC 'nin gerektirdiği Hizmetleri ekler.</span><span class="sxs-lookup"><span data-stu-id="b93e8-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="b93e8-143">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="b93e8-144">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="b93e8-145">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table) `string`gerektiriyorsa, tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="b93e8-146">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="b93e8-147">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="b93e8-148">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin `WriteMessage` yöntemini çağırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="b93e8-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="b93e8-149">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="b93e8-149">Services injected into Startup</span></span>

<span data-ttu-id="b93e8-150">Genel ana bilgisayar ( `Startup` <xref:Microsoft.Extensions.Hosting.IHostBuilder>) kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="b93e8-151">Hizmetler şu şekilde eklenebilir `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b93e8-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="b93e8-152">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b93e8-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b93e8-153">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="b93e8-153">Framework-provided services</span></span>

<span data-ttu-id="b93e8-154">`Startup.ConfigureServices` Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="b93e8-155">Başlangıçta, için `IServiceCollection` `ConfigureServices` belirtilen, [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="b93e8-156">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="b93e8-157">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="b93e8-158">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="b93e8-158">Service Type</span></span> | <span data-ttu-id="b93e8-159">Ömür</span><span class="sxs-lookup"><span data-stu-id="b93e8-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="b93e8-160">Larsa</span><span class="sxs-lookup"><span data-stu-id="b93e8-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="b93e8-161">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="b93e8-162">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="b93e8-163">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="b93e8-164">Larsa</span><span class="sxs-lookup"><span data-stu-id="b93e8-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="b93e8-165">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="b93e8-166">Larsa</span><span class="sxs-lookup"><span data-stu-id="b93e8-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="b93e8-167">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="b93e8-168">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="b93e8-169">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="b93e8-170">Larsa</span><span class="sxs-lookup"><span data-stu-id="b93e8-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="b93e8-171">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="b93e8-172">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="b93e8-173">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="b93e8-174">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="b93e8-174">Register additional services with extension methods</span></span>

<span data-ttu-id="b93e8-175">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek `Add{SERVICE_NAME}` bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="b93e8-176">Aşağıdaki kod, [Adddbcontext\<tcontext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="b93e8-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="b93e8-177">Daha fazla bilgi için API belgelerindeki <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="b93e8-178">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-178">Service lifetimes</span></span>

<span data-ttu-id="b93e8-179">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="b93e8-180">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="b93e8-181">Larsa</span><span class="sxs-lookup"><span data-stu-id="b93e8-181">Transient</span></span>

<span data-ttu-id="b93e8-182">Geçici ömür Hizmetleri (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="b93e8-183">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="b93e8-184">Yayıl</span><span class="sxs-lookup"><span data-stu-id="b93e8-184">Scoped</span></span>

<span data-ttu-id="b93e8-185">Kapsamlı ömür Hizmetleri (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="b93e8-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="b93e8-186">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="b93e8-187">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="b93e8-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="b93e8-188">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="b93e8-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="b93e8-189">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-189">Singleton</span></span>

<span data-ttu-id="b93e8-190">Tek yaşam süresi Hizmetleri<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>() her istendiğinde oluşturulur (veya `Startup.ConfigureServices` çalıştırıldığında ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="b93e8-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="b93e8-191">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="b93e8-192">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="b93e8-193">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="b93e8-194">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="b93e8-195">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="b93e8-196">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-196">Service registration methods</span></span>

<span data-ttu-id="b93e8-197">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="b93e8-198">Yöntem</span><span class="sxs-lookup"><span data-stu-id="b93e8-198">Method</span></span> | <span data-ttu-id="b93e8-199">Automatic</span><span class="sxs-lookup"><span data-stu-id="b93e8-199">Automatic</span></span><br><span data-ttu-id="b93e8-200">nesne</span><span class="sxs-lookup"><span data-stu-id="b93e8-200">object</span></span><br><span data-ttu-id="b93e8-201">elden</span><span class="sxs-lookup"><span data-stu-id="b93e8-201">disposal</span></span> | <span data-ttu-id="b93e8-202">Birden çok</span><span class="sxs-lookup"><span data-stu-id="b93e8-202">Multiple</span></span><br><span data-ttu-id="b93e8-203">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="b93e8-203">implementations</span></span> | <span data-ttu-id="b93e8-204">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="b93e8-205">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b93e8-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="b93e8-206">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-206">Yes</span></span> | <span data-ttu-id="b93e8-207">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-207">Yes</span></span> | <span data-ttu-id="b93e8-208">No</span><span class="sxs-lookup"><span data-stu-id="b93e8-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="b93e8-209">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="b93e8-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="b93e8-210">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-210">Yes</span></span> | <span data-ttu-id="b93e8-211">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-211">Yes</span></span> | <span data-ttu-id="b93e8-212">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="b93e8-213">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b93e8-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="b93e8-214">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-214">Yes</span></span> | <span data-ttu-id="b93e8-215">Hayır</span><span class="sxs-lookup"><span data-stu-id="b93e8-215">No</span></span> | <span data-ttu-id="b93e8-216">Hayır</span><span class="sxs-lookup"><span data-stu-id="b93e8-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="b93e8-217">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="b93e8-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="b93e8-218">No</span><span class="sxs-lookup"><span data-stu-id="b93e8-218">No</span></span> | <span data-ttu-id="b93e8-219">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-219">Yes</span></span> | <span data-ttu-id="b93e8-220">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="b93e8-221">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="b93e8-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="b93e8-222">Hayır</span><span class="sxs-lookup"><span data-stu-id="b93e8-222">No</span></span> | <span data-ttu-id="b93e8-223">Hayır</span><span class="sxs-lookup"><span data-stu-id="b93e8-223">No</span></span> | <span data-ttu-id="b93e8-224">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-224">Yes</span></span> |

<span data-ttu-id="b93e8-225">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="b93e8-226">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="b93e8-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="b93e8-227">`TryAdd{LIFETIME}`Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="b93e8-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="b93e8-228">Aşağıdaki örnekte, ilk satır için `MyDependency` `IMyDependency`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="b93e8-229">Zaten kayıtlı bir uygulamaya sahip olduğundan `IMyDependency` ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="b93e8-230">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="b93e8-231">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="b93e8-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="b93e8-232">Aracılığıyla `IEnumerable<{SERVICE}>`birden çok hizmet çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="b93e8-233">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="b93e8-234">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="b93e8-235">Aşağıdaki örnekte, ilk satır için `MyDep` `IMyDep1`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="b93e8-236">İkinci satır için `IMyDep2`kaydedilir `MyDep` .</span><span class="sxs-lookup"><span data-stu-id="b93e8-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="b93e8-237">Zaten kayıtlı bir uygulamasına sahip olduğundan `IMyDep1` , üçüncü satırın etkisi yoktur: `MyDep`</span><span class="sxs-lookup"><span data-stu-id="b93e8-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="b93e8-238">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="b93e8-238">Constructor injection behavior</span></span>

<span data-ttu-id="b93e8-239">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="b93e8-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="b93e8-241">`ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="b93e8-242">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="b93e8-243">`IServiceProvider` Hizmetler veya `ActivatorUtilities`tarafından çözümlendiğinde, [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="b93e8-244">Hizmetler tarafından `ActivatorUtilities`çözümlendiğinde, [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="b93e8-245">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="b93e8-246">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="b93e8-246">Entity Framework contexts</span></span>

<span data-ttu-id="b93e8-247">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="b93e8-248">Veritabanı bağlamı kaydedilirken aşırı yükleme [>bir\<adddbcontext tcontext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) tarafından belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="b93e8-249">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="b93e8-250">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-250">Lifetime and registration options</span></span>

<span data-ttu-id="b93e8-251">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="b93e8-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="b93e8-252">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="b93e8-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="b93e8-253">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="b93e8-254">Bir `Operation` tane sağlanmazsa Oluşturucu bir GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b93e8-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="b93e8-255">`OperationService` , Diğer `Operation` türlerin her birine bağlı olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="b93e8-256">`OperationService` Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="b93e8-257">Kapsayıcıda istendiğinde geçici `OperationId` hizmetler oluşturulduğunda, `IOperationTransient` hizmet öğesinden `OperationId` farklı olur. `OperationService`</span><span class="sxs-lookup"><span data-stu-id="b93e8-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="b93e8-258">`OperationService``IOperationTransient` sınıfının yeni bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="b93e8-259">Yeni örnek farklı `OperationId`bir şekilde oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="b93e8-260">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği `OperationService` içindeki ile aynı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="b93e8-261">İstemci istekleri arasında her iki hizmet de farklı `OperationId` bir değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="b93e8-262">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde `OperationId` kullanıldığında, tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="b93e8-263">' `Startup.ConfigureServices`De, her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="b93e8-264">`IOperationSingletonInstance` Hizmet, bilinen kimliği olan belirli bir örnek kullanıyor `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="b93e8-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="b93e8-265">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="b93e8-266">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="b93e8-267">Örnek uygulama, her `IndexModel` `IOperation` tür ve ' i ister `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="b93e8-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="b93e8-268">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="b93e8-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="b93e8-269">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="b93e8-270">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="b93e8-270">**First request:**</span></span>

<span data-ttu-id="b93e8-271">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="b93e8-271">Controller operations:</span></span>

<span data-ttu-id="b93e8-272">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="b93e8-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="b93e8-273">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b93e8-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b93e8-274">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b93e8-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b93e8-275">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b93e8-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b93e8-276">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="b93e8-276">`OperationService` operations:</span></span>

<span data-ttu-id="b93e8-277">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="b93e8-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="b93e8-278">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b93e8-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b93e8-279">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b93e8-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b93e8-280">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b93e8-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b93e8-281">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="b93e8-281">**Second request:**</span></span>

<span data-ttu-id="b93e8-282">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="b93e8-282">Controller operations:</span></span>

<span data-ttu-id="b93e8-283">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="b93e8-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="b93e8-284">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b93e8-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b93e8-285">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b93e8-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b93e8-286">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b93e8-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b93e8-287">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="b93e8-287">`OperationService` operations:</span></span>

<span data-ttu-id="b93e8-288">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="b93e8-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="b93e8-289">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b93e8-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b93e8-290">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b93e8-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b93e8-291">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b93e8-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b93e8-292">`OperationId` Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="b93e8-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="b93e8-293">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-293">*Transient* objects are always different.</span></span> <span data-ttu-id="b93e8-294">Hem birinci `OperationId` hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="b93e8-295">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="b93e8-296">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="b93e8-297">*Tek* nesneler, ' de `Operation` `Startup.ConfigureServices`bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="b93e8-298">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="b93e8-298">Call services from main</span></span>

<span data-ttu-id="b93e8-299">Uygulamanın kapsamındaki <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b93e8-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="b93e8-300">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="b93e8-301">Aşağıdaki örnek, `MyScopedService` içinde `Program.Main`için nasıl bağlam alınacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="b93e8-302">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b93e8-302">Scope validation</span></span>

<span data-ttu-id="b93e8-303">Uygulama geliştirme ortamında çalışırken ve Konağı derlemek için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) çağırdığında, varsayılan hizmet sağlayıcı aşağıdakileri doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="b93e8-304">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="b93e8-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="b93e8-305">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="b93e8-306">Kök hizmet sağlayıcısı çağrıldığında oluşturulur <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> .</span><span class="sxs-lookup"><span data-stu-id="b93e8-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="b93e8-307">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="b93e8-308">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="b93e8-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="b93e8-309">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="b93e8-310">Hizmet kapsamlarını doğrulamak `BuildServiceProvider` , çağrıldığında bu durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="b93e8-311">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="b93e8-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="b93e8-312">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-312">Request Services</span></span>

<span data-ttu-id="b93e8-313">ASP.NET Core isteği `HttpContext` içinde bulunan hizmetler, [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="b93e8-314">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="b93e8-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="b93e8-315">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de `RequestServices`bulunan türler tarafından karşılanır. `ApplicationServices`</span><span class="sxs-lookup"><span data-stu-id="b93e8-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="b93e8-316">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="b93e8-317">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="b93e8-318">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="b93e8-319">`RequestServices` Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="b93e8-320">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-320">Design services for dependency injection</span></span>

<span data-ttu-id="b93e8-321">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b93e8-321">Best practices are to:</span></span>

* <span data-ttu-id="b93e8-322">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="b93e8-323">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="b93e8-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="b93e8-324">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="b93e8-325">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="b93e8-326">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="b93e8-327">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="b93e8-328">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="b93e8-329">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="b93e8-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="b93e8-330">Razor Pages sayfa modeli sınıfları ve MVC denetleyici sınıflarının UI kaygılarıyla odaklanıp ilgilenmeyeceğini aklınızda bulundurun.</span><span class="sxs-lookup"><span data-stu-id="b93e8-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="b93e8-331">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="b93e8-332">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="b93e8-332">Disposal of services</span></span>

<span data-ttu-id="b93e8-333">Kapsayıcı, oluşturduğu <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="b93e8-334">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="b93e8-335">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="b93e8-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="b93e8-336">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="b93e8-336">In the following example:</span></span>

* <span data-ttu-id="b93e8-337">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="b93e8-338">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="b93e8-339">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="b93e8-340">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="b93e8-341">Hizmet verme seçeneklerinin bir tartışması için bkz. [ASP.NET Core ' de, Ndisposaveya ' ı atılamayan dört yol](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="b93e8-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="b93e8-342">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="b93e8-342">Default service container replacement</span></span>

<span data-ttu-id="b93e8-343">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="b93e8-344">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="b93e8-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="b93e8-345">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="b93e8-345">Property injection</span></span>
* <span data-ttu-id="b93e8-346">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="b93e8-346">Injection based on name</span></span>
* <span data-ttu-id="b93e8-347">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="b93e8-347">Child containers</span></span>
* <span data-ttu-id="b93e8-348">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="b93e8-348">Custom lifetime management</span></span>
* <span data-ttu-id="b93e8-349">`Func<T>`yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="b93e8-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="b93e8-350">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="b93e8-350">Convention-based registration</span></span>

<span data-ttu-id="b93e8-351">Aşağıdaki 3. taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="b93e8-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="b93e8-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="b93e8-353">Drıioc</span><span class="sxs-lookup"><span data-stu-id="b93e8-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="b93e8-354">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="b93e8-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="b93e8-355">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="b93e8-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="b93e8-356">E</span><span class="sxs-lookup"><span data-stu-id="b93e8-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="b93e8-357">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="b93e8-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="b93e8-358">Unity</span><span class="sxs-lookup"><span data-stu-id="b93e8-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="b93e8-359">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="b93e8-359">Thread safety</span></span>

<span data-ttu-id="b93e8-360">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b93e8-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="b93e8-361">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="b93e8-362">Tek bir hizmetin fabrika yöntemi (örneğin, [AddSingleton\<TService> için ikinci bağımsız değişken) (ıseviecollection,\<Func ıserviceprovider, TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="b93e8-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="b93e8-363">Bir Type (`static`) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="b93e8-364">Öneriler</span><span class="sxs-lookup"><span data-stu-id="b93e8-364">Recommendations</span></span>

* <span data-ttu-id="b93e8-365">`async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b93e8-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="b93e8-366">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="b93e8-367">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="b93e8-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="b93e8-368">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="b93e8-369">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="b93e8-370">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="b93e8-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="b93e8-371">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="b93e8-372">Hizmetlere statik erişimi önleyin (örneğin, başka bir yerde kullanmak üzere, statik olarak yazılan [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ).</span><span class="sxs-lookup"><span data-stu-id="b93e8-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="b93e8-373">*Hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="b93e8-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="b93e8-374">Örneğin, yerine şunu kullandığınızda <xref:System.IServiceProvider.GetService*> bir hizmet örneği elde etme çağrısı yapmayın:</span><span class="sxs-lookup"><span data-stu-id="b93e8-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="b93e8-375">**Olmayan**</span><span class="sxs-lookup"><span data-stu-id="b93e8-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="b93e8-376">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="b93e8-376">**Correct**:</span></span>

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

* <span data-ttu-id="b93e8-377">Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme.</span><span class="sxs-lookup"><span data-stu-id="b93e8-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="b93e8-378">Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="b93e8-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="b93e8-379">Uygulamasına `HttpContext` statik erişimi önleyin (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="b93e8-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="b93e8-380">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="b93e8-381">Özel durumlar&mdash;genellikle çerçevenin kendisi içinde özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="b93e8-382">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="b93e8-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="b93e8-383">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="b93e8-384">DI 'de çok kiracılı için önerilen desenler</span><span class="sxs-lookup"><span data-stu-id="b93e8-384">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="b93e8-385">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , çok kiracılı bir uygulama sağlar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-385">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="b93e8-386">Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="b93e8-386">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="b93e8-387">CMS 'e özgü özelliklerden herhangi https://github.com/OrchardCMS/OrchardCore.Samples biri olmadan yalnızca Orchard Core çerçevesini kullanarak modüler ve çok kiracılı uygulamalar oluşturmaya yönelik örnekler için adresindeki örnek uygulamalar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-387">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b93e8-388">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b93e8-388">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="b93e8-389">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="b93e8-389">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="b93e8-390">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="b93e8-390">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="b93e8-391">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="b93e8-391">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="b93e8-392">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="b93e8-392">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b93e8-393">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="b93e8-393">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="b93e8-394">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection>..</span><span class="sxs-lookup"><span data-stu-id="b93e8-394">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="b93e8-395">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b93e8-395">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="b93e8-396">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="b93e8-396">Overview of dependency injection</span></span>

<span data-ttu-id="b93e8-397">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-397">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="b93e8-398">Aşağıdaki `MyDependency` sınıfı, bir uygulamadaki diğer `WriteMessage` sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="b93e8-398">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="b93e8-399">Yöntemi bir sınıf için `MyDependency` kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir. `WriteMessage`</span><span class="sxs-lookup"><span data-stu-id="b93e8-399">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="b93e8-400">`MyDependency` Sınıfı, `IndexModel` sınıfının bir bağımlılığı olur:</span><span class="sxs-lookup"><span data-stu-id="b93e8-400">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="b93e8-401">Sınıf oluşturur ve `MyDependency` örneğe doğrudan bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-401">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="b93e8-402">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-402">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="b93e8-403">Farklı bir `MyDependency` uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-403">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="b93e8-404">`MyDependency` Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-404">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="b93e8-405">Uygulamasına bağlı olarak `MyDependency`, birden çok sınıfı olan büyük bir projede yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-405">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="b93e8-406">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-406">This implementation is difficult to unit test.</span></span> <span data-ttu-id="b93e8-407">Uygulamanın bu yaklaşım ile mümkün olmayan bir sahte `MyDependency` veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-407">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="b93e8-408">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-408">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="b93e8-409">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="b93e8-409">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="b93e8-410">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="b93e8-410">Registration of the dependency in a service container.</span></span> <span data-ttu-id="b93e8-411">ASP.NET Core, <xref:System.IServiceProvider>yerleşik bir hizmet kapsayıcısı sağlar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-411">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="b93e8-412">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-412">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="b93e8-413">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="b93e8-413">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="b93e8-414">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-414">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="b93e8-415">[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="b93e8-415">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="b93e8-416">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="b93e8-416">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="b93e8-417">`MyDependency`kendi oluşturucusunda <xref:Microsoft.Extensions.Logging.ILogger`1> bir ister.</span><span class="sxs-lookup"><span data-stu-id="b93e8-417">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="b93e8-418">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-418">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="b93e8-419">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="b93e8-419">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="b93e8-420">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="b93e8-420">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="b93e8-421">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="b93e8-421">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="b93e8-422">`IMyDependency`ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-422">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="b93e8-423">`IMyDependency`kaydedilir `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b93e8-423">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b93e8-424">`ILogger<TCategoryName>`günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="b93e8-424">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="b93e8-425">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="b93e8-425">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="b93e8-426">Örnek uygulamada, `IMyDependency` hizmet somut tür `MyDependency`ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-426">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="b93e8-427">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-427">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="b93e8-428">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-428">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="b93e8-429">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="b93e8-429">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="b93e8-430">Örneğin, `services.AddMvc()` Razor Pages ve MVC 'nin gerektirdiği Hizmetleri ekler.</span><span class="sxs-lookup"><span data-stu-id="b93e8-430">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="b93e8-431">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-431">We recommended that apps follow this convention.</span></span> <span data-ttu-id="b93e8-432">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-432">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="b93e8-433">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table) `string`gerektiriyorsa, tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-433">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="b93e8-434">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-434">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="b93e8-435">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-435">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="b93e8-436">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin `WriteMessage` yöntemini çağırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="b93e8-436">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="b93e8-437">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="b93e8-437">Services injected into Startup</span></span>

<span data-ttu-id="b93e8-438">Genel ana bilgisayar ( `Startup` <xref:Microsoft.Extensions.Hosting.IHostBuilder>) kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-438">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="b93e8-439">Hizmetler şu şekilde eklenebilir `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b93e8-439">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="b93e8-440">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b93e8-440">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b93e8-441">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="b93e8-441">Framework-provided services</span></span>

<span data-ttu-id="b93e8-442">`Startup.ConfigureServices` Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-442">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="b93e8-443">Başlangıçta, için `IServiceCollection` `ConfigureServices` belirtilen, [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-443">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="b93e8-444">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-444">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="b93e8-445">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-445">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="b93e8-446">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="b93e8-446">Service Type</span></span> | <span data-ttu-id="b93e8-447">Ömür</span><span class="sxs-lookup"><span data-stu-id="b93e8-447">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="b93e8-448">Larsa</span><span class="sxs-lookup"><span data-stu-id="b93e8-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="b93e8-449">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="b93e8-450">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-450">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="b93e8-451">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-451">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="b93e8-452">Larsa</span><span class="sxs-lookup"><span data-stu-id="b93e8-452">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="b93e8-453">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-453">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="b93e8-454">Larsa</span><span class="sxs-lookup"><span data-stu-id="b93e8-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="b93e8-455">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-455">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="b93e8-456">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-456">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="b93e8-457">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-457">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="b93e8-458">Larsa</span><span class="sxs-lookup"><span data-stu-id="b93e8-458">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="b93e8-459">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-459">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="b93e8-460">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-460">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="b93e8-461">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-461">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="b93e8-462">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="b93e8-462">Register additional services with extension methods</span></span>

<span data-ttu-id="b93e8-463">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek `Add{SERVICE_NAME}` bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-463">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="b93e8-464">Aşağıdaki kod, [Adddbcontext\<tcontext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="b93e8-464">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="b93e8-465">Daha fazla bilgi için API belgelerindeki <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-465">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="b93e8-466">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-466">Service lifetimes</span></span>

<span data-ttu-id="b93e8-467">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-467">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="b93e8-468">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-468">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="b93e8-469">Larsa</span><span class="sxs-lookup"><span data-stu-id="b93e8-469">Transient</span></span>

<span data-ttu-id="b93e8-470">Geçici ömür Hizmetleri (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-470">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="b93e8-471">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-471">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="b93e8-472">Yayıl</span><span class="sxs-lookup"><span data-stu-id="b93e8-472">Scoped</span></span>

<span data-ttu-id="b93e8-473">Kapsamlı ömür Hizmetleri (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="b93e8-473">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="b93e8-474">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-474">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="b93e8-475">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="b93e8-475">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="b93e8-476">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="b93e8-476">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="b93e8-477">Adet</span><span class="sxs-lookup"><span data-stu-id="b93e8-477">Singleton</span></span>

<span data-ttu-id="b93e8-478">Tek yaşam süresi Hizmetleri<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>() her istendiğinde oluşturulur (veya `Startup.ConfigureServices` çalıştırıldığında ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="b93e8-478">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="b93e8-479">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-479">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="b93e8-480">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-480">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="b93e8-481">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-481">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="b93e8-482">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-482">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="b93e8-483">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-483">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="b93e8-484">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-484">Service registration methods</span></span>

<span data-ttu-id="b93e8-485">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-485">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="b93e8-486">Yöntem</span><span class="sxs-lookup"><span data-stu-id="b93e8-486">Method</span></span> | <span data-ttu-id="b93e8-487">Automatic</span><span class="sxs-lookup"><span data-stu-id="b93e8-487">Automatic</span></span><br><span data-ttu-id="b93e8-488">nesne</span><span class="sxs-lookup"><span data-stu-id="b93e8-488">object</span></span><br><span data-ttu-id="b93e8-489">elden</span><span class="sxs-lookup"><span data-stu-id="b93e8-489">disposal</span></span> | <span data-ttu-id="b93e8-490">Birden çok</span><span class="sxs-lookup"><span data-stu-id="b93e8-490">Multiple</span></span><br><span data-ttu-id="b93e8-491">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="b93e8-491">implementations</span></span> | <span data-ttu-id="b93e8-492">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-492">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="b93e8-493">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b93e8-493">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="b93e8-494">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-494">Yes</span></span> | <span data-ttu-id="b93e8-495">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-495">Yes</span></span> | <span data-ttu-id="b93e8-496">Hayır</span><span class="sxs-lookup"><span data-stu-id="b93e8-496">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="b93e8-497">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="b93e8-497">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="b93e8-498">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-498">Yes</span></span> | <span data-ttu-id="b93e8-499">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-499">Yes</span></span> | <span data-ttu-id="b93e8-500">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-500">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="b93e8-501">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b93e8-501">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="b93e8-502">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-502">Yes</span></span> | <span data-ttu-id="b93e8-503">Hayır</span><span class="sxs-lookup"><span data-stu-id="b93e8-503">No</span></span> | <span data-ttu-id="b93e8-504">Hayır</span><span class="sxs-lookup"><span data-stu-id="b93e8-504">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="b93e8-505">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="b93e8-505">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="b93e8-506">No</span><span class="sxs-lookup"><span data-stu-id="b93e8-506">No</span></span> | <span data-ttu-id="b93e8-507">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-507">Yes</span></span> | <span data-ttu-id="b93e8-508">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-508">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="b93e8-509">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="b93e8-509">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="b93e8-510">Hayır</span><span class="sxs-lookup"><span data-stu-id="b93e8-510">No</span></span> | <span data-ttu-id="b93e8-511">Hayır</span><span class="sxs-lookup"><span data-stu-id="b93e8-511">No</span></span> | <span data-ttu-id="b93e8-512">Yes</span><span class="sxs-lookup"><span data-stu-id="b93e8-512">Yes</span></span> |

<span data-ttu-id="b93e8-513">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-513">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="b93e8-514">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="b93e8-514">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="b93e8-515">`TryAdd{LIFETIME}`Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="b93e8-515">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="b93e8-516">Aşağıdaki örnekte, ilk satır için `MyDependency` `IMyDependency`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-516">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="b93e8-517">Zaten kayıtlı bir uygulamaya sahip olduğundan `IMyDependency` ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-517">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="b93e8-518">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-518">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="b93e8-519">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="b93e8-519">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="b93e8-520">Aracılığıyla `IEnumerable<{SERVICE}>`birden çok hizmet çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-520">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="b93e8-521">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-521">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="b93e8-522">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-522">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="b93e8-523">Aşağıdaki örnekte, ilk satır için `MyDep` `IMyDep1`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-523">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="b93e8-524">İkinci satır için `IMyDep2`kaydedilir `MyDep` .</span><span class="sxs-lookup"><span data-stu-id="b93e8-524">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="b93e8-525">Zaten kayıtlı bir uygulamasına sahip olduğundan `IMyDep1` , üçüncü satırın etkisi yoktur: `MyDep`</span><span class="sxs-lookup"><span data-stu-id="b93e8-525">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="b93e8-526">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="b93e8-526">Constructor injection behavior</span></span>

<span data-ttu-id="b93e8-527">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-527">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="b93e8-528"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-528"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="b93e8-529">`ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-529">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="b93e8-530">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-530">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="b93e8-531">`IServiceProvider` Hizmetler veya `ActivatorUtilities`tarafından çözümlendiğinde, [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-531">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="b93e8-532">Hizmetler tarafından `ActivatorUtilities`çözümlendiğinde, [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-532">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="b93e8-533">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-533">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="b93e8-534">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="b93e8-534">Entity Framework contexts</span></span>

<span data-ttu-id="b93e8-535">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-535">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="b93e8-536">Veritabanı bağlamı kaydedilirken aşırı yükleme [>bir\<adddbcontext tcontext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) tarafından belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-536">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="b93e8-537">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-537">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="b93e8-538">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-538">Lifetime and registration options</span></span>

<span data-ttu-id="b93e8-539">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="b93e8-539">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="b93e8-540">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="b93e8-540">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="b93e8-541">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-541">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="b93e8-542">Bir `Operation` tane sağlanmazsa Oluşturucu bir GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b93e8-542">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="b93e8-543">`OperationService` , Diğer `Operation` türlerin her birine bağlı olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-543">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="b93e8-544">`OperationService` Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-544">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="b93e8-545">Kapsayıcıda istendiğinde geçici `OperationId` hizmetler oluşturulduğunda, `IOperationTransient` hizmet öğesinden `OperationId` farklı olur. `OperationService`</span><span class="sxs-lookup"><span data-stu-id="b93e8-545">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="b93e8-546">`OperationService``IOperationTransient` sınıfının yeni bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-546">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="b93e8-547">Yeni örnek farklı `OperationId`bir şekilde oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-547">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="b93e8-548">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği `OperationService` içindeki ile aynı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-548">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="b93e8-549">İstemci istekleri arasında her iki hizmet de farklı `OperationId` bir değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-549">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="b93e8-550">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde `OperationId` kullanıldığında, tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-550">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="b93e8-551">' `Startup.ConfigureServices`De, her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-551">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="b93e8-552">`IOperationSingletonInstance` Hizmet, bilinen kimliği olan belirli bir örnek kullanıyor `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="b93e8-552">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="b93e8-553">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-553">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="b93e8-554">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-554">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="b93e8-555">Örnek uygulama, her `IndexModel` `IOperation` tür ve ' i ister `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="b93e8-555">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="b93e8-556">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="b93e8-556">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="b93e8-557">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-557">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="b93e8-558">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="b93e8-558">**First request:**</span></span>

<span data-ttu-id="b93e8-559">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="b93e8-559">Controller operations:</span></span>

<span data-ttu-id="b93e8-560">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="b93e8-560">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="b93e8-561">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b93e8-561">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b93e8-562">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b93e8-562">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b93e8-563">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b93e8-563">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b93e8-564">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="b93e8-564">`OperationService` operations:</span></span>

<span data-ttu-id="b93e8-565">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="b93e8-565">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="b93e8-566">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b93e8-566">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b93e8-567">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b93e8-567">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b93e8-568">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b93e8-568">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b93e8-569">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="b93e8-569">**Second request:**</span></span>

<span data-ttu-id="b93e8-570">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="b93e8-570">Controller operations:</span></span>

<span data-ttu-id="b93e8-571">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="b93e8-571">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="b93e8-572">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b93e8-572">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b93e8-573">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b93e8-573">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b93e8-574">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b93e8-574">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b93e8-575">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="b93e8-575">`OperationService` operations:</span></span>

<span data-ttu-id="b93e8-576">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="b93e8-576">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="b93e8-577">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b93e8-577">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b93e8-578">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b93e8-578">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b93e8-579">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b93e8-579">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b93e8-580">`OperationId` Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="b93e8-580">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="b93e8-581">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-581">*Transient* objects are always different.</span></span> <span data-ttu-id="b93e8-582">Hem birinci `OperationId` hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-582">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="b93e8-583">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-583">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="b93e8-584">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-584">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="b93e8-585">*Tek* nesneler, ' de `Operation` `Startup.ConfigureServices`bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-585">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="b93e8-586">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="b93e8-586">Call services from main</span></span>

<span data-ttu-id="b93e8-587">Uygulamanın kapsamındaki <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b93e8-587">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="b93e8-588">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-588">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="b93e8-589">Aşağıdaki örnek, `MyScopedService` içinde `Program.Main`için nasıl bağlam alınacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-589">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="b93e8-590">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b93e8-590">Scope validation</span></span>

<span data-ttu-id="b93e8-591">Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-591">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="b93e8-592">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="b93e8-592">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="b93e8-593">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-593">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="b93e8-594">Kök hizmet sağlayıcısı çağrıldığında oluşturulur <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> .</span><span class="sxs-lookup"><span data-stu-id="b93e8-594">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="b93e8-595">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-595">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="b93e8-596">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="b93e8-596">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="b93e8-597">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-597">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="b93e8-598">Hizmet kapsamlarını doğrulamak `BuildServiceProvider` , çağrıldığında bu durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-598">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="b93e8-599">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="b93e8-599">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="b93e8-600">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-600">Request Services</span></span>

<span data-ttu-id="b93e8-601">ASP.NET Core isteği `HttpContext` içinde bulunan hizmetler, [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-601">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="b93e8-602">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="b93e8-602">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="b93e8-603">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de `RequestServices`bulunan türler tarafından karşılanır. `ApplicationServices`</span><span class="sxs-lookup"><span data-stu-id="b93e8-603">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="b93e8-604">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-604">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="b93e8-605">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-605">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="b93e8-606">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b93e8-606">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="b93e8-607">`RequestServices` Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-607">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="b93e8-608">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="b93e8-608">Design services for dependency injection</span></span>

<span data-ttu-id="b93e8-609">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b93e8-609">Best practices are to:</span></span>

* <span data-ttu-id="b93e8-610">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-610">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="b93e8-611">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="b93e8-611">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="b93e8-612">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-612">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="b93e8-613">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-613">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="b93e8-614">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="b93e8-614">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="b93e8-615">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-615">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="b93e8-616">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-616">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="b93e8-617">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="b93e8-617">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="b93e8-618">Sayfaların sayfa modeli sınıflarının Razor ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="b93e8-618">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="b93e8-619">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-619">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="b93e8-620">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="b93e8-620">Disposal of services</span></span>

<span data-ttu-id="b93e8-621">Kapsayıcı, oluşturduğu <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-621">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="b93e8-622">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-622">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="b93e8-623">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="b93e8-623">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="b93e8-624">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="b93e8-624">In the following example:</span></span>

* <span data-ttu-id="b93e8-625">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-625">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="b93e8-626">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-626">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="b93e8-627">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-627">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="b93e8-628">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-628">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="b93e8-629">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="b93e8-629">Default service container replacement</span></span>

<span data-ttu-id="b93e8-630">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-630">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="b93e8-631">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="b93e8-631">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="b93e8-632">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="b93e8-632">Property injection</span></span>
* <span data-ttu-id="b93e8-633">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="b93e8-633">Injection based on name</span></span>
* <span data-ttu-id="b93e8-634">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="b93e8-634">Child containers</span></span>
* <span data-ttu-id="b93e8-635">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="b93e8-635">Custom lifetime management</span></span>
* <span data-ttu-id="b93e8-636">`Func<T>`yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="b93e8-636">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="b93e8-637">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="b93e8-637">Convention-based registration</span></span>

<span data-ttu-id="b93e8-638">Aşağıdaki 3. taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="b93e8-638">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="b93e8-639">Autofac</span><span class="sxs-lookup"><span data-stu-id="b93e8-639">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="b93e8-640">Drıioc</span><span class="sxs-lookup"><span data-stu-id="b93e8-640">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="b93e8-641">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="b93e8-641">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="b93e8-642">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="b93e8-642">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="b93e8-643">E</span><span class="sxs-lookup"><span data-stu-id="b93e8-643">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="b93e8-644">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="b93e8-644">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="b93e8-645">Unity</span><span class="sxs-lookup"><span data-stu-id="b93e8-645">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="b93e8-646">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="b93e8-646">Thread safety</span></span>

<span data-ttu-id="b93e8-647">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b93e8-647">Create thread-safe singleton services.</span></span> <span data-ttu-id="b93e8-648">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-648">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="b93e8-649">Tek bir hizmetin fabrika yöntemi (örneğin, [AddSingleton\<TService> için ikinci bağımsız değişken) (ıseviecollection,\<Func ıserviceprovider, TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="b93e8-649">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="b93e8-650">Bir Type (`static`) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-650">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="b93e8-651">Öneriler</span><span class="sxs-lookup"><span data-stu-id="b93e8-651">Recommendations</span></span>

* <span data-ttu-id="b93e8-652">`async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b93e8-652">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="b93e8-653">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-653">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="b93e8-654">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="b93e8-654">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="b93e8-655">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-655">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="b93e8-656">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-656">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="b93e8-657">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="b93e8-657">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="b93e8-658">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="b93e8-658">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="b93e8-659">Hizmetlere statik erişimi önleyin (örneğin, başka bir yerde kullanmak üzere, statik olarak yazılan [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ).</span><span class="sxs-lookup"><span data-stu-id="b93e8-659">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="b93e8-660">[Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="b93e8-660">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="b93e8-661">Bunun yerine <xref:System.IServiceProvider.GetService*> , şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:</span><span class="sxs-lookup"><span data-stu-id="b93e8-661">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="b93e8-662">**Olmayan**</span><span class="sxs-lookup"><span data-stu-id="b93e8-662">**Incorrect:**</span></span>

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

    <span data-ttu-id="b93e8-663">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="b93e8-663">**Correct**:</span></span>

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

  * <span data-ttu-id="b93e8-664">Kullanarak <xref:System.IServiceProvider.GetService*>çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin.</span><span class="sxs-lookup"><span data-stu-id="b93e8-664">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="b93e8-665">Uygulamasına `HttpContext` statik erişimi önleyin (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="b93e8-665">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="b93e8-666">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-666">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="b93e8-667">Özel durumlar&mdash;genellikle çerçevenin kendisi içinde özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="b93e8-667">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="b93e8-668">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="b93e8-668">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="b93e8-669">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b93e8-669">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b93e8-670">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b93e8-670">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="b93e8-671">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="b93e8-671">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="b93e8-672">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="b93e8-672">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="b93e8-673">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="b93e8-673">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="b93e8-674">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="b93e8-674">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
