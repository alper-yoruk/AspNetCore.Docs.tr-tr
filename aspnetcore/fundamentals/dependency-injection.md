---
title: ASP.NET Core'da bağımlılık ekleme
author: rick-anderson
description: ASP.NET Core bağımlılık ekleme ve nasıl kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 0a51647463362d6cfac335688d42d4be013f8b9c
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712525"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="e1e9f-103">ASP.NET Core'da bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="e1e9f-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e1e9f-104">[Kirk Larkabağı](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="e1e9f-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="e1e9f-105">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="e1e9f-106">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="e1e9f-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="e1e9f-107">Seçeneklerin bağımlılığı ekleme hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/options> ..</span><span class="sxs-lookup"><span data-stu-id="e1e9f-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="e1e9f-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1e9f-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="e1e9f-109">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="e1e9f-109">Overview of dependency injection</span></span>

<span data-ttu-id="e1e9f-110">*Bağımlılık* , başka bir nesnenin bağımlı olduğu bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-110">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="e1e9f-111">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` diğer sınıfların bağımlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="e1e9f-112">Bir sınıf, `MyDependency` yöntemini kullanmak için sınıfının bir örneğini oluşturabilir `WriteMessage` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-112">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="e1e9f-113">Aşağıdaki örnekte, `MyDependency` sınıfı sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-113">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="e1e9f-114">Sınıfı oluşturur ve doğrudan `MyDependency` sınıfa bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-114">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="e1e9f-115">Önceki örnekte olduğu gibi kod bağımlılıkları sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-115">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="e1e9f-116">`MyDependency`Farklı bir uygulamayla değiştirmek için, `IndexModel` sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-116">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="e1e9f-117">`MyDependency`Bağımlılıkları varsa, sınıf tarafından da yapılandırılmalıdır `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-117">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="e1e9f-118">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="e1e9f-119">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="e1e9f-120">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="e1e9f-121">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="e1e9f-122">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="e1e9f-123">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="e1e9f-124">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="e1e9f-125">Hizmetler genellikle uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-125">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="e1e9f-126">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="e1e9f-127">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="e1e9f-128">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirimi `WriteMessage` yöntemini tanımlar:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="e1e9f-129">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="e1e9f-130">Örnek uygulama, `IMyDependency` hizmeti somut tür ile kaydeder `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-130">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="e1e9f-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Yöntemi, hizmeti tek bir isteğin ömrü olan kapsamlı bir yaşam süresine kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="e1e9f-132">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="e1e9f-133">Örnek uygulamada, `IMyDependency` hizmet istenir ve yöntemi çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-133">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="e1e9f-134">DI modelini kullanarak denetleyici:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-134">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="e1e9f-135">Somut türü kullanmaz `MyDependency` , yalnızca `IMyDependency` uyguladığı arabirim.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-135">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="e1e9f-136">Bu, denetleyicinin, denetleyiciyi değiştirmeden kullandığı uygulamayı değiştirmeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="e1e9f-137">Bir örneği oluşturmaz `MyDependency` , bu, dı kapsayıcısı tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-137">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="e1e9f-138">Arabirim uygulanması, `IMyDependency` yerleşik günlük API 'si kullanılarak artırılabilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="e1e9f-139">Updated `ConfigureServices` yöntemi yeni `IMyDependency` uygulamayı kaydeder:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="e1e9f-140">`MyDependency2`<xref:Microsoft.Extensions.Logging.ILogger%601>, oluşturucuda istediği öğesine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-140">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="e1e9f-141">`ILogger<TCategoryName>`[Framework tarafından sağlanmış bir hizmettir](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-141">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="e1e9f-142">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-142">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="e1e9f-143">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-143">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="e1e9f-144">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-144">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="e1e9f-145">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-145">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="e1e9f-146">Kapsayıcı, `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="e1e9f-147">Bağımlılık ekleme terminolojisi ' nde bir hizmet:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="e1e9f-148">Genellikle hizmet gibi diğer nesnelere hizmet sağlayan bir nesnedir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-148">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="e1e9f-149">Bir Web hizmetiyle ilgili değildir, ancak hizmet bir Web hizmeti kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="e1e9f-150">Çerçeve, güçlü bir [günlük](xref:fundamentals/logging/index) sistemi sağlar.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="e1e9f-151">`IMyDependency`Yukarıdaki örneklerde gösterilen uygulamalar, günlüğü uygulamamak için değil temel dı 'yi göstermek için yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-151">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="e1e9f-152">Çoğu uygulamanın Günlükçüler yazması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="e1e9f-153">Aşağıdaki kod, bir hizmetin kaydedilmesini gerektirmeyen varsayılan günlük kaydını kullanmayı göstermektedir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="e1e9f-154">Yukarıdaki kodu kullanarak, `ConfigureServices` Framework tarafından [günlüğe kaydetme](xref:fundamentals/logging/index) sağlandığı için güncelleştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-154">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="e1e9f-155">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="e1e9f-155">Services injected into Startup</span></span>

<span data-ttu-id="e1e9f-156">Hizmetler `Startup` oluşturucuya ve `Startup.Configure` metoduna eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-156">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="e1e9f-157">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmetler eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-157">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="e1e9f-158">Dı kapsayıcısına kayıtlı tüm hizmetler `Startup.Configure` metoduna eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-158">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="e1e9f-159">Daha fazla bilgi için, bkz <xref:fundamentals/startup> . ve [erişim yapılandırması başlangıç](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-159">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="e1e9f-160">Uzantı yöntemleriyle hizmet gruplarını kaydet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-160">Register groups of services with extension methods</span></span>

<span data-ttu-id="e1e9f-161">ASP.NET Core Framework, bir grup ilgili hizmeti kaydetmek için bir kural kullanır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-161">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="e1e9f-162">Kural, `Add{GROUP_NAME}` bir Framework özelliği için gereken tüm hizmetleri kaydetmek için tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-162">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="e1e9f-163">Örneğin, Microsoft. Extensions. Dependencyınjection. MvcServiceCollectionExtensions. AddControllers> genişletme yöntemi <MVC denetleyicileri için gereken hizmetleri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-163">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="e1e9f-164">Aşağıdaki kod, Razor bireysel kullanıcı hesapları kullanılarak sayfalar şablonu tarafından oluşturulur ve uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemeyi gösterir <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ve <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-164">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="e1e9f-165">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-165">Service lifetimes</span></span>

