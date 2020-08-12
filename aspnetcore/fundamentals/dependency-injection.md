---
title: ASP.NET Core'da bağımlılık ekleme
author: rick-anderson
description: ASP.NET Core bağımlılık ekleme ve nasıl kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 0d8b349d0381e2902907ea841e07bbc96db5b847
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130683"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="d5150-103">ASP.NET Core'da bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="d5150-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d5150-104">[Kirk Larkabağı](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="d5150-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="d5150-105">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="d5150-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d5150-106">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="d5150-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d5150-107">Seçeneklerin bağımlılığı ekleme hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/options> ..</span><span class="sxs-lookup"><span data-stu-id="d5150-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="d5150-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d5150-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d5150-109">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="d5150-109">Overview of dependency injection</span></span>

<span data-ttu-id="d5150-110">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="d5150-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="d5150-111">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d5150-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="d5150-112">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="d5150-113">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="d5150-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="d5150-114">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d5150-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="d5150-115">Önceki örnekte olduğu gibi kod bağımlılıkları sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="d5150-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d5150-116">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="d5150-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="d5150-117">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d5150-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="d5150-118">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="d5150-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d5150-119">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="d5150-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d5150-120">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d5150-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d5150-121">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="d5150-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d5150-122">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="d5150-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d5150-123">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="d5150-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d5150-124">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="d5150-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d5150-125">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d5150-126">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="d5150-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d5150-127">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="d5150-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d5150-128">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="d5150-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d5150-129">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="d5150-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d5150-130">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d5150-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d5150-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Yöntemi, hizmeti tek bir isteğin ömrü olan kapsamlı bir yaşam süresine kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d5150-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="d5150-132">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d5150-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="d5150-133">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="d5150-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="d5150-134">DI düzeniyle:</span><span class="sxs-lookup"><span data-stu-id="d5150-134">With the DI pattern:</span></span>

