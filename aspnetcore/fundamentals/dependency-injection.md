---
title: ASP.NET Core'da bağımlılık ekleme
author: rick-anderson
description: ASP.NET Core bağımlılık ekleme ve nasıl kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ddb583f69758055500ff63960f469c1cea44c77e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102595"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="0a7f6-103">ASP.NET Core'da bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="0a7f6-104">[Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler) tarafından</span><span class="sxs-lookup"><span data-stu-id="0a7f6-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a7f6-105">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0a7f6-106">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="0a7f6-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0a7f6-107">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a7f6-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0a7f6-108">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="0a7f6-108">Overview of dependency injection</span></span>

<span data-ttu-id="0a7f6-109">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0a7f6-110">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0a7f6-111">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0a7f6-112">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0a7f6-113">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0a7f6-114">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0a7f6-115">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0a7f6-116">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0a7f6-117">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0a7f6-118">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0a7f6-119">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0a7f6-120">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0a7f6-121">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0a7f6-122">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0a7f6-123">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0a7f6-124">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0a7f6-125">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0a7f6-126">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0a7f6-127">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0a7f6-128">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0a7f6-129">`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0a7f6-130">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0a7f6-131">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0a7f6-132">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0a7f6-133">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0a7f6-134">`IMyDependency`ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0a7f6-135">`IMyDependency`kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0a7f6-136">`ILogger<TCategoryName>`günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0a7f6-137">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0a7f6-138">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0a7f6-139">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0a7f6-140">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0a7f6-141">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0a7f6-142">Örneğin, `services.AddMvc()` hizmet Razor SAYFALARıNı ve MVC gerektir öğesini ekler.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0a7f6-143">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0a7f6-144">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0a7f6-145">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0a7f6-146">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0a7f6-147">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0a7f6-148">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0a7f6-149">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="0a7f6-149">Services injected into Startup</span></span>

<span data-ttu-id="0a7f6-150">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0a7f6-151">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0a7f6-152">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0a7f6-153">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="0a7f6-153">Framework-provided services</span></span>

<span data-ttu-id="0a7f6-154">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0a7f6-155">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0a7f6-156">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0a7f6-157">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0a7f6-158">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="0a7f6-158">Service Type</span></span> | <span data-ttu-id="0a7f6-159">Ömür</span><span class="sxs-lookup"><span data-stu-id="0a7f6-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0a7f6-160">Larsa</span><span class="sxs-lookup"><span data-stu-id="0a7f6-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="0a7f6-161">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="0a7f6-162">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0a7f6-163">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0a7f6-164">Larsa</span><span class="sxs-lookup"><span data-stu-id="0a7f6-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0a7f6-165">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0a7f6-166">Larsa</span><span class="sxs-lookup"><span data-stu-id="0a7f6-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0a7f6-167">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0a7f6-168">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0a7f6-169">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0a7f6-170">Larsa</span><span class="sxs-lookup"><span data-stu-id="0a7f6-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0a7f6-171">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0a7f6-172">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0a7f6-173">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0a7f6-174">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-174">Register additional services with extension methods</span></span>

<span data-ttu-id="0a7f6-175">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0a7f6-176">Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="0a7f6-177">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0a7f6-178">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-178">Service lifetimes</span></span>

<span data-ttu-id="0a7f6-179">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0a7f6-180">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0a7f6-181">Larsa</span><span class="sxs-lookup"><span data-stu-id="0a7f6-181">Transient</span></span>

<span data-ttu-id="0a7f6-182">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0a7f6-183">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="0a7f6-184">Yayıl</span><span class="sxs-lookup"><span data-stu-id="0a7f6-184">Scoped</span></span>

<span data-ttu-id="0a7f6-185">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="0a7f6-186">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0a7f6-187">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0a7f6-188">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0a7f6-189">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-189">Singleton</span></span>

<span data-ttu-id="0a7f6-190">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0a7f6-191">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0a7f6-192">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0a7f6-193">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="0a7f6-194">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0a7f6-195">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0a7f6-196">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-196">Service registration methods</span></span>

<span data-ttu-id="0a7f6-197">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0a7f6-198">Yöntem</span><span class="sxs-lookup"><span data-stu-id="0a7f6-198">Method</span></span> | <span data-ttu-id="0a7f6-199">Automatic</span><span class="sxs-lookup"><span data-stu-id="0a7f6-199">Automatic</span></span><br><span data-ttu-id="0a7f6-200">nesne</span><span class="sxs-lookup"><span data-stu-id="0a7f6-200">object</span></span><br><span data-ttu-id="0a7f6-201">elden</span><span class="sxs-lookup"><span data-stu-id="0a7f6-201">disposal</span></span> | <span data-ttu-id="0a7f6-202">Birden çok</span><span class="sxs-lookup"><span data-stu-id="0a7f6-202">Multiple</span></span><br><span data-ttu-id="0a7f6-203">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="0a7f6-203">implementations</span></span> | <span data-ttu-id="0a7f6-204">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0a7f6-205">Örnek:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0a7f6-206">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-206">Yes</span></span> | <span data-ttu-id="0a7f6-207">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-207">Yes</span></span> | <span data-ttu-id="0a7f6-208">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0a7f6-209">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0a7f6-210">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-210">Yes</span></span> | <span data-ttu-id="0a7f6-211">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-211">Yes</span></span> | <span data-ttu-id="0a7f6-212">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0a7f6-213">Örnek:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0a7f6-214">Evet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-214">Yes</span></span> | <span data-ttu-id="0a7f6-215">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-215">No</span></span> | <span data-ttu-id="0a7f6-216">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0a7f6-217">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0a7f6-218">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-218">No</span></span> | <span data-ttu-id="0a7f6-219">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-219">Yes</span></span> | <span data-ttu-id="0a7f6-220">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0a7f6-221">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0a7f6-222">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-222">No</span></span> | <span data-ttu-id="0a7f6-223">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-223">No</span></span> | <span data-ttu-id="0a7f6-224">Evet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-224">Yes</span></span> |

<span data-ttu-id="0a7f6-225">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0a7f6-226">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0a7f6-227">`TryAdd{LIFETIME}`Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0a7f6-228">Aşağıdaki örnekte, ilk satır `MyDependency` için kaydedilir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0a7f6-229">`IMyDependency`Zaten kayıtlı bir uygulamaya sahip olduğundan ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0a7f6-230">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0a7f6-231">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0a7f6-232">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0a7f6-233">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0a7f6-234">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0a7f6-235">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0a7f6-236">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0a7f6-237">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0a7f6-238">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="0a7f6-238">Constructor injection behavior</span></span>

<span data-ttu-id="0a7f6-239">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0a7f6-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0a7f6-241">`ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0a7f6-242">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0a7f6-243">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0a7f6-244">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0a7f6-245">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0a7f6-246">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="0a7f6-246">Entity Framework contexts</span></span>