<span data-ttu-id="e1e9f-166">Hizmetler aşağıdaki yaşam sürelerinin biriyle kaydedilebilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-166">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="e1e9f-167">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-167">Transient</span></span>
* <span data-ttu-id="e1e9f-168">Yayıl</span><span class="sxs-lookup"><span data-stu-id="e1e9f-168">Scoped</span></span>
* <span data-ttu-id="e1e9f-169">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-169">Singleton</span></span>

<span data-ttu-id="e1e9f-170">Aşağıdaki bölümlerde, önceki yaşam sürelerinin her biri açıklanır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-170">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="e1e9f-171">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-171">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="e1e9f-172">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-172">Transient</span></span>

<span data-ttu-id="e1e9f-173">Geçici ömür Hizmetleri, hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-173">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="e1e9f-174">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-174">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="e1e9f-175">Geçici Hizmetleri ile kaydedin <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-175">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="e1e9f-176">İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-176">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="e1e9f-177">Yayıl</span><span class="sxs-lookup"><span data-stu-id="e1e9f-177">Scoped</span></span>

<span data-ttu-id="e1e9f-178">Kapsamlı ömür Hizmetleri, istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-178">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="e1e9f-179">Kapsamlı hizmetleri ile kaydedin <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-179">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="e1e9f-180">İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-180">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="e1e9f-181">Entity Framework Core kullanılırken, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> genişletme yöntemi, `DbContext` Varsayılan olarak kapsamlı yaşam süresine sahip türleri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-181">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="e1e9f-182">Kapsamlı bir hizmeti tek bir ***sunucudan çözümleyin.***</span><span class="sxs-lookup"><span data-stu-id="e1e9f-182">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="e1e9f-183">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-183">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="e1e9f-184">Şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-184">It's fine to:</span></span>

* <span data-ttu-id="e1e9f-185">Tek bir hizmeti kapsamlı veya geçici bir hizmetten çözümleyin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-185">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="e1e9f-186">Kapsamlı bir hizmeti başka bir kapsamlı veya geçici hizmetten çözün.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-186">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="e1e9f-187">Varsayılan olarak, geliştirme ortamında, bir hizmetin daha uzun bir yaşam süresine sahip başka bir hizmetten çözülmesi bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-187">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="e1e9f-188">Daha fazla bilgi için bkz. [kapsam doğrulaması](#sv).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-188">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="e1e9f-189">Ara yazılım kapsamındaki hizmetleri kullanmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-189">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="e1e9f-190">Hizmeti, ara yazılımı `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-190">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="e1e9f-191">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) kullanılması, kapsamlı hizmeti tek bir gibi davranmaya zordığı için bir çalışma zamanı özel durumu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-191">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="e1e9f-192">[Ömür ve kayıt seçenekleri](#lifetime-and-registration-options) bölümündeki örnek, `InvokeAsync` yaklaşımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-192">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="e1e9f-193">[Fabrika tabanlı ara yazılım](xref:fundamentals/middleware/extensibility)kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-193">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="e1e9f-194">Bu yaklaşım kullanılarak kaydedilen ara yazılım, kapsamlı hizmetlerin ara yazılım yöntemine eklenmiş olmasına olanak tanıyan istemci isteği (bağlantı) başına etkinleştirilir `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-194">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="e1e9f-195">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-195">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="e1e9f-196">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-196">Singleton</span></span>

<span data-ttu-id="e1e9f-197">Tek yaşam süresi Hizmetleri şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-197">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="e1e9f-198">İlk kez istenirler.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-198">The first time they're requested.</span></span>
* <span data-ttu-id="e1e9f-199">Geliştirici tarafından doğrudan kapsayıcıya bir uygulama örneği sağlarken.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-199">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="e1e9f-200">Bu yaklaşım nadiren gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-200">This approach is rarely needed.</span></span>

<span data-ttu-id="e1e9f-201">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-201">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="e1e9f-202">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-202">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="e1e9f-203">Tek tasarım modelini uygulamayın ve tek bir atma kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-203">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="e1e9f-204">Hizmetler, kapsayıcıyı hizmetten çözümleyen kodla hiçbir şekilde atılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-204">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="e1e9f-205">Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı kendiliğinden otomatik olarak atar.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-205">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="e1e9f-206">İle Singleton hizmetlerini kaydettirin <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-206">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="e1e9f-207">Tek hizmetler iş parçacığı güvenli olmalıdır ve genellikle durum bilgisiz hizmetlerde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-207">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="e1e9f-208">İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-208">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="e1e9f-209">Uygulama kapatılıncaya kadar bellek yayımlanmadığı için, tek bir hizmetle bellek kullanımını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-209">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="e1e9f-210">Kapsamlı bir hizmeti tek bir ***sunucudan çözümleyin.***</span><span class="sxs-lookup"><span data-stu-id="e1e9f-210">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="e1e9f-211">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="e1e9f-212">Tek bir hizmeti kapsamlı veya geçici bir hizmetten çözümlemek çok iyidir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-212">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="e1e9f-213">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-213">Service registration methods</span></span>

<span data-ttu-id="e1e9f-214">Framework, belirli senaryolarda yararlı olan hizmet kayıt uzantısı yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-214">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="e1e9f-215">Yöntem</span><span class="sxs-lookup"><span data-stu-id="e1e9f-215">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="e1e9f-216">Automatic</span><span class="sxs-lookup"><span data-stu-id="e1e9f-216">Automatic</span></span><br><span data-ttu-id="e1e9f-217">object</span><span class="sxs-lookup"><span data-stu-id="e1e9f-217">object</span></span><br><span data-ttu-id="e1e9f-218">elden</span><span class="sxs-lookup"><span data-stu-id="e1e9f-218">disposal</span></span> | <span data-ttu-id="e1e9f-219">Birden çok</span><span class="sxs-lookup"><span data-stu-id="e1e9f-219">Multiple</span></span><br><span data-ttu-id="e1e9f-220">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="e1e9f-220">implementations</span></span> | <span data-ttu-id="e1e9f-221">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-221">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="e1e9f-222">Örnek:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-222">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="e1e9f-223">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-223">Yes</span></span>                             | <span data-ttu-id="e1e9f-224">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-224">Yes</span></span>                         | <span data-ttu-id="e1e9f-225">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-225">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="e1e9f-226">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-226">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="e1e9f-227">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-227">Yes</span></span>                             | <span data-ttu-id="e1e9f-228">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-228">Yes</span></span>                         | <span data-ttu-id="e1e9f-229">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-229">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="e1e9f-230">Örnek:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-230">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="e1e9f-231">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-231">Yes</span></span>                             | <span data-ttu-id="e1e9f-232">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-232">No</span></span>                          | <span data-ttu-id="e1e9f-233">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-233">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="e1e9f-234">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-234">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="e1e9f-235">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-235">No</span></span>                              | <span data-ttu-id="e1e9f-236">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-236">Yes</span></span>                         | <span data-ttu-id="e1e9f-237">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-237">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="e1e9f-238">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-238">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="e1e9f-239">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-239">No</span></span>                              | <span data-ttu-id="e1e9f-240">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-240">No</span></span>                          | <span data-ttu-id="e1e9f-241">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-241">Yes</span></span>       |

