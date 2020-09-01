---
title: ASP.NET Core bağımlılık ekleme
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
ms.openlocfilehash: 2d002e075f9d57654589b540e522307c363d9660
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153551"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="967cc-103">ASP.NET Core bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="967cc-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="967cc-104">[Kirk Larkabağı](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="967cc-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="967cc-105">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="967cc-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="967cc-106">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="967cc-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="967cc-107">Seçeneklerin bağımlılığı ekleme hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/options> ..</span><span class="sxs-lookup"><span data-stu-id="967cc-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="967cc-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="967cc-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="967cc-109">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="967cc-109">Overview of dependency injection</span></span>

<span data-ttu-id="967cc-110">*Bağımlılık* , başka bir nesnenin bağımlı olduğu bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="967cc-110">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="967cc-111">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` diğer sınıfların bağımlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="967cc-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="967cc-112">Bir sınıf, `MyDependency` yöntemini kullanmak için sınıfının bir örneğini oluşturabilir `WriteMessage` .</span><span class="sxs-lookup"><span data-stu-id="967cc-112">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="967cc-113">Aşağıdaki örnekte, `MyDependency` sınıfı sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="967cc-113">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="967cc-114">Sınıfı oluşturur ve doğrudan `MyDependency` sınıfa bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-114">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="967cc-115">Önceki örnekte olduğu gibi kod bağımlılıkları sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="967cc-115">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="967cc-116">`MyDependency`Farklı bir uygulamayla değiştirmek için, `IndexModel` sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="967cc-116">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="967cc-117">`MyDependency`Bağımlılıkları varsa, sınıf tarafından da yapılandırılmalıdır `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="967cc-117">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="967cc-118">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="967cc-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="967cc-119">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="967cc-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="967cc-120">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="967cc-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="967cc-121">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="967cc-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="967cc-122">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="967cc-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="967cc-123">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="967cc-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="967cc-124">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="967cc-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="967cc-125">Hizmetler genellikle uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-125">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="967cc-126">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="967cc-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="967cc-127">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="967cc-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="967cc-128">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirimi `WriteMessage` yöntemini tanımlar:</span><span class="sxs-lookup"><span data-stu-id="967cc-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="967cc-129">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="967cc-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="967cc-130">Örnek uygulama, `IMyDependency` hizmeti somut tür ile kaydeder `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="967cc-130">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="967cc-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Yöntemi, hizmeti tek bir isteğin ömrü olan kapsamlı bir yaşam süresine kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967cc-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="967cc-132">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="967cc-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="967cc-133">Örnek uygulamada, `IMyDependency` hizmet istenir ve yöntemi çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="967cc-133">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="967cc-134">DI modelini kullanarak denetleyici:</span><span class="sxs-lookup"><span data-stu-id="967cc-134">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="967cc-135">Somut türü kullanmaz `MyDependency` , yalnızca `IMyDependency` uyguladığı arabirim.</span><span class="sxs-lookup"><span data-stu-id="967cc-135">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="967cc-136">Bu, denetleyicinin, denetleyiciyi değiştirmeden kullandığı uygulamayı değiştirmeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="967cc-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="967cc-137">Bir örneği oluşturmaz `MyDependency` , bu, dı kapsayıcısı tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="967cc-137">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="967cc-138">Arabirim uygulanması, `IMyDependency` yerleşik günlük API 'si kullanılarak artırılabilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="967cc-139">Updated `ConfigureServices` yöntemi yeni `IMyDependency` uygulamayı kaydeder:</span><span class="sxs-lookup"><span data-stu-id="967cc-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="967cc-140">`MyDependency2`<xref:Microsoft.Extensions.Logging.ILogger%601>, oluşturucuda istediği öğesine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-140">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="967cc-141">`ILogger<TCategoryName>`[Framework tarafından sağlanmış bir hizmettir](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="967cc-141">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="967cc-142">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="967cc-142">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="967cc-143">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="967cc-143">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="967cc-144">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="967cc-144">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="967cc-145">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="967cc-145">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="967cc-146">Kapsayıcı, `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="967cc-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="967cc-147">Bağımlılık ekleme terminolojisi ' nde bir hizmet:</span><span class="sxs-lookup"><span data-stu-id="967cc-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="967cc-148">Genellikle hizmet gibi diğer nesnelere hizmet sağlayan bir nesnedir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="967cc-148">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="967cc-149">Bir Web hizmetiyle ilgili değildir, ancak hizmet bir Web hizmeti kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="967cc-150">Çerçeve, güçlü bir [günlük](xref:fundamentals/logging/index) sistemi sağlar.</span><span class="sxs-lookup"><span data-stu-id="967cc-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="967cc-151">`IMyDependency`Yukarıdaki örneklerde gösterilen uygulamalar, günlüğü uygulamamak için değil temel dı 'yi göstermek için yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="967cc-151">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="967cc-152">Çoğu uygulamanın Günlükçüler yazması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="967cc-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="967cc-153">Aşağıdaki kod, bir hizmetin kaydedilmesini gerektirmeyen varsayılan günlük kaydını kullanmayı göstermektedir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="967cc-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="967cc-154">Yukarıdaki kodu kullanarak, `ConfigureServices` Framework tarafından [günlüğe kaydetme](xref:fundamentals/logging/index) sağlandığı için güncelleştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="967cc-154">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="967cc-155">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="967cc-155">Services injected into Startup</span></span>

<span data-ttu-id="967cc-156">Hizmetler `Startup` oluşturucuya ve `Startup.Configure` metoduna eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-156">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="967cc-157">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmetler eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="967cc-157">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="967cc-158">Dı kapsayıcısına kayıtlı tüm hizmetler `Startup.Configure` metoduna eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-158">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="967cc-159">Daha fazla bilgi için, bkz <xref:fundamentals/startup> . ve [erişim yapılandırması başlangıç](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="967cc-159">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="967cc-160">Uzantı yöntemleriyle hizmet gruplarını kaydet</span><span class="sxs-lookup"><span data-stu-id="967cc-160">Register groups of services with extension methods</span></span>

<span data-ttu-id="967cc-161">ASP.NET Core Framework, bir grup ilgili hizmeti kaydetmek için bir kural kullanır.</span><span class="sxs-lookup"><span data-stu-id="967cc-161">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="967cc-162">Kural, `Add{GROUP_NAME}` bir Framework özelliği için gereken tüm hizmetleri kaydetmek için tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="967cc-162">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="967cc-163">Örneğin, Microsoft. Extensions. Dependencyınjection. MvcServiceCollectionExtensions. AddControllers> genişletme yöntemi <MVC denetleyicileri için gereken hizmetleri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967cc-163">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="967cc-164">Aşağıdaki kod, Razor bireysel kullanıcı hesapları kullanılarak sayfalar şablonu tarafından oluşturulur ve uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemeyi gösterir <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ve <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="967cc-164">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="967cc-165">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="967cc-165">Service lifetimes</span></span>

<span data-ttu-id="967cc-166">Hizmetler aşağıdaki yaşam sürelerinin biriyle kaydedilebilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-166">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="967cc-167">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-167">Transient</span></span>
* <span data-ttu-id="967cc-168">Yayıl</span><span class="sxs-lookup"><span data-stu-id="967cc-168">Scoped</span></span>
* <span data-ttu-id="967cc-169">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-169">Singleton</span></span>

<span data-ttu-id="967cc-170">Aşağıdaki bölümlerde, önceki yaşam sürelerinin her biri açıklanır.</span><span class="sxs-lookup"><span data-stu-id="967cc-170">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="967cc-171">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="967cc-171">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="967cc-172">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-172">Transient</span></span>

<span data-ttu-id="967cc-173">Geçici ömür Hizmetleri, hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="967cc-173">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="967cc-174">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="967cc-174">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="967cc-175">Geçici Hizmetleri ile kaydedin <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="967cc-175">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="967cc-176">İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="967cc-176">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="967cc-177">Yayıl</span><span class="sxs-lookup"><span data-stu-id="967cc-177">Scoped</span></span>

<span data-ttu-id="967cc-178">Kapsamlı ömür Hizmetleri, istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="967cc-178">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="967cc-179">Kapsamlı hizmetleri ile kaydedin <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="967cc-179">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="967cc-180">İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="967cc-180">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="967cc-181">Entity Framework Core kullanılırken, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> genişletme yöntemi, `DbContext` Varsayılan olarak kapsamlı yaşam süresine sahip türleri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967cc-181">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="967cc-182">Kapsamlı bir hizmeti tek bir ***sunucudan çözümleyin.***</span><span class="sxs-lookup"><span data-stu-id="967cc-182">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="967cc-183">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-183">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="967cc-184">Şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="967cc-184">It's fine to:</span></span>

* <span data-ttu-id="967cc-185">Tek bir hizmeti kapsamlı veya geçici bir hizmetten çözümleyin.</span><span class="sxs-lookup"><span data-stu-id="967cc-185">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="967cc-186">Kapsamlı bir hizmeti başka bir kapsamlı veya geçici hizmetten çözün.</span><span class="sxs-lookup"><span data-stu-id="967cc-186">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="967cc-187">Varsayılan olarak, geliştirme ortamında, bir hizmetin daha uzun bir yaşam süresine sahip başka bir hizmetten çözülmesi bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="967cc-187">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="967cc-188">Daha fazla bilgi için bkz. [kapsam doğrulaması](#sv).</span><span class="sxs-lookup"><span data-stu-id="967cc-188">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="967cc-189">Ara yazılım kapsamındaki hizmetleri kullanmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="967cc-189">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="967cc-190">Hizmeti, ara yazılımı `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="967cc-190">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="967cc-191">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) kullanılması, kapsamlı hizmeti tek bir gibi davranmaya zordığı için bir çalışma zamanı özel durumu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="967cc-191">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="967cc-192">[Ömür ve kayıt seçenekleri](#lifetime-and-registration-options) bölümündeki örnek, `InvokeAsync` yaklaşımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="967cc-192">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="967cc-193">[Fabrika tabanlı ara yazılım](xref:fundamentals/middleware/extensibility)kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-193">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="967cc-194">Bu yaklaşım kullanılarak kaydedilen ara yazılım, kapsamlı hizmetlerin ara yazılım yöntemine eklenmiş olmasına olanak tanıyan istemci isteği (bağlantı) başına etkinleştirilir `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="967cc-194">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="967cc-195">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="967cc-195">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="967cc-196">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-196">Singleton</span></span>

<span data-ttu-id="967cc-197">Tek yaşam süresi Hizmetleri şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="967cc-197">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="967cc-198">İlk kez istenirler.</span><span class="sxs-lookup"><span data-stu-id="967cc-198">The first time they're requested.</span></span>
* <span data-ttu-id="967cc-199">Geliştirici tarafından doğrudan kapsayıcıya bir uygulama örneği sağlarken.</span><span class="sxs-lookup"><span data-stu-id="967cc-199">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="967cc-200">Bu yaklaşım nadiren gereklidir.</span><span class="sxs-lookup"><span data-stu-id="967cc-200">This approach is rarely needed.</span></span>

<span data-ttu-id="967cc-201">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="967cc-201">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="967cc-202">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="967cc-202">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="967cc-203">Tek tasarım modelini uygulamayın ve tek bir atma kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="967cc-203">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="967cc-204">Hizmetler, kapsayıcıyı hizmetten çözümleyen kodla hiçbir şekilde atılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-204">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="967cc-205">Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı kendiliğinden otomatik olarak atar.</span><span class="sxs-lookup"><span data-stu-id="967cc-205">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="967cc-206">İle Singleton hizmetlerini kaydettirin <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="967cc-206">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="967cc-207">Tek hizmetler iş parçacığı güvenli olmalıdır ve genellikle durum bilgisiz hizmetlerde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="967cc-207">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="967cc-208">İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="967cc-208">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="967cc-209">Uygulama kapatılıncaya kadar bellek yayımlanmadığı için, tek bir hizmetle bellek kullanımını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="967cc-209">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="967cc-210">Kapsamlı bir hizmeti tek bir ***sunucudan çözümleyin.***</span><span class="sxs-lookup"><span data-stu-id="967cc-210">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="967cc-211">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="967cc-212">Tek bir hizmeti kapsamlı veya geçici bir hizmetten çözümlemek çok iyidir.</span><span class="sxs-lookup"><span data-stu-id="967cc-212">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="967cc-213">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="967cc-213">Service registration methods</span></span>

<span data-ttu-id="967cc-214">Framework, belirli senaryolarda yararlı olan hizmet kayıt uzantısı yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="967cc-214">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="967cc-215">Yöntem</span><span class="sxs-lookup"><span data-stu-id="967cc-215">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="967cc-216">Automatic</span><span class="sxs-lookup"><span data-stu-id="967cc-216">Automatic</span></span><br><span data-ttu-id="967cc-217">object</span><span class="sxs-lookup"><span data-stu-id="967cc-217">object</span></span><br><span data-ttu-id="967cc-218">elden</span><span class="sxs-lookup"><span data-stu-id="967cc-218">disposal</span></span> | <span data-ttu-id="967cc-219">Birden çok</span><span class="sxs-lookup"><span data-stu-id="967cc-219">Multiple</span></span><br><span data-ttu-id="967cc-220">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="967cc-220">implementations</span></span> | <span data-ttu-id="967cc-221">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="967cc-221">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="967cc-222">Örnek:</span><span class="sxs-lookup"><span data-stu-id="967cc-222">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="967cc-223">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-223">Yes</span></span>                             | <span data-ttu-id="967cc-224">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-224">Yes</span></span>                         | <span data-ttu-id="967cc-225">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-225">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="967cc-226">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="967cc-226">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="967cc-227">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-227">Yes</span></span>                             | <span data-ttu-id="967cc-228">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-228">Yes</span></span>                         | <span data-ttu-id="967cc-229">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-229">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="967cc-230">Örnek:</span><span class="sxs-lookup"><span data-stu-id="967cc-230">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="967cc-231">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-231">Yes</span></span>                             | <span data-ttu-id="967cc-232">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-232">No</span></span>                          | <span data-ttu-id="967cc-233">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-233">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="967cc-234">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="967cc-234">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="967cc-235">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-235">No</span></span>                              | <span data-ttu-id="967cc-236">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-236">Yes</span></span>                         | <span data-ttu-id="967cc-237">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-237">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="967cc-238">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="967cc-238">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="967cc-239">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-239">No</span></span>                              | <span data-ttu-id="967cc-240">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-240">No</span></span>                          | <span data-ttu-id="967cc-241">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-241">Yes</span></span>       |

<span data-ttu-id="967cc-242">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="967cc-242">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="967cc-243">[Test için izleme türleri](xref:test/integration-tests#inject-mock-services)olduğunda birden çok uygulama kullanılması yaygındır.</span><span class="sxs-lookup"><span data-stu-id="967cc-243">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="967cc-244">Framework Ayrıca, `TryAdd{LIFETIME}` yalnızca kayıtlı bir uygulama olmadığında hizmeti kaydeden genişletme yöntemleri de sağlar.</span><span class="sxs-lookup"><span data-stu-id="967cc-244">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="967cc-245">Aşağıdaki örnekte, `AddSingleton` `MyDependency` için bir uygulama olarak Yazmaçları çağrısı `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="967cc-245">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="967cc-246">`TryAddSingleton` `IMyDependency` Zaten kayıtlı bir uygulamaya sahip olduğundan, çağrısının bir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="967cc-246">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="967cc-247">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="967cc-247">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="967cc-248">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) yöntemleri, yalnızca *aynı türde*bir uygulama olmadığında hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967cc-248">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="967cc-249">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="967cc-249">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="967cc-250">Hizmetleri kaydederken, aynı türden biri zaten eklenmediyse geliştirici bir örnek eklememelidir.</span><span class="sxs-lookup"><span data-stu-id="967cc-250">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="967cc-251">Genellikle, kitaplık yazarları `TryAddEnumerable` kapsayıcıda bir uygulamanın birden çok kopyasını kaydetmemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="967cc-251">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="967cc-252">Aşağıdaki örnekte, `TryAddEnumerable` `MyDependency` için bir uygulama olarak kaydeden ilk çağrı `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="967cc-252">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="967cc-253">İçin ikinci çağrı kaydettirir `MyDependency` `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="967cc-253">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="967cc-254">`IMyDependency1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü çağrının etkisi yoktur `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="967cc-254">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="967cc-255">Hizmet kaydı, aynı türde birden çok uygulama kaydedilirken genellikle sıralı olarak bağımsızdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-255">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="967cc-256">`IServiceCollection` bir <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> nesne koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="967cc-256">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="967cc-257">Aşağıdaki örnek, oluşturma ve ekleme yoluyla bir hizmetin nasıl kaydedileceği gösterilmektedir `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="967cc-257">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="967cc-258">Yerleşik `Add{LIFETIME}` Yöntemler aynı yaklaşımı kullanır.</span><span class="sxs-lookup"><span data-stu-id="967cc-258">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="967cc-259">Örneğin, bkz. [Addkapsamlıdır kaynak kodu](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="967cc-259">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="967cc-260">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="967cc-260">Constructor injection behavior</span></span>

<span data-ttu-id="967cc-261">Hizmetler, kullanılarak çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-261">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="967cc-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="967cc-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="967cc-263">Kapsayıcıda kayıtlı olmayan nesneler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="967cc-263">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="967cc-264">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), MVC denetleyicileri ve [model ciltler](xref:mvc/models/model-binding)gibi Framework özellikleriyle kullanılır.</span><span class="sxs-lookup"><span data-stu-id="967cc-264">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="967cc-265">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="967cc-265">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="967cc-266">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="967cc-266">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="967cc-267">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="967cc-267">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="967cc-268">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-268">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="967cc-269">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="967cc-269">Entity Framework contexts</span></span>

<span data-ttu-id="967cc-270">Varsayılan olarak, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan, Entity Framework bağlamları [kapsama yaşam süresi](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="967cc-270">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="967cc-271">Farklı bir yaşam süresi kullanmak için, bir aşırı yükleme kullanarak yaşam süresini belirtin <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> .</span><span class="sxs-lookup"><span data-stu-id="967cc-271">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="967cc-272">Belirli bir yaşam süresinin Hizmetleri, hizmetin yaşam süresinden kısa olan bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-272">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="967cc-273">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="967cc-273">Lifetime and registration options</span></span>

<span data-ttu-id="967cc-274">Hizmet yaşam süreleri ve kayıt seçenekleri arasındaki farkı göstermek için, bir görevi tanımlayıcı ile bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="967cc-274">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="967cc-275">Bir işlemin hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde hizmetin aynı ya da farklı örneklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="967cc-275">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="967cc-276">Aşağıdaki `Operation` sınıf önceki arabirimlerin tümünü uygular.</span><span class="sxs-lookup"><span data-stu-id="967cc-276">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="967cc-277">`Operation`Oluşturucu BIR GUID oluşturur ve son 4 karakteri `OperationId` özellikte depolar:</span><span class="sxs-lookup"><span data-stu-id="967cc-277">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="967cc-278">`Startup.ConfigureServices`Yöntemi, `Operation` adlandırılmış yaşam sürelerine göre sınıfının birden çok kaydı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="967cc-278">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="967cc-279">Örnek uygulama, isteklerin içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="967cc-279">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="967cc-280">`IndexModel`Ve ara yazılım her `IOperation` tür türü ister, `OperationId` her biri için günlük kaydı:</span><span class="sxs-lookup"><span data-stu-id="967cc-280">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="967cc-281">Uygulamasına benzer şekilde, `IndexModel` Ara yazılım aynı hizmetleri çözümler:</span><span class="sxs-lookup"><span data-stu-id="967cc-281">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="967cc-282">Kapsamındaki hizmetlerin yöntemde çözülmesi gerekir `InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="967cc-282">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="967cc-283">Günlükçü çıktısı şunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="967cc-283">The logger output shows:</span></span>

* <span data-ttu-id="967cc-284">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-284">*Transient* objects are always different.</span></span> <span data-ttu-id="967cc-285">Geçici `OperationId` değer, `IndexModel` Ara yazılım içindeki ve içinde farklıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-285">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="967cc-286">*Kapsamlı* nesneler her istek için aynıdır, ancak her istekte farklıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-286">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="967cc-287">*Tek* nesneler her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-287">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="967cc-288">Günlüğe kaydetme çıkışını azaltmak için, dosyadaki *appsettings.Development.js* "Logging: LogLevel: Microsoft: Error" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="967cc-288">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="967cc-289">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="967cc-289">Call services from main</span></span>

<span data-ttu-id="967cc-290"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967cc-290">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="967cc-291">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-291">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="967cc-292">Aşağıdaki örnek, kapsamındaki hizmete nasıl erişeceğinden `IMyDependency` ve yönteminin içinde nasıl çağrılacağını göstermektedir `WriteMessage` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="967cc-292">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="967cc-293">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="967cc-293">Scope validation</span></span>

<span data-ttu-id="967cc-294">Uygulama [geliştirme ortamında](xref:fundamentals/environments) çalıştırıldığında ve konağı oluşturmak Için [createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) çağırdığında, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="967cc-294">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="967cc-295">Kapsamlı hizmetler kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="967cc-295">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="967cc-296">Kapsamlı hizmetler tekton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="967cc-296">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="967cc-297">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="967cc-297">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="967cc-298">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulamanın ömrüne karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="967cc-298">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="967cc-299">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="967cc-299">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="967cc-300">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü, uygulama kapandığında yalnızca kök kapsayıcı tarafından atıldığı için etkin bir şekilde tek bir yükseltilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-300">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="967cc-301">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="967cc-301">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="967cc-302">Daha fazla bilgi için bkz. [kapsam doğrulaması](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="967cc-302">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="967cc-303">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="967cc-303">Request Services</span></span>

<span data-ttu-id="967cc-304">Bir ASP.NET Core isteği içinde kullanılabilen hizmetler, [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="967cc-304">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="967cc-305">Bir isteğin içinden hizmetler istendiğinde, hizmetler ve bağımlılıkları `RequestServices` koleksiyonundan çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="967cc-305">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="967cc-306">Çerçeve, istek başına bir kapsam oluşturur ve `RequestServices` kapsamlı hizmet sağlayıcısını kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="967cc-306">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="967cc-307">Tüm kapsamlı hizmetler, istek etkin olduğu sürece geçerli olur.</span><span class="sxs-lookup"><span data-stu-id="967cc-307">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="967cc-308">Koleksiyondan Hizmetleri çözümlemek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="967cc-308">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="967cc-309">Bu, test etmek daha kolay olan sınıfların sonucunu vermez.</span><span class="sxs-lookup"><span data-stu-id="967cc-309">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="967cc-310">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="967cc-310">Design services for dependency injection</span></span>

<span data-ttu-id="967cc-311">Bağımlılık ekleme için Hizmetleri tasarlarken:</span><span class="sxs-lookup"><span data-stu-id="967cc-311">When designing services for dependency injection:</span></span>

* <span data-ttu-id="967cc-312">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="967cc-312">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="967cc-313">Bunun yerine Singleton hizmetlerini kullanmak için uygulama tasarlayarak genel durum oluşturmamaya özen gösterin.</span><span class="sxs-lookup"><span data-stu-id="967cc-313">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="967cc-314">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="967cc-314">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="967cc-315">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="967cc-315">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="967cc-316">Hizmetleri küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="967cc-316">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="967cc-317">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu, sınıfta çok fazla sorumluluğun olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir işaret olabilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-317">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="967cc-318">Bazı sorumlulukları yeni sınıflara taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="967cc-318">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="967cc-319">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="967cc-319">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="967cc-320">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="967cc-320">Disposal of services</span></span>

<span data-ttu-id="967cc-321">Kapsayıcı, <xref:System.IDisposable.Dispose%2A> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="967cc-321">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="967cc-322">Kapsayıcıdan çözümlenen hizmetler hiçbir şekilde geliştirici tarafından atılamaz.</span><span class="sxs-lookup"><span data-stu-id="967cc-322">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="967cc-323">Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı kendiliğinden otomatik olarak atar.</span><span class="sxs-lookup"><span data-stu-id="967cc-323">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="967cc-324">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="967cc-324">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="967cc-325">Hata ayıklama konsolu dizin sayfasının her yenilemesinde aşağıdaki çıktıyı gösterir:</span><span class="sxs-lookup"><span data-stu-id="967cc-325">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="967cc-326">Hizmet kapsayıcısı tarafından oluşturulmayan hizmetler</span><span class="sxs-lookup"><span data-stu-id="967cc-326">Services not created by the service container</span></span>

<span data-ttu-id="967cc-327">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="967cc-327">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="967cc-328">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="967cc-328">In the preceding code:</span></span>

* <span data-ttu-id="967cc-329">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="967cc-329">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="967cc-330">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="967cc-330">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="967cc-331">Geliştirici, hizmetleri elden atan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="967cc-331">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="967cc-332">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="967cc-332">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="967cc-333">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="967cc-333">Transient, limited lifetime</span></span>

<span data-ttu-id="967cc-334">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="967cc-334">**Scenario**</span></span>

<span data-ttu-id="967cc-335">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="967cc-335">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="967cc-336">Örnek, kök kapsamda (kök kapsayıcı) çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="967cc-336">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="967cc-337">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-337">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="967cc-338">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="967cc-338">**Solution**</span></span>

<span data-ttu-id="967cc-339">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-339">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="967cc-340">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="967cc-340">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="967cc-341">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-341">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="967cc-342">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="967cc-342">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="967cc-343"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcının bağımlılıkları için kapsayıcıyı kullanırken, örneği kapsayıcının dışında örneklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-343">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="967cc-344">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="967cc-344">Shared instance, limited lifetime</span></span>

<span data-ttu-id="967cc-345">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="967cc-345">**Scenario**</span></span>

<span data-ttu-id="967cc-346">Uygulamanın <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek olması gerekir, ancak <xref:System.IDisposable> örnek sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-346">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="967cc-347">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="967cc-347">**Solution**</span></span>

<span data-ttu-id="967cc-348">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="967cc-348">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="967cc-349"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="967cc-349">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="967cc-350"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-350">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="967cc-351">Artık gerekli olmadığında kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="967cc-351">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="967cc-352">Genel IDisposable yönergeleri</span><span class="sxs-lookup"><span data-stu-id="967cc-352">General IDisposable guidelines</span></span>

* <span data-ttu-id="967cc-353"><xref:System.IDisposable>Örnekleri geçici bir yaşam süresine kaydetmez.</span><span class="sxs-lookup"><span data-stu-id="967cc-353">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="967cc-354">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-354">Use the factory pattern instead.</span></span>
* <span data-ttu-id="967cc-355"><xref:System.IDisposable>Kök kapsamda geçici veya kapsamlı bir yaşam süresine sahip örnekleri çözümleyin.</span><span class="sxs-lookup"><span data-stu-id="967cc-355">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="967cc-356">Bunun tek istisnası, uygulamanın oluşturma/yeniden oluşturma/yeniden oluşturma ve uygulama oluşturmasıdır <xref:System.IServiceProvider> , ancak bu ideal bir modeldir.</span><span class="sxs-lookup"><span data-stu-id="967cc-356">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="967cc-357">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="967cc-357">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="967cc-358"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-358">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="967cc-359">Hizmetlerin ömrünü denetlemek için kapsamları kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-359">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="967cc-360">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="967cc-360">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="967cc-361">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="967cc-361">Default service container replacement</span></span>

<span data-ttu-id="967cc-362">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="967cc-362">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="967cc-363">Desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="967cc-363">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="967cc-364">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="967cc-364">Property injection</span></span>
* <span data-ttu-id="967cc-365">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="967cc-365">Injection based on name</span></span>
* <span data-ttu-id="967cc-366">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="967cc-366">Child containers</span></span>
* <span data-ttu-id="967cc-367">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="967cc-367">Custom lifetime management</span></span>
* <span data-ttu-id="967cc-368">`Func<T>` yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="967cc-368">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="967cc-369">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="967cc-369">Convention-based registration</span></span>

<span data-ttu-id="967cc-370">Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-370">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="967cc-371">Autofac</span><span class="sxs-lookup"><span data-stu-id="967cc-371">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="967cc-372">Drıioc</span><span class="sxs-lookup"><span data-stu-id="967cc-372">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="967cc-373">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="967cc-373">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="967cc-374">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="967cc-374">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="967cc-375">E</span><span class="sxs-lookup"><span data-stu-id="967cc-375">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="967cc-376">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="967cc-376">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="967cc-377">Unity</span><span class="sxs-lookup"><span data-stu-id="967cc-377">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="967cc-378">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="967cc-378">Thread safety</span></span>

<span data-ttu-id="967cc-379">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967cc-379">Create thread-safe singleton services.</span></span> <span data-ttu-id="967cc-380">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-380">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="967cc-381">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="967cc-381">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="967cc-382">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından yalnızca bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-382">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="967cc-383">Öneriler</span><span class="sxs-lookup"><span data-stu-id="967cc-383">Recommendations</span></span>

* <span data-ttu-id="967cc-384">`async/await` ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="967cc-384">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="967cc-385">C# zaman uyumsuz oluşturucuları desteklemediğinden, hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-385">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="967cc-386">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="967cc-386">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="967cc-387">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="967cc-387">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="967cc-388">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-388">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="967cc-389">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="967cc-389">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="967cc-390">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="967cc-390">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="967cc-391">Hizmetlere statik erişimi önleyin.</span><span class="sxs-lookup"><span data-stu-id="967cc-391">Avoid static access to services.</span></span> <span data-ttu-id="967cc-392">Örneğin, başka bir yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik bir alan veya özellik olarak yakalanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="967cc-392">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="967cc-393">Dı fabrikalarını hızlı ve zaman uyumlu tutun.</span><span class="sxs-lookup"><span data-stu-id="967cc-393">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="967cc-394">*Hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="967cc-394">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="967cc-395">Örneğin, <xref:System.IServiceProvider.GetService%2A> yerine şunu kullandığınızda bir hizmet örneği elde etme çağrısı yapmayın:</span><span class="sxs-lookup"><span data-stu-id="967cc-395">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="967cc-396">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="967cc-396">**Incorrect:**</span></span>

    ![Yanlış kod](dependency-injection/_static/bad.png)

  <span data-ttu-id="967cc-398">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="967cc-398">**Correct**:</span></span>

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
* <span data-ttu-id="967cc-399">Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme.</span><span class="sxs-lookup"><span data-stu-id="967cc-399">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="967cc-400">Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="967cc-400">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="967cc-401">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="967cc-401">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="967cc-402">İçindeki çağrılardan <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> kaçının `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="967cc-402">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="967cc-403">Çağırma `BuildServiceProvider` genellikle geliştirici içindeki bir hizmeti çözmek istediğinde oluşur `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="967cc-403">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="967cc-404">Örneğin, yapılandırmasından yüklendiği durumu göz önünde bulundurun `LoginPath` .</span><span class="sxs-lookup"><span data-stu-id="967cc-404">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="967cc-405">Aşağıdaki yaklaşımdan kaçının:</span><span class="sxs-lookup"><span data-stu-id="967cc-405">Avoid the following approach:</span></span>

  ![BuildServiceProvider çağrılırken hatalı kod](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="967cc-407">Önceki görüntüde yeşil dalgalı çizgiyi seçtiğinizde `services.BuildServiceProvider` AŞAĞıDAKI ASP0000 uyarısı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="967cc-407">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="967cc-408">Uygulama kodundan ' BuildServiceProvider ' çağrısı ASP0000, oluşturulan Singleton Hizmetleri ek bir kopyasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="967cc-408">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="967cc-409">Bağımlılık ekleme hizmetleri gibi alternatifleri, ' configure ' için parametre olarak düşünün.</span><span class="sxs-lookup"><span data-stu-id="967cc-409">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="967cc-410">Çağırma `BuildServiceProvider` ikinci bir kapsayıcı oluşturur ve bu, parçalanmış tek ton oluşturabilir ve birden çok kapsayıcı genelinde nesne grafiklerine başvuru oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-410">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="967cc-411">Almanın doğru yolu, `LoginPath` Seçenekler deseninin, dı için yerleşik desteğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="967cc-411">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="967cc-412">Atılabilir geçici hizmetler, aktiften çıkarma için kapsayıcı tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="967cc-412">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="967cc-413">Bu, en üst düzey kapsayıcıdan çözümlenirse bir bellek sızıntısını açabilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-413">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="967cc-414">Uygulamanın kapsamlı hizmetleri yakalayan tekton içermediğinden emin olmak için kapsam doğrulamayı etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="967cc-414">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="967cc-415">Daha fazla bilgi için bkz. [kapsam doğrulaması](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="967cc-415">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="967cc-416">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="967cc-416">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="967cc-417">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="967cc-417">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="967cc-418">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="967cc-418">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="967cc-419">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="967cc-419">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="967cc-420">DI 'de çok kiracılı için önerilen desenler</span><span class="sxs-lookup"><span data-stu-id="967cc-420">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="967cc-421">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , ASP.NET Core üzerinde modüler, çok kiracılı uygulamalar oluşturmaya yönelik bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="967cc-421">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="967cc-422">Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="967cc-422">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="967cc-423">Yalnızca Orchard Core çerçevesini kullanarak, CMS 'ye özgü özelliklerden herhangi biri olmadan modüler ve çok kiracılı uygulamalar oluşturma örnekleri için bkz. [Orchard çekirdek örnekleri](https://github.com/OrchardCMS/OrchardCore.Samples) .</span><span class="sxs-lookup"><span data-stu-id="967cc-423">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="967cc-424">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="967cc-424">Framework-provided services</span></span>

<span data-ttu-id="967cc-425">`Startup.ConfigureServices`Yöntemi, uygulamanın kullandığı hizmetleri, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967cc-425">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="967cc-426">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="967cc-426">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="967cc-427">ASP.NET Core şablonlarına dayalı uygulamalar için Framework, 250 'den fazla hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967cc-427">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="967cc-428">Aşağıdaki tabloda, bu çerçeve kayıtlı hizmetlerinin küçük bir örneği listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="967cc-428">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="967cc-429">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="967cc-429">Service Type</span></span>                                                                                    | <span data-ttu-id="967cc-430">Ömür</span><span class="sxs-lookup"><span data-stu-id="967cc-430">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="967cc-431">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-431">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="967cc-432">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-432">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="967cc-433">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-433">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="967cc-434">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="967cc-435">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-435">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="967cc-436">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="967cc-437">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-437">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="967cc-438">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-438">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="967cc-439">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-439">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="967cc-440">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="967cc-441">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="967cc-442">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-442">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="967cc-443">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-443">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="967cc-444">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-444">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="967cc-445">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="967cc-445">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="967cc-446">Dı uygulaması geliştirme için NDC Konferansı desenleri</span><span class="sxs-lookup"><span data-stu-id="967cc-446">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="967cc-447">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="967cc-447">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="967cc-448">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="967cc-448">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="967cc-449">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="967cc-449">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="967cc-450">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="967cc-450">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="967cc-451">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="967cc-451">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="967cc-452">[Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler) tarafından</span><span class="sxs-lookup"><span data-stu-id="967cc-452">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="967cc-453">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="967cc-453">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="967cc-454">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="967cc-454">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="967cc-455">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="967cc-455">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="967cc-456">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="967cc-456">Overview of dependency injection</span></span>

<span data-ttu-id="967cc-457">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="967cc-457">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="967cc-458">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="967cc-458">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="967cc-459">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-459">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="967cc-460">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="967cc-460">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="967cc-461">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="967cc-461">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="967cc-462">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="967cc-462">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="967cc-463">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="967cc-463">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="967cc-464">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="967cc-464">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="967cc-465">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="967cc-465">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="967cc-466">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="967cc-466">This implementation is difficult to unit test.</span></span> <span data-ttu-id="967cc-467">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="967cc-467">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="967cc-468">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="967cc-468">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="967cc-469">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="967cc-469">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="967cc-470">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="967cc-470">Registration of the dependency in a service container.</span></span> <span data-ttu-id="967cc-471">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="967cc-471">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="967cc-472">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-472">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="967cc-473">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="967cc-473">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="967cc-474">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="967cc-474">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="967cc-475">[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="967cc-475">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="967cc-476">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="967cc-476">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="967cc-477">`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister.</span><span class="sxs-lookup"><span data-stu-id="967cc-477">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="967cc-478">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="967cc-478">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="967cc-479">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="967cc-479">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="967cc-480">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="967cc-480">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="967cc-481">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="967cc-481">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="967cc-482">`IMyDependency` ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-482">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="967cc-483">`IMyDependency` kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="967cc-483">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="967cc-484">`ILogger<TCategoryName>` günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="967cc-484">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="967cc-485">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="967cc-485">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="967cc-486">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="967cc-486">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="967cc-487">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="967cc-487">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="967cc-488">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="967cc-488">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="967cc-489">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="967cc-489">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="967cc-490">Örneğin, `services.AddMvc()` hizmet Razor SAYFALARıNı ve MVC gerektir öğesini ekler.</span><span class="sxs-lookup"><span data-stu-id="967cc-490">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="967cc-491">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="967cc-491">We recommended that apps follow this convention.</span></span> <span data-ttu-id="967cc-492">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="967cc-492">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="967cc-493">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-493">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="967cc-494">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="967cc-494">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="967cc-495">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="967cc-495">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="967cc-496">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="967cc-496">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="967cc-497">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="967cc-497">Services injected into Startup</span></span>

<span data-ttu-id="967cc-498">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="967cc-498">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="967cc-499">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="967cc-499">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="967cc-500">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="967cc-500">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="967cc-501">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="967cc-501">Framework-provided services</span></span>

<span data-ttu-id="967cc-502">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="967cc-502">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="967cc-503">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="967cc-503">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="967cc-504">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="967cc-504">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="967cc-505">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="967cc-505">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="967cc-506">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="967cc-506">Service Type</span></span> | <span data-ttu-id="967cc-507">Ömür</span><span class="sxs-lookup"><span data-stu-id="967cc-507">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="967cc-508">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-508">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="967cc-509">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-509">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="967cc-510">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-510">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="967cc-511">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="967cc-512">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="967cc-513">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="967cc-514">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-514">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="967cc-515">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-515">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="967cc-516">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-516">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="967cc-517">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="967cc-518">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="967cc-519">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-519">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="967cc-520">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-520">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="967cc-521">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-521">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="967cc-522">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="967cc-522">Register additional services with extension methods</span></span>

<span data-ttu-id="967cc-523">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="967cc-523">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="967cc-524">Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="967cc-524">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="967cc-525">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="967cc-525">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="967cc-526">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="967cc-526">Service lifetimes</span></span>

<span data-ttu-id="967cc-527">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="967cc-527">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="967cc-528">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-528">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="967cc-529">Larsa</span><span class="sxs-lookup"><span data-stu-id="967cc-529">Transient</span></span>

<span data-ttu-id="967cc-530">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="967cc-530">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="967cc-531">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="967cc-531">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="967cc-532">İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="967cc-532">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="967cc-533">Yayıl</span><span class="sxs-lookup"><span data-stu-id="967cc-533">Scoped</span></span>

<span data-ttu-id="967cc-534">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="967cc-534">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="967cc-535">İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="967cc-535">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="967cc-536">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="967cc-536">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="967cc-537">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="967cc-537">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="967cc-538">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="967cc-538">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="967cc-539">Adet</span><span class="sxs-lookup"><span data-stu-id="967cc-539">Singleton</span></span>

<span data-ttu-id="967cc-540">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="967cc-540">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="967cc-541">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="967cc-541">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="967cc-542">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-542">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="967cc-543">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="967cc-543">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="967cc-544">İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="967cc-544">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="967cc-545">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="967cc-545">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="967cc-546">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-546">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="967cc-547">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="967cc-547">Service registration methods</span></span>

<span data-ttu-id="967cc-548">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="967cc-548">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="967cc-549">Yöntem</span><span class="sxs-lookup"><span data-stu-id="967cc-549">Method</span></span> | <span data-ttu-id="967cc-550">Automatic</span><span class="sxs-lookup"><span data-stu-id="967cc-550">Automatic</span></span><br><span data-ttu-id="967cc-551">object</span><span class="sxs-lookup"><span data-stu-id="967cc-551">object</span></span><br><span data-ttu-id="967cc-552">elden</span><span class="sxs-lookup"><span data-stu-id="967cc-552">disposal</span></span> | <span data-ttu-id="967cc-553">Birden çok</span><span class="sxs-lookup"><span data-stu-id="967cc-553">Multiple</span></span><br><span data-ttu-id="967cc-554">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="967cc-554">implementations</span></span> | <span data-ttu-id="967cc-555">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="967cc-555">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="967cc-556">Örnek:</span><span class="sxs-lookup"><span data-stu-id="967cc-556">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="967cc-557">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-557">Yes</span></span> | <span data-ttu-id="967cc-558">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-558">Yes</span></span> | <span data-ttu-id="967cc-559">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-559">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="967cc-560">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="967cc-560">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="967cc-561">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-561">Yes</span></span> | <span data-ttu-id="967cc-562">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-562">Yes</span></span> | <span data-ttu-id="967cc-563">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-563">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="967cc-564">Örnek:</span><span class="sxs-lookup"><span data-stu-id="967cc-564">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="967cc-565">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-565">Yes</span></span> | <span data-ttu-id="967cc-566">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-566">No</span></span> | <span data-ttu-id="967cc-567">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-567">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="967cc-568">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="967cc-568">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="967cc-569">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-569">No</span></span> | <span data-ttu-id="967cc-570">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-570">Yes</span></span> | <span data-ttu-id="967cc-571">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-571">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="967cc-572">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="967cc-572">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="967cc-573">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-573">No</span></span> | <span data-ttu-id="967cc-574">Hayır</span><span class="sxs-lookup"><span data-stu-id="967cc-574">No</span></span> | <span data-ttu-id="967cc-575">Yes</span><span class="sxs-lookup"><span data-stu-id="967cc-575">Yes</span></span> |

<span data-ttu-id="967cc-576">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="967cc-576">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="967cc-577">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="967cc-577">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="967cc-578">`TryAdd{LIFETIME}` Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967cc-578">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="967cc-579">Aşağıdaki örnekte, ilk satır `MyDependency` için kaydedilir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="967cc-579">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="967cc-580">`IMyDependency`Zaten kayıtlı bir uygulamaya sahip olduğundan ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="967cc-580">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="967cc-581">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="967cc-581">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="967cc-582">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967cc-582">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="967cc-583">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="967cc-583">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="967cc-584">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="967cc-584">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="967cc-585">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="967cc-585">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="967cc-586">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="967cc-586">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="967cc-587">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="967cc-587">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="967cc-588">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="967cc-588">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="967cc-589">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="967cc-589">Constructor injection behavior</span></span>

<span data-ttu-id="967cc-590">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-590">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="967cc-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="967cc-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="967cc-592">`ActivatorUtilities` Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="967cc-592">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="967cc-593">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="967cc-593">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="967cc-594">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="967cc-594">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="967cc-595">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="967cc-595">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="967cc-596">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-596">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="967cc-597">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="967cc-597">Entity Framework contexts</span></span>

<span data-ttu-id="967cc-598">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="967cc-598">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="967cc-599">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="967cc-599">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="967cc-600">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-600">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="967cc-601">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="967cc-601">Lifetime and registration options</span></span>

<span data-ttu-id="967cc-602">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="967cc-602">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="967cc-603">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="967cc-603">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="967cc-604">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="967cc-604">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="967cc-605">`Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="967cc-605">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="967cc-606">, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` .</span><span class="sxs-lookup"><span data-stu-id="967cc-606">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="967cc-607">`OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="967cc-607">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="967cc-608">Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="967cc-608">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="967cc-609">`OperationService` sınıfının yeni bir örneğini alır `IOperationTransient` .</span><span class="sxs-lookup"><span data-stu-id="967cc-609">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="967cc-610">Yeni örnek farklı bir şekilde oluşturur `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="967cc-610">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="967cc-611">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="967cc-611">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="967cc-612">İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="967cc-612">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="967cc-613">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="967cc-613">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="967cc-614">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="967cc-614">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="967cc-615">`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="967cc-615">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="967cc-616">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="967cc-616">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="967cc-617">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="967cc-617">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="967cc-618">Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="967cc-618">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="967cc-619">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="967cc-619">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="967cc-620">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="967cc-620">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="967cc-621">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="967cc-621">**First request:**</span></span>

<span data-ttu-id="967cc-622">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="967cc-622">Controller operations:</span></span>

<span data-ttu-id="967cc-623">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="967cc-623">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="967cc-624">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="967cc-624">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="967cc-625">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="967cc-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="967cc-626">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="967cc-626">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="967cc-627">`OperationService` operasyonları</span><span class="sxs-lookup"><span data-stu-id="967cc-627">`OperationService` operations:</span></span>

<span data-ttu-id="967cc-628">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="967cc-628">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="967cc-629">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="967cc-629">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="967cc-630">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="967cc-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="967cc-631">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="967cc-631">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="967cc-632">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="967cc-632">**Second request:**</span></span>

<span data-ttu-id="967cc-633">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="967cc-633">Controller operations:</span></span>

<span data-ttu-id="967cc-634">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="967cc-634">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="967cc-635">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="967cc-635">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="967cc-636">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="967cc-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="967cc-637">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="967cc-637">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="967cc-638">`OperationService` operasyonları</span><span class="sxs-lookup"><span data-stu-id="967cc-638">`OperationService` operations:</span></span>

<span data-ttu-id="967cc-639">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="967cc-639">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="967cc-640">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="967cc-640">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="967cc-641">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="967cc-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="967cc-642">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="967cc-642">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="967cc-643">`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="967cc-643">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="967cc-644">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-644">*Transient* objects are always different.</span></span> <span data-ttu-id="967cc-645">Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-645">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="967cc-646">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="967cc-646">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="967cc-647">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-647">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="967cc-648">*Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="967cc-648">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="967cc-649">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="967cc-649">Call services from main</span></span>

<span data-ttu-id="967cc-650"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967cc-650">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="967cc-651">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-651">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="967cc-652">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="967cc-652">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="967cc-653">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="967cc-653">Scope validation</span></span>

<span data-ttu-id="967cc-654">Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="967cc-654">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="967cc-655">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="967cc-655">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="967cc-656">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="967cc-656">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="967cc-657">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="967cc-657">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="967cc-658">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="967cc-658">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="967cc-659">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="967cc-659">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="967cc-660">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="967cc-660">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="967cc-661">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="967cc-661">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="967cc-662">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="967cc-662">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="967cc-663">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="967cc-663">Request Services</span></span>

<span data-ttu-id="967cc-664">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="967cc-664">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="967cc-665">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="967cc-665">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="967cc-666">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="967cc-666">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="967cc-667">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-667">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="967cc-668">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="967cc-668">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="967cc-669">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="967cc-669">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="967cc-670">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="967cc-670">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="967cc-671">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="967cc-671">Design services for dependency injection</span></span>

<span data-ttu-id="967cc-672">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="967cc-672">Best practices are to:</span></span>

* <span data-ttu-id="967cc-673">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="967cc-673">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="967cc-674">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="967cc-674">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="967cc-675">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="967cc-675">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="967cc-676">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="967cc-676">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="967cc-677">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="967cc-677">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="967cc-678">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="967cc-678">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="967cc-679">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="967cc-679">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="967cc-680">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="967cc-680">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="967cc-681">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="967cc-681">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="967cc-682">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-682">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="967cc-683">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="967cc-683">Disposal of services</span></span>

<span data-ttu-id="967cc-684">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="967cc-684">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="967cc-685">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="967cc-685">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="967cc-686">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="967cc-686">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="967cc-687">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="967cc-687">In the following example:</span></span>

* <span data-ttu-id="967cc-688">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="967cc-688">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="967cc-689">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="967cc-689">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="967cc-690">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="967cc-690">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="967cc-691">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="967cc-691">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="967cc-692">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="967cc-692">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="967cc-693">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="967cc-693">Transient, limited lifetime</span></span>

<span data-ttu-id="967cc-694">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="967cc-694">**Scenario**</span></span>

<span data-ttu-id="967cc-695">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="967cc-695">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="967cc-696">Örnek, kök kapsamda çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="967cc-696">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="967cc-697">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-697">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="967cc-698">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="967cc-698">**Solution**</span></span>

<span data-ttu-id="967cc-699">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-699">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="967cc-700">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="967cc-700">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="967cc-701">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-701">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="967cc-702">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="967cc-702">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="967cc-703"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-703">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="967cc-704">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="967cc-704">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="967cc-705">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="967cc-705">**Scenario**</span></span>

<span data-ttu-id="967cc-706">Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-706">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="967cc-707">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="967cc-707">**Solution**</span></span>

<span data-ttu-id="967cc-708">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="967cc-708">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="967cc-709"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="967cc-709">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="967cc-710"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-710">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="967cc-711">Yaşam süresi sona erdirmek için kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="967cc-711">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="967cc-712">Genel Yönergeler</span><span class="sxs-lookup"><span data-stu-id="967cc-712">General Guidelines</span></span>

* <span data-ttu-id="967cc-713"><xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme.</span><span class="sxs-lookup"><span data-stu-id="967cc-713">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="967cc-714">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="967cc-714">Use the factory pattern instead.</span></span>
* <span data-ttu-id="967cc-715">Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün.</span><span class="sxs-lookup"><span data-stu-id="967cc-715">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="967cc-716">Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.</span><span class="sxs-lookup"><span data-stu-id="967cc-716">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="967cc-717">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="967cc-717">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="967cc-718"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-718">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="967cc-719">Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="967cc-719">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="967cc-720">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="967cc-720">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="967cc-721">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="967cc-721">Default service container replacement</span></span>

<span data-ttu-id="967cc-722">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="967cc-722">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="967cc-723">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="967cc-723">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="967cc-724">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="967cc-724">Property injection</span></span>
* <span data-ttu-id="967cc-725">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="967cc-725">Injection based on name</span></span>
* <span data-ttu-id="967cc-726">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="967cc-726">Child containers</span></span>
* <span data-ttu-id="967cc-727">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="967cc-727">Custom lifetime management</span></span>
* <span data-ttu-id="967cc-728">`Func<T>` yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="967cc-728">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="967cc-729">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="967cc-729">Convention-based registration</span></span>

<span data-ttu-id="967cc-730">Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="967cc-730">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="967cc-731">Autofac</span><span class="sxs-lookup"><span data-stu-id="967cc-731">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="967cc-732">Drıioc</span><span class="sxs-lookup"><span data-stu-id="967cc-732">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="967cc-733">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="967cc-733">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="967cc-734">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="967cc-734">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="967cc-735">E</span><span class="sxs-lookup"><span data-stu-id="967cc-735">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="967cc-736">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="967cc-736">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="967cc-737">Unity</span><span class="sxs-lookup"><span data-stu-id="967cc-737">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="967cc-738">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="967cc-738">Thread safety</span></span>

<span data-ttu-id="967cc-739">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967cc-739">Create thread-safe singleton services.</span></span> <span data-ttu-id="967cc-740">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-740">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="967cc-741">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="967cc-741">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="967cc-742">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="967cc-742">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="967cc-743">Öneriler</span><span class="sxs-lookup"><span data-stu-id="967cc-743">Recommendations</span></span>

* <span data-ttu-id="967cc-744">`async/await` ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="967cc-744">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="967cc-745">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="967cc-745">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="967cc-746">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="967cc-746">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="967cc-747">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="967cc-747">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="967cc-748">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967cc-748">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="967cc-749">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="967cc-749">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="967cc-750">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="967cc-750">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="967cc-751">Hizmetlere statik erişimi önleyin.</span><span class="sxs-lookup"><span data-stu-id="967cc-751">Avoid static access to services.</span></span> <span data-ttu-id="967cc-752">Örneğin, bir başka yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik olarak yazılanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="967cc-752">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="967cc-753">[Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="967cc-753">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="967cc-754"><xref:System.IServiceProvider.GetService*>Bunun yerine, şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:</span><span class="sxs-lookup"><span data-stu-id="967cc-754">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="967cc-755">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="967cc-755">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="967cc-756">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="967cc-756">**Correct**:</span></span>

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

* <span data-ttu-id="967cc-757">Kullanarak çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="967cc-757">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="967cc-758">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="967cc-758">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="967cc-759">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="967cc-759">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="967cc-760">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="967cc-760">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="967cc-761">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="967cc-761">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="967cc-762">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="967cc-762">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="967cc-763">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="967cc-763">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="967cc-764">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="967cc-764">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="967cc-765">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="967cc-765">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="967cc-766">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="967cc-766">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="967cc-767">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="967cc-767">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="967cc-768">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="967cc-768">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