<span data-ttu-id="0a7f6-247">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0a7f6-248">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0a7f6-249">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0a7f6-250">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-250">Lifetime and registration options</span></span>

<span data-ttu-id="0a7f6-251">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0a7f6-252">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0a7f6-253">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0a7f6-254">`Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0a7f6-255">, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0a7f6-256">`OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0a7f6-257">Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0a7f6-258">`OperationService`sınıfının yeni bir örneğini alır `IOperationTransient` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0a7f6-259">Yeni örnek farklı bir şekilde oluşturur `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0a7f6-260">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0a7f6-261">İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0a7f6-262">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0a7f6-263">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0a7f6-264">`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0a7f6-265">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0a7f6-266">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0a7f6-267">Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0a7f6-268">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0a7f6-269">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0a7f6-270">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-270">**First request:**</span></span>

<span data-ttu-id="0a7f6-271">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-271">Controller operations:</span></span>

<span data-ttu-id="0a7f6-272">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0a7f6-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0a7f6-273">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0a7f6-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0a7f6-274">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a7f6-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a7f6-275">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a7f6-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a7f6-276">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="0a7f6-276">`OperationService` operations:</span></span>

<span data-ttu-id="0a7f6-277">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0a7f6-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0a7f6-278">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0a7f6-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0a7f6-279">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a7f6-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a7f6-280">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a7f6-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a7f6-281">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-281">**Second request:**</span></span>