* <span data-ttu-id="d5150-135">Denetleyici somut türü kullanmaz `MyDependency` , yalnızca arabirimini kullanır `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d5150-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="d5150-136">Bu, denetleyicinin, denetleyiciyi değiştirmeden kullandığı uygulamayı değiştirmeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="d5150-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="d5150-137">Denetleyici bir örneği oluşturmaz `MyDependency` , bu, dı kapsayıcısı tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d5150-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="d5150-138">Arabirim uygulanması, `IMyDependency` yerleşik günlük API 'si kullanılarak artırılabilir:</span><span class="sxs-lookup"><span data-stu-id="d5150-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="d5150-139">Updated `ConfigureServices` yöntemi yeni `IMyDependency` uygulamayı kaydeder:</span><span class="sxs-lookup"><span data-stu-id="d5150-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="d5150-140">`MyDependency2`oluşturucuda bir ister <xref:Microsoft.Extensions.Logging.ILogger`1> .</span><span class="sxs-lookup"><span data-stu-id="d5150-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="d5150-141">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="d5150-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d5150-142">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="d5150-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d5150-143">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="d5150-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d5150-144">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="d5150-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="d5150-145">`ILogger<TCategoryName>`[Framework tarafından sağlanmış bir hizmettir](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="d5150-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="d5150-146">Kapsayıcı, `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="d5150-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="d5150-147">Bağımlılık ekleme terminolojisi ' nde bir hizmet:</span><span class="sxs-lookup"><span data-stu-id="d5150-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="d5150-148">Genellikle, hizmet gibi uygulamadaki diğer koda hizmet sağlayan bir nesnedir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d5150-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="d5150-149">Bir Web hizmetiyle ilgili değildir, ancak hizmet bir Web hizmeti kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="d5150-150">Çerçeve, güçlü bir [günlük](xref:fundamentals/logging/index) sistemi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5150-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="d5150-151">`IMyDependency`Uygulamalar, günlüğü uygulamak için değil temel dı 'yi göstermek için yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="d5150-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="d5150-152">Çoğu uygulamanın Günlükçüler yazması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d5150-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="d5150-153">Aşağıdaki kod, bir hizmetin kaydedilmesini gerektirmeyen varsayılan günlük kaydını kullanmayı göstermektedir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5150-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="d5150-154">Yukarıdaki kodu kullanarak, `ConfigureServices` Framework tarafından [günlüğe kaydetme](xref:fundamentals/logging/index) sağlandığı için güncelleştirmeniz gerekmez:</span><span class="sxs-lookup"><span data-stu-id="d5150-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="d5150-155">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="d5150-155">Services injected into Startup</span></span>

<span data-ttu-id="d5150-156">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="d5150-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d5150-157">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d5150-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="d5150-158">Daha fazla bilgi için, bkz <xref:fundamentals/startup> . ve [erişim yapılandırması başlangıç](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="d5150-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="d5150-159">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="d5150-159">Register additional services with extension methods</span></span>

<span data-ttu-id="d5150-160">Hizmeti kaydetmek için bir hizmet koleksiyonu genişletme yöntemi kullanılabilir olduğunda:</span><span class="sxs-lookup"><span data-stu-id="d5150-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="d5150-161">Bu kural, `Add{SERVICE_NAME}` Bu hizmetin gerektirdiği tüm hizmetleri kaydetmek için tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d5150-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="d5150-162">Bağımlı hizmetler de kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-162">The dependent services are also registered.</span></span>

<span data-ttu-id="d5150-163">Aşağıdaki kod, Razor bireysel kullanıcı hesapları kullanılarak sayfalar şablonu tarafından oluşturulur ve uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemeyi gösterir <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ve <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="d5150-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="d5150-164">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ve <xref:security/authentication/identity> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="d5150-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="d5150-165">Hizmetleri kaydetmek için bir uzantı yöntemi yazma yönergeleri için [hizmet koleksiyonunu birleştirme](#csc) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d5150-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="d5150-166">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="d5150-166">Service lifetimes</span></span>

<span data-ttu-id="d5150-167">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="d5150-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="d5150-168">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="d5150-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="d5150-169">Larsa</span><span class="sxs-lookup"><span data-stu-id="d5150-169">Transient</span></span>

<span data-ttu-id="d5150-170">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d5150-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="d5150-171">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d5150-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="d5150-172">İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="d5150-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="d5150-173">Yayıl</span><span class="sxs-lookup"><span data-stu-id="d5150-173">Scoped</span></span>

<span data-ttu-id="d5150-174">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="d5150-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="d5150-175">Entity Framework Core kullanılırken, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> genişletme yöntemi, `DbContext` Varsayılan olarak kapsamlı yaşam süresine sahip türleri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d5150-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="d5150-176">İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="d5150-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="d5150-177">Aşağıdaki yaklaşımlardan biriyle ara yazılım kapsamındaki Hizmetleri kullanın:</span><span class="sxs-lookup"><span data-stu-id="d5150-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="d5150-178">Hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d5150-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d5150-179">Ekleme by [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) , hizmeti tek bir gibi davranmaya zordığı için çalışma zamanında bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d5150-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="d5150-180">[Ömür ve kayıt seçeneklerinde](#lifetime-and-registration-options) örnek, `InvokeAsync` yaklaşımı kullanır.</span><span class="sxs-lookup"><span data-stu-id="d5150-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="d5150-181">[Fabrika tabanlı ara yazılım](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="d5150-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="d5150-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>Uzantı yöntemleri bir ara yazılımın kayıtlı türünün uygulayıp uygulamadığını denetler <xref:Microsoft.AspNetCore.Http.IMiddleware> .</span><span class="sxs-lookup"><span data-stu-id="d5150-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="d5150-183">Bu durumda, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kapsayıcıya kaydedilen örnek, <xref:Microsoft.AspNetCore.Http.IMiddleware> kural tabanlı ara yazılım etkinleştirme mantığını kullanmak yerine, uygulamayı çözmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d5150-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="d5150-184">Ara yazılım, uygulamanın hizmet kapsayıcısında kapsamlı veya geçici bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="d5150-185">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d5150-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="d5150-186">Kapsamlı bir hizmeti tek bir ***sunucudan çözümleyin.***</span><span class="sxs-lookup"><span data-stu-id="d5150-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d5150-187">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="d5150-188">Şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d5150-188">It's fine to:</span></span>

* <span data-ttu-id="d5150-189">Tek bir hizmeti kapsamlı veya geçici bir hizmetten çözümleyin.</span><span class="sxs-lookup"><span data-stu-id="d5150-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="d5150-190">Kapsamlı bir hizmeti başka bir kapsamlı veya geçici hizmetten çözün.</span><span class="sxs-lookup"><span data-stu-id="d5150-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="d5150-191">Varsayılan olarak, geliştirme ortamında, bir hizmetin daha uzun ömürlü bir hizmetin çözümlenmesi bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d5150-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="d5150-192">Daha fazla bilgi için bkz. [kapsam doğrulaması](#sv).</span><span class="sxs-lookup"><span data-stu-id="d5150-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="d5150-193">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-193">Singleton</span></span>

<span data-ttu-id="d5150-194">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d5150-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="d5150-195">İlk kez istenirler.</span><span class="sxs-lookup"><span data-stu-id="d5150-195">The first time they're requested.</span></span>
* <span data-ttu-id="d5150-196">Geliştirici tarafından doğrudan kapsayıcıya bir uygulama örneği sağlarken.</span><span class="sxs-lookup"><span data-stu-id="d5150-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="d5150-197">Bu yaklaşım nadiren gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d5150-197">This approach is rarely needed.</span></span>

<span data-ttu-id="d5150-198">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="d5150-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d5150-199">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="d5150-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="d5150-200">Tek tasarım modelini uygulamayın ve tek bir atma kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d5150-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="d5150-201">Hizmetler bir kapsayıcıdan hizmeti çözümleyen kodla hiçbir şekilde atılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="d5150-202">Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı tek otomatik olarak ayrı olarak silinir.</span><span class="sxs-lookup"><span data-stu-id="d5150-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="d5150-203">Tek hizmetler iş parçacığı güvenli olmalıdır ve genellikle durum bilgisiz hizmetlerde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d5150-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="d5150-204">İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="d5150-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="d5150-205">Uygulama kapatılıncaya kadar bellek yayımlanmadığı için, tek tek ile bellek kullanımı göz önünde bulundurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="d5150-206">Kapsamlı bir hizmeti tek bir ***sunucudan çözümleyin.***</span><span class="sxs-lookup"><span data-stu-id="d5150-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d5150-207">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="d5150-208">Tek bir hizmeti kapsamlı veya geçici bir hizmetten çözümlemek çok iyidir.</span><span class="sxs-lookup"><span data-stu-id="d5150-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d5150-209">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="d5150-209">Service registration methods</span></span>

<span data-ttu-id="d5150-210">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="d5150-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="d5150-211">Yöntem</span><span class="sxs-lookup"><span data-stu-id="d5150-211">Method</span></span> | <span data-ttu-id="d5150-212">Automatic</span><span class="sxs-lookup"><span data-stu-id="d5150-212">Automatic</span></span><br><span data-ttu-id="d5150-213">object</span><span class="sxs-lookup"><span data-stu-id="d5150-213">object</span></span><br><span data-ttu-id="d5150-214">elden</span><span class="sxs-lookup"><span data-stu-id="d5150-214">disposal</span></span> | <span data-ttu-id="d5150-215">Birden çok</span><span class="sxs-lookup"><span data-stu-id="d5150-215">Multiple</span></span><br><span data-ttu-id="d5150-216">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="d5150-216">implementations</span></span> | <span data-ttu-id="d5150-217">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d5150-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="d5150-218">Örnek:</span><span class="sxs-lookup"><span data-stu-id="d5150-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="d5150-219">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-219">Yes</span></span> | <span data-ttu-id="d5150-220">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-220">Yes</span></span> | <span data-ttu-id="d5150-221">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="d5150-222">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="d5150-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="d5150-223">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-223">Yes</span></span> | <span data-ttu-id="d5150-224">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-224">Yes</span></span> | <span data-ttu-id="d5150-225">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="d5150-226">Örnek:</span><span class="sxs-lookup"><span data-stu-id="d5150-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="d5150-227">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-227">Yes</span></span> | <span data-ttu-id="d5150-228">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-228">No</span></span> | <span data-ttu-id="d5150-229">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="d5150-230">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="d5150-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="d5150-231">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-231">No</span></span> | <span data-ttu-id="d5150-232">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-232">Yes</span></span> | <span data-ttu-id="d5150-233">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="d5150-234">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="d5150-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="d5150-235">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-235">No</span></span> | <span data-ttu-id="d5150-236">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-236">No</span></span> | <span data-ttu-id="d5150-237">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-237">Yes</span></span> |

<span data-ttu-id="d5150-238">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d5150-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d5150-239">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d5150-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d5150-240">`TryAdd{LIFETIME}`zaten kayıtlı bir uygulama yoksa Yöntemler hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d5150-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d5150-241">Aşağıdaki örnekte, ilk satır `MyDependency` için kaydedilir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d5150-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="d5150-242">`IMyDependency`Zaten kayıtlı bir uygulamaya sahip olduğundan ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="d5150-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="d5150-243">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="d5150-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="d5150-244">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri, zaten *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d5150-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="d5150-245">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="d5150-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d5150-246">Hizmetleri kaydederken, aynı türden biri zaten eklenmediyse geliştirici bir örnek eklememelidir.</span><span class="sxs-lookup"><span data-stu-id="d5150-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d5150-247">Genellikle, kitaplık yazarları `TryAddEnumerable` kapsayıcıda bir uygulamanın birden çok kopyasını kaydetmemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="d5150-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="d5150-248">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="d5150-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="d5150-249">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="d5150-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="d5150-250">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="d5150-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="d5150-251">Hizmet kaydı, aynı türde birden çok uygulama kaydedilirken genellikle sıralı olarak bağımsızdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="d5150-252">`IServiceCollection`, bir koleksiyonudur <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="d5150-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="d5150-253">Aşağıdaki kod, bir oluşturucunun bulunduğu bir hizmetin nasıl ekleneceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d5150-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="d5150-254">`Add{LIFETIME}`Yöntemler aynı yaklaşımı kullanır.</span><span class="sxs-lookup"><span data-stu-id="d5150-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="d5150-255">Örneğin, [kaynak koda Addkapsamlıdır öğesine](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)bakın.</span><span class="sxs-lookup"><span data-stu-id="d5150-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d5150-256">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="d5150-256">Constructor injection behavior</span></span>

<span data-ttu-id="d5150-257">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="d5150-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d5150-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="d5150-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="d5150-259">Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesneler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d5150-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="d5150-260">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), MVC denetleyicileri ve [model ciltler](xref:mvc/models/model-binding)gibi Framework özellikleriyle kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d5150-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="d5150-261">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d5150-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d5150-262">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d5150-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d5150-263">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d5150-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d5150-264">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d5150-265">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="d5150-265">Entity Framework contexts</span></span>

<span data-ttu-id="d5150-266">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="d5150-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d5150-267">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="d5150-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="d5150-268">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d5150-269">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="d5150-269">Lifetime and registration options</span></span>

<span data-ttu-id="d5150-270">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri tanımlayıcı olan bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="d5150-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="d5150-271">Bir işlemin hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı veya farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="d5150-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d5150-272">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="d5150-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="d5150-273">Oluşturucu, bir `Operation` GUID sağlanmamışsa son 4 karakteri üretir:</span><span class="sxs-lookup"><span data-stu-id="d5150-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="d5150-274">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="d5150-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d5150-275">Örnek uygulama, istekler içindeki ve arasındaki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="d5150-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="d5150-276">Örnek uygulama `IndexModel` ve ara yazılım `IOperation` , her tür türü ve günlük şunu ister `OperationId` :</span><span class="sxs-lookup"><span data-stu-id="d5150-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d5150-277">Ara yazılım, `IndexModel` ile benzerdir ve aynı hizmetleri çözümler:</span><span class="sxs-lookup"><span data-stu-id="d5150-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="d5150-278">Kapsamındaki hizmetlerin yöntemde çözülmesi gerekir `InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="d5150-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="d5150-279">Günlükçü çıktısı şunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="d5150-279">The logger output shows:</span></span>

* <span data-ttu-id="d5150-280">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-280">*Transient* objects are always different.</span></span> <span data-ttu-id="d5150-281">Geçici `OperationId` değer, `IndexModel` ve ara yazılım içinde farklıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="d5150-282">*Kapsamlı* nesneler her istekte aynıdır, ancak her istekte farklıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="d5150-283">*Tek* nesneler her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="d5150-284">Günlüğe kaydetme çıkışını azaltmak için, dosyadaki *appsettings.Development.js* "Logging: LogLevel: Microsoft: Error" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="d5150-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="d5150-285">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="d5150-285">Call services from main</span></span>

<span data-ttu-id="d5150-286"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d5150-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d5150-287">Bu yaklaşım, başlatma görevlerini çalıştırmak için başlangıçta kapsamlı bir hizmete erişmek için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="d5150-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="d5150-288">Yukarıdaki kod, sayfa öğreticisindeki [tohum başlatıcısı 'Nı eklemedir](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) Razor .</span><span class="sxs-lookup"><span data-stu-id="d5150-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="d5150-289">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `IMyDependency` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="d5150-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="d5150-290">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="d5150-290">Scope validation</span></span>

<span data-ttu-id="d5150-291">Uygulama [geliştirme ortamında](xref:fundamentals/environments) çalışırken ve Konağı derlemek Için [createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) çağırdığında, varsayılan hizmet sağlayıcı aşağıdakileri doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="d5150-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d5150-292">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="d5150-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d5150-293">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="d5150-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="d5150-294">Geçici hizmetler doğrudan veya dolaylı olarak Singleton veya kapsamlı hizmetlere eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="d5150-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="d5150-295">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d5150-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="d5150-296">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulamanın ömrüne karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="d5150-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d5150-297">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="d5150-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d5150-298">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek bir şekilde yükseltilir çünkü uygulama kapatıldığında yalnızca kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="d5150-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="d5150-299">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="d5150-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d5150-300">Daha fazla bilgi için bkz. [kapsam doğrulaması](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d5150-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="d5150-301">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="d5150-301">Request Services</span></span>

<span data-ttu-id="d5150-302">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="d5150-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="d5150-303">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="d5150-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="d5150-304">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="d5150-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="d5150-305">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="d5150-306">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="d5150-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="d5150-307">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d5150-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="d5150-308">ASP.NET Core, istek başına kapsam oluşturur ve `RequestServices` kapsamlı hizmet sağlayıcısını kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="d5150-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="d5150-309">Tüm kapsamlı hizmetler, istek etkin olduğu sürece geçerli olur.</span><span class="sxs-lookup"><span data-stu-id="d5150-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="d5150-310">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="d5150-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d5150-311">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="d5150-311">Design services for dependency injection</span></span>

<span data-ttu-id="d5150-312">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d5150-312">Best practices are to:</span></span>

* <span data-ttu-id="d5150-313">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="d5150-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="d5150-314">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="d5150-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d5150-315">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="d5150-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="d5150-316">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="d5150-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d5150-317">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="d5150-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d5150-318">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="d5150-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d5150-319">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir işaret.</span><span class="sxs-lookup"><span data-stu-id="d5150-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d5150-320">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="d5150-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="d5150-321">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="d5150-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d5150-322">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="d5150-322">Disposal of services</span></span>

<span data-ttu-id="d5150-323">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="d5150-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d5150-324">Hizmetler, bir kapsayıcıdan hizmeti çözümleyen hiçbir kodla asla çıkarılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="d5150-325">Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı Singleton öğesini bırakır.</span><span class="sxs-lookup"><span data-stu-id="d5150-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="d5150-326">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="d5150-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d5150-327">Hata ayıklama konsolu dizin sayfasının her yenilemesinde aşağıdaki çıktıyı gösterir:</span><span class="sxs-lookup"><span data-stu-id="d5150-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="d5150-328">Hizmet kapsayıcısı tarafından oluşturulmayan hizmetler</span><span class="sxs-lookup"><span data-stu-id="d5150-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="d5150-329">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d5150-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="d5150-330">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="d5150-330">In the preceding code:</span></span>

* <span data-ttu-id="d5150-331">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="d5150-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d5150-332">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="d5150-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="d5150-333">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="d5150-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d5150-334">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="d5150-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="d5150-335">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="d5150-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="d5150-336">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="d5150-336">Transient, limited lifetime</span></span>

<span data-ttu-id="d5150-337">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="d5150-337">**Scenario**</span></span>

<span data-ttu-id="d5150-338">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="d5150-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="d5150-339">Örnek, kök kapsamda (kök kapsayıcı) çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="d5150-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="d5150-340">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="d5150-341">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="d5150-341">**Solution**</span></span>

<span data-ttu-id="d5150-342">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="d5150-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="d5150-343">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="d5150-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="d5150-344">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="d5150-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="d5150-345">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="d5150-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="d5150-346"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="d5150-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="d5150-347">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="d5150-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="d5150-348">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="d5150-348">**Scenario**</span></span>

<span data-ttu-id="d5150-349">Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="d5150-350">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="d5150-350">**Solution**</span></span>

<span data-ttu-id="d5150-351">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d5150-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="d5150-352"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="d5150-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="d5150-353"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="d5150-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="d5150-354">Yaşam süresi sona erdirmek için kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="d5150-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="d5150-355">Genel IDisposable yönergeleri</span><span class="sxs-lookup"><span data-stu-id="d5150-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="d5150-356"><xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme.</span><span class="sxs-lookup"><span data-stu-id="d5150-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="d5150-357">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="d5150-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="d5150-358">Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün.</span><span class="sxs-lookup"><span data-stu-id="d5150-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="d5150-359">Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.</span><span class="sxs-lookup"><span data-stu-id="d5150-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="d5150-360">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="d5150-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="d5150-361"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="d5150-362">Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d5150-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="d5150-363">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="d5150-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d5150-364">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="d5150-364">Default service container replacement</span></span>

<span data-ttu-id="d5150-365">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d5150-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d5150-366">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5150-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="d5150-367">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="d5150-367">Property injection</span></span>
* <span data-ttu-id="d5150-368">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="d5150-368">Injection based on name</span></span>
* <span data-ttu-id="d5150-369">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="d5150-369">Child containers</span></span>
* <span data-ttu-id="d5150-370">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="d5150-370">Custom lifetime management</span></span>
* <span data-ttu-id="d5150-371">`Func<T>`yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="d5150-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d5150-372">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="d5150-372">Convention-based registration</span></span>

<span data-ttu-id="d5150-373">Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d5150-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="d5150-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="d5150-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="d5150-375">Drıioc</span><span class="sxs-lookup"><span data-stu-id="d5150-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="d5150-376">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="d5150-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="d5150-377">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="d5150-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="d5150-378">E</span><span class="sxs-lookup"><span data-stu-id="d5150-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="d5150-379">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="d5150-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="d5150-380">Unity</span><span class="sxs-lookup"><span data-stu-id="d5150-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="d5150-381">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="d5150-381">Thread safety</span></span>

<span data-ttu-id="d5150-382">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d5150-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="d5150-383">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="d5150-384">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d5150-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d5150-385">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d5150-386">Öneriler</span><span class="sxs-lookup"><span data-stu-id="d5150-386">Recommendations</span></span>

* <span data-ttu-id="d5150-387">`async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="d5150-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="d5150-388">C# zaman uyumsuz oluşturucuları desteklemez.</span><span class="sxs-lookup"><span data-stu-id="d5150-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="d5150-389">Önerilen model, hizmeti eşzamanlı olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d5150-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="d5150-390">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="d5150-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d5150-391">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="d5150-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d5150-392">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d5150-393">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="d5150-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="d5150-394">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="d5150-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="d5150-395">Hizmetlere statik erişimi önleyin.</span><span class="sxs-lookup"><span data-stu-id="d5150-395">Avoid static access to services.</span></span> <span data-ttu-id="d5150-396">Örneğin, başka bir yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik olarak yazılanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="d5150-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="d5150-397">Dı fabrikalarını hızlı ve zaman uyumlu tutun.</span><span class="sxs-lookup"><span data-stu-id="d5150-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="d5150-398">*Hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="d5150-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="d5150-399">Örneğin, <xref:System.IServiceProvider.GetService*> yerine şunu kullandığınızda bir hizmet örneği elde etme çağrısı yapmayın:</span><span class="sxs-lookup"><span data-stu-id="d5150-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="d5150-400">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="d5150-400">**Incorrect:**</span></span>

    ![Yanlış kod](dependency-injection/_static/bad.png)

  <span data-ttu-id="d5150-402">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="d5150-402">**Correct**:</span></span>

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
* <span data-ttu-id="d5150-403">Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme.</span><span class="sxs-lookup"><span data-stu-id="d5150-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="d5150-404">Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="d5150-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="d5150-405">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d5150-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="d5150-406">İçindeki çağrılardan <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> kaçının `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5150-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="d5150-407">Çağırma `BuildServiceProvider` genellikle geliştirici içindeki bir hizmeti çözmek istediğinde oluşur `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5150-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="d5150-408">Örneğin, ihtiyacınız olan durumu göz önünde bulundurun `LoginPath` .</span><span class="sxs-lookup"><span data-stu-id="d5150-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="d5150-409">Aşağıdaki koddan kaçının:</span><span class="sxs-lookup"><span data-stu-id="d5150-409">Avoid the following code:</span></span>

  ![BuildServiceProvider çağrılırken hatalı kod](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="d5150-411">Önceki görüntüde yeşil dalgalı çizgiyi seçtiğinizde `services.BuildServiceProvider` AŞAĞıDAKI ASP0000 uyarısı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="d5150-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="d5150-412">Uygulama kodundan ' BuildServiceProvider ' çağrısı ASP0000, oluşturulan Singleton Hizmetleri ek bir kopyasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="d5150-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="d5150-413">Bağımlılık ekleme hizmetleri gibi alternatifleri, ' configure ' için parametre olarak düşünün.</span><span class="sxs-lookup"><span data-stu-id="d5150-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="d5150-414">Çağırma `BuildServiceProvider` ikinci bir kapsayıcı oluşturur ve bu, parçalanmış tek ton oluşturabilir ve birden çok kapsayıcı genelinde nesne grafiklerine başvuru oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="d5150-415">Get için doğru bir yol, `LoginPath` dı ile seçenek deseninin kullanılması:</span><span class="sxs-lookup"><span data-stu-id="d5150-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="d5150-416">Atılabilir geçici hizmetler, aktiften çıkarma için kapsayıcı tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="d5150-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="d5150-417">Bu, en üst düzey kapsayıcıdan çözümlenirse bir bellek sızıntısını açabilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="d5150-418">Uygulamanın tekton yakaladığı kapsamlı hizmetler içermediğinden emin olmak için kapsam doğrulamayı etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d5150-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="d5150-419">Daha fazla bilgi için bkz. [kapsam doğrulaması](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d5150-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="d5150-420">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d5150-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d5150-421">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="d5150-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d5150-422">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="d5150-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d5150-423">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d5150-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="d5150-424">DI 'de çok kiracılı için önerilen desenler</span><span class="sxs-lookup"><span data-stu-id="d5150-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="d5150-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , çok kiracılı bir uygulama sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5150-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="d5150-426">Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="d5150-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="d5150-427">https://github.com/OrchardCMS/OrchardCore.SamplesCMS 'e özgü özelliklerden herhangi biri olmadan yalnızca Orchard Core çerçevesini kullanarak modüler ve çok kiracılı uygulamalar oluşturmaya yönelik örnekler için adresindeki örnek uygulamalar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d5150-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d5150-428">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="d5150-428">Framework-provided services</span></span>

<span data-ttu-id="d5150-429">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="d5150-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d5150-430">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d5150-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d5150-431">ASP.NET Core şablonlarına dayalı uygulamalar, Framework tarafından kaydedilen 250 taneden fazla hizmete sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d5150-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="d5150-432">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="d5150-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="d5150-433">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="d5150-433">Service Type</span></span> | <span data-ttu-id="d5150-434">Ömür</span><span class="sxs-lookup"><span data-stu-id="d5150-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d5150-435">Larsa</span><span class="sxs-lookup"><span data-stu-id="d5150-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="d5150-436">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="d5150-437">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="d5150-438">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="d5150-439">Larsa</span><span class="sxs-lookup"><span data-stu-id="d5150-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="d5150-440">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="d5150-441">Larsa</span><span class="sxs-lookup"><span data-stu-id="d5150-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="d5150-442">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="d5150-443">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="d5150-444">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="d5150-445">Larsa</span><span class="sxs-lookup"><span data-stu-id="d5150-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="d5150-446">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="d5150-447">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="d5150-448">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d5150-449">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d5150-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d5150-450">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="d5150-450">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="d5150-451">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="d5150-451">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="d5150-452">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="d5150-452">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d5150-453">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="d5150-453">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d5150-454">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="d5150-454">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5150-455">[Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler) tarafından</span><span class="sxs-lookup"><span data-stu-id="d5150-455">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="d5150-456">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="d5150-456">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d5150-457">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="d5150-457">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d5150-458">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d5150-458">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d5150-459">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="d5150-459">Overview of dependency injection</span></span>

<span data-ttu-id="d5150-460">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="d5150-460">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="d5150-461">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d5150-461">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="d5150-462">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-462">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="d5150-463">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="d5150-463">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="d5150-464">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d5150-464">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="d5150-465">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="d5150-465">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d5150-466">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="d5150-466">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="d5150-467">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d5150-467">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="d5150-468">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="d5150-468">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d5150-469">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="d5150-469">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d5150-470">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d5150-470">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d5150-471">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="d5150-471">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d5150-472">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="d5150-472">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d5150-473">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="d5150-473">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d5150-474">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="d5150-474">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d5150-475">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-475">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d5150-476">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="d5150-476">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d5150-477">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="d5150-477">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d5150-478">[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="d5150-478">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d5150-479">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="d5150-479">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d5150-480">`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister.</span><span class="sxs-lookup"><span data-stu-id="d5150-480">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="d5150-481">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="d5150-481">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d5150-482">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="d5150-482">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d5150-483">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="d5150-483">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d5150-484">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="d5150-484">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="d5150-485">`IMyDependency`ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-485">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="d5150-486">`IMyDependency`kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5150-486">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d5150-487">`ILogger<TCategoryName>`günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="d5150-487">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="d5150-488">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="d5150-488">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="d5150-489">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d5150-489">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d5150-490">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="d5150-490">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="d5150-491">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d5150-491">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="d5150-492">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="d5150-492">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="d5150-493">Örneğin, `services.AddMvc()` hizmet Razor SAYFALARıNı ve MVC gerektir öğesini ekler.</span><span class="sxs-lookup"><span data-stu-id="d5150-493">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="d5150-494">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="d5150-494">We recommended that apps follow this convention.</span></span> <span data-ttu-id="d5150-495">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="d5150-495">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="d5150-496">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="d5150-496">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="d5150-497">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="d5150-497">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="d5150-498">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d5150-498">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="d5150-499">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="d5150-499">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="d5150-500">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="d5150-500">Services injected into Startup</span></span>

<span data-ttu-id="d5150-501">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="d5150-501">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d5150-502">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d5150-502">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="d5150-503">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d5150-503">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d5150-504">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="d5150-504">Framework-provided services</span></span>

<span data-ttu-id="d5150-505">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="d5150-505">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d5150-506">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d5150-506">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d5150-507">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="d5150-507">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="d5150-508">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="d5150-508">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="d5150-509">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="d5150-509">Service Type</span></span> | <span data-ttu-id="d5150-510">Ömür</span><span class="sxs-lookup"><span data-stu-id="d5150-510">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d5150-511">Larsa</span><span class="sxs-lookup"><span data-stu-id="d5150-511">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="d5150-512">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="d5150-513">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="d5150-514">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="d5150-515">Larsa</span><span class="sxs-lookup"><span data-stu-id="d5150-515">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="d5150-516">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-516">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="d5150-517">Larsa</span><span class="sxs-lookup"><span data-stu-id="d5150-517">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="d5150-518">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="d5150-519">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="d5150-520">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="d5150-521">Larsa</span><span class="sxs-lookup"><span data-stu-id="d5150-521">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="d5150-522">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="d5150-523">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="d5150-524">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-524">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="d5150-525">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="d5150-525">Register additional services with extension methods</span></span>

<span data-ttu-id="d5150-526">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d5150-526">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="d5150-527">Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="d5150-527">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="d5150-528">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="d5150-528">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="d5150-529">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="d5150-529">Service lifetimes</span></span>

<span data-ttu-id="d5150-530">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="d5150-530">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="d5150-531">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="d5150-531">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="d5150-532">Larsa</span><span class="sxs-lookup"><span data-stu-id="d5150-532">Transient</span></span>

<span data-ttu-id="d5150-533">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d5150-533">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="d5150-534">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d5150-534">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="d5150-535">İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="d5150-535">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="d5150-536">Yayıl</span><span class="sxs-lookup"><span data-stu-id="d5150-536">Scoped</span></span>

<span data-ttu-id="d5150-537">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="d5150-537">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="d5150-538">İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="d5150-538">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="d5150-539">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d5150-539">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d5150-540">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="d5150-540">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="d5150-541">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d5150-541">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="d5150-542">Adet</span><span class="sxs-lookup"><span data-stu-id="d5150-542">Singleton</span></span>

<span data-ttu-id="d5150-543">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="d5150-543">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="d5150-544">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="d5150-544">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d5150-545">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-545">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="d5150-546">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d5150-546">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="d5150-547">İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="d5150-547">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="d5150-548">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="d5150-548">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d5150-549">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-549">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d5150-550">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="d5150-550">Service registration methods</span></span>

<span data-ttu-id="d5150-551">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="d5150-551">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="d5150-552">Yöntem</span><span class="sxs-lookup"><span data-stu-id="d5150-552">Method</span></span> | <span data-ttu-id="d5150-553">Automatic</span><span class="sxs-lookup"><span data-stu-id="d5150-553">Automatic</span></span><br><span data-ttu-id="d5150-554">object</span><span class="sxs-lookup"><span data-stu-id="d5150-554">object</span></span><br><span data-ttu-id="d5150-555">elden</span><span class="sxs-lookup"><span data-stu-id="d5150-555">disposal</span></span> | <span data-ttu-id="d5150-556">Birden çok</span><span class="sxs-lookup"><span data-stu-id="d5150-556">Multiple</span></span><br><span data-ttu-id="d5150-557">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="d5150-557">implementations</span></span> | <span data-ttu-id="d5150-558">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d5150-558">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="d5150-559">Örnek:</span><span class="sxs-lookup"><span data-stu-id="d5150-559">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="d5150-560">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-560">Yes</span></span> | <span data-ttu-id="d5150-561">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-561">Yes</span></span> | <span data-ttu-id="d5150-562">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-562">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="d5150-563">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="d5150-563">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="d5150-564">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-564">Yes</span></span> | <span data-ttu-id="d5150-565">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-565">Yes</span></span> | <span data-ttu-id="d5150-566">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-566">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="d5150-567">Örnek:</span><span class="sxs-lookup"><span data-stu-id="d5150-567">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="d5150-568">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-568">Yes</span></span> | <span data-ttu-id="d5150-569">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-569">No</span></span> | <span data-ttu-id="d5150-570">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-570">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="d5150-571">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="d5150-571">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="d5150-572">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-572">No</span></span> | <span data-ttu-id="d5150-573">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-573">Yes</span></span> | <span data-ttu-id="d5150-574">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-574">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="d5150-575">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="d5150-575">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="d5150-576">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-576">No</span></span> | <span data-ttu-id="d5150-577">Hayır</span><span class="sxs-lookup"><span data-stu-id="d5150-577">No</span></span> | <span data-ttu-id="d5150-578">Evet</span><span class="sxs-lookup"><span data-stu-id="d5150-578">Yes</span></span> |

<span data-ttu-id="d5150-579">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d5150-579">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d5150-580">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d5150-580">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d5150-581">`TryAdd{LIFETIME}`Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d5150-581">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d5150-582">Aşağıdaki örnekte, ilk satır `MyDependency` için kaydedilir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d5150-582">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="d5150-583">`IMyDependency`Zaten kayıtlı bir uygulamaya sahip olduğundan ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="d5150-583">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="d5150-584">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="d5150-584">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="d5150-585">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d5150-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="d5150-586">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="d5150-586">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d5150-587">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="d5150-587">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d5150-588">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d5150-588">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="d5150-589">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="d5150-589">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="d5150-590">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="d5150-590">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="d5150-591">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="d5150-591">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d5150-592">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="d5150-592">Constructor injection behavior</span></span>

<span data-ttu-id="d5150-593">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="d5150-593">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d5150-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d5150-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="d5150-595">`ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d5150-595">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="d5150-596">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d5150-596">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d5150-597">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d5150-597">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d5150-598">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d5150-598">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d5150-599">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-599">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d5150-600">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="d5150-600">Entity Framework contexts</span></span>

<span data-ttu-id="d5150-601">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="d5150-601">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d5150-602">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="d5150-602">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="d5150-603">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-603">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d5150-604">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="d5150-604">Lifetime and registration options</span></span>

<span data-ttu-id="d5150-605">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="d5150-605">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="d5150-606">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="d5150-606">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d5150-607">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="d5150-607">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="d5150-608">`Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d5150-608">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="d5150-609">, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` .</span><span class="sxs-lookup"><span data-stu-id="d5150-609">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="d5150-610">`OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="d5150-610">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="d5150-611">Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="d5150-611">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="d5150-612">`OperationService`sınıfının yeni bir örneğini alır `IOperationTransient` .</span><span class="sxs-lookup"><span data-stu-id="d5150-612">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="d5150-613">Yeni örnek farklı bir şekilde oluşturur `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="d5150-613">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="d5150-614">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="d5150-614">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="d5150-615">İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="d5150-615">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="d5150-616">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="d5150-616">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="d5150-617">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="d5150-617">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="d5150-618">`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="d5150-618">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="d5150-619">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="d5150-619">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="d5150-620">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="d5150-620">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="d5150-621">Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="d5150-621">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="d5150-622">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d5150-622">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="d5150-623">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="d5150-623">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="d5150-624">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="d5150-624">**First request:**</span></span>

<span data-ttu-id="d5150-625">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="d5150-625">Controller operations:</span></span>

<span data-ttu-id="d5150-626">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="d5150-626">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="d5150-627">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d5150-627">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d5150-628">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d5150-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d5150-629">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d5150-629">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d5150-630">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="d5150-630">`OperationService` operations:</span></span>

<span data-ttu-id="d5150-631">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="d5150-631">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="d5150-632">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d5150-632">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d5150-633">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d5150-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d5150-634">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d5150-634">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d5150-635">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="d5150-635">**Second request:**</span></span>

<span data-ttu-id="d5150-636">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="d5150-636">Controller operations:</span></span>

<span data-ttu-id="d5150-637">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="d5150-637">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="d5150-638">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d5150-638">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d5150-639">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d5150-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d5150-640">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d5150-640">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d5150-641">`OperationService`operasyonları</span><span class="sxs-lookup"><span data-stu-id="d5150-641">`OperationService` operations:</span></span>

<span data-ttu-id="d5150-642">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="d5150-642">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="d5150-643">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d5150-643">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d5150-644">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d5150-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d5150-645">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d5150-645">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d5150-646">`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="d5150-646">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="d5150-647">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-647">*Transient* objects are always different.</span></span> <span data-ttu-id="d5150-648">Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-648">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="d5150-649">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d5150-649">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="d5150-650">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-650">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="d5150-651">*Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5150-651">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="d5150-652">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="d5150-652">Call services from main</span></span>

<span data-ttu-id="d5150-653"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d5150-653">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d5150-654">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-654">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="d5150-655">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="d5150-655">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="d5150-656">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="d5150-656">Scope validation</span></span>

<span data-ttu-id="d5150-657">Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="d5150-657">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d5150-658">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="d5150-658">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d5150-659">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="d5150-659">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="d5150-660">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d5150-660">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="d5150-661">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="d5150-661">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d5150-662">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="d5150-662">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d5150-663">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="d5150-663">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="d5150-664">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="d5150-664">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d5150-665">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="d5150-665">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="d5150-666">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="d5150-666">Request Services</span></span>

<span data-ttu-id="d5150-667">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="d5150-667">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="d5150-668">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="d5150-668">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="d5150-669">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="d5150-669">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="d5150-670">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-670">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="d5150-671">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="d5150-671">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="d5150-672">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d5150-672">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="d5150-673">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="d5150-673">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d5150-674">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="d5150-674">Design services for dependency injection</span></span>

<span data-ttu-id="d5150-675">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d5150-675">Best practices are to:</span></span>

* <span data-ttu-id="d5150-676">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="d5150-676">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="d5150-677">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="d5150-677">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d5150-678">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="d5150-678">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="d5150-679">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="d5150-679">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d5150-680">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="d5150-680">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d5150-681">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="d5150-681">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d5150-682">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="d5150-682">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d5150-683">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="d5150-683">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="d5150-684">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="d5150-684">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="d5150-685">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-685">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d5150-686">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="d5150-686">Disposal of services</span></span>

<span data-ttu-id="d5150-687">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="d5150-687">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d5150-688">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="d5150-688">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="d5150-689">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="d5150-689">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="d5150-690">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="d5150-690">In the following example:</span></span>

* <span data-ttu-id="d5150-691">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="d5150-691">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d5150-692">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="d5150-692">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="d5150-693">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="d5150-693">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d5150-694">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="d5150-694">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="d5150-695">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="d5150-695">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="d5150-696">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="d5150-696">Transient, limited lifetime</span></span>

<span data-ttu-id="d5150-697">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="d5150-697">**Scenario**</span></span>

<span data-ttu-id="d5150-698">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="d5150-698">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="d5150-699">Örnek, kök kapsamda çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="d5150-699">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="d5150-700">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-700">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="d5150-701">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="d5150-701">**Solution**</span></span>

<span data-ttu-id="d5150-702">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="d5150-702">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="d5150-703">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="d5150-703">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="d5150-704">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="d5150-704">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="d5150-705">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="d5150-705">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="d5150-706"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="d5150-706">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="d5150-707">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="d5150-707">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="d5150-708">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="d5150-708">**Scenario**</span></span>

<span data-ttu-id="d5150-709">Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-709">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="d5150-710">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="d5150-710">**Solution**</span></span>

<span data-ttu-id="d5150-711">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d5150-711">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="d5150-712"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="d5150-712">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="d5150-713"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="d5150-713">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="d5150-714">Yaşam süresi sona erdirmek için kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="d5150-714">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="d5150-715">Genel Yönergeler</span><span class="sxs-lookup"><span data-stu-id="d5150-715">General Guidelines</span></span>

* <span data-ttu-id="d5150-716"><xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme.</span><span class="sxs-lookup"><span data-stu-id="d5150-716">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="d5150-717">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="d5150-717">Use the factory pattern instead.</span></span>
* <span data-ttu-id="d5150-718">Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün.</span><span class="sxs-lookup"><span data-stu-id="d5150-718">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="d5150-719">Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.</span><span class="sxs-lookup"><span data-stu-id="d5150-719">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="d5150-720">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="d5150-720">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="d5150-721"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-721">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="d5150-722">Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d5150-722">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="d5150-723">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="d5150-723">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d5150-724">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="d5150-724">Default service container replacement</span></span>

<span data-ttu-id="d5150-725">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d5150-725">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d5150-726">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5150-726">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="d5150-727">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="d5150-727">Property injection</span></span>
* <span data-ttu-id="d5150-728">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="d5150-728">Injection based on name</span></span>
* <span data-ttu-id="d5150-729">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="d5150-729">Child containers</span></span>
* <span data-ttu-id="d5150-730">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="d5150-730">Custom lifetime management</span></span>
* <span data-ttu-id="d5150-731">`Func<T>`yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="d5150-731">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d5150-732">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="d5150-732">Convention-based registration</span></span>

<span data-ttu-id="d5150-733">Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d5150-733">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="d5150-734">Autofac</span><span class="sxs-lookup"><span data-stu-id="d5150-734">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="d5150-735">Drıioc</span><span class="sxs-lookup"><span data-stu-id="d5150-735">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="d5150-736">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="d5150-736">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="d5150-737">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="d5150-737">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="d5150-738">E</span><span class="sxs-lookup"><span data-stu-id="d5150-738">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="d5150-739">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="d5150-739">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="d5150-740">Unity</span><span class="sxs-lookup"><span data-stu-id="d5150-740">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="d5150-741">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="d5150-741">Thread safety</span></span>

<span data-ttu-id="d5150-742">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d5150-742">Create thread-safe singleton services.</span></span> <span data-ttu-id="d5150-743">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-743">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="d5150-744">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d5150-744">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d5150-745">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="d5150-745">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d5150-746">Öneriler</span><span class="sxs-lookup"><span data-stu-id="d5150-746">Recommendations</span></span>

* <span data-ttu-id="d5150-747">`async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="d5150-747">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="d5150-748">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d5150-748">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="d5150-749">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="d5150-749">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d5150-750">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="d5150-750">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d5150-751">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5150-751">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d5150-752">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="d5150-752">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="d5150-753">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="d5150-753">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="d5150-754">Hizmetlere statik erişimi önleyin.</span><span class="sxs-lookup"><span data-stu-id="d5150-754">Avoid static access to services.</span></span> <span data-ttu-id="d5150-755">Örneğin, bir başka yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik olarak yazılanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="d5150-755">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="d5150-756">[Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="d5150-756">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="d5150-757"><xref:System.IServiceProvider.GetService*>Bunun yerine, şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:</span><span class="sxs-lookup"><span data-stu-id="d5150-757">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="d5150-758">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="d5150-758">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="d5150-759">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="d5150-759">**Correct**:</span></span>

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

* <span data-ttu-id="d5150-760">Kullanarak çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="d5150-760">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="d5150-761">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d5150-761">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="d5150-762">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d5150-762">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d5150-763">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="d5150-763">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d5150-764">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="d5150-764">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d5150-765">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d5150-765">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5150-766">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d5150-766">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d5150-767">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="d5150-767">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="d5150-768">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="d5150-768">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="d5150-769">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="d5150-769">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d5150-770">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="d5150-770">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d5150-771">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="d5150-771">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
