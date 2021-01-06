---
title: ASP.NET Core bağımlılık ekleme
author: rick-anderson
description: ASP.NET Core bağımlılık ekleme ve nasıl kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 3f7cce475b5c7b0fcbb93644b2c39acd637a6f9d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94595486"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="88be0-103">ASP.NET Core bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="88be0-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88be0-104">[Kirk Larkabağı](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="88be0-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="88be0-105">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="88be0-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="88be0-106">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="88be0-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="88be0-107">Web Apps dışındaki uygulamalarda bağımlılık ekleme hakkında daha fazla bilgi için bkz. [.net 'e bağımlılık ekleme](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="88be0-107">For information on using dependency injection in applications other than web apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="88be0-108">Seçeneklerin bağımlılığı ekleme hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/options> ..</span><span class="sxs-lookup"><span data-stu-id="88be0-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="88be0-109">Bu konuda ASP.NET Core bağımlılık ekleme hakkında bilgi verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="88be0-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="88be0-110">Bağımlılık ekleme 'yi kullanmayla ilgili birincil belgeler, [.net ' de bağımlılık ekleme](/dotnet/core/extensions/dependency-injection)' de yer alır.</span><span class="sxs-lookup"><span data-stu-id="88be0-110">The primary documentation on using dependency injection is contained in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="88be0-111">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="88be0-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="88be0-112">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="88be0-112">Overview of dependency injection</span></span>

<span data-ttu-id="88be0-113">*Bağımlılık* , başka bir nesnenin bağımlı olduğu bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="88be0-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="88be0-114">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` diğer sınıfların bağımlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="88be0-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="88be0-115">Bir sınıf, `MyDependency` yöntemini kullanmak için sınıfının bir örneğini oluşturabilir `WriteMessage` .</span><span class="sxs-lookup"><span data-stu-id="88be0-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="88be0-116">Aşağıdaki örnekte, `MyDependency` sınıfı sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="88be0-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="88be0-117">Sınıfı oluşturur ve doğrudan `MyDependency` sınıfa bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="88be0-118">Önceki örnekte olduğu gibi kod bağımlılıkları sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="88be0-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="88be0-119">`MyDependency`Farklı bir uygulamayla değiştirmek için, `IndexModel` sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="88be0-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="88be0-120">`MyDependency`Bağımlılıkları varsa, sınıf tarafından da yapılandırılmalıdır `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="88be0-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="88be0-121">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="88be0-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="88be0-122">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="88be0-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="88be0-123">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="88be0-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="88be0-124">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="88be0-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="88be0-125">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="88be0-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="88be0-126">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="88be0-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="88be0-127">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="88be0-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="88be0-128">Hizmetler genellikle uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="88be0-129">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="88be0-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="88be0-130">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="88be0-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="88be0-131">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirimi `WriteMessage` yöntemini tanımlar:</span><span class="sxs-lookup"><span data-stu-id="88be0-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="88be0-132">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="88be0-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="88be0-133">Örnek uygulama, `IMyDependency` hizmeti somut tür ile kaydeder `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="88be0-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="88be0-134"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Yöntemi, hizmeti tek bir isteğin ömrü olan kapsamlı bir yaşam süresine kaydeder.</span><span class="sxs-lookup"><span data-stu-id="88be0-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="88be0-135">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="88be0-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="88be0-136">Örnek uygulamada, `IMyDependency` hizmet istenir ve yöntemi çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="88be0-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="88be0-137">DI modelini kullanarak denetleyici:</span><span class="sxs-lookup"><span data-stu-id="88be0-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="88be0-138">Somut türü kullanmaz `MyDependency` , yalnızca `IMyDependency` uyguladığı arabirim.</span><span class="sxs-lookup"><span data-stu-id="88be0-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="88be0-139">Bu, denetleyicinin, denetleyiciyi değiştirmeden kullandığı uygulamayı değiştirmeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="88be0-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="88be0-140">Bir örneği oluşturmaz `MyDependency` , bu, dı kapsayıcısı tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="88be0-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="88be0-141">Arabirim uygulanması, `IMyDependency` yerleşik günlük API 'si kullanılarak artırılabilir:</span><span class="sxs-lookup"><span data-stu-id="88be0-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="88be0-142">Updated `ConfigureServices` yöntemi yeni `IMyDependency` uygulamayı kaydeder:</span><span class="sxs-lookup"><span data-stu-id="88be0-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="88be0-143">`MyDependency2`<xref:Microsoft.Extensions.Logging.ILogger%601>, oluşturucuda istediği öğesine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="88be0-144">`ILogger<TCategoryName>`[Framework tarafından sağlanmış bir hizmettir](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="88be0-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="88be0-145">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="88be0-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="88be0-146">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="88be0-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="88be0-147">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="88be0-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="88be0-148">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği* veya *nesne grafiği* olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="88be0-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="88be0-149">Kapsayıcı, `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="88be0-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="88be0-150">Bağımlılık ekleme terminolojisi ' nde bir hizmet:</span><span class="sxs-lookup"><span data-stu-id="88be0-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="88be0-151">Genellikle hizmet gibi diğer nesnelere hizmet sağlayan bir nesnedir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="88be0-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="88be0-152">Bir Web hizmetiyle ilgili değildir, ancak hizmet bir Web hizmeti kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="88be0-153">Çerçeve, güçlü bir [günlük](xref:fundamentals/logging/index) sistemi sağlar.</span><span class="sxs-lookup"><span data-stu-id="88be0-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="88be0-154">`IMyDependency`Yukarıdaki örneklerde gösterilen uygulamalar, günlüğü uygulamamak için değil temel dı 'yi göstermek için yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="88be0-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="88be0-155">Çoğu uygulamanın Günlükçüler yazması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88be0-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="88be0-156">Aşağıdaki kod, bir hizmetin kaydedilmesini gerektirmeyen varsayılan günlük kaydını kullanmayı göstermektedir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="88be0-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="88be0-157">Yukarıdaki kodu kullanarak, `ConfigureServices` Framework tarafından [günlüğe kaydetme](xref:fundamentals/logging/index) sağlandığı için güncelleştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88be0-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="88be0-158">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="88be0-158">Services injected into Startup</span></span>

<span data-ttu-id="88be0-159">Hizmetler `Startup` oluşturucuya ve `Startup.Configure` metoduna eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="88be0-160">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmetler eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="88be0-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="88be0-161">Dı kapsayıcısına kayıtlı tüm hizmetler `Startup.Configure` metoduna eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="88be0-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="88be0-162">Daha fazla bilgi için, bkz <xref:fundamentals/startup> . ve [erişim yapılandırması başlangıç](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="88be0-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="88be0-163">Uzantı yöntemleriyle hizmet gruplarını kaydet</span><span class="sxs-lookup"><span data-stu-id="88be0-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="88be0-164">ASP.NET Core Framework, bir grup ilgili hizmeti kaydetmek için bir kural kullanır.</span><span class="sxs-lookup"><span data-stu-id="88be0-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="88be0-165">Kural, `Add{GROUP_NAME}` bir Framework özelliği için gereken tüm hizmetleri kaydetmek için tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="88be0-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="88be0-166">Örneğin, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> genişletme YÖNTEMI MVC denetleyicileri için gereken hizmetleri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="88be0-166">For example, the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="88be0-167">Aşağıdaki kod, Razor bireysel kullanıcı hesapları kullanılarak sayfalar şablonu tarafından oluşturulur ve uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemeyi gösterir <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ve <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="88be0-167">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="88be0-168">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="88be0-168">Service lifetimes</span></span>

<span data-ttu-id="88be0-169">Bkz. [.net 'Te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) içindeki [hizmet yaşam süreleri](/dotnet/core/extensions/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="88be0-169">See [Service lifetimes](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="88be0-170">Ara yazılım kapsamındaki hizmetleri kullanmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="88be0-170">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="88be0-171">Hizmeti, ara yazılımı `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="88be0-171">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="88be0-172">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) kullanılması, kapsamlı hizmeti tek bir gibi davranmaya zordığı için bir çalışma zamanı özel durumu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="88be0-172">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="88be0-173">[Ömür ve kayıt seçenekleri](#lifetime-and-registration-options) bölümündeki örnek, `InvokeAsync` yaklaşımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="88be0-173">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="88be0-174">[Fabrika tabanlı ara yazılım](xref:fundamentals/middleware/extensibility)kullanın.</span><span class="sxs-lookup"><span data-stu-id="88be0-174">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="88be0-175">Bu yaklaşım kullanılarak kaydedilen ara yazılım, kapsamlı hizmetlerin ara yazılım yöntemine eklenmiş olmasına olanak tanıyan istemci isteği (bağlantı) başına etkinleştirilir `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="88be0-175">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="88be0-176">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="88be0-176">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="88be0-177">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="88be0-177">Service registration methods</span></span>

<span data-ttu-id="88be0-178">[.Net ' te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) içindeki [hizmet kayıt yöntemlerine](/dotnet/core/extensions/dependency-injection#service-registration-methods) bakın</span><span class="sxs-lookup"><span data-stu-id="88be0-178">See [Service registration methods](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

 <span data-ttu-id="88be0-179">[Test için izleme türleri](xref:test/integration-tests#inject-mock-services)olduğunda birden çok uygulama kullanılması yaygındır.</span><span class="sxs-lookup"><span data-stu-id="88be0-179">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="88be0-180">Hizmeti yalnızca bir uygulama türüyle kaydetmek, bu hizmeti aynı uygulama ve hizmet türüyle kaydetmeye eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="88be0-180">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="88be0-181">Bu, bir hizmetin birden çok uygulamasının açık bir hizmet türü kullanmayan yöntemler kullanılarak kaydedilamamasının nedenleridir.</span><span class="sxs-lookup"><span data-stu-id="88be0-181">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="88be0-182">Bu yöntemler bir hizmetin birden fazla *örneğini* kaydedebilir, ancak hepsi aynı *uygulama* türüne sahip olur.</span><span class="sxs-lookup"><span data-stu-id="88be0-182">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="88be0-183">Yukarıdaki hizmet kayıt yöntemlerinden herhangi biri aynı hizmet türünün birden çok hizmet örneğini kaydetmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-183">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="88be0-184">Aşağıdaki örnekte, `AddSingleton` hizmet türü olarak ile iki kez çağırılır `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="88be0-184">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="88be0-185">İçin ikinci çağrı, `AddSingleton` olarak çözümlendikten önceki bir öncekini geçersiz kılar `IMyDependency` ve aracılığıyla birden çok hizmet çözümlendiğinde bir öncekini ekler `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="88be0-185">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="88be0-186">Hizmetler, ile çözümlendiklerinde kaydedildikleri sırada görüntülenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="88be0-186">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a><span data-ttu-id="88be0-187">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="88be0-187">Constructor injection behavior</span></span>

<span data-ttu-id="88be0-188">[.Net ' te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) bölümüne bkz. [Oluşturucu Ekleme davranışı](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)</span><span class="sxs-lookup"><span data-stu-id="88be0-188">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="88be0-189">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="88be0-189">Entity Framework contexts</span></span>

<span data-ttu-id="88be0-190">Varsayılan olarak, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan, Entity Framework bağlamları [kapsama yaşam süresi](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="88be0-190">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="88be0-191">Farklı bir yaşam süresi kullanmak için, bir aşırı yükleme kullanarak yaşam süresini belirtin <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> .</span><span class="sxs-lookup"><span data-stu-id="88be0-191">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="88be0-192">Belirli bir yaşam süresinin Hizmetleri, hizmetin yaşam süresinden kısa olan bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-192">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="88be0-193">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="88be0-193">Lifetime and registration options</span></span>

<span data-ttu-id="88be0-194">Hizmet yaşam süreleri ve kayıt seçenekleri arasındaki farkı göstermek için, bir görevi tanımlayıcı ile bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="88be0-194">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="88be0-195">Bir işlemin hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde hizmetin aynı ya da farklı örneklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="88be0-195">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="88be0-196">Aşağıdaki `Operation` sınıf önceki arabirimlerin tümünü uygular.</span><span class="sxs-lookup"><span data-stu-id="88be0-196">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="88be0-197">`Operation`Oluşturucu BIR GUID oluşturur ve son 4 karakteri `OperationId` özellikte depolar:</span><span class="sxs-lookup"><span data-stu-id="88be0-197">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="88be0-198">`Startup.ConfigureServices`Yöntemi, `Operation` adlandırılmış yaşam sürelerine göre sınıfının birden çok kaydı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="88be0-198">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="88be0-199">Örnek uygulama, isteklerin içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="88be0-199">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="88be0-200">`IndexModel`Ve ara yazılım her `IOperation` tür türü ister, `OperationId` her biri için günlük kaydı:</span><span class="sxs-lookup"><span data-stu-id="88be0-200">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="88be0-201">Uygulamasına benzer şekilde, `IndexModel` Ara yazılım aynı hizmetleri çözümler:</span><span class="sxs-lookup"><span data-stu-id="88be0-201">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="88be0-202">Kapsamındaki hizmetlerin yöntemde çözülmesi gerekir `InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="88be0-202">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="88be0-203">Günlükçü çıktısı şunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="88be0-203">The logger output shows:</span></span>

* <span data-ttu-id="88be0-204">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-204">*Transient* objects are always different.</span></span> <span data-ttu-id="88be0-205">Geçici `OperationId` değer, `IndexModel` Ara yazılım içindeki ve içinde farklıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-205">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="88be0-206">*Kapsamlı* nesneler her istek için aynıdır, ancak her istekte farklıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-206">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="88be0-207">*Tek* nesneler her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-207">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="88be0-208">Günlüğe kaydetme çıkışını azaltmak için, dosyadaki *appsettings.Development.js* "Logging: LogLevel: Microsoft: Error" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="88be0-208">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="88be0-209">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="88be0-209">Call services from main</span></span>

<span data-ttu-id="88be0-210"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="88be0-210">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="88be0-211">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-211">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="88be0-212">Aşağıdaki örnek, kapsamındaki hizmete nasıl erişeceğinden `IMyDependency` ve yönteminin içinde nasıl çağrılacağını göstermektedir `WriteMessage` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="88be0-212">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="88be0-213">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="88be0-213">Scope validation</span></span>

<span data-ttu-id="88be0-214">[.Net ' te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) bölümüne bkz. [Oluşturucu Ekleme davranışı](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)</span><span class="sxs-lookup"><span data-stu-id="88be0-214">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="88be0-215">Daha fazla bilgi için bkz. [kapsam doğrulaması](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="88be0-215">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="88be0-216">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="88be0-216">Request Services</span></span>

<span data-ttu-id="88be0-217">Bir ASP.NET Core isteği içinde kullanılabilen hizmetler, [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="88be0-217">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="88be0-218">Bir isteğin içinden hizmetler istendiğinde, hizmetler ve bağımlılıkları `RequestServices` koleksiyonundan çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="88be0-218">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="88be0-219">Çerçeve, istek başına bir kapsam oluşturur ve `RequestServices` kapsamlı hizmet sağlayıcısını kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="88be0-219">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="88be0-220">Tüm kapsamlı hizmetler, istek etkin olduğu sürece geçerli olur.</span><span class="sxs-lookup"><span data-stu-id="88be0-220">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="88be0-221">Koleksiyondan Hizmetleri çözümlemek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="88be0-221">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="88be0-222">Bu, test etmek daha kolay olan sınıfların sonucunu vermez.</span><span class="sxs-lookup"><span data-stu-id="88be0-222">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="88be0-223">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="88be0-223">Design services for dependency injection</span></span>

<span data-ttu-id="88be0-224">Bağımlılık ekleme için Hizmetleri tasarlarken:</span><span class="sxs-lookup"><span data-stu-id="88be0-224">When designing services for dependency injection:</span></span>

* <span data-ttu-id="88be0-225">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="88be0-225">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="88be0-226">Bunun yerine Singleton hizmetlerini kullanmak için uygulama tasarlayarak genel durum oluşturmamaya özen gösterin.</span><span class="sxs-lookup"><span data-stu-id="88be0-226">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="88be0-227">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="88be0-227">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="88be0-228">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="88be0-228">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="88be0-229">Hizmetleri küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="88be0-229">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="88be0-230">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu, sınıfta çok fazla sorumluluğun olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir işaret olabilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-230">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="88be0-231">Bazı sorumlulukları yeni sınıflara taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="88be0-231">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="88be0-232">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="88be0-232">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="88be0-233">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="88be0-233">Disposal of services</span></span>

<span data-ttu-id="88be0-234">Kapsayıcı, <xref:System.IDisposable.Dispose%2A> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="88be0-234">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="88be0-235">Kapsayıcıdan çözümlenen hizmetler hiçbir şekilde geliştirici tarafından atılamaz.</span><span class="sxs-lookup"><span data-stu-id="88be0-235">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="88be0-236">Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı kendiliğinden otomatik olarak atar.</span><span class="sxs-lookup"><span data-stu-id="88be0-236">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="88be0-237">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="88be0-237">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="88be0-238">Hata ayıklama konsolu dizin sayfasının her yenilemesinde aşağıdaki çıktıyı gösterir:</span><span class="sxs-lookup"><span data-stu-id="88be0-238">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="88be0-239">Hizmet kapsayıcısı tarafından oluşturulmayan hizmetler</span><span class="sxs-lookup"><span data-stu-id="88be0-239">Services not created by the service container</span></span>

<span data-ttu-id="88be0-240">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="88be0-240">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="88be0-241">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="88be0-241">In the preceding code:</span></span>

* <span data-ttu-id="88be0-242">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="88be0-242">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="88be0-243">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="88be0-243">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="88be0-244">Geliştirici, hizmetleri elden atan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="88be0-244">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="88be0-245">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="88be0-245">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="88be0-246">[.Net ' te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) [konusunda geçici ve paylaşılan örnek için IDisposable Guidance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) bölümüne bakın</span><span class="sxs-lookup"><span data-stu-id="88be0-246">See [IDisposable guidance for Transient and shared instance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="88be0-247">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="88be0-247">Default service container replacement</span></span>

<span data-ttu-id="88be0-248">Bkz. [.net 'Te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) için [varsayılan hizmet kapsayıcısı değiştirme](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement)</span><span class="sxs-lookup"><span data-stu-id="88be0-248">See [Default service container replacement](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="recommendations"></a><span data-ttu-id="88be0-249">Öneriler</span><span class="sxs-lookup"><span data-stu-id="88be0-249">Recommendations</span></span>

<span data-ttu-id="88be0-250">[.Net ' te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) içindeki [önerilere](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) bakın</span><span class="sxs-lookup"><span data-stu-id="88be0-250">See [Recommendations](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

* <span data-ttu-id="88be0-251">*Hizmet bulucu deseninin* kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="88be0-251">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="88be0-252">Örneğin, <xref:System.IServiceProvider.GetService%2A> yerine şunu kullandığınızda bir hizmet örneği elde etme çağrısı yapmayın:</span><span class="sxs-lookup"><span data-stu-id="88be0-252">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="88be0-253">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="88be0-253">**Incorrect:**</span></span>

    ![Yanlış kod](dependency-injection/_static/bad.png)

  <span data-ttu-id="88be0-255">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="88be0-255">**Correct**:</span></span>

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
* <span data-ttu-id="88be0-256">Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme.</span><span class="sxs-lookup"><span data-stu-id="88be0-256">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="88be0-257">Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="88be0-257">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="88be0-258">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="88be0-258">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="88be0-259">İçindeki çağrılardan <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> kaçının `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="88be0-259">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="88be0-260">Çağırma `BuildServiceProvider` genellikle geliştirici içindeki bir hizmeti çözmek istediğinde oluşur `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="88be0-260">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="88be0-261">Örneğin, yapılandırmasından yüklendiği durumu göz önünde bulundurun `LoginPath` .</span><span class="sxs-lookup"><span data-stu-id="88be0-261">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="88be0-262">Aşağıdaki yaklaşımdan kaçının:</span><span class="sxs-lookup"><span data-stu-id="88be0-262">Avoid the following approach:</span></span>

  ![BuildServiceProvider çağrılırken hatalı kod](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="88be0-264">Önceki görüntüde yeşil dalgalı çizgiyi seçtiğinizde `services.BuildServiceProvider` AŞAĞıDAKI ASP0000 uyarısı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="88be0-264">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="88be0-265">Uygulama kodundan ' BuildServiceProvider ' çağrısı ASP0000, oluşturulan Singleton Hizmetleri ek bir kopyasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="88be0-265">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="88be0-266">Bağımlılık ekleme hizmetleri gibi alternatifleri, ' configure ' için parametre olarak düşünün.</span><span class="sxs-lookup"><span data-stu-id="88be0-266">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="88be0-267">Çağırma `BuildServiceProvider` ikinci bir kapsayıcı oluşturur ve bu, parçalanmış tek ton oluşturabilir ve birden çok kapsayıcı genelinde nesne grafiklerine başvuru oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-267">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="88be0-268">Almanın doğru yolu, `LoginPath` Seçenekler deseninin, dı için yerleşik desteğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="88be0-268">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="88be0-269">Atılabilir geçici hizmetler, aktiften çıkarma için kapsayıcı tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="88be0-269">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="88be0-270">Bu, en üst düzey kapsayıcıdan çözümlenirse bir bellek sızıntısını açabilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-270">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="88be0-271">Uygulamanın kapsamlı hizmetleri yakalayan tekton içermediğinden emin olmak için kapsam doğrulamayı etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="88be0-271">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="88be0-272">Daha fazla bilgi için bkz. [kapsam doğrulaması](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="88be0-272">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="88be0-273">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="88be0-273">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="88be0-274">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="88be0-274">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="88be0-275">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="88be0-275">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="88be0-276">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="88be0-276">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="88be0-277">DI 'de çok kiracılı için önerilen desenler</span><span class="sxs-lookup"><span data-stu-id="88be0-277">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="88be0-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , ASP.NET Core üzerinde modüler, çok kiracılı uygulamalar oluşturmaya yönelik bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="88be0-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="88be0-279">Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="88be0-279">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="88be0-280">Yalnızca Orchard Core çerçevesini kullanarak, CMS 'ye özgü özelliklerden herhangi biri olmadan modüler ve çok kiracılı uygulamalar oluşturma örnekleri için bkz. [Orchard çekirdek örnekleri](https://github.com/OrchardCMS/OrchardCore.Samples) .</span><span class="sxs-lookup"><span data-stu-id="88be0-280">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="88be0-281">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="88be0-281">Framework-provided services</span></span>

<span data-ttu-id="88be0-282">`Startup.ConfigureServices`Yöntemi, uygulamanın kullandığı hizmetleri, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere kaydeder.</span><span class="sxs-lookup"><span data-stu-id="88be0-282">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="88be0-283">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="88be0-283">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="88be0-284">ASP.NET Core şablonlarına dayalı uygulamalar için Framework, 250 'den fazla hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="88be0-284">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="88be0-285">Aşağıdaki tabloda, bu çerçeve kayıtlı hizmetlerinin küçük bir örneği listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="88be0-285">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="88be0-286">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="88be0-286">Service Type</span></span>                                                                                    | <span data-ttu-id="88be0-287">Ömür</span><span class="sxs-lookup"><span data-stu-id="88be0-287">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="88be0-288">Larsa</span><span class="sxs-lookup"><span data-stu-id="88be0-288">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="88be0-289">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-289">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="88be0-290">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-290">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="88be0-291">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-291">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="88be0-292">Larsa</span><span class="sxs-lookup"><span data-stu-id="88be0-292">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="88be0-293">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-293">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="88be0-294">Larsa</span><span class="sxs-lookup"><span data-stu-id="88be0-294">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="88be0-295">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-295">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="88be0-296">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-296">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="88be0-297">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-297">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="88be0-298">Larsa</span><span class="sxs-lookup"><span data-stu-id="88be0-298">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="88be0-299">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-299">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="88be0-300">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-300">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="88be0-301">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-301">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="88be0-302">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="88be0-302">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="88be0-303">Dı uygulaması geliştirme için NDC Konferansı desenleri</span><span class="sxs-lookup"><span data-stu-id="88be0-303">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="88be0-304">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="88be0-304">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="88be0-305">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="88be0-305">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="88be0-306">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="88be0-306">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="88be0-307">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="88be0-307">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="88be0-308">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="88be0-308">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="88be0-309">[Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler) tarafından</span><span class="sxs-lookup"><span data-stu-id="88be0-309">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="88be0-310">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="88be0-310">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="88be0-311">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="88be0-311">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="88be0-312">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="88be0-312">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="88be0-313">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="88be0-313">Overview of dependency injection</span></span>

<span data-ttu-id="88be0-314">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="88be0-314">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="88be0-315">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="88be0-315">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="88be0-316">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-316">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="88be0-317">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="88be0-317">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="88be0-318">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="88be0-318">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="88be0-319">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="88be0-319">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="88be0-320">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="88be0-320">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="88be0-321">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="88be0-321">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="88be0-322">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="88be0-322">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="88be0-323">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="88be0-323">This implementation is difficult to unit test.</span></span> <span data-ttu-id="88be0-324">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="88be0-324">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="88be0-325">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="88be0-325">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="88be0-326">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="88be0-326">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="88be0-327">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="88be0-327">Registration of the dependency in a service container.</span></span> <span data-ttu-id="88be0-328">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="88be0-328">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="88be0-329">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-329">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="88be0-330">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="88be0-330">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="88be0-331">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="88be0-331">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="88be0-332">[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="88be0-332">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="88be0-333">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="88be0-333">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="88be0-334">`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister.</span><span class="sxs-lookup"><span data-stu-id="88be0-334">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="88be0-335">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="88be0-335">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="88be0-336">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="88be0-336">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="88be0-337">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="88be0-337">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="88be0-338">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği* veya *nesne grafiği* olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="88be0-338">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="88be0-339">`IMyDependency` ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-339">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="88be0-340">`IMyDependency` kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="88be0-340">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="88be0-341">`ILogger<TCategoryName>` günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="88be0-341">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="88be0-342">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="88be0-342">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="88be0-343">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="88be0-343">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="88be0-344">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="88be0-344">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="88be0-345">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="88be0-345">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="88be0-346">Her `services.Add{SERVICE_NAME}` genişletme yöntemi, hizmetlerini ekler ve potansiyel olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="88be0-346">Each `services.Add{SERVICE_NAME}` extension method adds, and potentially configures, services.</span></span> <span data-ttu-id="88be0-347">Örneğin,, `services.AddControllersWithViews` `services.AddRazorPages` ve uygulamaları için `services.AddControllers` gereken ASP.NET Core Hizmetleri ekler.</span><span class="sxs-lookup"><span data-stu-id="88be0-347">For example, `services.AddControllersWithViews`, `services.AddRazorPages`, and `services.AddControllers` adds the services ASP.NET Core apps require.</span></span> <span data-ttu-id="88be0-348">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="88be0-348">We recommended that apps follow this convention.</span></span> <span data-ttu-id="88be0-349"><xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="88be0-349">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span> <span data-ttu-id="88be0-350">Dı uzantısı yöntemlerine ilişkin ad alanı bölümü de dahil olmak üzere `Microsoft.Extensions.DependencyInjection` :</span><span class="sxs-lookup"><span data-stu-id="88be0-350">Including the namespace portion `Microsoft.Extensions.DependencyInjection` for DI extension methods also:</span></span>
>
> * <span data-ttu-id="88be0-351">Ek blokları eklemeden [IntelliSense](/visualstudio/ide/using-intellisense) 'de görüntülenmesine izin verir `using` .</span><span class="sxs-lookup"><span data-stu-id="88be0-351">Allows them to be displayed in [IntelliSense](/visualstudio/ide/using-intellisense) without adding additional `using` blocks.</span></span>
> * <span data-ttu-id="88be0-352">`using` `Startup` Bu uzantı yöntemlerinin tipik olarak çağrıldığı sınıfta aşırı deyimleri engeller.</span><span class="sxs-lookup"><span data-stu-id="88be0-352">Prevents excessive `using` statements in the `Startup` class where these extension methods are typically called from.</span></span>

<span data-ttu-id="88be0-353">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="88be0-353">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="88be0-354">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="88be0-354">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="88be0-355">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88be0-355">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="88be0-356">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="88be0-356">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="88be0-357">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="88be0-357">Services injected into Startup</span></span>

<span data-ttu-id="88be0-358">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="88be0-358">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="88be0-359">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="88be0-359">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="88be0-360">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="88be0-360">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="88be0-361">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="88be0-361">Framework-provided services</span></span>

<span data-ttu-id="88be0-362">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="88be0-362">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="88be0-363">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="88be0-363">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="88be0-364">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="88be0-364">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="88be0-365">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="88be0-365">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="88be0-366">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="88be0-366">Service Type</span></span> | <span data-ttu-id="88be0-367">Ömür</span><span class="sxs-lookup"><span data-stu-id="88be0-367">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="88be0-368">Larsa</span><span class="sxs-lookup"><span data-stu-id="88be0-368">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="88be0-369">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-369">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="88be0-370">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-370">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="88be0-371">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-371">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="88be0-372">Larsa</span><span class="sxs-lookup"><span data-stu-id="88be0-372">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="88be0-373">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-373">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="88be0-374">Larsa</span><span class="sxs-lookup"><span data-stu-id="88be0-374">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="88be0-375">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-375">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="88be0-376">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-376">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="88be0-377">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-377">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="88be0-378">Larsa</span><span class="sxs-lookup"><span data-stu-id="88be0-378">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="88be0-379">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-379">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="88be0-380">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-380">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="88be0-381">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-381">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="88be0-382">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="88be0-382">Register additional services with extension methods</span></span>

<span data-ttu-id="88be0-383">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="88be0-383">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="88be0-384">Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="88be0-384">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="88be0-385">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="88be0-385">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="88be0-386">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="88be0-386">Service lifetimes</span></span>

<span data-ttu-id="88be0-387">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="88be0-387">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="88be0-388">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="88be0-388">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="88be0-389">Larsa</span><span class="sxs-lookup"><span data-stu-id="88be0-389">Transient</span></span>

<span data-ttu-id="88be0-390">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="88be0-390">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="88be0-391">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88be0-391">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="88be0-392">İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="88be0-392">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="88be0-393">Yayıl</span><span class="sxs-lookup"><span data-stu-id="88be0-393">Scoped</span></span>

<span data-ttu-id="88be0-394">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="88be0-394">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="88be0-395">İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="88be0-395">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="88be0-396">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="88be0-396">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="88be0-397">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="88be0-397">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="88be0-398">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="88be0-398">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="88be0-399">Adet</span><span class="sxs-lookup"><span data-stu-id="88be0-399">Singleton</span></span>

<span data-ttu-id="88be0-400">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="88be0-400">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="88be0-401">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="88be0-401">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="88be0-402">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-402">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="88be0-403">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="88be0-403">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="88be0-404">İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="88be0-404">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="88be0-405">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="88be0-405">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="88be0-406">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-406">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="88be0-407">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="88be0-407">Service registration methods</span></span>

<span data-ttu-id="88be0-408">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="88be0-408">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="88be0-409">Yöntem</span><span class="sxs-lookup"><span data-stu-id="88be0-409">Method</span></span> | <span data-ttu-id="88be0-410">Automatic</span><span class="sxs-lookup"><span data-stu-id="88be0-410">Automatic</span></span><br><span data-ttu-id="88be0-411">object</span><span class="sxs-lookup"><span data-stu-id="88be0-411">object</span></span><br><span data-ttu-id="88be0-412">elden</span><span class="sxs-lookup"><span data-stu-id="88be0-412">disposal</span></span> | <span data-ttu-id="88be0-413">Birden çok</span><span class="sxs-lookup"><span data-stu-id="88be0-413">Multiple</span></span><br><span data-ttu-id="88be0-414">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="88be0-414">implementations</span></span> | <span data-ttu-id="88be0-415">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="88be0-415">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="88be0-416">Örnek:</span><span class="sxs-lookup"><span data-stu-id="88be0-416">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="88be0-417">Yes</span><span class="sxs-lookup"><span data-stu-id="88be0-417">Yes</span></span> | <span data-ttu-id="88be0-418">Yes</span><span class="sxs-lookup"><span data-stu-id="88be0-418">Yes</span></span> | <span data-ttu-id="88be0-419">Hayır</span><span class="sxs-lookup"><span data-stu-id="88be0-419">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="88be0-420">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="88be0-420">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="88be0-421">Yes</span><span class="sxs-lookup"><span data-stu-id="88be0-421">Yes</span></span> | <span data-ttu-id="88be0-422">Yes</span><span class="sxs-lookup"><span data-stu-id="88be0-422">Yes</span></span> | <span data-ttu-id="88be0-423">Yes</span><span class="sxs-lookup"><span data-stu-id="88be0-423">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="88be0-424">Örnek:</span><span class="sxs-lookup"><span data-stu-id="88be0-424">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="88be0-425">Yes</span><span class="sxs-lookup"><span data-stu-id="88be0-425">Yes</span></span> | <span data-ttu-id="88be0-426">Hayır</span><span class="sxs-lookup"><span data-stu-id="88be0-426">No</span></span> | <span data-ttu-id="88be0-427">Hayır</span><span class="sxs-lookup"><span data-stu-id="88be0-427">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="88be0-428">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="88be0-428">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="88be0-429">Hayır</span><span class="sxs-lookup"><span data-stu-id="88be0-429">No</span></span> | <span data-ttu-id="88be0-430">Yes</span><span class="sxs-lookup"><span data-stu-id="88be0-430">Yes</span></span> | <span data-ttu-id="88be0-431">Yes</span><span class="sxs-lookup"><span data-stu-id="88be0-431">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="88be0-432">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="88be0-432">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="88be0-433">Hayır</span><span class="sxs-lookup"><span data-stu-id="88be0-433">No</span></span> | <span data-ttu-id="88be0-434">Hayır</span><span class="sxs-lookup"><span data-stu-id="88be0-434">No</span></span> | <span data-ttu-id="88be0-435">Yes</span><span class="sxs-lookup"><span data-stu-id="88be0-435">Yes</span></span> |

<span data-ttu-id="88be0-436">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="88be0-436">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="88be0-437">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="88be0-437">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="88be0-438">Hizmeti yalnızca bir uygulama türüyle kaydetmek, bu hizmeti aynı uygulama ve hizmet türüyle kaydetmeye eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="88be0-438">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="88be0-439">Bu, bir hizmetin birden çok uygulamasının açık bir hizmet türü kullanmayan yöntemler kullanılarak kaydedilamamasının nedenleridir.</span><span class="sxs-lookup"><span data-stu-id="88be0-439">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="88be0-440">Bu yöntemler bir hizmetin birden fazla *örneğini* kaydedebilir, ancak hepsi aynı *uygulama* türüne sahip olur.</span><span class="sxs-lookup"><span data-stu-id="88be0-440">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="88be0-441">Yukarıdaki hizmet kayıt yöntemlerinden herhangi biri aynı hizmet türünün birden çok hizmet örneğini kaydetmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-441">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="88be0-442">Aşağıdaki örnekte, `AddSingleton` hizmet türü olarak ile iki kez çağırılır `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="88be0-442">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="88be0-443">İçin ikinci çağrı, `AddSingleton` olarak çözümlendikten önceki bir öncekini geçersiz kılar `IMyDependency` ve aracılığıyla birden çok hizmet çözümlendiğinde bir öncekini ekler `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="88be0-443">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="88be0-444">Hizmetler, ile çözümlendiklerinde kaydedildikleri sırada görüntülenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="88be0-444">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="88be0-445">Framework Ayrıca, `TryAdd{LIFETIME}` yalnızca kayıtlı bir uygulama olmadığında hizmeti kaydeden genişletme yöntemleri de sağlar.</span><span class="sxs-lookup"><span data-stu-id="88be0-445">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="88be0-446">Aşağıdaki örnekte, `AddSingleton` `MyDependency` için bir uygulama olarak Yazmaçları çağrısı `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="88be0-446">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="88be0-447">' A çağrı, `TryAddSingleton` `IMyDependency` zaten kayıtlı bir uygulamaya sahip olduğu için hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="88be0-447">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="88be0-448">Daha fazla bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="88be0-448">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="88be0-449">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde* bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="88be0-449">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="88be0-450">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="88be0-450">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="88be0-451">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="88be0-451">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="88be0-452">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88be0-452">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="88be0-453">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="88be0-453">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="88be0-454">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="88be0-454">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="88be0-455">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="88be0-455">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="88be0-456">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="88be0-456">Constructor injection behavior</span></span>

<span data-ttu-id="88be0-457">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="88be0-457">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="88be0-458"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="88be0-458"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="88be0-459">`ActivatorUtilities` Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88be0-459">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="88be0-460">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="88be0-460">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="88be0-461">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="88be0-461">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="88be0-462">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="88be0-462">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="88be0-463">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-463">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="88be0-464">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="88be0-464">Entity Framework contexts</span></span>

<span data-ttu-id="88be0-465">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="88be0-465">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="88be0-466">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="88be0-466">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="88be0-467">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-467">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="88be0-468">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="88be0-468">Lifetime and registration options</span></span>

<span data-ttu-id="88be0-469">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="88be0-469">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="88be0-470">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="88be0-470">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="88be0-471">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="88be0-471">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="88be0-472">`Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="88be0-472">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="88be0-473">, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` .</span><span class="sxs-lookup"><span data-stu-id="88be0-473">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="88be0-474">`OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="88be0-474">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="88be0-475">Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="88be0-475">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="88be0-476">`OperationService` sınıfının yeni bir örneğini alır `IOperationTransient` .</span><span class="sxs-lookup"><span data-stu-id="88be0-476">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="88be0-477">Yeni örnek farklı bir şekilde oluşturur `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="88be0-477">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="88be0-478">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="88be0-478">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="88be0-479">İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="88be0-479">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="88be0-480">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="88be0-480">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="88be0-481">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="88be0-481">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="88be0-482">`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="88be0-482">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="88be0-483">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="88be0-483">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="88be0-484">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="88be0-484">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="88be0-485">Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="88be0-485">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="88be0-486">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="88be0-486">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="88be0-487">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="88be0-487">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="88be0-488">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="88be0-488">**First request:**</span></span>

<span data-ttu-id="88be0-489">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="88be0-489">Controller operations:</span></span>

<span data-ttu-id="88be0-490">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="88be0-490">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="88be0-491">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="88be0-491">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="88be0-492">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="88be0-492">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="88be0-493">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="88be0-493">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="88be0-494">`OperationService` operasyonları</span><span class="sxs-lookup"><span data-stu-id="88be0-494">`OperationService` operations:</span></span>

<span data-ttu-id="88be0-495">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="88be0-495">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="88be0-496">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="88be0-496">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="88be0-497">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="88be0-497">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="88be0-498">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="88be0-498">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="88be0-499">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="88be0-499">**Second request:**</span></span>

<span data-ttu-id="88be0-500">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="88be0-500">Controller operations:</span></span>

<span data-ttu-id="88be0-501">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="88be0-501">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="88be0-502">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="88be0-502">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="88be0-503">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="88be0-503">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="88be0-504">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="88be0-504">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="88be0-505">`OperationService` operasyonları</span><span class="sxs-lookup"><span data-stu-id="88be0-505">`OperationService` operations:</span></span>

<span data-ttu-id="88be0-506">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="88be0-506">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="88be0-507">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="88be0-507">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="88be0-508">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="88be0-508">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="88be0-509">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="88be0-509">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="88be0-510">`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="88be0-510">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="88be0-511">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-511">*Transient* objects are always different.</span></span> <span data-ttu-id="88be0-512">Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-512">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="88be0-513">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="88be0-513">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="88be0-514">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-514">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="88be0-515">*Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="88be0-515">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="88be0-516">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="88be0-516">Call services from main</span></span>

<span data-ttu-id="88be0-517"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="88be0-517">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="88be0-518">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-518">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="88be0-519">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="88be0-519">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="88be0-520">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="88be0-520">Scope validation</span></span>

<span data-ttu-id="88be0-521">Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="88be0-521">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="88be0-522">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="88be0-522">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="88be0-523">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="88be0-523">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="88be0-524">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="88be0-524">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="88be0-525">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="88be0-525">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="88be0-526">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="88be0-526">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="88be0-527">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="88be0-527">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="88be0-528">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="88be0-528">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="88be0-529">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="88be0-529">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="88be0-530">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="88be0-530">Request Services</span></span>

<span data-ttu-id="88be0-531">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="88be0-531">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="88be0-532">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="88be0-532">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="88be0-533">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="88be0-533">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="88be0-534">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-534">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="88be0-535">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="88be0-535">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="88be0-536">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="88be0-536">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="88be0-537">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="88be0-537">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="88be0-538">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="88be0-538">Design services for dependency injection</span></span>

<span data-ttu-id="88be0-539">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="88be0-539">Best practices are to:</span></span>

* <span data-ttu-id="88be0-540">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="88be0-540">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="88be0-541">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="88be0-541">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="88be0-542">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="88be0-542">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="88be0-543">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="88be0-543">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="88be0-544">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="88be0-544">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="88be0-545">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="88be0-545">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="88be0-546">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="88be0-546">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="88be0-547">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="88be0-547">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="88be0-548">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="88be0-548">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="88be0-549">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-549">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="88be0-550">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="88be0-550">Disposal of services</span></span>

<span data-ttu-id="88be0-551">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="88be0-551">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="88be0-552">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="88be0-552">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="88be0-553">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="88be0-553">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="88be0-554">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="88be0-554">In the following example:</span></span>

* <span data-ttu-id="88be0-555">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="88be0-555">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="88be0-556">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="88be0-556">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="88be0-557">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="88be0-557">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="88be0-558">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="88be0-558">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="88be0-559">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="88be0-559">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="88be0-560">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="88be0-560">Transient, limited lifetime</span></span>

<span data-ttu-id="88be0-561">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="88be0-561">**Scenario**</span></span>

<span data-ttu-id="88be0-562">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="88be0-562">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="88be0-563">Örnek, kök kapsamda çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="88be0-563">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="88be0-564">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-564">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="88be0-565">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="88be0-565">**Solution**</span></span>

<span data-ttu-id="88be0-566">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="88be0-566">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="88be0-567">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="88be0-567">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="88be0-568">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="88be0-568">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="88be0-569">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="88be0-569">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="88be0-570"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="88be0-570">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="88be0-571">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="88be0-571">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="88be0-572">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="88be0-572">**Scenario**</span></span>

<span data-ttu-id="88be0-573">Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-573">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="88be0-574">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="88be0-574">**Solution**</span></span>

<span data-ttu-id="88be0-575">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="88be0-575">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="88be0-576"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="88be0-576">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="88be0-577"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="88be0-577">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="88be0-578">Yaşam süresi sona erdirmek için kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="88be0-578">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="88be0-579">Genel Yönergeler</span><span class="sxs-lookup"><span data-stu-id="88be0-579">General Guidelines</span></span>

* <span data-ttu-id="88be0-580"><xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme.</span><span class="sxs-lookup"><span data-stu-id="88be0-580">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="88be0-581">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="88be0-581">Use the factory pattern instead.</span></span>
* <span data-ttu-id="88be0-582">Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün.</span><span class="sxs-lookup"><span data-stu-id="88be0-582">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="88be0-583">Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.</span><span class="sxs-lookup"><span data-stu-id="88be0-583">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="88be0-584">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="88be0-584">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="88be0-585"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-585">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="88be0-586">Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="88be0-586">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="88be0-587">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="88be0-587">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="88be0-588">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="88be0-588">Default service container replacement</span></span>

<span data-ttu-id="88be0-589">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="88be0-589">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="88be0-590">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="88be0-590">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="88be0-591">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="88be0-591">Property injection</span></span>
* <span data-ttu-id="88be0-592">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="88be0-592">Injection based on name</span></span>
* <span data-ttu-id="88be0-593">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="88be0-593">Child containers</span></span>
* <span data-ttu-id="88be0-594">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="88be0-594">Custom lifetime management</span></span>
* <span data-ttu-id="88be0-595">`Func<T>` yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="88be0-595">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="88be0-596">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="88be0-596">Convention-based registration</span></span>

<span data-ttu-id="88be0-597">Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="88be0-597">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="88be0-598">Autofac</span><span class="sxs-lookup"><span data-stu-id="88be0-598">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="88be0-599">Drıioc</span><span class="sxs-lookup"><span data-stu-id="88be0-599">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="88be0-600">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="88be0-600">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="88be0-601">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="88be0-601">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="88be0-602">E</span><span class="sxs-lookup"><span data-stu-id="88be0-602">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="88be0-603">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="88be0-603">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="88be0-604">Unity</span><span class="sxs-lookup"><span data-stu-id="88be0-604">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="88be0-605">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="88be0-605">Thread safety</span></span>

<span data-ttu-id="88be0-606">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="88be0-606">Create thread-safe singleton services.</span></span> <span data-ttu-id="88be0-607">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-607">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="88be0-608">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88be0-608">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="88be0-609">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="88be0-609">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="88be0-610">Öneriler</span><span class="sxs-lookup"><span data-stu-id="88be0-610">Recommendations</span></span>

* <span data-ttu-id="88be0-611">`async/await` ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="88be0-611">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="88be0-612">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="88be0-612">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="88be0-613">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="88be0-613">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="88be0-614">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="88be0-614">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="88be0-615">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88be0-615">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="88be0-616">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="88be0-616">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="88be0-617">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="88be0-617">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="88be0-618">Hizmetlere statik erişimi önleyin.</span><span class="sxs-lookup"><span data-stu-id="88be0-618">Avoid static access to services.</span></span> <span data-ttu-id="88be0-619">Örneğin, bir başka yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik olarak yazılanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="88be0-619">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="88be0-620">[Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin* kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="88be0-620">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="88be0-621"><xref:System.IServiceProvider.GetService*>Bunun yerine, şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:</span><span class="sxs-lookup"><span data-stu-id="88be0-621">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="88be0-622">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="88be0-622">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="88be0-623">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="88be0-623">**Correct**:</span></span>

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

* <span data-ttu-id="88be0-624">Kullanarak çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="88be0-624">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="88be0-625">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="88be0-625">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="88be0-626">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="88be0-626">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="88be0-627">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="88be0-627">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="88be0-628">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="88be0-628">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="88be0-629">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="88be0-629">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88be0-630">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="88be0-630">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="88be0-631">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="88be0-631">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="88be0-632">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="88be0-632">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="88be0-633">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="88be0-633">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="88be0-634">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="88be0-634">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="88be0-635">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="88be0-635">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