<span data-ttu-id="0a7f6-282">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-282">Controller operations:</span></span>

<span data-ttu-id="0a7f6-283">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0a7f6-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0a7f6-284">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0a7f6-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0a7f6-285">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a7f6-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a7f6-286">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a7f6-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a7f6-287">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="0a7f6-287">`OperationService` operations:</span></span>

<span data-ttu-id="0a7f6-288">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0a7f6-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0a7f6-289">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0a7f6-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0a7f6-290">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a7f6-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a7f6-291">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a7f6-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a7f6-292">`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0a7f6-293">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-293">*Transient* objects are always different.</span></span> <span data-ttu-id="0a7f6-294">Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0a7f6-295">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0a7f6-296">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0a7f6-297">*Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0a7f6-298">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-298">Call services from main</span></span>

<span data-ttu-id="0a7f6-299"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0a7f6-300">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0a7f6-301">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0a7f6-302">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="0a7f6-302">Scope validation</span></span>

<span data-ttu-id="0a7f6-303">Uygulama geliştirme ortamında çalışırken ve Konağı derlemek için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) çağırdığında, varsayılan hizmet sağlayıcı aşağıdakileri doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0a7f6-304">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0a7f6-305">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0a7f6-306">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0a7f6-307">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0a7f6-308">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0a7f6-309">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0a7f6-310">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0a7f6-311">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="0a7f6-312">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-312">Request Services</span></span>

<span data-ttu-id="0a7f6-313">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0a7f6-314">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0a7f6-315">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0a7f6-316">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0a7f6-317">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="0a7f6-318">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0a7f6-319">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0a7f6-320">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-320">Design services for dependency injection</span></span>

<span data-ttu-id="0a7f6-321">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-321">Best practices are to:</span></span>

* <span data-ttu-id="0a7f6-322">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0a7f6-323">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0a7f6-324">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0a7f6-325">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0a7f6-326">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0a7f6-327">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0a7f6-328">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0a7f6-329">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0a7f6-330">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0a7f6-331">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0a7f6-332">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="0a7f6-332">Disposal of services</span></span>

<span data-ttu-id="0a7f6-333">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0a7f6-334">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0a7f6-335">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="0a7f6-336">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-336">In the following example:</span></span>

* <span data-ttu-id="0a7f6-337">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0a7f6-338">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0a7f6-339">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0a7f6-340">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="0a7f6-341">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="0a7f6-341">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="0a7f6-342">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="0a7f6-342">Transient, limited lifetime</span></span>

<span data-ttu-id="0a7f6-343">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-343">**Scenario**</span></span>

<span data-ttu-id="0a7f6-344">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-344">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="0a7f6-345">Örnek, kök kapsamda çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-345">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="0a7f6-346">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-346">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="0a7f6-347">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-347">**Solution**</span></span>

<span data-ttu-id="0a7f6-348">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-348">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="0a7f6-349">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-349">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="0a7f6-350">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-350">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="0a7f6-351">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-351">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="0a7f6-352"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-352">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="0a7f6-353">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="0a7f6-353">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="0a7f6-354">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-354">**Scenario**</span></span>

<span data-ttu-id="0a7f6-355">Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-355">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="0a7f6-356">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-356">**Solution**</span></span>

<span data-ttu-id="0a7f6-357">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-357">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="0a7f6-358"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-358">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="0a7f6-359"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-359">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="0a7f6-360">Yaşam süresi sona erdirmek için kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-360">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="0a7f6-361">Genel Yönergeler</span><span class="sxs-lookup"><span data-stu-id="0a7f6-361">General Guidelines</span></span>

* <span data-ttu-id="0a7f6-362"><xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-362">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="0a7f6-363">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-363">Use the factory pattern instead.</span></span>
* <span data-ttu-id="0a7f6-364">Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-364">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="0a7f6-365">Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-365">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="0a7f6-366">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-366">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="0a7f6-367"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-367">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="0a7f6-368">Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-368">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="0a7f6-369">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-369">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0a7f6-370">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-370">Default service container replacement</span></span>

<span data-ttu-id="0a7f6-371">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-371">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0a7f6-372">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-372">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0a7f6-373">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-373">Property injection</span></span>
* <span data-ttu-id="0a7f6-374">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-374">Injection based on name</span></span>
* <span data-ttu-id="0a7f6-375">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="0a7f6-375">Child containers</span></span>
* <span data-ttu-id="0a7f6-376">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="0a7f6-376">Custom lifetime management</span></span>
* <span data-ttu-id="0a7f6-377">`Func<T>`yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="0a7f6-377">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0a7f6-378">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="0a7f6-378">Convention-based registration</span></span>

<span data-ttu-id="0a7f6-379">Aşağıdaki 3. taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-379">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0a7f6-380">Autofac</span><span class="sxs-lookup"><span data-stu-id="0a7f6-380">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0a7f6-381">Drıioc</span><span class="sxs-lookup"><span data-stu-id="0a7f6-381">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0a7f6-382">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="0a7f6-382">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0a7f6-383">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="0a7f6-383">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0a7f6-384">E</span><span class="sxs-lookup"><span data-stu-id="0a7f6-384">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0a7f6-385">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="0a7f6-385">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0a7f6-386">Unity</span><span class="sxs-lookup"><span data-stu-id="0a7f6-386">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0a7f6-387">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="0a7f6-387">Thread safety</span></span>

<span data-ttu-id="0a7f6-388">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-388">Create thread-safe singleton services.</span></span> <span data-ttu-id="0a7f6-389">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-389">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0a7f6-390">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-390">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0a7f6-391">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-391">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0a7f6-392">Öneriler</span><span class="sxs-lookup"><span data-stu-id="0a7f6-392">Recommendations</span></span>

* <span data-ttu-id="0a7f6-393">`async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-393">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0a7f6-394">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-394">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0a7f6-395">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-395">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0a7f6-396">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-396">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0a7f6-397">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-397">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0a7f6-398">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-398">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0a7f6-399">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-399">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="0a7f6-400">Hizmetlere statik erişimi önleyin (örneğin, başka bir yerde kullanmak üzere, statik olarak yazılan [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-400">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="0a7f6-401">*Hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-401">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="0a7f6-402">Örneğin, <xref:System.IServiceProvider.GetService*> yerine şunu kullandığınızda bir hizmet örneği elde etme çağrısı yapmayın:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-402">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="0a7f6-403">**Olmayan**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-403">**Incorrect:**</span></span>

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

  <span data-ttu-id="0a7f6-404">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-404">**Correct**:</span></span>

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