<span data-ttu-id="e1e9f-242">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-242">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="e1e9f-243">[Test için izleme türleri](xref:test/integration-tests#inject-mock-services)olduğunda birden çok uygulama kullanılması yaygındır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-243">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="e1e9f-244">Framework Ayrıca, `TryAdd{LIFETIME}` yalnızca kayıtlı bir uygulama olmadığında hizmeti kaydeden genişletme yöntemleri de sağlar.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-244">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="e1e9f-245">Aşağıdaki örnekte, `AddSingleton` `MyDependency` için bir uygulama olarak Yazmaçları çağrısı `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-245">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="e1e9f-246">`TryAddSingleton` `IMyDependency` Zaten kayıtlı bir uygulamaya sahip olduğundan, çağrısının bir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-246">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="e1e9f-247">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-247">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="e1e9f-248">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) yöntemleri, yalnızca *aynı türde*bir uygulama olmadığında hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-248">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="e1e9f-249">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-249">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="e1e9f-250">Hizmetleri kaydederken, aynı türden biri zaten eklenmediyse geliştirici bir örnek eklememelidir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-250">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="e1e9f-251">Genellikle, kitaplık yazarları `TryAddEnumerable` kapsayıcıda bir uygulamanın birden çok kopyasını kaydetmemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-251">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="e1e9f-252">Aşağıdaki örnekte, `TryAddEnumerable` `MyDependency` için bir uygulama olarak kaydeden ilk çağrı `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-252">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="e1e9f-253">İçin ikinci çağrı kaydettirir `MyDependency` `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-253">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="e1e9f-254">`IMyDependency1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü çağrının etkisi yoktur `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-254">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="e1e9f-255">Hizmet kaydı, aynı türde birden çok uygulama kaydedilirken genellikle sıralı olarak bağımsızdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-255">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="e1e9f-256">`IServiceCollection` bir <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> nesne koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-256">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="e1e9f-257">Aşağıdaki örnek, oluşturma ve ekleme yoluyla bir hizmetin nasıl kaydedileceği gösterilmektedir `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-257">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="e1e9f-258">Yerleşik `Add{LIFETIME}` Yöntemler aynı yaklaşımı kullanır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-258">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="e1e9f-259">Örneğin, bkz. [Addkapsamlıdır kaynak kodu](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-259">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="e1e9f-260">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="e1e9f-260">Constructor injection behavior</span></span>

<span data-ttu-id="e1e9f-261">Hizmetler, kullanılarak çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-261">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="e1e9f-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="e1e9f-263">Kapsayıcıda kayıtlı olmayan nesneler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-263">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="e1e9f-264">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), MVC denetleyicileri ve [model ciltler](xref:mvc/models/model-binding)gibi Framework özellikleriyle kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-264">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="e1e9f-265">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-265">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="e1e9f-266">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-266">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="e1e9f-267">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-267">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="e1e9f-268">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-268">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="e1e9f-269">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="e1e9f-269">Entity Framework contexts</span></span>

<span data-ttu-id="e1e9f-270">Varsayılan olarak, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan, Entity Framework bağlamları [kapsama yaşam süresi](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-270">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="e1e9f-271">Farklı bir yaşam süresi kullanmak için, bir aşırı yükleme kullanarak yaşam süresini belirtin <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-271">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="e1e9f-272">Belirli bir yaşam süresinin Hizmetleri, hizmetin yaşam süresinden kısa olan bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-272">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="e1e9f-273">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-273">Lifetime and registration options</span></span>

<span data-ttu-id="e1e9f-274">Hizmet yaşam süreleri ve kayıt seçenekleri arasındaki farkı göstermek için, bir görevi tanımlayıcı ile bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-274">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="e1e9f-275">Bir işlemin hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde hizmetin aynı ya da farklı örneklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-275">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="e1e9f-276">Aşağıdaki `Operation` sınıf önceki arabirimlerin tümünü uygular.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-276">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="e1e9f-277">`Operation`Oluşturucu BIR GUID oluşturur ve son 4 karakteri `OperationId` özellikte depolar:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-277">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="e1e9f-278">`Startup.ConfigureServices`Yöntemi, `Operation` adlandırılmış yaşam sürelerine göre sınıfının birden çok kaydı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-278">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="e1e9f-279">Örnek uygulama, isteklerin içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-279">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="e1e9f-280">`IndexModel`Ve ara yazılım her `IOperation` tür türü ister, `OperationId` her biri için günlük kaydı:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-280">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="e1e9f-281">Uygulamasına benzer şekilde, `IndexModel` Ara yazılım aynı hizmetleri çözümler:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-281">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="e1e9f-282">Kapsamındaki hizmetlerin yöntemde çözülmesi gerekir `InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-282">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="e1e9f-283">Günlükçü çıktısı şunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-283">The logger output shows:</span></span>

* <span data-ttu-id="e1e9f-284">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-284">*Transient* objects are always different.</span></span> <span data-ttu-id="e1e9f-285">Geçici `OperationId` değer, `IndexModel` Ara yazılım içindeki ve içinde farklıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-285">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="e1e9f-286">*Kapsamlı* nesneler her istek için aynıdır, ancak her istekte farklıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-286">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="e1e9f-287">*Tek* nesneler her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-287">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="e1e9f-288">Günlüğe kaydetme çıkışını azaltmak için, dosyadaki *appsettings.Development.js* "Logging: LogLevel: Microsoft: Error" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-288">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="e1e9f-289">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-289">Call services from main</span></span>

