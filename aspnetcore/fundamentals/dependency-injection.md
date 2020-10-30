---
title: ASP.NET Core bağımlılık ekleme
author: rick-anderson
description: ASP.NET Core bağımlılık ekleme ve nasıl kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 53ccb90e92b99385fcc1d9358686b505ac1a0dcc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060527"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="884b8-103">ASP.NET Core bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="884b8-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="884b8-104">[Kirk Larkabağı](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="884b8-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="884b8-105">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="884b8-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="884b8-106">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="884b8-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="884b8-107">Web Apps dışındaki uygulamalarda bağımlılık ekleme hakkında daha fazla bilgi için bkz. [.net 'e bağımlılık ekleme](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="884b8-107">For information on using dependency injection in applications other than web apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="884b8-108">Seçeneklerin bağımlılığı ekleme hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/options> ..</span><span class="sxs-lookup"><span data-stu-id="884b8-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="884b8-109">Bu konuda ASP.NET Core bağımlılık ekleme hakkında bilgi verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="884b8-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="884b8-110">Bağımlılık ekleme 'yi kullanmayla ilgili birincil belgeler, [.net ' de bağımlılık ekleme](/dotnet/core/extensions/dependency-injection)' de yer alır.</span><span class="sxs-lookup"><span data-stu-id="884b8-110">The primary documentation on using dependency injection is contained in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="884b8-111">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="884b8-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="884b8-112">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="884b8-112">Overview of dependency injection</span></span>

<span data-ttu-id="884b8-113">*Bağımlılık* , başka bir nesnenin bağımlı olduğu bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="884b8-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="884b8-114">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` diğer sınıfların bağımlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="884b8-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="884b8-115">Bir sınıf, `MyDependency` yöntemini kullanmak için sınıfının bir örneğini oluşturabilir `WriteMessage` .</span><span class="sxs-lookup"><span data-stu-id="884b8-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="884b8-116">Aşağıdaki örnekte, `MyDependency` sınıfı sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="884b8-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="884b8-117">Sınıfı oluşturur ve doğrudan `MyDependency` sınıfa bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="884b8-118">Önceki örnekte olduğu gibi kod bağımlılıkları sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="884b8-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="884b8-119">`MyDependency`Farklı bir uygulamayla değiştirmek için, `IndexModel` sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="884b8-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="884b8-120">`MyDependency`Bağımlılıkları varsa, sınıf tarafından da yapılandırılmalıdır `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="884b8-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="884b8-121">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="884b8-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="884b8-122">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="884b8-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="884b8-123">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="884b8-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="884b8-124">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="884b8-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="884b8-125">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="884b8-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="884b8-126">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="884b8-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="884b8-127">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="884b8-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="884b8-128">Hizmetler genellikle uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="884b8-129">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="884b8-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="884b8-130">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="884b8-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="884b8-131">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirimi `WriteMessage` yöntemini tanımlar:</span><span class="sxs-lookup"><span data-stu-id="884b8-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="884b8-132">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="884b8-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="884b8-133">Örnek uygulama, `IMyDependency` hizmeti somut tür ile kaydeder `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="884b8-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="884b8-134"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Yöntemi, hizmeti tek bir isteğin ömrü olan kapsamlı bir yaşam süresine kaydeder.</span><span class="sxs-lookup"><span data-stu-id="884b8-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="884b8-135">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="884b8-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="884b8-136">Örnek uygulamada, `IMyDependency` hizmet istenir ve yöntemi çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="884b8-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="884b8-137">DI modelini kullanarak denetleyici:</span><span class="sxs-lookup"><span data-stu-id="884b8-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="884b8-138">Somut türü kullanmaz `MyDependency` , yalnızca `IMyDependency` uyguladığı arabirim.</span><span class="sxs-lookup"><span data-stu-id="884b8-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="884b8-139">Bu, denetleyicinin, denetleyiciyi değiştirmeden kullandığı uygulamayı değiştirmeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="884b8-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="884b8-140">Bir örneği oluşturmaz `MyDependency` , bu, dı kapsayıcısı tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="884b8-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="884b8-141">Arabirim uygulanması, `IMyDependency` yerleşik günlük API 'si kullanılarak artırılabilir:</span><span class="sxs-lookup"><span data-stu-id="884b8-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="884b8-142">Updated `ConfigureServices` yöntemi yeni `IMyDependency` uygulamayı kaydeder:</span><span class="sxs-lookup"><span data-stu-id="884b8-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="884b8-143">`MyDependency2`<xref:Microsoft.Extensions.Logging.ILogger%601>, oluşturucuda istediği öğesine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="884b8-144">`ILogger<TCategoryName>`[Framework tarafından sağlanmış bir hizmettir](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="884b8-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="884b8-145">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="884b8-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="884b8-146">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="884b8-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="884b8-147">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="884b8-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="884b8-148">Çözümlenmesi gereken, genellikle *bağımlılık ağacı* , *bağımlılık grafiği* veya *nesne grafiği* olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="884b8-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph* .</span></span>

<span data-ttu-id="884b8-149">Kapsayıcı, `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="884b8-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="884b8-150">Bağımlılık ekleme terminolojisi ' nde bir hizmet:</span><span class="sxs-lookup"><span data-stu-id="884b8-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="884b8-151">Genellikle hizmet gibi diğer nesnelere hizmet sağlayan bir nesnedir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="884b8-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="884b8-152">Bir Web hizmetiyle ilgili değildir, ancak hizmet bir Web hizmeti kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="884b8-153">Çerçeve, güçlü bir [günlük](xref:fundamentals/logging/index) sistemi sağlar.</span><span class="sxs-lookup"><span data-stu-id="884b8-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="884b8-154">`IMyDependency`Yukarıdaki örneklerde gösterilen uygulamalar, günlüğü uygulamamak için değil temel dı 'yi göstermek için yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="884b8-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="884b8-155">Çoğu uygulamanın Günlükçüler yazması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="884b8-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="884b8-156">Aşağıdaki kod, bir hizmetin kaydedilmesini gerektirmeyen varsayılan günlük kaydını kullanmayı göstermektedir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="884b8-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="884b8-157">Yukarıdaki kodu kullanarak, `ConfigureServices` Framework tarafından [günlüğe kaydetme](xref:fundamentals/logging/index) sağlandığı için güncelleştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="884b8-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="884b8-158">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="884b8-158">Services injected into Startup</span></span>

<span data-ttu-id="884b8-159">Hizmetler `Startup` oluşturucuya ve `Startup.Configure` metoduna eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="884b8-160">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmetler eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="884b8-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="884b8-161">Dı kapsayıcısına kayıtlı tüm hizmetler `Startup.Configure` metoduna eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="884b8-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="884b8-162">Daha fazla bilgi için, bkz <xref:fundamentals/startup> . ve [erişim yapılandırması başlangıç](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="884b8-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="884b8-163">Uzantı yöntemleriyle hizmet gruplarını kaydet</span><span class="sxs-lookup"><span data-stu-id="884b8-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="884b8-164">ASP.NET Core Framework, bir grup ilgili hizmeti kaydetmek için bir kural kullanır.</span><span class="sxs-lookup"><span data-stu-id="884b8-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="884b8-165">Kural, `Add{GROUP_NAME}` bir Framework özelliği için gereken tüm hizmetleri kaydetmek için tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="884b8-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="884b8-166">Örneğin, Microsoft. Extensions. Dependencyınjection. MvcServiceCollectionExtensions. AddControllers> genişletme yöntemi <MVC denetleyicileri için gereken hizmetleri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="884b8-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="884b8-167">Aşağıdaki kod, :::no-loc(Razor)::: bireysel kullanıcı hesapları kullanılarak sayfalar şablonu tarafından oluşturulur ve uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemeyi gösterir <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ve <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A> :</span><span class="sxs-lookup"><span data-stu-id="884b8-167">The following code is generated by the :::no-loc(Razor)::: Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="884b8-168">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="884b8-168">Service lifetimes</span></span>

<span data-ttu-id="884b8-169">Bkz. [.net 'Te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) içindeki [hizmet yaşam süreleri](/dotnet/core/extensions/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="884b8-169">See [Service lifetimes](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="884b8-170">Ara yazılım kapsamındaki hizmetleri kullanmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="884b8-170">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="884b8-171">Hizmeti, ara yazılımı `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="884b8-171">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="884b8-172">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) kullanılması, kapsamlı hizmeti tek bir gibi davranmaya zordığı için bir çalışma zamanı özel durumu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="884b8-172">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="884b8-173">[Ömür ve kayıt seçenekleri](#lifetime-and-registration-options) bölümündeki örnek, `InvokeAsync` yaklaşımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="884b8-173">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="884b8-174">[Fabrika tabanlı ara yazılım](xref:fundamentals/middleware/extensibility)kullanın.</span><span class="sxs-lookup"><span data-stu-id="884b8-174">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="884b8-175">Bu yaklaşım kullanılarak kaydedilen ara yazılım, kapsamlı hizmetlerin ara yazılım yöntemine eklenmiş olmasına olanak tanıyan istemci isteği (bağlantı) başına etkinleştirilir `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="884b8-175">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="884b8-176">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="884b8-176">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="884b8-177">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="884b8-177">Service registration methods</span></span>

<span data-ttu-id="884b8-178">[.Net ' te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) içindeki [hizmet kayıt yöntemlerine](/dotnet/core/extensions/dependency-injection#service-registration-methods) bakın</span><span class="sxs-lookup"><span data-stu-id="884b8-178">See [Service registration methods](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

 <span data-ttu-id="884b8-179">[Test için izleme türleri](xref:test/integration-tests#inject-mock-services)olduğunda birden çok uygulama kullanılması yaygındır.</span><span class="sxs-lookup"><span data-stu-id="884b8-179">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="884b8-180">Hizmeti yalnızca bir uygulama türüyle kaydetmek, bu hizmeti aynı uygulama ve hizmet türüyle kaydetmeye eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="884b8-180">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="884b8-181">Bu, bir hizmetin birden çok uygulamasının açık bir hizmet türü kullanmayan yöntemler kullanılarak kaydedilamamasının nedenleridir.</span><span class="sxs-lookup"><span data-stu-id="884b8-181">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="884b8-182">Bu yöntemler bir hizmetin birden fazla *örneğini* kaydedebilir, ancak hepsi aynı *uygulama* türüne sahip olur.</span><span class="sxs-lookup"><span data-stu-id="884b8-182">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="884b8-183">Yukarıdaki hizmet kayıt yöntemlerinden herhangi biri aynı hizmet türünün birden çok hizmet örneğini kaydetmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-183">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="884b8-184">Aşağıdaki örnekte, `AddSingleton` hizmet türü olarak ile iki kez çağırılır `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="884b8-184">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="884b8-185">İçin ikinci çağrı, `AddSingleton` olarak çözümlendikten önceki bir öncekini geçersiz kılar `IMyDependency` ve aracılığıyla birden çok hizmet çözümlendiğinde bir öncekini ekler `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="884b8-185">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="884b8-186">Hizmetler, ile çözümlendiklerinde kaydedildikleri sırada görüntülenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="884b8-186">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a><span data-ttu-id="884b8-187">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="884b8-187">Constructor injection behavior</span></span>

<span data-ttu-id="884b8-188">[.Net ' te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) bölümüne bkz. [Oluşturucu Ekleme davranışı](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)</span><span class="sxs-lookup"><span data-stu-id="884b8-188">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="884b8-189">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="884b8-189">Entity Framework contexts</span></span>

<span data-ttu-id="884b8-190">Varsayılan olarak, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan, Entity Framework bağlamları [kapsama yaşam süresi](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="884b8-190">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="884b8-191">Farklı bir yaşam süresi kullanmak için, bir aşırı yükleme kullanarak yaşam süresini belirtin <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> .</span><span class="sxs-lookup"><span data-stu-id="884b8-191">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="884b8-192">Belirli bir yaşam süresinin Hizmetleri, hizmetin yaşam süresinden kısa olan bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-192">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="884b8-193">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="884b8-193">Lifetime and registration options</span></span>

<span data-ttu-id="884b8-194">Hizmet yaşam süreleri ve kayıt seçenekleri arasındaki farkı göstermek için, bir görevi tanımlayıcı ile bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="884b8-194">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="884b8-195">Bir işlemin hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde hizmetin aynı ya da farklı örneklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="884b8-195">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="884b8-196">Aşağıdaki `Operation` sınıf önceki arabirimlerin tümünü uygular.</span><span class="sxs-lookup"><span data-stu-id="884b8-196">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="884b8-197">`Operation`Oluşturucu BIR GUID oluşturur ve son 4 karakteri `OperationId` özellikte depolar:</span><span class="sxs-lookup"><span data-stu-id="884b8-197">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="884b8-198">`Startup.ConfigureServices`Yöntemi, `Operation` adlandırılmış yaşam sürelerine göre sınıfının birden çok kaydı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="884b8-198">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="884b8-199">Örnek uygulama, isteklerin içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="884b8-199">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="884b8-200">`IndexModel`Ve ara yazılım her `IOperation` tür türü ister, `OperationId` her biri için günlük kaydı:</span><span class="sxs-lookup"><span data-stu-id="884b8-200">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="884b8-201">Uygulamasına benzer şekilde, `IndexModel` Ara yazılım aynı hizmetleri çözümler:</span><span class="sxs-lookup"><span data-stu-id="884b8-201">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="884b8-202">Kapsamındaki hizmetlerin yöntemde çözülmesi gerekir `InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="884b8-202">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="884b8-203">Günlükçü çıktısı şunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="884b8-203">The logger output shows:</span></span>

* <span data-ttu-id="884b8-204">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-204">*Transient* objects are always different.</span></span> <span data-ttu-id="884b8-205">Geçici `OperationId` değer, `IndexModel` Ara yazılım içindeki ve içinde farklıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-205">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="884b8-206">*Kapsamlı* nesneler her istek için aynıdır, ancak her istekte farklıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-206">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="884b8-207">*Tek* nesneler her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-207">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="884b8-208">Günlüğe kaydetme çıkışını azaltmak için, dosyadaki *appsettings.Development.js* "Logging: LogLevel: Microsoft: Error" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="884b8-208">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="884b8-209">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="884b8-209">Call services from main</span></span>

<span data-ttu-id="884b8-210"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="884b8-210">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="884b8-211">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-211">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="884b8-212">Aşağıdaki örnek, kapsamındaki hizmete nasıl erişeceğinden `IMyDependency` ve yönteminin içinde nasıl çağrılacağını göstermektedir `WriteMessage` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="884b8-212">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="884b8-213">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="884b8-213">Scope validation</span></span>

<span data-ttu-id="884b8-214">[.Net ' te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) bölümüne bkz. [Oluşturucu Ekleme davranışı](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)</span><span class="sxs-lookup"><span data-stu-id="884b8-214">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="884b8-215">Daha fazla bilgi için bkz. [kapsam doğrulaması](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="884b8-215">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="884b8-216">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="884b8-216">Request Services</span></span>

<span data-ttu-id="884b8-217">Bir ASP.NET Core isteği içinde kullanılabilen hizmetler, [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="884b8-217">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="884b8-218">Bir isteğin içinden hizmetler istendiğinde, hizmetler ve bağımlılıkları `RequestServices` koleksiyonundan çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="884b8-218">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="884b8-219">Çerçeve, istek başına bir kapsam oluşturur ve `RequestServices` kapsamlı hizmet sağlayıcısını kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="884b8-219">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="884b8-220">Tüm kapsamlı hizmetler, istek etkin olduğu sürece geçerli olur.</span><span class="sxs-lookup"><span data-stu-id="884b8-220">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="884b8-221">Koleksiyondan Hizmetleri çözümlemek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="884b8-221">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="884b8-222">Bu, test etmek daha kolay olan sınıfların sonucunu vermez.</span><span class="sxs-lookup"><span data-stu-id="884b8-222">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="884b8-223">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="884b8-223">Design services for dependency injection</span></span>

<span data-ttu-id="884b8-224">Bağımlılık ekleme için Hizmetleri tasarlarken:</span><span class="sxs-lookup"><span data-stu-id="884b8-224">When designing services for dependency injection:</span></span>

* <span data-ttu-id="884b8-225">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="884b8-225">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="884b8-226">Bunun yerine Singleton hizmetlerini kullanmak için uygulama tasarlayarak genel durum oluşturmamaya özen gösterin.</span><span class="sxs-lookup"><span data-stu-id="884b8-226">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="884b8-227">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="884b8-227">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="884b8-228">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="884b8-228">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="884b8-229">Hizmetleri küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="884b8-229">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="884b8-230">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu, sınıfta çok fazla sorumluluğun olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir işaret olabilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-230">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="884b8-231">Bazı sorumlulukları yeni sınıflara taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="884b8-231">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="884b8-232">:::no-loc(Razor):::Sayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="884b8-232">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="884b8-233">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="884b8-233">Disposal of services</span></span>

<span data-ttu-id="884b8-234">Kapsayıcı, <xref:System.IDisposable.Dispose%2A> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="884b8-234">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="884b8-235">Kapsayıcıdan çözümlenen hizmetler hiçbir şekilde geliştirici tarafından atılamaz.</span><span class="sxs-lookup"><span data-stu-id="884b8-235">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="884b8-236">Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı kendiliğinden otomatik olarak atar.</span><span class="sxs-lookup"><span data-stu-id="884b8-236">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="884b8-237">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="884b8-237">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="884b8-238">Hata ayıklama konsolu dizin sayfasının her yenilemesinde aşağıdaki çıktıyı gösterir:</span><span class="sxs-lookup"><span data-stu-id="884b8-238">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="884b8-239">Hizmet kapsayıcısı tarafından oluşturulmayan hizmetler</span><span class="sxs-lookup"><span data-stu-id="884b8-239">Services not created by the service container</span></span>

<span data-ttu-id="884b8-240">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="884b8-240">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="884b8-241">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="884b8-241">In the preceding code:</span></span>

* <span data-ttu-id="884b8-242">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="884b8-242">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="884b8-243">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="884b8-243">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="884b8-244">Geliştirici, hizmetleri elden atan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="884b8-244">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="884b8-245">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="884b8-245">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="884b8-246">[.Net ' te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) [konusunda geçici ve paylaşılan örnek için IDisposable Guidance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) bölümüne bakın</span><span class="sxs-lookup"><span data-stu-id="884b8-246">See [IDisposable guidance for Transient and shared instance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="884b8-247">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="884b8-247">Default service container replacement</span></span>

<span data-ttu-id="884b8-248">Bkz. [.net 'Te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) için [varsayılan hizmet kapsayıcısı değiştirme](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement)</span><span class="sxs-lookup"><span data-stu-id="884b8-248">See [Default service container replacement](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="recommendations"></a><span data-ttu-id="884b8-249">Öneriler</span><span class="sxs-lookup"><span data-stu-id="884b8-249">Recommendations</span></span>

<span data-ttu-id="884b8-250">[.Net ' te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection) içindeki [önerilere](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) bakın</span><span class="sxs-lookup"><span data-stu-id="884b8-250">See [Recommendations](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

* <span data-ttu-id="884b8-251">*Hizmet bulucu deseninin* kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="884b8-251">Avoid using the *service locator pattern* .</span></span> <span data-ttu-id="884b8-252">Örneğin, <xref:System.IServiceProvider.GetService%2A> yerine şunu kullandığınızda bir hizmet örneği elde etme çağrısı yapmayın:</span><span class="sxs-lookup"><span data-stu-id="884b8-252">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="884b8-253">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="884b8-253">**Incorrect:**</span></span>

    ![Yanlış kod](dependency-injection/_static/bad.png)

  <span data-ttu-id="884b8-255">**Doğru** :</span><span class="sxs-lookup"><span data-stu-id="884b8-255">**Correct** :</span></span>

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
* <span data-ttu-id="884b8-256">Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme.</span><span class="sxs-lookup"><span data-stu-id="884b8-256">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="884b8-257">Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="884b8-257">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="884b8-258">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="884b8-258">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="884b8-259">İçindeki çağrılardan <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> kaçının `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="884b8-259">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="884b8-260">Çağırma `BuildServiceProvider` genellikle geliştirici içindeki bir hizmeti çözmek istediğinde oluşur `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="884b8-260">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="884b8-261">Örneğin, yapılandırmasından yüklendiği durumu göz önünde bulundurun `LoginPath` .</span><span class="sxs-lookup"><span data-stu-id="884b8-261">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="884b8-262">Aşağıdaki yaklaşımdan kaçının:</span><span class="sxs-lookup"><span data-stu-id="884b8-262">Avoid the following approach:</span></span>

  ![BuildServiceProvider çağrılırken hatalı kod](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="884b8-264">Önceki görüntüde yeşil dalgalı çizgiyi seçtiğinizde `services.BuildServiceProvider` AŞAĞıDAKI ASP0000 uyarısı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="884b8-264">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="884b8-265">Uygulama kodundan ' BuildServiceProvider ' çağrısı ASP0000, oluşturulan Singleton Hizmetleri ek bir kopyasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="884b8-265">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="884b8-266">Bağımlılık ekleme hizmetleri gibi alternatifleri, ' configure ' için parametre olarak düşünün.</span><span class="sxs-lookup"><span data-stu-id="884b8-266">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="884b8-267">Çağırma `BuildServiceProvider` ikinci bir kapsayıcı oluşturur ve bu, parçalanmış tek ton oluşturabilir ve birden çok kapsayıcı genelinde nesne grafiklerine başvuru oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-267">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="884b8-268">Almanın doğru yolu, `LoginPath` Seçenekler deseninin, dı için yerleşik desteğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="884b8-268">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="884b8-269">Atılabilir geçici hizmetler, aktiften çıkarma için kapsayıcı tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="884b8-269">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="884b8-270">Bu, en üst düzey kapsayıcıdan çözümlenirse bir bellek sızıntısını açabilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-270">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="884b8-271">Uygulamanın kapsamlı hizmetleri yakalayan tekton içermediğinden emin olmak için kapsam doğrulamayı etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="884b8-271">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="884b8-272">Daha fazla bilgi için bkz. [kapsam doğrulaması](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="884b8-272">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="884b8-273">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="884b8-273">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="884b8-274">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="884b8-274">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="884b8-275">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="884b8-275">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="884b8-276">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="884b8-276">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="884b8-277">DI 'de çok kiracılı için önerilen desenler</span><span class="sxs-lookup"><span data-stu-id="884b8-277">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="884b8-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , ASP.NET Core üzerinde modüler, çok kiracılı uygulamalar oluşturmaya yönelik bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="884b8-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="884b8-279">Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="884b8-279">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="884b8-280">Yalnızca Orchard Core çerçevesini kullanarak, CMS 'ye özgü özelliklerden herhangi biri olmadan modüler ve çok kiracılı uygulamalar oluşturma örnekleri için bkz. [Orchard çekirdek örnekleri](https://github.com/OrchardCMS/OrchardCore.Samples) .</span><span class="sxs-lookup"><span data-stu-id="884b8-280">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="884b8-281">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="884b8-281">Framework-provided services</span></span>

<span data-ttu-id="884b8-282">`Startup.ConfigureServices`Yöntemi, uygulamanın kullandığı hizmetleri, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere kaydeder.</span><span class="sxs-lookup"><span data-stu-id="884b8-282">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="884b8-283">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="884b8-283">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="884b8-284">ASP.NET Core şablonlarına dayalı uygulamalar için Framework, 250 'den fazla hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="884b8-284">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="884b8-285">Aşağıdaki tabloda, bu çerçeve kayıtlı hizmetlerinin küçük bir örneği listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="884b8-285">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="884b8-286">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="884b8-286">Service Type</span></span>                                                                                    | <span data-ttu-id="884b8-287">Ömür</span><span class="sxs-lookup"><span data-stu-id="884b8-287">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="884b8-288">Larsa</span><span class="sxs-lookup"><span data-stu-id="884b8-288">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="884b8-289">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-289">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="884b8-290">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-290">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="884b8-291">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-291">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="884b8-292">Larsa</span><span class="sxs-lookup"><span data-stu-id="884b8-292">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="884b8-293">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-293">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="884b8-294">Larsa</span><span class="sxs-lookup"><span data-stu-id="884b8-294">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="884b8-295">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-295">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="884b8-296">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-296">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="884b8-297">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-297">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="884b8-298">Larsa</span><span class="sxs-lookup"><span data-stu-id="884b8-298">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="884b8-299">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-299">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="884b8-300">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-300">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="884b8-301">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-301">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="884b8-302">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="884b8-302">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="884b8-303">Dı uygulaması geliştirme için NDC Konferansı desenleri</span><span class="sxs-lookup"><span data-stu-id="884b8-303">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="884b8-304">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="884b8-304">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="884b8-305">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="884b8-305">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="884b8-306">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="884b8-306">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="884b8-307">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="884b8-307">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="884b8-308">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="884b8-308">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="884b8-309">[Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler) tarafından</span><span class="sxs-lookup"><span data-stu-id="884b8-309">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="884b8-310">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="884b8-310">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="884b8-311">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="884b8-311">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="884b8-312">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="884b8-312">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="884b8-313">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="884b8-313">Overview of dependency injection</span></span>

<span data-ttu-id="884b8-314">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="884b8-314">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="884b8-315">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="884b8-315">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="884b8-316">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-316">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="884b8-317">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="884b8-317">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="884b8-318">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="884b8-318">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="884b8-319">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="884b8-319">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="884b8-320">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="884b8-320">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="884b8-321">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="884b8-321">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="884b8-322">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="884b8-322">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="884b8-323">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="884b8-323">This implementation is difficult to unit test.</span></span> <span data-ttu-id="884b8-324">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="884b8-324">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="884b8-325">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="884b8-325">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="884b8-326">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="884b8-326">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="884b8-327">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="884b8-327">Registration of the dependency in a service container.</span></span> <span data-ttu-id="884b8-328">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="884b8-328">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="884b8-329">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-329">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="884b8-330">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="884b8-330">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="884b8-331">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="884b8-331">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="884b8-332">[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="884b8-332">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="884b8-333">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="884b8-333">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="884b8-334">`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister.</span><span class="sxs-lookup"><span data-stu-id="884b8-334">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="884b8-335">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="884b8-335">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="884b8-336">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="884b8-336">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="884b8-337">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="884b8-337">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="884b8-338">Çözümlenmesi gereken, genellikle *bağımlılık ağacı* , *bağımlılık grafiği* veya *nesne grafiği* olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="884b8-338">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph* .</span></span>

<span data-ttu-id="884b8-339">`IMyDependency` ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-339">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="884b8-340">`IMyDependency` kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="884b8-340">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="884b8-341">`ILogger<TCategoryName>` günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="884b8-341">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="884b8-342">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="884b8-342">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="884b8-343">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="884b8-343">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="884b8-344">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="884b8-344">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="884b8-345">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="884b8-345">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="884b8-346">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="884b8-346">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="884b8-347">Örneğin, `services.AddMvc()` hizmet :::no-loc(Razor)::: SAYFALARıNı ve MVC gerektir öğesini ekler.</span><span class="sxs-lookup"><span data-stu-id="884b8-347">For example, `services.AddMvc()` adds the services :::no-loc(Razor)::: Pages and MVC require.</span></span> <span data-ttu-id="884b8-348">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="884b8-348">We recommended that apps follow this convention.</span></span> <span data-ttu-id="884b8-349">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="884b8-349">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="884b8-350">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="884b8-350">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="884b8-351">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="884b8-351">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="884b8-352">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="884b8-352">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="884b8-353">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="884b8-353">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="884b8-354">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="884b8-354">Services injected into Startup</span></span>

<span data-ttu-id="884b8-355">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="884b8-355">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="884b8-356">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="884b8-356">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="884b8-357">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="884b8-357">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="884b8-358">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="884b8-358">Framework-provided services</span></span>

<span data-ttu-id="884b8-359">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="884b8-359">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="884b8-360">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="884b8-360">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="884b8-361">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="884b8-361">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="884b8-362">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="884b8-362">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="884b8-363">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="884b8-363">Service Type</span></span> | <span data-ttu-id="884b8-364">Ömür</span><span class="sxs-lookup"><span data-stu-id="884b8-364">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="884b8-365">Larsa</span><span class="sxs-lookup"><span data-stu-id="884b8-365">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="884b8-366">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-366">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="884b8-367">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-367">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="884b8-368">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-368">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="884b8-369">Larsa</span><span class="sxs-lookup"><span data-stu-id="884b8-369">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="884b8-370">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-370">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="884b8-371">Larsa</span><span class="sxs-lookup"><span data-stu-id="884b8-371">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="884b8-372">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-372">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="884b8-373">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-373">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="884b8-374">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-374">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="884b8-375">Larsa</span><span class="sxs-lookup"><span data-stu-id="884b8-375">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="884b8-376">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-376">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="884b8-377">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-377">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="884b8-378">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-378">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="884b8-379">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="884b8-379">Register additional services with extension methods</span></span>

<span data-ttu-id="884b8-380">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="884b8-380">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="884b8-381">Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*> :</span><span class="sxs-lookup"><span data-stu-id="884b8-381">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="884b8-382">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="884b8-382">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="884b8-383">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="884b8-383">Service lifetimes</span></span>

<span data-ttu-id="884b8-384">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="884b8-384">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="884b8-385">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="884b8-385">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="884b8-386">Larsa</span><span class="sxs-lookup"><span data-stu-id="884b8-386">Transient</span></span>

<span data-ttu-id="884b8-387">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="884b8-387">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="884b8-388">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="884b8-388">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="884b8-389">İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="884b8-389">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="884b8-390">Yayıl</span><span class="sxs-lookup"><span data-stu-id="884b8-390">Scoped</span></span>

<span data-ttu-id="884b8-391">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="884b8-391">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="884b8-392">İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="884b8-392">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="884b8-393">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="884b8-393">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="884b8-394">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="884b8-394">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="884b8-395">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="884b8-395">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="884b8-396">Adet</span><span class="sxs-lookup"><span data-stu-id="884b8-396">Singleton</span></span>

<span data-ttu-id="884b8-397">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="884b8-397">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="884b8-398">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="884b8-398">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="884b8-399">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-399">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="884b8-400">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="884b8-400">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="884b8-401">İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="884b8-401">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="884b8-402">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="884b8-402">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="884b8-403">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-403">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="884b8-404">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="884b8-404">Service registration methods</span></span>

<span data-ttu-id="884b8-405">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="884b8-405">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="884b8-406">Yöntem</span><span class="sxs-lookup"><span data-stu-id="884b8-406">Method</span></span> | <span data-ttu-id="884b8-407">Automatic</span><span class="sxs-lookup"><span data-stu-id="884b8-407">Automatic</span></span><br><span data-ttu-id="884b8-408">object</span><span class="sxs-lookup"><span data-stu-id="884b8-408">object</span></span><br><span data-ttu-id="884b8-409">elden</span><span class="sxs-lookup"><span data-stu-id="884b8-409">disposal</span></span> | <span data-ttu-id="884b8-410">Birden çok</span><span class="sxs-lookup"><span data-stu-id="884b8-410">Multiple</span></span><br><span data-ttu-id="884b8-411">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="884b8-411">implementations</span></span> | <span data-ttu-id="884b8-412">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="884b8-412">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="884b8-413">Örnek:</span><span class="sxs-lookup"><span data-stu-id="884b8-413">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="884b8-414">Yes</span><span class="sxs-lookup"><span data-stu-id="884b8-414">Yes</span></span> | <span data-ttu-id="884b8-415">Yes</span><span class="sxs-lookup"><span data-stu-id="884b8-415">Yes</span></span> | <span data-ttu-id="884b8-416">Hayır</span><span class="sxs-lookup"><span data-stu-id="884b8-416">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="884b8-417">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="884b8-417">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="884b8-418">Yes</span><span class="sxs-lookup"><span data-stu-id="884b8-418">Yes</span></span> | <span data-ttu-id="884b8-419">Yes</span><span class="sxs-lookup"><span data-stu-id="884b8-419">Yes</span></span> | <span data-ttu-id="884b8-420">Yes</span><span class="sxs-lookup"><span data-stu-id="884b8-420">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="884b8-421">Örnek:</span><span class="sxs-lookup"><span data-stu-id="884b8-421">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="884b8-422">Yes</span><span class="sxs-lookup"><span data-stu-id="884b8-422">Yes</span></span> | <span data-ttu-id="884b8-423">Hayır</span><span class="sxs-lookup"><span data-stu-id="884b8-423">No</span></span> | <span data-ttu-id="884b8-424">Hayır</span><span class="sxs-lookup"><span data-stu-id="884b8-424">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="884b8-425">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="884b8-425">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="884b8-426">Hayır</span><span class="sxs-lookup"><span data-stu-id="884b8-426">No</span></span> | <span data-ttu-id="884b8-427">Yes</span><span class="sxs-lookup"><span data-stu-id="884b8-427">Yes</span></span> | <span data-ttu-id="884b8-428">Yes</span><span class="sxs-lookup"><span data-stu-id="884b8-428">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="884b8-429">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="884b8-429">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="884b8-430">Hayır</span><span class="sxs-lookup"><span data-stu-id="884b8-430">No</span></span> | <span data-ttu-id="884b8-431">Hayır</span><span class="sxs-lookup"><span data-stu-id="884b8-431">No</span></span> | <span data-ttu-id="884b8-432">Yes</span><span class="sxs-lookup"><span data-stu-id="884b8-432">Yes</span></span> |

<span data-ttu-id="884b8-433">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="884b8-433">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="884b8-434">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="884b8-434">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="884b8-435">Hizmeti yalnızca bir uygulama türüyle kaydetmek, bu hizmeti aynı uygulama ve hizmet türüyle kaydetmeye eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="884b8-435">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="884b8-436">Bu, bir hizmetin birden çok uygulamasının açık bir hizmet türü kullanmayan yöntemler kullanılarak kaydedilamamasının nedenleridir.</span><span class="sxs-lookup"><span data-stu-id="884b8-436">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="884b8-437">Bu yöntemler bir hizmetin birden fazla *örneğini* kaydedebilir, ancak hepsi aynı *uygulama* türüne sahip olur.</span><span class="sxs-lookup"><span data-stu-id="884b8-437">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="884b8-438">Yukarıdaki hizmet kayıt yöntemlerinden herhangi biri aynı hizmet türünün birden çok hizmet örneğini kaydetmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-438">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="884b8-439">Aşağıdaki örnekte, `AddSingleton` hizmet türü olarak ile iki kez çağırılır `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="884b8-439">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="884b8-440">İçin ikinci çağrı, `AddSingleton` olarak çözümlendikten önceki bir öncekini geçersiz kılar `IMyDependency` ve aracılığıyla birden çok hizmet çözümlendiğinde bir öncekini ekler `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="884b8-440">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="884b8-441">Hizmetler, ile çözümlendiklerinde kaydedildikleri sırada görüntülenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="884b8-441">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="884b8-442">Framework Ayrıca, `TryAdd{LIFETIME}` yalnızca kayıtlı bir uygulama olmadığında hizmeti kaydeden genişletme yöntemleri de sağlar.</span><span class="sxs-lookup"><span data-stu-id="884b8-442">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="884b8-443">Aşağıdaki örnekte, `AddSingleton` `MyDependency` için bir uygulama olarak Yazmaçları çağrısı `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="884b8-443">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="884b8-444">' A çağrı, `TryAddSingleton` `IMyDependency` zaten kayıtlı bir uygulamaya sahip olduğu için hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="884b8-444">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="884b8-445">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="884b8-445">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="884b8-446">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde* bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="884b8-446">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type* .</span></span> <span data-ttu-id="884b8-447">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="884b8-447">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="884b8-448">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="884b8-448">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="884b8-449">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="884b8-449">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="884b8-450">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="884b8-450">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="884b8-451">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="884b8-451">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="884b8-452">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="884b8-452">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="884b8-453">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="884b8-453">Constructor injection behavior</span></span>

<span data-ttu-id="884b8-454">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="884b8-454">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="884b8-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="884b8-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="884b8-456">`ActivatorUtilities` Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="884b8-456">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="884b8-457">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="884b8-457">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="884b8-458">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="884b8-458">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="884b8-459">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="884b8-459">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="884b8-460">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-460">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="884b8-461">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="884b8-461">Entity Framework contexts</span></span>

<span data-ttu-id="884b8-462">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="884b8-462">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="884b8-463">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="884b8-463">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="884b8-464">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-464">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="884b8-465">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="884b8-465">Lifetime and registration options</span></span>

<span data-ttu-id="884b8-466">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="884b8-466">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="884b8-467">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="884b8-467">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="884b8-468">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="884b8-468">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="884b8-469">`Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="884b8-469">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="884b8-470">, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` .</span><span class="sxs-lookup"><span data-stu-id="884b8-470">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="884b8-471">`OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="884b8-471">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="884b8-472">Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="884b8-472">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="884b8-473">`OperationService` sınıfının yeni bir örneğini alır `IOperationTransient` .</span><span class="sxs-lookup"><span data-stu-id="884b8-473">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="884b8-474">Yeni örnek farklı bir şekilde oluşturur `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="884b8-474">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="884b8-475">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="884b8-475">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="884b8-476">İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="884b8-476">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="884b8-477">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="884b8-477">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="884b8-478">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="884b8-478">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="884b8-479">`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="884b8-479">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="884b8-480">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="884b8-480">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="884b8-481">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="884b8-481">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="884b8-482">Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="884b8-482">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="884b8-483">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="884b8-483">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="884b8-484">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="884b8-484">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="884b8-485">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="884b8-485">**First request:**</span></span>

<span data-ttu-id="884b8-486">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="884b8-486">Controller operations:</span></span>

<span data-ttu-id="884b8-487">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="884b8-487">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="884b8-488">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="884b8-488">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="884b8-489">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="884b8-489">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="884b8-490">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="884b8-490">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="884b8-491">`OperationService` operasyonları</span><span class="sxs-lookup"><span data-stu-id="884b8-491">`OperationService` operations:</span></span>

<span data-ttu-id="884b8-492">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="884b8-492">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="884b8-493">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="884b8-493">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="884b8-494">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="884b8-494">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="884b8-495">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="884b8-495">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="884b8-496">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="884b8-496">**Second request:**</span></span>

<span data-ttu-id="884b8-497">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="884b8-497">Controller operations:</span></span>

<span data-ttu-id="884b8-498">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="884b8-498">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="884b8-499">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="884b8-499">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="884b8-500">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="884b8-500">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="884b8-501">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="884b8-501">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="884b8-502">`OperationService` operasyonları</span><span class="sxs-lookup"><span data-stu-id="884b8-502">`OperationService` operations:</span></span>

<span data-ttu-id="884b8-503">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="884b8-503">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="884b8-504">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="884b8-504">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="884b8-505">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="884b8-505">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="884b8-506">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="884b8-506">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="884b8-507">`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="884b8-507">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="884b8-508">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-508">*Transient* objects are always different.</span></span> <span data-ttu-id="884b8-509">Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-509">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="884b8-510">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="884b8-510">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="884b8-511">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-511">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="884b8-512">*Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="884b8-512">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="884b8-513">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="884b8-513">Call services from main</span></span>

<span data-ttu-id="884b8-514"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="884b8-514">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="884b8-515">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-515">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="884b8-516">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="884b8-516">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="884b8-517">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="884b8-517">Scope validation</span></span>

<span data-ttu-id="884b8-518">Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="884b8-518">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="884b8-519">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="884b8-519">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="884b8-520">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="884b8-520">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="884b8-521">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="884b8-521">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="884b8-522">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="884b8-522">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="884b8-523">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="884b8-523">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="884b8-524">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="884b8-524">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="884b8-525">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="884b8-525">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="884b8-526">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="884b8-526">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="884b8-527">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="884b8-527">Request Services</span></span>

<span data-ttu-id="884b8-528">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="884b8-528">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="884b8-529">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="884b8-529">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="884b8-530">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="884b8-530">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="884b8-531">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-531">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="884b8-532">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="884b8-532">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="884b8-533">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="884b8-533">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="884b8-534">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="884b8-534">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="884b8-535">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="884b8-535">Design services for dependency injection</span></span>

<span data-ttu-id="884b8-536">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="884b8-536">Best practices are to:</span></span>

* <span data-ttu-id="884b8-537">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="884b8-537">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="884b8-538">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="884b8-538">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="884b8-539">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="884b8-539">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="884b8-540">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="884b8-540">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="884b8-541">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="884b8-541">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="884b8-542">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="884b8-542">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="884b8-543">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="884b8-543">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="884b8-544">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="884b8-544">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="884b8-545">:::no-loc(Razor):::Sayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="884b8-545">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="884b8-546">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-546">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="884b8-547">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="884b8-547">Disposal of services</span></span>

<span data-ttu-id="884b8-548">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="884b8-548">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="884b8-549">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="884b8-549">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="884b8-550">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="884b8-550">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="884b8-551">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="884b8-551">In the following example:</span></span>

* <span data-ttu-id="884b8-552">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="884b8-552">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="884b8-553">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="884b8-553">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="884b8-554">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="884b8-554">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="884b8-555">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="884b8-555">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="884b8-556">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="884b8-556">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="884b8-557">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="884b8-557">Transient, limited lifetime</span></span>

<span data-ttu-id="884b8-558">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="884b8-558">**Scenario**</span></span>

<span data-ttu-id="884b8-559">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="884b8-559">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="884b8-560">Örnek, kök kapsamda çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="884b8-560">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="884b8-561">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-561">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="884b8-562">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="884b8-562">**Solution**</span></span>

<span data-ttu-id="884b8-563">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="884b8-563">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="884b8-564">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="884b8-564">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="884b8-565">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="884b8-565">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="884b8-566">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="884b8-566">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="884b8-567"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="884b8-567">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="884b8-568">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="884b8-568">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="884b8-569">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="884b8-569">**Scenario**</span></span>

<span data-ttu-id="884b8-570">Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-570">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="884b8-571">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="884b8-571">**Solution**</span></span>

<span data-ttu-id="884b8-572">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="884b8-572">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="884b8-573"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="884b8-573">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="884b8-574"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="884b8-574">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="884b8-575">Yaşam süresi sona erdirmek için kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="884b8-575">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="884b8-576">Genel Yönergeler</span><span class="sxs-lookup"><span data-stu-id="884b8-576">General Guidelines</span></span>

* <span data-ttu-id="884b8-577"><xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme.</span><span class="sxs-lookup"><span data-stu-id="884b8-577">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="884b8-578">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="884b8-578">Use the factory pattern instead.</span></span>
* <span data-ttu-id="884b8-579">Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün.</span><span class="sxs-lookup"><span data-stu-id="884b8-579">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="884b8-580">Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.</span><span class="sxs-lookup"><span data-stu-id="884b8-580">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="884b8-581">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="884b8-581">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="884b8-582"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-582">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="884b8-583">Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="884b8-583">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="884b8-584">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="884b8-584">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="884b8-585">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="884b8-585">Default service container replacement</span></span>

<span data-ttu-id="884b8-586">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="884b8-586">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="884b8-587">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="884b8-587">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="884b8-588">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="884b8-588">Property injection</span></span>
* <span data-ttu-id="884b8-589">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="884b8-589">Injection based on name</span></span>
* <span data-ttu-id="884b8-590">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="884b8-590">Child containers</span></span>
* <span data-ttu-id="884b8-591">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="884b8-591">Custom lifetime management</span></span>
* <span data-ttu-id="884b8-592">`Func<T>` yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="884b8-592">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="884b8-593">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="884b8-593">Convention-based registration</span></span>

<span data-ttu-id="884b8-594">Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="884b8-594">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="884b8-595">Autofac</span><span class="sxs-lookup"><span data-stu-id="884b8-595">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="884b8-596">Drıioc</span><span class="sxs-lookup"><span data-stu-id="884b8-596">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="884b8-597">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="884b8-597">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="884b8-598">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="884b8-598">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="884b8-599">E</span><span class="sxs-lookup"><span data-stu-id="884b8-599">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="884b8-600">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="884b8-600">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="884b8-601">Unity</span><span class="sxs-lookup"><span data-stu-id="884b8-601">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="884b8-602">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="884b8-602">Thread safety</span></span>

<span data-ttu-id="884b8-603">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="884b8-603">Create thread-safe singleton services.</span></span> <span data-ttu-id="884b8-604">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-604">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="884b8-605">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="884b8-605">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="884b8-606">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="884b8-606">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="884b8-607">Öneriler</span><span class="sxs-lookup"><span data-stu-id="884b8-607">Recommendations</span></span>

* <span data-ttu-id="884b8-608">`async/await` ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="884b8-608">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="884b8-609">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="884b8-609">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="884b8-610">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="884b8-610">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="884b8-611">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="884b8-611">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="884b8-612">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="884b8-612">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="884b8-613">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="884b8-613">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="884b8-614">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="884b8-614">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="884b8-615">Hizmetlere statik erişimi önleyin.</span><span class="sxs-lookup"><span data-stu-id="884b8-615">Avoid static access to services.</span></span> <span data-ttu-id="884b8-616">Örneğin, bir başka yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik olarak yazılanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="884b8-616">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="884b8-617">[Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin* kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="884b8-617">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="884b8-618"><xref:System.IServiceProvider.GetService*>Bunun yerine, şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:</span><span class="sxs-lookup"><span data-stu-id="884b8-618">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="884b8-619">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="884b8-619">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="884b8-620">**Doğru** :</span><span class="sxs-lookup"><span data-stu-id="884b8-620">**Correct** :</span></span>

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

* <span data-ttu-id="884b8-621">Kullanarak çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="884b8-621">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="884b8-622">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="884b8-622">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="884b8-623">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="884b8-623">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="884b8-624">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="884b8-624">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="884b8-625">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="884b8-625">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="884b8-626">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="884b8-626">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="884b8-627">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="884b8-627">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="884b8-628">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="884b8-628">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="884b8-629">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="884b8-629">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="884b8-630">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="884b8-630">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="884b8-631">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="884b8-631">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="884b8-632">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="884b8-632">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