* <span data-ttu-id="0a7f6-405">Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-405">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="0a7f6-406">Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-406">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="0a7f6-407">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-407">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0a7f6-408">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-408">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0a7f6-409">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-409">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0a7f6-410">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-410">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0a7f6-411">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-411">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="0a7f6-412">DI 'de çok kiracılı için önerilen desenler</span><span class="sxs-lookup"><span data-stu-id="0a7f6-412">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="0a7f6-413">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , çok kiracılı bir uygulama sağlar.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-413">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="0a7f6-414">Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-414">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="0a7f6-415">https://github.com/OrchardCMS/OrchardCore.SamplesCMS 'e özgü özelliklerden herhangi biri olmadan yalnızca Orchard Core çerçevesini kullanarak modüler ve çok kiracılı uygulamalar oluşturmaya yönelik örnekler için adresindeki örnek uygulamalar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-415">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a7f6-416">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0a7f6-416">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0a7f6-417">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="0a7f6-417">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="0a7f6-418">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="0a7f6-418">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="0a7f6-419">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="0a7f6-419">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="0a7f6-420">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="0a7f6-420">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0a7f6-421">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-421">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0a7f6-422">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-422">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0a7f6-423">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="0a7f6-423">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0a7f6-424">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a7f6-424">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0a7f6-425">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="0a7f6-425">Overview of dependency injection</span></span>