<span data-ttu-id="e1e9f-290"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-290">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="e1e9f-291">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-291">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="e1e9f-292">Aşağıdaki örnek, kapsamındaki hizmete nasıl erişeceğinden `IMyDependency` ve yönteminin içinde nasıl çağrılacağını göstermektedir `WriteMessage` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-292">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="e1e9f-293">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e1e9f-293">Scope validation</span></span>

<span data-ttu-id="e1e9f-294">Uygulama [geliştirme ortamında](xref:fundamentals/environments) çalıştırıldığında ve konağı oluşturmak Için [createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) çağırdığında, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-294">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="e1e9f-295">Kapsamlı hizmetler kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-295">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="e1e9f-296">Kapsamlı hizmetler tekton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-296">Scoped services aren't injected into singletons.</span></span>
* <span data-ttu-id="e1e9f-297">Geçici hizmetler, tekton veya kapsamlı hizmetlere eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-297">Transient services aren't injected into singletons or scoped services.</span></span>

<span data-ttu-id="e1e9f-298">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-298">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="e1e9f-299">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulamanın ömrüne karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-299">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="e1e9f-300">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-300">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="e1e9f-301">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü, uygulama kapandığında yalnızca kök kapsayıcı tarafından atıldığı için etkin bir şekilde tek bir yükseltilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-301">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="e1e9f-302">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-302">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="e1e9f-303">Daha fazla bilgi için bkz. [kapsam doğrulaması](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-303">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="e1e9f-304">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-304">Request Services</span></span>