<span data-ttu-id="0a7f6-426">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-426">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0a7f6-427">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-427">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0a7f6-428">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-428">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0a7f6-429">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-429">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0a7f6-430">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-430">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0a7f6-431">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-431">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0a7f6-432">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-432">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0a7f6-433">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-433">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0a7f6-434">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-434">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0a7f6-435">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-435">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0a7f6-436">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-436">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0a7f6-437">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-437">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0a7f6-438">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-438">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0a7f6-439">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-439">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0a7f6-440">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-440">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0a7f6-441">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-441">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0a7f6-442">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-442">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0a7f6-443">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-443">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0a7f6-444">[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-444">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0a7f6-445">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-445">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0a7f6-446">`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-446">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0a7f6-447">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-447">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0a7f6-448">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-448">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0a7f6-449">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-449">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0a7f6-450">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-450">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0a7f6-451">`IMyDependency`ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-451">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0a7f6-452">`IMyDependency`kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-452">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0a7f6-453">`ILogger<TCategoryName>`günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-453">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0a7f6-454">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-454">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0a7f6-455">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-455">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0a7f6-456">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-456">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0a7f6-457">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-457">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0a7f6-458">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-458">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0a7f6-459">Örneğin, `services.AddMvc()` hizmet Razor SAYFALARıNı ve MVC gerektir öğesini ekler.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-459">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0a7f6-460">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-460">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0a7f6-461">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-461">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0a7f6-462">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-462">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0a7f6-463">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-463">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0a7f6-464">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-464">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0a7f6-465">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-465">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0a7f6-466">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="0a7f6-466">Services injected into Startup</span></span>

<span data-ttu-id="0a7f6-467">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-467">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0a7f6-468">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-468">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0a7f6-469">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-469">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0a7f6-470">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="0a7f6-470">Framework-provided services</span></span>

<span data-ttu-id="0a7f6-471">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-471">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0a7f6-472">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-472">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0a7f6-473">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-473">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0a7f6-474">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-474">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0a7f6-475">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="0a7f6-475">Service Type</span></span> | <span data-ttu-id="0a7f6-476">Ömür</span><span class="sxs-lookup"><span data-stu-id="0a7f6-476">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0a7f6-477">Larsa</span><span class="sxs-lookup"><span data-stu-id="0a7f6-477">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="0a7f6-478">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-478">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="0a7f6-479">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-479">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0a7f6-480">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-480">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0a7f6-481">Larsa</span><span class="sxs-lookup"><span data-stu-id="0a7f6-481">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0a7f6-482">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0a7f6-483">Larsa</span><span class="sxs-lookup"><span data-stu-id="0a7f6-483">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0a7f6-484">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-484">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0a7f6-485">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-485">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0a7f6-486">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-486">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0a7f6-487">Larsa</span><span class="sxs-lookup"><span data-stu-id="0a7f6-487">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0a7f6-488">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-488">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0a7f6-489">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-489">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0a7f6-490">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-490">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0a7f6-491">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-491">Register additional services with extension methods</span></span>

<span data-ttu-id="0a7f6-492">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-492">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0a7f6-493">Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-493">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="0a7f6-494">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-494">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0a7f6-495">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-495">Service lifetimes</span></span>

<span data-ttu-id="0a7f6-496">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-496">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0a7f6-497">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-497">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0a7f6-498">Larsa</span><span class="sxs-lookup"><span data-stu-id="0a7f6-498">Transient</span></span>

<span data-ttu-id="0a7f6-499">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-499">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0a7f6-500">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-500">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="0a7f6-501">Yayıl</span><span class="sxs-lookup"><span data-stu-id="0a7f6-501">Scoped</span></span>

<span data-ttu-id="0a7f6-502">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-502">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="0a7f6-503">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-503">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0a7f6-504">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-504">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0a7f6-505">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-505">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0a7f6-506">Adet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-506">Singleton</span></span>

<span data-ttu-id="0a7f6-507">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-507">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0a7f6-508">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-508">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0a7f6-509">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-509">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0a7f6-510">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-510">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="0a7f6-511">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-511">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0a7f6-512">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-512">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0a7f6-513">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-513">Service registration methods</span></span>

<span data-ttu-id="0a7f6-514">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-514">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0a7f6-515">Yöntem</span><span class="sxs-lookup"><span data-stu-id="0a7f6-515">Method</span></span> | <span data-ttu-id="0a7f6-516">Automatic</span><span class="sxs-lookup"><span data-stu-id="0a7f6-516">Automatic</span></span><br><span data-ttu-id="0a7f6-517">nesne</span><span class="sxs-lookup"><span data-stu-id="0a7f6-517">object</span></span><br><span data-ttu-id="0a7f6-518">elden</span><span class="sxs-lookup"><span data-stu-id="0a7f6-518">disposal</span></span> | <span data-ttu-id="0a7f6-519">Birden çok</span><span class="sxs-lookup"><span data-stu-id="0a7f6-519">Multiple</span></span><br><span data-ttu-id="0a7f6-520">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="0a7f6-520">implementations</span></span> | <span data-ttu-id="0a7f6-521">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-521">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0a7f6-522">Örnek:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-522">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0a7f6-523">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-523">Yes</span></span> | <span data-ttu-id="0a7f6-524">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-524">Yes</span></span> | <span data-ttu-id="0a7f6-525">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-525">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0a7f6-526">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-526">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0a7f6-527">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-527">Yes</span></span> | <span data-ttu-id="0a7f6-528">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-528">Yes</span></span> | <span data-ttu-id="0a7f6-529">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-529">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0a7f6-530">Örnek:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-530">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0a7f6-531">Evet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-531">Yes</span></span> | <span data-ttu-id="0a7f6-532">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-532">No</span></span> | <span data-ttu-id="0a7f6-533">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-533">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0a7f6-534">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-534">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0a7f6-535">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-535">No</span></span> | <span data-ttu-id="0a7f6-536">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-536">Yes</span></span> | <span data-ttu-id="0a7f6-537">Yes</span><span class="sxs-lookup"><span data-stu-id="0a7f6-537">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0a7f6-538">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-538">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0a7f6-539">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-539">No</span></span> | <span data-ttu-id="0a7f6-540">Hayır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-540">No</span></span> | <span data-ttu-id="0a7f6-541">Evet</span><span class="sxs-lookup"><span data-stu-id="0a7f6-541">Yes</span></span> |

<span data-ttu-id="0a7f6-542">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-542">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0a7f6-543">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-543">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0a7f6-544">`TryAdd{LIFETIME}`Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-544">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0a7f6-545">Aşağıdaki örnekte, ilk satır `MyDependency` için kaydedilir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-545">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0a7f6-546">`IMyDependency`Zaten kayıtlı bir uygulamaya sahip olduğundan ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-546">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0a7f6-547">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-547">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0a7f6-548">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-548">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0a7f6-549">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-549">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0a7f6-550">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-550">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0a7f6-551">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-551">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0a7f6-552">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-552">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0a7f6-553">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-553">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0a7f6-554">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-554">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0a7f6-555">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="0a7f6-555">Constructor injection behavior</span></span>

<span data-ttu-id="0a7f6-556">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-556">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0a7f6-557"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-557"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0a7f6-558">`ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-558">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0a7f6-559">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-559">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0a7f6-560">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-560">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0a7f6-561">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-561">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0a7f6-562">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-562">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0a7f6-563">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="0a7f6-563">Entity Framework contexts</span></span>

<span data-ttu-id="0a7f6-564">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-564">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0a7f6-565">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-565">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0a7f6-566">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-566">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0a7f6-567">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-567">Lifetime and registration options</span></span>

<span data-ttu-id="0a7f6-568">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-568">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0a7f6-569">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-569">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0a7f6-570">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-570">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0a7f6-571">`Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-571">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0a7f6-572">, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-572">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0a7f6-573">`OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-573">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0a7f6-574">Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-574">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0a7f6-575">`OperationService`sınıfının yeni bir örneğini alır `IOperationTransient` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-575">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0a7f6-576">Yeni örnek farklı bir şekilde oluşturur `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-576">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0a7f6-577">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-577">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0a7f6-578">İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-578">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0a7f6-579">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-579">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0a7f6-580">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-580">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0a7f6-581">`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-581">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0a7f6-582">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-582">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0a7f6-583">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-583">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0a7f6-584">Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-584">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0a7f6-585">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-585">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0a7f6-586">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-586">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0a7f6-587">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-587">**First request:**</span></span>

<span data-ttu-id="0a7f6-588">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-588">Controller operations:</span></span>

<span data-ttu-id="0a7f6-589">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0a7f6-589">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0a7f6-590">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0a7f6-590">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0a7f6-591">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a7f6-591">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a7f6-592">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a7f6-592">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a7f6-593">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="0a7f6-593">`OperationService` operations:</span></span>

<span data-ttu-id="0a7f6-594">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0a7f6-594">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0a7f6-595">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0a7f6-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0a7f6-596">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a7f6-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a7f6-597">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a7f6-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a7f6-598">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-598">**Second request:**</span></span>

<span data-ttu-id="0a7f6-599">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-599">Controller operations:</span></span>

<span data-ttu-id="0a7f6-600">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0a7f6-600">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0a7f6-601">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0a7f6-601">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0a7f6-602">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a7f6-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a7f6-603">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a7f6-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a7f6-604">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="0a7f6-604">`OperationService` operations:</span></span>

<span data-ttu-id="0a7f6-605">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0a7f6-605">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0a7f6-606">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0a7f6-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0a7f6-607">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a7f6-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a7f6-608">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a7f6-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a7f6-609">`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-609">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0a7f6-610">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-610">*Transient* objects are always different.</span></span> <span data-ttu-id="0a7f6-611">Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-611">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0a7f6-612">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-612">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0a7f6-613">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-613">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0a7f6-614">*Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-614">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0a7f6-615">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="0a7f6-615">Call services from main</span></span>

<span data-ttu-id="0a7f6-616"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-616">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0a7f6-617">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-617">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0a7f6-618">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="0a7f6-618">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0a7f6-619">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="0a7f6-619">Scope validation</span></span>

<span data-ttu-id="0a7f6-620">Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-620">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0a7f6-621">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-621">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0a7f6-622">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-622">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0a7f6-623">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-623">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0a7f6-624">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-624">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0a7f6-625">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-625">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0a7f6-626">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-626">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0a7f6-627">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-627">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0a7f6-628">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-628">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="0a7f6-629">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-629">Request Services</span></span>

<span data-ttu-id="0a7f6-630">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-630">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0a7f6-631">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-631">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0a7f6-632">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-632">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0a7f6-633">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-633">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0a7f6-634">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-634">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="0a7f6-635">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-635">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0a7f6-636">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-636">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0a7f6-637">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="0a7f6-637">Design services for dependency injection</span></span>

<span data-ttu-id="0a7f6-638">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-638">Best practices are to:</span></span>

* <span data-ttu-id="0a7f6-639">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-639">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0a7f6-640">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-640">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0a7f6-641">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-641">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0a7f6-642">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-642">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0a7f6-643">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-643">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0a7f6-644">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-644">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0a7f6-645">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-645">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0a7f6-646">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-646">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0a7f6-647">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-647">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0a7f6-648">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-648">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0a7f6-649">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="0a7f6-649">Disposal of services</span></span>

<span data-ttu-id="0a7f6-650">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-650">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0a7f6-651">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-651">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0a7f6-652">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-652">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="0a7f6-653">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-653">In the following example:</span></span>

* <span data-ttu-id="0a7f6-654">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-654">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0a7f6-655">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-655">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0a7f6-656">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-656">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0a7f6-657">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-657">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="0a7f6-658">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="0a7f6-658">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="0a7f6-659">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="0a7f6-659">Transient, limited lifetime</span></span>

<span data-ttu-id="0a7f6-660">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-660">**Scenario**</span></span>

<span data-ttu-id="0a7f6-661">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-661">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="0a7f6-662">Örnek, kök kapsamda çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-662">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="0a7f6-663">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-663">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="0a7f6-664">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-664">**Solution**</span></span>

<span data-ttu-id="0a7f6-665">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-665">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="0a7f6-666">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-666">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="0a7f6-667">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-667">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="0a7f6-668">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-668">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="0a7f6-669"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-669">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="0a7f6-670">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="0a7f6-670">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="0a7f6-671">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-671">**Scenario**</span></span>

<span data-ttu-id="0a7f6-672">Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-672">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="0a7f6-673">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-673">**Solution**</span></span>

<span data-ttu-id="0a7f6-674">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-674">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="0a7f6-675"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-675">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="0a7f6-676"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-676">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="0a7f6-677">Yaşam süresi sona erdirmek için kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-677">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="0a7f6-678">Genel Yönergeler</span><span class="sxs-lookup"><span data-stu-id="0a7f6-678">General Guidelines</span></span>

* <span data-ttu-id="0a7f6-679"><xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-679">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="0a7f6-680">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-680">Use the factory pattern instead.</span></span>
* <span data-ttu-id="0a7f6-681">Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-681">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="0a7f6-682">Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-682">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="0a7f6-683">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-683">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="0a7f6-684"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-684">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="0a7f6-685">Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-685">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="0a7f6-686">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-686">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0a7f6-687">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-687">Default service container replacement</span></span>

<span data-ttu-id="0a7f6-688">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-688">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0a7f6-689">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-689">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0a7f6-690">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-690">Property injection</span></span>
* <span data-ttu-id="0a7f6-691">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-691">Injection based on name</span></span>
* <span data-ttu-id="0a7f6-692">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="0a7f6-692">Child containers</span></span>
* <span data-ttu-id="0a7f6-693">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="0a7f6-693">Custom lifetime management</span></span>
* <span data-ttu-id="0a7f6-694">`Func<T>`yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="0a7f6-694">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0a7f6-695">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="0a7f6-695">Convention-based registration</span></span>

<span data-ttu-id="0a7f6-696">Aşağıdaki 3. taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-696">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0a7f6-697">Autofac</span><span class="sxs-lookup"><span data-stu-id="0a7f6-697">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0a7f6-698">Drıioc</span><span class="sxs-lookup"><span data-stu-id="0a7f6-698">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0a7f6-699">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="0a7f6-699">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0a7f6-700">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="0a7f6-700">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0a7f6-701">E</span><span class="sxs-lookup"><span data-stu-id="0a7f6-701">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0a7f6-702">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="0a7f6-702">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0a7f6-703">Unity</span><span class="sxs-lookup"><span data-stu-id="0a7f6-703">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0a7f6-704">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="0a7f6-704">Thread safety</span></span>

<span data-ttu-id="0a7f6-705">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-705">Create thread-safe singleton services.</span></span> <span data-ttu-id="0a7f6-706">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-706">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0a7f6-707">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-707">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0a7f6-708">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-708">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0a7f6-709">Öneriler</span><span class="sxs-lookup"><span data-stu-id="0a7f6-709">Recommendations</span></span>

* <span data-ttu-id="0a7f6-710">`async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-710">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0a7f6-711">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-711">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0a7f6-712">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-712">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0a7f6-713">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-713">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0a7f6-714">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-714">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0a7f6-715">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-715">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0a7f6-716">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-716">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="0a7f6-717">Hizmetlere statik erişimi önleyin (örneğin, başka bir yerde kullanmak üzere, statik olarak yazılan [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-717">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="0a7f6-718">[Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-718">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="0a7f6-719"><xref:System.IServiceProvider.GetService*>Bunun yerine, şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-719">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="0a7f6-720">**Olmayan**</span><span class="sxs-lookup"><span data-stu-id="0a7f6-720">**Incorrect:**</span></span>

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

    <span data-ttu-id="0a7f6-721">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="0a7f6-721">**Correct**:</span></span>

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

  * <span data-ttu-id="0a7f6-722">Kullanarak çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-722">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="0a7f6-723">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0a7f6-723">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0a7f6-724">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-724">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0a7f6-725">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-725">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0a7f6-726">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="0a7f6-726">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0a7f6-727">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0a7f6-727">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a7f6-728">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0a7f6-728">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0a7f6-729">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="0a7f6-729">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="0a7f6-730">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="0a7f6-730">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="0a7f6-731">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="0a7f6-731">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="0a7f6-732">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="0a7f6-732">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0a7f6-733">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="0a7f6-733">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