<span data-ttu-id="e1e9f-305">Bir ASP.NET Core isteği içinde kullanılabilen hizmetler, [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-305">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="e1e9f-306">Bir isteğin içinden hizmetler istendiğinde, hizmetler ve bağımlılıkları `RequestServices` koleksiyonundan çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-306">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="e1e9f-307">Çerçeve, istek başına bir kapsam oluşturur ve `RequestServices` kapsamlı hizmet sağlayıcısını kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-307">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="e1e9f-308">Tüm kapsamlı hizmetler, istek etkin olduğu sürece geçerli olur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-308">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="e1e9f-309">Koleksiyondan Hizmetleri çözümlemek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-309">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="e1e9f-310">Bu, test etmek daha kolay olan sınıfların sonucunu vermez.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-310">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="e1e9f-311">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-311">Design services for dependency injection</span></span>

<span data-ttu-id="e1e9f-312">Bağımlılık ekleme için Hizmetleri tasarlarken:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-312">When designing services for dependency injection:</span></span>

* <span data-ttu-id="e1e9f-313">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-313">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="e1e9f-314">Bunun yerine Singleton hizmetlerini kullanmak için uygulama tasarlayarak genel durum oluşturmamaya özen gösterin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-314">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="e1e9f-315">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-315">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="e1e9f-316">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-316">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="e1e9f-317">Hizmetleri küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-317">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="e1e9f-318">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu, sınıfta çok fazla sorumluluğun olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir işaret olabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-318">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="e1e9f-319">Bazı sorumlulukları yeni sınıflara taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-319">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="e1e9f-320">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-320">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="e1e9f-321">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="e1e9f-321">Disposal of services</span></span>

<span data-ttu-id="e1e9f-322">Kapsayıcı, <xref:System.IDisposable.Dispose%2A> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-322">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="e1e9f-323">Kapsayıcıdan çözümlenen hizmetler hiçbir şekilde geliştirici tarafından atılamaz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-323">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="e1e9f-324">Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı kendiliğinden otomatik olarak atar.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-324">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="e1e9f-325">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-325">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="e1e9f-326">Hata ayıklama konsolu dizin sayfasının her yenilemesinde aşağıdaki çıktıyı gösterir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-326">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="e1e9f-327">Hizmet kapsayıcısı tarafından oluşturulmayan hizmetler</span><span class="sxs-lookup"><span data-stu-id="e1e9f-327">Services not created by the service container</span></span>

<span data-ttu-id="e1e9f-328">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-328">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="e1e9f-329">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-329">In the preceding code:</span></span>

* <span data-ttu-id="e1e9f-330">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-330">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="e1e9f-331">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-331">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="e1e9f-332">Geliştirici, hizmetleri elden atan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-332">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="e1e9f-333">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="e1e9f-333">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="e1e9f-334">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="e1e9f-334">Transient, limited lifetime</span></span>

<span data-ttu-id="e1e9f-335">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-335">**Scenario**</span></span>

<span data-ttu-id="e1e9f-336">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-336">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="e1e9f-337">Örnek, kök kapsamda (kök kapsayıcı) çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-337">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="e1e9f-338">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-338">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="e1e9f-339">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-339">**Solution**</span></span>

<span data-ttu-id="e1e9f-340">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-340">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="e1e9f-341">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-341">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="e1e9f-342">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-342">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="e1e9f-343">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-343">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="e1e9f-344"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcının bağımlılıkları için kapsayıcıyı kullanırken, örneği kapsayıcının dışında örneklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-344">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="e1e9f-345">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="e1e9f-345">Shared instance, limited lifetime</span></span>

<span data-ttu-id="e1e9f-346">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-346">**Scenario**</span></span>

<span data-ttu-id="e1e9f-347">Uygulamanın <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek olması gerekir, ancak <xref:System.IDisposable> örnek sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-347">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="e1e9f-348">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-348">**Solution**</span></span>

<span data-ttu-id="e1e9f-349">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-349">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="e1e9f-350"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-350">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="e1e9f-351"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-351">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="e1e9f-352">Artık gerekli olmadığında kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-352">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="e1e9f-353">Genel IDisposable yönergeleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-353">General IDisposable guidelines</span></span>

* <span data-ttu-id="e1e9f-354"><xref:System.IDisposable>Örnekleri geçici bir yaşam süresine kaydetmez.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-354">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="e1e9f-355">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-355">Use the factory pattern instead.</span></span>
* <span data-ttu-id="e1e9f-356"><xref:System.IDisposable>Kök kapsamda geçici veya kapsamlı bir yaşam süresine sahip örnekleri çözümleyin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-356">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="e1e9f-357">Bunun tek istisnası, uygulamanın oluşturma/yeniden oluşturma/yeniden oluşturma ve uygulama oluşturmasıdır <xref:System.IServiceProvider> , ancak bu ideal bir modeldir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-357">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="e1e9f-358">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-358">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="e1e9f-359"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-359">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="e1e9f-360">Hizmetlerin ömrünü denetlemek için kapsamları kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-360">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="e1e9f-361">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-361">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="e1e9f-362">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="e1e9f-362">Default service container replacement</span></span>

<span data-ttu-id="e1e9f-363">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-363">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="e1e9f-364">Desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-364">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="e1e9f-365">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="e1e9f-365">Property injection</span></span>
* <span data-ttu-id="e1e9f-366">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="e1e9f-366">Injection based on name</span></span>
* <span data-ttu-id="e1e9f-367">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="e1e9f-367">Child containers</span></span>
* <span data-ttu-id="e1e9f-368">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="e1e9f-368">Custom lifetime management</span></span>
* <span data-ttu-id="e1e9f-369">`Func<T>` yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="e1e9f-369">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="e1e9f-370">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="e1e9f-370">Convention-based registration</span></span>

<span data-ttu-id="e1e9f-371">Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-371">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="e1e9f-372">Autofac</span><span class="sxs-lookup"><span data-stu-id="e1e9f-372">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="e1e9f-373">Drıioc</span><span class="sxs-lookup"><span data-stu-id="e1e9f-373">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="e1e9f-374">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="e1e9f-374">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="e1e9f-375">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="e1e9f-375">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="e1e9f-376">E</span><span class="sxs-lookup"><span data-stu-id="e1e9f-376">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="e1e9f-377">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="e1e9f-377">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="e1e9f-378">Unity</span><span class="sxs-lookup"><span data-stu-id="e1e9f-378">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="e1e9f-379">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="e1e9f-379">Thread safety</span></span>

<span data-ttu-id="e1e9f-380">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-380">Create thread-safe singleton services.</span></span> <span data-ttu-id="e1e9f-381">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-381">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="e1e9f-382">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-382">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="e1e9f-383">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından yalnızca bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-383">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="e1e9f-384">Öneriler</span><span class="sxs-lookup"><span data-stu-id="e1e9f-384">Recommendations</span></span>

* <span data-ttu-id="e1e9f-385">`async/await` ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-385">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="e1e9f-386">C# zaman uyumsuz oluşturucuları desteklemediğinden, hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-386">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="e1e9f-387">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-387">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="e1e9f-388">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-388">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="e1e9f-389">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-389">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="e1e9f-390">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-390">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="e1e9f-391">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-391">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="e1e9f-392">Hizmetlere statik erişimi önleyin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-392">Avoid static access to services.</span></span> <span data-ttu-id="e1e9f-393">Örneğin, başka bir yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik bir alan veya özellik olarak yakalanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-393">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="e1e9f-394">Dı fabrikalarını hızlı ve zaman uyumlu tutun.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-394">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="e1e9f-395">*Hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-395">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="e1e9f-396">Örneğin, <xref:System.IServiceProvider.GetService%2A> yerine şunu kullandığınızda bir hizmet örneği elde etme çağrısı yapmayın:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-396">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="e1e9f-397">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-397">**Incorrect:**</span></span>

    ![Yanlış kod](dependency-injection/_static/bad.png)

  <span data-ttu-id="e1e9f-399">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-399">**Correct**:</span></span>

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
* <span data-ttu-id="e1e9f-400">Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-400">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="e1e9f-401">Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-401">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="e1e9f-402">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-402">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="e1e9f-403">İçindeki çağrılardan <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> kaçının `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-403">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="e1e9f-404">Çağırma `BuildServiceProvider` genellikle geliştirici içindeki bir hizmeti çözmek istediğinde oluşur `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-404">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="e1e9f-405">Örneğin, yapılandırmasından yüklendiği durumu göz önünde bulundurun `LoginPath` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-405">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="e1e9f-406">Aşağıdaki yaklaşımdan kaçının:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-406">Avoid the following approach:</span></span>

  ![BuildServiceProvider çağrılırken hatalı kod](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="e1e9f-408">Önceki görüntüde yeşil dalgalı çizgiyi seçtiğinizde `services.BuildServiceProvider` AŞAĞıDAKI ASP0000 uyarısı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-408">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="e1e9f-409">Uygulama kodundan ' BuildServiceProvider ' çağrısı ASP0000, oluşturulan Singleton Hizmetleri ek bir kopyasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-409">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="e1e9f-410">Bağımlılık ekleme hizmetleri gibi alternatifleri, ' configure ' için parametre olarak düşünün.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-410">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="e1e9f-411">Çağırma `BuildServiceProvider` ikinci bir kapsayıcı oluşturur ve bu, parçalanmış tek ton oluşturabilir ve birden çok kapsayıcı genelinde nesne grafiklerine başvuru oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-411">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="e1e9f-412">Almanın doğru yolu, `LoginPath` Seçenekler deseninin, dı için yerleşik desteğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-412">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="e1e9f-413">Atılabilir geçici hizmetler, aktiften çıkarma için kapsayıcı tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-413">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="e1e9f-414">Bu, en üst düzey kapsayıcıdan çözümlenirse bir bellek sızıntısını açabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-414">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="e1e9f-415">Uygulamanın tekton yakalayan kapsamlı hizmetler içermediğinden emin olmak için kapsam doğrulamayı etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-415">Enable scope validation to make sure the app doesn't have scoped services that capture singletons.</span></span> <span data-ttu-id="e1e9f-416">Daha fazla bilgi için bkz. [kapsam doğrulaması](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-416">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="e1e9f-417">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-417">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="e1e9f-418">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-418">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="e1e9f-419">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-419">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="e1e9f-420">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-420">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="e1e9f-421">DI 'de çok kiracılı için önerilen desenler</span><span class="sxs-lookup"><span data-stu-id="e1e9f-421">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="e1e9f-422">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , ASP.NET Core üzerinde modüler, çok kiracılı uygulamalar oluşturmaya yönelik bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-422">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="e1e9f-423">Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-423">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="e1e9f-424">Yalnızca Orchard Core çerçevesini kullanarak, CMS 'ye özgü özelliklerden herhangi biri olmadan modüler ve çok kiracılı uygulamalar oluşturma örnekleri için bkz. [Orchard çekirdek örnekleri](https://github.com/OrchardCMS/OrchardCore.Samples) .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-424">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e1e9f-425">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="e1e9f-425">Framework-provided services</span></span>

<span data-ttu-id="e1e9f-426">`Startup.ConfigureServices`Yöntemi, uygulamanın kullandığı hizmetleri, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-426">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="e1e9f-427">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-427">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="e1e9f-428">ASP.NET Core şablonlarına dayalı uygulamalar için Framework, 250 'den fazla hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-428">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="e1e9f-429">Aşağıdaki tabloda, bu çerçeve kayıtlı hizmetlerinin küçük bir örneği listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-429">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="e1e9f-430">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="e1e9f-430">Service Type</span></span>                                                                                    | <span data-ttu-id="e1e9f-431">Ömür</span><span class="sxs-lookup"><span data-stu-id="e1e9f-431">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="e1e9f-432">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-432">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="e1e9f-433">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-433">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="e1e9f-434">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="e1e9f-435">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-435">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="e1e9f-436">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-436">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="e1e9f-437">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="e1e9f-438">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-438">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="e1e9f-439">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-439">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="e1e9f-440">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="e1e9f-441">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-441">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="e1e9f-442">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-442">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="e1e9f-443">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-443">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="e1e9f-444">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-444">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="e1e9f-445">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-445">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="e1e9f-446">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e1e9f-446">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="e1e9f-447">Dı uygulaması geliştirme için NDC Konferansı desenleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-447">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="e1e9f-448">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="e1e9f-448">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="e1e9f-449">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="e1e9f-449">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="e1e9f-450">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="e1e9f-450">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="e1e9f-451">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="e1e9f-451">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="e1e9f-452">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="e1e9f-452">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e1e9f-453">[Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler) tarafından</span><span class="sxs-lookup"><span data-stu-id="e1e9f-453">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="e1e9f-454">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-454">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="e1e9f-455">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="e1e9f-455">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="e1e9f-456">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1e9f-456">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="e1e9f-457">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="e1e9f-457">Overview of dependency injection</span></span>

<span data-ttu-id="e1e9f-458">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-458">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="e1e9f-459">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-459">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="e1e9f-460">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-460">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="e1e9f-461">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-461">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="e1e9f-462">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-462">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="e1e9f-463">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-463">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="e1e9f-464">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-464">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="e1e9f-465">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-465">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="e1e9f-466">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-466">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="e1e9f-467">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-467">This implementation is difficult to unit test.</span></span> <span data-ttu-id="e1e9f-468">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-468">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="e1e9f-469">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-469">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="e1e9f-470">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-470">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="e1e9f-471">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-471">Registration of the dependency in a service container.</span></span> <span data-ttu-id="e1e9f-472">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-472">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="e1e9f-473">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-473">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="e1e9f-474">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-474">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="e1e9f-475">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-475">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="e1e9f-476">[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-476">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="e1e9f-477">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-477">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="e1e9f-478">`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-478">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="e1e9f-479">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-479">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="e1e9f-480">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-480">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="e1e9f-481">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-481">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="e1e9f-482">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-482">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="e1e9f-483">`IMyDependency` ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-483">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="e1e9f-484">`IMyDependency` kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-484">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e1e9f-485">`ILogger<TCategoryName>` günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-485">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="e1e9f-486">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-486">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="e1e9f-487">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-487">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="e1e9f-488">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-488">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="e1e9f-489">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-489">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="e1e9f-490">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-490">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="e1e9f-491">Örneğin, `services.AddMvc()` hizmet Razor SAYFALARıNı ve MVC gerektir öğesini ekler.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-491">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="e1e9f-492">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-492">We recommended that apps follow this convention.</span></span> <span data-ttu-id="e1e9f-493">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-493">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="e1e9f-494">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-494">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="e1e9f-495">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-495">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="e1e9f-496">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-496">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="e1e9f-497">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-497">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="e1e9f-498">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="e1e9f-498">Services injected into Startup</span></span>

<span data-ttu-id="e1e9f-499">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-499">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="e1e9f-500">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-500">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="e1e9f-501">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-501">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e1e9f-502">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="e1e9f-502">Framework-provided services</span></span>

<span data-ttu-id="e1e9f-503">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-503">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="e1e9f-504">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-504">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="e1e9f-505">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-505">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="e1e9f-506">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-506">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="e1e9f-507">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="e1e9f-507">Service Type</span></span> | <span data-ttu-id="e1e9f-508">Ömür</span><span class="sxs-lookup"><span data-stu-id="e1e9f-508">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="e1e9f-509">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-509">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="e1e9f-510">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-510">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="e1e9f-511">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="e1e9f-512">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="e1e9f-513">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-513">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="e1e9f-514">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="e1e9f-515">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-515">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="e1e9f-516">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-516">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="e1e9f-517">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="e1e9f-518">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="e1e9f-519">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-519">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="e1e9f-520">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-520">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="e1e9f-521">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-521">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="e1e9f-522">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-522">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="e1e9f-523">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="e1e9f-523">Register additional services with extension methods</span></span>

<span data-ttu-id="e1e9f-524">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-524">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="e1e9f-525">Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-525">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="e1e9f-526">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-526">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="e1e9f-527">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-527">Service lifetimes</span></span>

<span data-ttu-id="e1e9f-528">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-528">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="e1e9f-529">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-529">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="e1e9f-530">Larsa</span><span class="sxs-lookup"><span data-stu-id="e1e9f-530">Transient</span></span>

<span data-ttu-id="e1e9f-531">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-531">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="e1e9f-532">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-532">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="e1e9f-533">İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-533">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="e1e9f-534">Yayıl</span><span class="sxs-lookup"><span data-stu-id="e1e9f-534">Scoped</span></span>

<span data-ttu-id="e1e9f-535">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-535">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="e1e9f-536">İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-536">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="e1e9f-537">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-537">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="e1e9f-538">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-538">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="e1e9f-539">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-539">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="e1e9f-540">Adet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-540">Singleton</span></span>

<span data-ttu-id="e1e9f-541">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-541">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="e1e9f-542">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-542">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="e1e9f-543">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-543">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="e1e9f-544">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-544">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="e1e9f-545">İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-545">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="e1e9f-546">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-546">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="e1e9f-547">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-547">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="e1e9f-548">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-548">Service registration methods</span></span>

<span data-ttu-id="e1e9f-549">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-549">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="e1e9f-550">Yöntem</span><span class="sxs-lookup"><span data-stu-id="e1e9f-550">Method</span></span> | <span data-ttu-id="e1e9f-551">Automatic</span><span class="sxs-lookup"><span data-stu-id="e1e9f-551">Automatic</span></span><br><span data-ttu-id="e1e9f-552">object</span><span class="sxs-lookup"><span data-stu-id="e1e9f-552">object</span></span><br><span data-ttu-id="e1e9f-553">elden</span><span class="sxs-lookup"><span data-stu-id="e1e9f-553">disposal</span></span> | <span data-ttu-id="e1e9f-554">Birden çok</span><span class="sxs-lookup"><span data-stu-id="e1e9f-554">Multiple</span></span><br><span data-ttu-id="e1e9f-555">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="e1e9f-555">implementations</span></span> | <span data-ttu-id="e1e9f-556">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-556">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="e1e9f-557">Örnek:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-557">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="e1e9f-558">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-558">Yes</span></span> | <span data-ttu-id="e1e9f-559">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-559">Yes</span></span> | <span data-ttu-id="e1e9f-560">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-560">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="e1e9f-561">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-561">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="e1e9f-562">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-562">Yes</span></span> | <span data-ttu-id="e1e9f-563">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-563">Yes</span></span> | <span data-ttu-id="e1e9f-564">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-564">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="e1e9f-565">Örnek:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-565">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="e1e9f-566">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-566">Yes</span></span> | <span data-ttu-id="e1e9f-567">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-567">No</span></span> | <span data-ttu-id="e1e9f-568">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-568">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="e1e9f-569">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-569">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="e1e9f-570">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-570">No</span></span> | <span data-ttu-id="e1e9f-571">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-571">Yes</span></span> | <span data-ttu-id="e1e9f-572">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-572">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="e1e9f-573">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-573">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="e1e9f-574">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-574">No</span></span> | <span data-ttu-id="e1e9f-575">Hayır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-575">No</span></span> | <span data-ttu-id="e1e9f-576">Evet</span><span class="sxs-lookup"><span data-stu-id="e1e9f-576">Yes</span></span> |

<span data-ttu-id="e1e9f-577">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-577">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="e1e9f-578">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-578">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="e1e9f-579">`TryAdd{LIFETIME}` Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-579">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="e1e9f-580">Aşağıdaki örnekte, ilk satır `MyDependency` için kaydedilir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-580">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="e1e9f-581">`IMyDependency`Zaten kayıtlı bir uygulamaya sahip olduğundan ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-581">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="e1e9f-582">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-582">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="e1e9f-583">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-583">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="e1e9f-584">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-584">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="e1e9f-585">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-585">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="e1e9f-586">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-586">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="e1e9f-587">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-587">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="e1e9f-588">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-588">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="e1e9f-589">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-589">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="e1e9f-590">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="e1e9f-590">Constructor injection behavior</span></span>

<span data-ttu-id="e1e9f-591">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-591">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="e1e9f-592"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-592"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="e1e9f-593">`ActivatorUtilities` Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-593">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="e1e9f-594">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-594">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="e1e9f-595">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-595">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="e1e9f-596">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-596">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="e1e9f-597">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-597">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="e1e9f-598">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="e1e9f-598">Entity Framework contexts</span></span>

<span data-ttu-id="e1e9f-599">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-599">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="e1e9f-600">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-600">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="e1e9f-601">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-601">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="e1e9f-602">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-602">Lifetime and registration options</span></span>

<span data-ttu-id="e1e9f-603">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-603">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="e1e9f-604">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-604">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="e1e9f-605">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-605">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="e1e9f-606">`Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-606">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="e1e9f-607">, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-607">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="e1e9f-608">`OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-608">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="e1e9f-609">Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-609">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="e1e9f-610">`OperationService` sınıfının yeni bir örneğini alır `IOperationTransient` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-610">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="e1e9f-611">Yeni örnek farklı bir şekilde oluşturur `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-611">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="e1e9f-612">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-612">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="e1e9f-613">İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-613">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="e1e9f-614">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-614">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="e1e9f-615">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-615">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="e1e9f-616">`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-616">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="e1e9f-617">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-617">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="e1e9f-618">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-618">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="e1e9f-619">Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-619">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="e1e9f-620">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-620">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="e1e9f-621">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-621">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="e1e9f-622">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-622">**First request:**</span></span>

<span data-ttu-id="e1e9f-623">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-623">Controller operations:</span></span>

<span data-ttu-id="e1e9f-624">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="e1e9f-624">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="e1e9f-625">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e1e9f-625">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e1e9f-626">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e1e9f-626">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e1e9f-627">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e1e9f-627">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e1e9f-628">`OperationService` operasyonları</span><span class="sxs-lookup"><span data-stu-id="e1e9f-628">`OperationService` operations:</span></span>

<span data-ttu-id="e1e9f-629">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="e1e9f-629">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="e1e9f-630">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e1e9f-630">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e1e9f-631">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e1e9f-631">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e1e9f-632">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e1e9f-632">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e1e9f-633">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-633">**Second request:**</span></span>

<span data-ttu-id="e1e9f-634">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-634">Controller operations:</span></span>

<span data-ttu-id="e1e9f-635">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="e1e9f-635">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="e1e9f-636">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e1e9f-636">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e1e9f-637">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e1e9f-637">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e1e9f-638">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e1e9f-638">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e1e9f-639">`OperationService` operasyonları</span><span class="sxs-lookup"><span data-stu-id="e1e9f-639">`OperationService` operations:</span></span>

<span data-ttu-id="e1e9f-640">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="e1e9f-640">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="e1e9f-641">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e1e9f-641">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e1e9f-642">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e1e9f-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e1e9f-643">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e1e9f-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e1e9f-644">`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-644">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="e1e9f-645">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-645">*Transient* objects are always different.</span></span> <span data-ttu-id="e1e9f-646">Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-646">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="e1e9f-647">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-647">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="e1e9f-648">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-648">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="e1e9f-649">*Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-649">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="e1e9f-650">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="e1e9f-650">Call services from main</span></span>

<span data-ttu-id="e1e9f-651"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-651">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="e1e9f-652">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-652">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="e1e9f-653">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="e1e9f-653">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="e1e9f-654">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e1e9f-654">Scope validation</span></span>

<span data-ttu-id="e1e9f-655">Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-655">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="e1e9f-656">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-656">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="e1e9f-657">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-657">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="e1e9f-658">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-658">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="e1e9f-659">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-659">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="e1e9f-660">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-660">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="e1e9f-661">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-661">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="e1e9f-662">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-662">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="e1e9f-663">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-663">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="e1e9f-664">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-664">Request Services</span></span>

<span data-ttu-id="e1e9f-665">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-665">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="e1e9f-666">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-666">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="e1e9f-667">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-667">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="e1e9f-668">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-668">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="e1e9f-669">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-669">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="e1e9f-670">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-670">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="e1e9f-671">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-671">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="e1e9f-672">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="e1e9f-672">Design services for dependency injection</span></span>

<span data-ttu-id="e1e9f-673">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-673">Best practices are to:</span></span>

* <span data-ttu-id="e1e9f-674">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-674">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="e1e9f-675">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-675">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="e1e9f-676">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-676">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="e1e9f-677">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-677">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="e1e9f-678">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-678">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="e1e9f-679">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-679">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="e1e9f-680">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-680">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="e1e9f-681">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-681">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="e1e9f-682">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-682">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="e1e9f-683">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-683">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="e1e9f-684">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="e1e9f-684">Disposal of services</span></span>

<span data-ttu-id="e1e9f-685">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-685">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="e1e9f-686">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-686">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="e1e9f-687">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-687">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="e1e9f-688">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-688">In the following example:</span></span>

* <span data-ttu-id="e1e9f-689">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-689">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="e1e9f-690">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-690">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="e1e9f-691">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-691">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="e1e9f-692">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-692">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="e1e9f-693">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="e1e9f-693">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="e1e9f-694">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="e1e9f-694">Transient, limited lifetime</span></span>

<span data-ttu-id="e1e9f-695">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-695">**Scenario**</span></span>

<span data-ttu-id="e1e9f-696">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-696">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="e1e9f-697">Örnek, kök kapsamda çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-697">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="e1e9f-698">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-698">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="e1e9f-699">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-699">**Solution**</span></span>

<span data-ttu-id="e1e9f-700">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-700">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="e1e9f-701">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-701">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="e1e9f-702">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-702">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="e1e9f-703">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-703">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="e1e9f-704"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-704">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="e1e9f-705">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="e1e9f-705">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="e1e9f-706">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-706">**Scenario**</span></span>

<span data-ttu-id="e1e9f-707">Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-707">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="e1e9f-708">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-708">**Solution**</span></span>

<span data-ttu-id="e1e9f-709">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-709">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="e1e9f-710"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-710">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="e1e9f-711"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-711">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="e1e9f-712">Yaşam süresi sona erdirmek için kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-712">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="e1e9f-713">Genel Yönergeler</span><span class="sxs-lookup"><span data-stu-id="e1e9f-713">General Guidelines</span></span>

* <span data-ttu-id="e1e9f-714"><xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-714">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="e1e9f-715">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-715">Use the factory pattern instead.</span></span>
* <span data-ttu-id="e1e9f-716">Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-716">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="e1e9f-717">Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-717">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="e1e9f-718">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-718">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="e1e9f-719"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-719">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="e1e9f-720">Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-720">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="e1e9f-721">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-721">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="e1e9f-722">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="e1e9f-722">Default service container replacement</span></span>

<span data-ttu-id="e1e9f-723">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-723">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="e1e9f-724">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-724">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="e1e9f-725">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="e1e9f-725">Property injection</span></span>
* <span data-ttu-id="e1e9f-726">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="e1e9f-726">Injection based on name</span></span>
* <span data-ttu-id="e1e9f-727">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="e1e9f-727">Child containers</span></span>
* <span data-ttu-id="e1e9f-728">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="e1e9f-728">Custom lifetime management</span></span>
* <span data-ttu-id="e1e9f-729">`Func<T>` yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="e1e9f-729">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="e1e9f-730">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="e1e9f-730">Convention-based registration</span></span>

<span data-ttu-id="e1e9f-731">Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-731">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="e1e9f-732">Autofac</span><span class="sxs-lookup"><span data-stu-id="e1e9f-732">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="e1e9f-733">Drıioc</span><span class="sxs-lookup"><span data-stu-id="e1e9f-733">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="e1e9f-734">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="e1e9f-734">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="e1e9f-735">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="e1e9f-735">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="e1e9f-736">E</span><span class="sxs-lookup"><span data-stu-id="e1e9f-736">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="e1e9f-737">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="e1e9f-737">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="e1e9f-738">Unity</span><span class="sxs-lookup"><span data-stu-id="e1e9f-738">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="e1e9f-739">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="e1e9f-739">Thread safety</span></span>

<span data-ttu-id="e1e9f-740">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-740">Create thread-safe singleton services.</span></span> <span data-ttu-id="e1e9f-741">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-741">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="e1e9f-742">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-742">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="e1e9f-743">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-743">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="e1e9f-744">Öneriler</span><span class="sxs-lookup"><span data-stu-id="e1e9f-744">Recommendations</span></span>

* <span data-ttu-id="e1e9f-745">`async/await` ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-745">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="e1e9f-746">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-746">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="e1e9f-747">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-747">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="e1e9f-748">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-748">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="e1e9f-749">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-749">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="e1e9f-750">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-750">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="e1e9f-751">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-751">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="e1e9f-752">Hizmetlere statik erişimi önleyin.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-752">Avoid static access to services.</span></span> <span data-ttu-id="e1e9f-753">Örneğin, bir başka yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik olarak yazılanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-753">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="e1e9f-754">[Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-754">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="e1e9f-755"><xref:System.IServiceProvider.GetService*>Bunun yerine, şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-755">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="e1e9f-756">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="e1e9f-756">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="e1e9f-757">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="e1e9f-757">**Correct**:</span></span>

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

* <span data-ttu-id="e1e9f-758">Kullanarak çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-758">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="e1e9f-759">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="e1e9f-759">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="e1e9f-760">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-760">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="e1e9f-761">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-761">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="e1e9f-762">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="e1e9f-762">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="e1e9f-763">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e1e9f-763">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1e9f-764">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e1e9f-764">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="e1e9f-765">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="e1e9f-765">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="e1e9f-766">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="e1e9f-766">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="e1e9f-767">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="e1e9f-767">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="e1e9f-768">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="e1e9f-768">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="e1e9f-769">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="e1e9f-769">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
