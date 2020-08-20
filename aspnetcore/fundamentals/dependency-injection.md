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
ms.openlocfilehash: ececea3c7cc2f0cdf39bbfd29feec061f9bc6764
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628800"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="7c695-103">ASP.NET Core'da bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="7c695-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c695-104">[Kirk Larkabağı](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="7c695-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="7c695-105">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="7c695-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="7c695-106">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="7c695-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="7c695-107">Seçeneklerin bağımlılığı ekleme hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/options> ..</span><span class="sxs-lookup"><span data-stu-id="7c695-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="7c695-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c695-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="7c695-109">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="7c695-109">Overview of dependency injection</span></span>

<span data-ttu-id="7c695-110">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="7c695-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="7c695-111">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="7c695-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="7c695-112">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="7c695-113">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="7c695-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="7c695-114">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="7c695-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="7c695-115">Önceki örnekte olduğu gibi kod bağımlılıkları sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="7c695-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="7c695-116">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7c695-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="7c695-117">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7c695-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="7c695-118">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="7c695-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="7c695-119">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="7c695-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="7c695-120">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7c695-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="7c695-121">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="7c695-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="7c695-122">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="7c695-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="7c695-123">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="7c695-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="7c695-124">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="7c695-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="7c695-125">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="7c695-126">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="7c695-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="7c695-127">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="7c695-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="7c695-128">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="7c695-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="7c695-129">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="7c695-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="7c695-130">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="7c695-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="7c695-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Yöntemi, hizmeti tek bir isteğin ömrü olan kapsamlı bir yaşam süresine kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7c695-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="7c695-132">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="7c695-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="7c695-133">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="7c695-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="7c695-134">DI düzeniyle:</span><span class="sxs-lookup"><span data-stu-id="7c695-134">With the DI pattern:</span></span>

* <span data-ttu-id="7c695-135">Denetleyici somut türü kullanmaz `MyDependency` , yalnızca arabirimini kullanır `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="7c695-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="7c695-136">Bu, denetleyicinin, denetleyiciyi değiştirmeden kullandığı uygulamayı değiştirmeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="7c695-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="7c695-137">Denetleyici bir örneği oluşturmaz `MyDependency` , bu, dı kapsayıcısı tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7c695-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="7c695-138">Arabirim uygulanması, `IMyDependency` yerleşik günlük API 'si kullanılarak artırılabilir:</span><span class="sxs-lookup"><span data-stu-id="7c695-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="7c695-139">Updated `ConfigureServices` yöntemi yeni `IMyDependency` uygulamayı kaydeder:</span><span class="sxs-lookup"><span data-stu-id="7c695-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="7c695-140">`MyDependency2` oluşturucuda bir ister <xref:Microsoft.Extensions.Logging.ILogger`1> .</span><span class="sxs-lookup"><span data-stu-id="7c695-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="7c695-141">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="7c695-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="7c695-142">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="7c695-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="7c695-143">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="7c695-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="7c695-144">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="7c695-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="7c695-145">`ILogger<TCategoryName>`[Framework tarafından sağlanmış bir hizmettir](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="7c695-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="7c695-146">Kapsayıcı, `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="7c695-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="7c695-147">Bağımlılık ekleme terminolojisi ' nde bir hizmet:</span><span class="sxs-lookup"><span data-stu-id="7c695-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="7c695-148">Genellikle, hizmet gibi uygulamadaki diğer koda hizmet sağlayan bir nesnedir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="7c695-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="7c695-149">Bir Web hizmetiyle ilgili değildir, ancak hizmet bir Web hizmeti kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="7c695-150">Çerçeve, güçlü bir [günlük](xref:fundamentals/logging/index) sistemi sağlar.</span><span class="sxs-lookup"><span data-stu-id="7c695-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="7c695-151">`IMyDependency`Uygulamalar, günlüğü uygulamak için değil temel dı 'yi göstermek için yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="7c695-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="7c695-152">Çoğu uygulamanın Günlükçüler yazması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7c695-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="7c695-153">Aşağıdaki kod, bir hizmetin kaydedilmesini gerektirmeyen varsayılan günlük kaydını kullanmayı göstermektedir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7c695-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="7c695-154">Yukarıdaki kodu kullanarak, `ConfigureServices` Framework tarafından [günlüğe kaydetme](xref:fundamentals/logging/index) sağlandığı için güncelleştirmeniz gerekmez:</span><span class="sxs-lookup"><span data-stu-id="7c695-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="7c695-155">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="7c695-155">Services injected into Startup</span></span>

<span data-ttu-id="7c695-156">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="7c695-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="7c695-157">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7c695-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="7c695-158">Daha fazla bilgi için, bkz <xref:fundamentals/startup> . ve [erişim yapılandırması başlangıç](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="7c695-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="7c695-159">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="7c695-159">Register additional services with extension methods</span></span>

<span data-ttu-id="7c695-160">Hizmeti kaydetmek için bir hizmet koleksiyonu genişletme yöntemi kullanılabilir olduğunda:</span><span class="sxs-lookup"><span data-stu-id="7c695-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="7c695-161">Bu kural, `Add{SERVICE_NAME}` Bu hizmetin gerektirdiği tüm hizmetleri kaydetmek için tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="7c695-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="7c695-162">Bağımlı hizmetler de kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-162">The dependent services are also registered.</span></span>

<span data-ttu-id="7c695-163">Aşağıdaki kod, Razor bireysel kullanıcı hesapları kullanılarak sayfalar şablonu tarafından oluşturulur ve uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemeyi gösterir <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ve <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="7c695-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="7c695-164">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ve <xref:security/authentication/identity> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="7c695-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="7c695-165">Hizmetleri kaydetmek için bir uzantı yöntemi yazma yönergeleri için [hizmet koleksiyonunu birleştirme](#csc) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="7c695-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="7c695-166">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="7c695-166">Service lifetimes</span></span>

<span data-ttu-id="7c695-167">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="7c695-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="7c695-168">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="7c695-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="7c695-169">Larsa</span><span class="sxs-lookup"><span data-stu-id="7c695-169">Transient</span></span>

<span data-ttu-id="7c695-170">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7c695-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="7c695-171">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7c695-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="7c695-172">İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="7c695-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="7c695-173">Yayıl</span><span class="sxs-lookup"><span data-stu-id="7c695-173">Scoped</span></span>

<span data-ttu-id="7c695-174">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="7c695-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="7c695-175">Entity Framework Core kullanılırken, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> genişletme yöntemi, `DbContext` Varsayılan olarak kapsamlı yaşam süresine sahip türleri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7c695-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="7c695-176">İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="7c695-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="7c695-177">Aşağıdaki yaklaşımlardan biriyle ara yazılım kapsamındaki Hizmetleri kullanın:</span><span class="sxs-lookup"><span data-stu-id="7c695-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="7c695-178">Hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7c695-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="7c695-179">Ekleme by [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) , hizmeti tek bir gibi davranmaya zordığı için çalışma zamanında bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7c695-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="7c695-180">[Ömür ve kayıt seçeneklerinde](#lifetime-and-registration-options) örnek, `InvokeAsync` yaklaşımı kullanır.</span><span class="sxs-lookup"><span data-stu-id="7c695-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="7c695-181">[Fabrika tabanlı ara yazılım](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="7c695-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="7c695-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> Uzantı yöntemleri bir ara yazılımın kayıtlı türünün uygulayıp uygulamadığını denetler <xref:Microsoft.AspNetCore.Http.IMiddleware> .</span><span class="sxs-lookup"><span data-stu-id="7c695-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="7c695-183">Bu durumda, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kapsayıcıya kaydedilen örnek, <xref:Microsoft.AspNetCore.Http.IMiddleware> kural tabanlı ara yazılım etkinleştirme mantığını kullanmak yerine, uygulamayı çözmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7c695-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="7c695-184">Ara yazılım, uygulamanın hizmet kapsayıcısında kapsamlı veya geçici bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="7c695-185">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="7c695-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="7c695-186">Kapsamlı bir hizmeti tek bir ***sunucudan çözümleyin.***</span><span class="sxs-lookup"><span data-stu-id="7c695-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="7c695-187">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="7c695-188">Şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="7c695-188">It's fine to:</span></span>

* <span data-ttu-id="7c695-189">Tek bir hizmeti kapsamlı veya geçici bir hizmetten çözümleyin.</span><span class="sxs-lookup"><span data-stu-id="7c695-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="7c695-190">Kapsamlı bir hizmeti başka bir kapsamlı veya geçici hizmetten çözün.</span><span class="sxs-lookup"><span data-stu-id="7c695-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="7c695-191">Varsayılan olarak, geliştirme ortamında, bir hizmetin daha uzun ömürlü bir hizmetin çözümlenmesi bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7c695-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="7c695-192">Daha fazla bilgi için bkz. [kapsam doğrulaması](#sv).</span><span class="sxs-lookup"><span data-stu-id="7c695-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="7c695-193">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-193">Singleton</span></span>

<span data-ttu-id="7c695-194">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="7c695-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="7c695-195">İlk kez istenirler.</span><span class="sxs-lookup"><span data-stu-id="7c695-195">The first time they're requested.</span></span>
* <span data-ttu-id="7c695-196">Geliştirici tarafından doğrudan kapsayıcıya bir uygulama örneği sağlarken.</span><span class="sxs-lookup"><span data-stu-id="7c695-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="7c695-197">Bu yaklaşım nadiren gereklidir.</span><span class="sxs-lookup"><span data-stu-id="7c695-197">This approach is rarely needed.</span></span>

<span data-ttu-id="7c695-198">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="7c695-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="7c695-199">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="7c695-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="7c695-200">Tek tasarım modelini uygulamayın ve tek bir atma kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="7c695-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="7c695-201">Hizmetler bir kapsayıcıdan hizmeti çözümleyen kodla hiçbir şekilde atılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="7c695-202">Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı tek otomatik olarak ayrı olarak silinir.</span><span class="sxs-lookup"><span data-stu-id="7c695-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="7c695-203">Tek hizmetler iş parçacığı güvenli olmalıdır ve genellikle durum bilgisiz hizmetlerde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7c695-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="7c695-204">İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="7c695-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="7c695-205">Uygulama kapatılıncaya kadar bellek yayımlanmadığı için, tek tek ile bellek kullanımı göz önünde bulundurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="7c695-206">Kapsamlı bir hizmeti tek bir ***sunucudan çözümleyin.***</span><span class="sxs-lookup"><span data-stu-id="7c695-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="7c695-207">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="7c695-208">Tek bir hizmeti kapsamlı veya geçici bir hizmetten çözümlemek çok iyidir.</span><span class="sxs-lookup"><span data-stu-id="7c695-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="7c695-209">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="7c695-209">Service registration methods</span></span>

<span data-ttu-id="7c695-210">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="7c695-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="7c695-211">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7c695-211">Method</span></span> | <span data-ttu-id="7c695-212">Automatic</span><span class="sxs-lookup"><span data-stu-id="7c695-212">Automatic</span></span><br><span data-ttu-id="7c695-213">object</span><span class="sxs-lookup"><span data-stu-id="7c695-213">object</span></span><br><span data-ttu-id="7c695-214">elden</span><span class="sxs-lookup"><span data-stu-id="7c695-214">disposal</span></span> | <span data-ttu-id="7c695-215">Birden çok</span><span class="sxs-lookup"><span data-stu-id="7c695-215">Multiple</span></span><br><span data-ttu-id="7c695-216">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="7c695-216">implementations</span></span> | <span data-ttu-id="7c695-217">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="7c695-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="7c695-218">Örnek:</span><span class="sxs-lookup"><span data-stu-id="7c695-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="7c695-219">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-219">Yes</span></span> | <span data-ttu-id="7c695-220">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-220">Yes</span></span> | <span data-ttu-id="7c695-221">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="7c695-222">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="7c695-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="7c695-223">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-223">Yes</span></span> | <span data-ttu-id="7c695-224">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-224">Yes</span></span> | <span data-ttu-id="7c695-225">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="7c695-226">Örnek:</span><span class="sxs-lookup"><span data-stu-id="7c695-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="7c695-227">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-227">Yes</span></span> | <span data-ttu-id="7c695-228">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-228">No</span></span> | <span data-ttu-id="7c695-229">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="7c695-230">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="7c695-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="7c695-231">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-231">No</span></span> | <span data-ttu-id="7c695-232">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-232">Yes</span></span> | <span data-ttu-id="7c695-233">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="7c695-234">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="7c695-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="7c695-235">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-235">No</span></span> | <span data-ttu-id="7c695-236">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-236">No</span></span> | <span data-ttu-id="7c695-237">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-237">Yes</span></span> |

<span data-ttu-id="7c695-238">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="7c695-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="7c695-239">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="7c695-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="7c695-240">`TryAdd{LIFETIME}` zaten kayıtlı bir uygulama yoksa Yöntemler hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7c695-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="7c695-241">Aşağıdaki örnekte, ilk satır `MyDependency` için kaydedilir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="7c695-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="7c695-242">`IMyDependency`Zaten kayıtlı bir uygulamaya sahip olduğundan ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="7c695-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="7c695-243">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="7c695-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="7c695-244">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri, zaten *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7c695-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="7c695-245">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="7c695-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="7c695-246">Hizmetleri kaydederken, aynı türden biri zaten eklenmediyse geliştirici bir örnek eklememelidir.</span><span class="sxs-lookup"><span data-stu-id="7c695-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="7c695-247">Genellikle, kitaplık yazarları `TryAddEnumerable` kapsayıcıda bir uygulamanın birden çok kopyasını kaydetmemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="7c695-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="7c695-248">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="7c695-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="7c695-249">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="7c695-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="7c695-250">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="7c695-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="7c695-251">Hizmet kaydı, aynı türde birden çok uygulama kaydedilirken genellikle sıralı olarak bağımsızdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="7c695-252">`IServiceCollection` , bir koleksiyonudur <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="7c695-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="7c695-253">Aşağıdaki kod, bir oluşturucunun bulunduğu bir hizmetin nasıl ekleneceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="7c695-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="7c695-254">`Add{LIFETIME}`Yöntemler aynı yaklaşımı kullanır.</span><span class="sxs-lookup"><span data-stu-id="7c695-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="7c695-255">Örneğin, [kaynak koda Addkapsamlıdır öğesine](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)bakın.</span><span class="sxs-lookup"><span data-stu-id="7c695-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="7c695-256">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="7c695-256">Constructor injection behavior</span></span>

<span data-ttu-id="7c695-257">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="7c695-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="7c695-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="7c695-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="7c695-259">Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesneler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7c695-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="7c695-260">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), MVC denetleyicileri ve [model ciltler](xref:mvc/models/model-binding)gibi Framework özellikleriyle kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7c695-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="7c695-261">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7c695-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="7c695-262">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7c695-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="7c695-263">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7c695-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="7c695-264">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="7c695-265">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="7c695-265">Entity Framework contexts</span></span>

<span data-ttu-id="7c695-266">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="7c695-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="7c695-267">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="7c695-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="7c695-268">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="7c695-269">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="7c695-269">Lifetime and registration options</span></span>

<span data-ttu-id="7c695-270">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri tanımlayıcı olan bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="7c695-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="7c695-271">Bir işlemin hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı veya farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="7c695-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="7c695-272">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="7c695-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="7c695-273">Oluşturucu, bir `Operation` GUID sağlanmamışsa son 4 karakteri üretir:</span><span class="sxs-lookup"><span data-stu-id="7c695-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="7c695-274">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="7c695-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="7c695-275">Örnek uygulama, istekler içindeki ve arasındaki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7c695-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="7c695-276">Örnek uygulama `IndexModel` ve ara yazılım `IOperation` , her tür türü ve günlük şunu ister `OperationId` :</span><span class="sxs-lookup"><span data-stu-id="7c695-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="7c695-277">Ara yazılım, `IndexModel` ile benzerdir ve aynı hizmetleri çözümler:</span><span class="sxs-lookup"><span data-stu-id="7c695-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="7c695-278">Kapsamındaki hizmetlerin yöntemde çözülmesi gerekir `InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="7c695-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="7c695-279">Günlükçü çıktısı şunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="7c695-279">The logger output shows:</span></span>

* <span data-ttu-id="7c695-280">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-280">*Transient* objects are always different.</span></span> <span data-ttu-id="7c695-281">Geçici `OperationId` değer, `IndexModel` ve ara yazılım içinde farklıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="7c695-282">*Kapsamlı* nesneler her istekte aynıdır, ancak her istekte farklıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="7c695-283">*Tek* nesneler her istek için aynıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="7c695-284">Günlüğe kaydetme çıkışını azaltmak için, dosyadaki *appsettings.Development.js* "Logging: LogLevel: Microsoft: Error" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7c695-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="7c695-285">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="7c695-285">Call services from main</span></span>

<span data-ttu-id="7c695-286"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7c695-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="7c695-287">Bu yaklaşım, başlatma görevlerini çalıştırmak için başlangıçta kapsamlı bir hizmete erişmek için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="7c695-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

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

<span data-ttu-id="7c695-288">Yukarıdaki kod, sayfa öğreticisindeki [tohum başlatıcısı 'Nı eklemedir](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) Razor .</span><span class="sxs-lookup"><span data-stu-id="7c695-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="7c695-289">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `IMyDependency` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="7c695-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="7c695-290">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7c695-290">Scope validation</span></span>

<span data-ttu-id="7c695-291">Uygulama [geliştirme ortamında](xref:fundamentals/environments) çalışırken ve Konağı derlemek Için [createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) çağırdığında, varsayılan hizmet sağlayıcı aşağıdakileri doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="7c695-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="7c695-292">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="7c695-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="7c695-293">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="7c695-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="7c695-294">Geçici hizmetler doğrudan veya dolaylı olarak Singleton veya kapsamlı hizmetlere eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="7c695-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="7c695-295">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7c695-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="7c695-296">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulamanın ömrüne karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="7c695-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="7c695-297">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="7c695-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="7c695-298">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek bir şekilde yükseltilir çünkü uygulama kapatıldığında yalnızca kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="7c695-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="7c695-299">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="7c695-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="7c695-300">Daha fazla bilgi için bkz. [kapsam doğrulaması](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="7c695-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="7c695-301">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="7c695-301">Request Services</span></span>

<span data-ttu-id="7c695-302">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="7c695-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="7c695-303">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="7c695-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="7c695-304">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="7c695-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="7c695-305">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="7c695-306">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="7c695-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="7c695-307">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7c695-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="7c695-308">ASP.NET Core, istek başına kapsam oluşturur ve `RequestServices` kapsamlı hizmet sağlayıcısını kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="7c695-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="7c695-309">Tüm kapsamlı hizmetler, istek etkin olduğu sürece geçerli olur.</span><span class="sxs-lookup"><span data-stu-id="7c695-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="7c695-310">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="7c695-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="7c695-311">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="7c695-311">Design services for dependency injection</span></span>

<span data-ttu-id="7c695-312">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="7c695-312">Best practices are to:</span></span>

* <span data-ttu-id="7c695-313">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="7c695-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="7c695-314">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7c695-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="7c695-315">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="7c695-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="7c695-316">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="7c695-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="7c695-317">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="7c695-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="7c695-318">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="7c695-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="7c695-319">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir işaret.</span><span class="sxs-lookup"><span data-stu-id="7c695-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="7c695-320">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="7c695-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="7c695-321">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="7c695-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="7c695-322">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="7c695-322">Disposal of services</span></span>

<span data-ttu-id="7c695-323">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="7c695-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="7c695-324">Hizmetler, bir kapsayıcıdan hizmeti çözümleyen hiçbir kodla asla çıkarılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="7c695-325">Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı Singleton öğesini bırakır.</span><span class="sxs-lookup"><span data-stu-id="7c695-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="7c695-326">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="7c695-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7c695-327">Hata ayıklama konsolu dizin sayfasının her yenilemesinde aşağıdaki çıktıyı gösterir:</span><span class="sxs-lookup"><span data-stu-id="7c695-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="7c695-328">Hizmet kapsayıcısı tarafından oluşturulmayan hizmetler</span><span class="sxs-lookup"><span data-stu-id="7c695-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="7c695-329">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="7c695-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="7c695-330">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="7c695-330">In the preceding code:</span></span>

* <span data-ttu-id="7c695-331">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="7c695-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="7c695-332">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="7c695-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="7c695-333">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="7c695-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="7c695-334">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="7c695-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="7c695-335">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="7c695-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="7c695-336">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="7c695-336">Transient, limited lifetime</span></span>

<span data-ttu-id="7c695-337">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="7c695-337">**Scenario**</span></span>

<span data-ttu-id="7c695-338">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="7c695-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="7c695-339">Örnek, kök kapsamda (kök kapsayıcı) çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="7c695-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="7c695-340">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="7c695-341">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="7c695-341">**Solution**</span></span>

<span data-ttu-id="7c695-342">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7c695-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="7c695-343">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="7c695-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="7c695-344">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="7c695-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="7c695-345">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="7c695-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="7c695-346"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="7c695-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="7c695-347">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="7c695-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="7c695-348">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="7c695-348">**Scenario**</span></span>

<span data-ttu-id="7c695-349">Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="7c695-350">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="7c695-350">**Solution**</span></span>

<span data-ttu-id="7c695-351">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="7c695-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="7c695-352"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="7c695-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="7c695-353"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="7c695-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="7c695-354">Yaşam süresi sona erdirmek için kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="7c695-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="7c695-355">Genel IDisposable yönergeleri</span><span class="sxs-lookup"><span data-stu-id="7c695-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="7c695-356"><xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme.</span><span class="sxs-lookup"><span data-stu-id="7c695-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="7c695-357">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7c695-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="7c695-358">Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün.</span><span class="sxs-lookup"><span data-stu-id="7c695-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="7c695-359">Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.</span><span class="sxs-lookup"><span data-stu-id="7c695-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="7c695-360">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="7c695-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="7c695-361"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="7c695-362">Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7c695-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="7c695-363">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="7c695-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="7c695-364">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="7c695-364">Default service container replacement</span></span>

<span data-ttu-id="7c695-365">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="7c695-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="7c695-366">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="7c695-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="7c695-367">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="7c695-367">Property injection</span></span>
* <span data-ttu-id="7c695-368">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="7c695-368">Injection based on name</span></span>
* <span data-ttu-id="7c695-369">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="7c695-369">Child containers</span></span>
* <span data-ttu-id="7c695-370">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="7c695-370">Custom lifetime management</span></span>
* <span data-ttu-id="7c695-371">`Func<T>` yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="7c695-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="7c695-372">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="7c695-372">Convention-based registration</span></span>

<span data-ttu-id="7c695-373">Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7c695-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="7c695-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="7c695-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="7c695-375">Drıioc</span><span class="sxs-lookup"><span data-stu-id="7c695-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="7c695-376">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="7c695-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="7c695-377">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="7c695-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="7c695-378">E</span><span class="sxs-lookup"><span data-stu-id="7c695-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="7c695-379">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="7c695-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="7c695-380">Unity</span><span class="sxs-lookup"><span data-stu-id="7c695-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="7c695-381">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="7c695-381">Thread safety</span></span>

<span data-ttu-id="7c695-382">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7c695-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="7c695-383">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="7c695-384">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7c695-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="7c695-385">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="7c695-386">Öneriler</span><span class="sxs-lookup"><span data-stu-id="7c695-386">Recommendations</span></span>

* <span data-ttu-id="7c695-387">`async/await` ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7c695-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="7c695-388">C# zaman uyumsuz oluşturucuları desteklemez.</span><span class="sxs-lookup"><span data-stu-id="7c695-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="7c695-389">Önerilen model, hizmeti eşzamanlı olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="7c695-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="7c695-390">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7c695-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="7c695-391">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="7c695-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="7c695-392">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7c695-393">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="7c695-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="7c695-394">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="7c695-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="7c695-395">Hizmetlere statik erişimi önleyin.</span><span class="sxs-lookup"><span data-stu-id="7c695-395">Avoid static access to services.</span></span> <span data-ttu-id="7c695-396">Örneğin, başka bir yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik olarak yazılanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7c695-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="7c695-397">Dı fabrikalarını hızlı ve zaman uyumlu tutun.</span><span class="sxs-lookup"><span data-stu-id="7c695-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="7c695-398">*Hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7c695-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="7c695-399">Örneğin, <xref:System.IServiceProvider.GetService*> yerine şunu kullandığınızda bir hizmet örneği elde etme çağrısı yapmayın:</span><span class="sxs-lookup"><span data-stu-id="7c695-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="7c695-400">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="7c695-400">**Incorrect:**</span></span>

    ![Yanlış kod](dependency-injection/_static/bad.png)

  <span data-ttu-id="7c695-402">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="7c695-402">**Correct**:</span></span>

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
* <span data-ttu-id="7c695-403">Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme.</span><span class="sxs-lookup"><span data-stu-id="7c695-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="7c695-404">Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="7c695-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="7c695-405">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="7c695-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="7c695-406">İçindeki çağrılardan <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> kaçının `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7c695-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="7c695-407">Çağırma `BuildServiceProvider` genellikle geliştirici içindeki bir hizmeti çözmek istediğinde oluşur `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7c695-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="7c695-408">Örneğin, ihtiyacınız olan durumu göz önünde bulundurun `LoginPath` .</span><span class="sxs-lookup"><span data-stu-id="7c695-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="7c695-409">Aşağıdaki koddan kaçının:</span><span class="sxs-lookup"><span data-stu-id="7c695-409">Avoid the following code:</span></span>

  ![BuildServiceProvider çağrılırken hatalı kod](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="7c695-411">Önceki görüntüde yeşil dalgalı çizgiyi seçtiğinizde `services.BuildServiceProvider` AŞAĞıDAKI ASP0000 uyarısı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="7c695-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="7c695-412">Uygulama kodundan ' BuildServiceProvider ' çağrısı ASP0000, oluşturulan Singleton Hizmetleri ek bir kopyasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="7c695-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="7c695-413">Bağımlılık ekleme hizmetleri gibi alternatifleri, ' configure ' için parametre olarak düşünün.</span><span class="sxs-lookup"><span data-stu-id="7c695-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="7c695-414">Çağırma `BuildServiceProvider` ikinci bir kapsayıcı oluşturur ve bu, parçalanmış tek ton oluşturabilir ve birden çok kapsayıcı genelinde nesne grafiklerine başvuru oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="7c695-415">Get için doğru bir yol, `LoginPath` dı ile seçenek deseninin kullanılması:</span><span class="sxs-lookup"><span data-stu-id="7c695-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="7c695-416">Atılabilir geçici hizmetler, aktiften çıkarma için kapsayıcı tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="7c695-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="7c695-417">Bu, en üst düzey kapsayıcıdan çözümlenirse bir bellek sızıntısını açabilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="7c695-418">Uygulamanın tekton yakaladığı kapsamlı hizmetler içermediğinden emin olmak için kapsam doğrulamayı etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="7c695-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="7c695-419">Daha fazla bilgi için bkz. [kapsam doğrulaması](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="7c695-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="7c695-420">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7c695-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="7c695-421">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="7c695-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="7c695-422">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="7c695-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="7c695-423">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7c695-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="7c695-424">DI 'de çok kiracılı için önerilen desenler</span><span class="sxs-lookup"><span data-stu-id="7c695-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="7c695-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , çok kiracılı bir uygulama sağlar.</span><span class="sxs-lookup"><span data-stu-id="7c695-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="7c695-426">Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="7c695-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="7c695-427">https://github.com/OrchardCMS/OrchardCore.SamplesCMS 'e özgü özelliklerden herhangi biri olmadan yalnızca Orchard Core çerçevesini kullanarak modüler ve çok kiracılı uygulamalar oluşturmaya yönelik örnekler için adresindeki örnek uygulamalar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="7c695-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="7c695-428">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="7c695-428">Framework-provided services</span></span>

<span data-ttu-id="7c695-429">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="7c695-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="7c695-430">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7c695-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="7c695-431">ASP.NET Core şablonlarına dayalı uygulamalar, Framework tarafından kaydedilen 250 taneden fazla hizmete sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7c695-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="7c695-432">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="7c695-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="7c695-433">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="7c695-433">Service Type</span></span> | <span data-ttu-id="7c695-434">Ömür</span><span class="sxs-lookup"><span data-stu-id="7c695-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="7c695-435">Larsa</span><span class="sxs-lookup"><span data-stu-id="7c695-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="7c695-436">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="7c695-437">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="7c695-438">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="7c695-439">Larsa</span><span class="sxs-lookup"><span data-stu-id="7c695-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="7c695-440">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="7c695-441">Larsa</span><span class="sxs-lookup"><span data-stu-id="7c695-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="7c695-442">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="7c695-443">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="7c695-444">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="7c695-445">Larsa</span><span class="sxs-lookup"><span data-stu-id="7c695-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="7c695-446">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="7c695-447">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="7c695-448">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="7c695-449">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7c695-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="7c695-450">Dı uygulaması geliştirme için NDC Konferansı desenleri</span><span class="sxs-lookup"><span data-stu-id="7c695-450">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="7c695-451">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="7c695-451">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="7c695-452">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="7c695-452">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="7c695-453">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="7c695-453">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="7c695-454">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="7c695-454">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="7c695-455">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="7c695-455">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c695-456">[Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler) tarafından</span><span class="sxs-lookup"><span data-stu-id="7c695-456">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="7c695-457">ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.</span><span class="sxs-lookup"><span data-stu-id="7c695-457">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="7c695-458">MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="7c695-458">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="7c695-459">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c695-459">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="7c695-460">Bağımlılık eklenmesine genel bakış</span><span class="sxs-lookup"><span data-stu-id="7c695-460">Overview of dependency injection</span></span>

<span data-ttu-id="7c695-461">*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="7c695-461">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="7c695-462">Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:</span><span class="sxs-lookup"><span data-stu-id="7c695-462">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="7c695-463">`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-463">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="7c695-464">`MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="7c695-464">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="7c695-465">Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="7c695-465">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="7c695-466">Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="7c695-466">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="7c695-467">`MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7c695-467">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="7c695-468">`MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7c695-468">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="7c695-469">Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.</span><span class="sxs-lookup"><span data-stu-id="7c695-469">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="7c695-470">Bu uygulamanın birim testi zordur.</span><span class="sxs-lookup"><span data-stu-id="7c695-470">This implementation is difficult to unit test.</span></span> <span data-ttu-id="7c695-471">Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7c695-471">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="7c695-472">Bağımlılık ekleme bu sorunları şu şekilde giderir:</span><span class="sxs-lookup"><span data-stu-id="7c695-472">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="7c695-473">Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.</span><span class="sxs-lookup"><span data-stu-id="7c695-473">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="7c695-474">Bir hizmet kapsayıcısına bağımlılığın kaydı.</span><span class="sxs-lookup"><span data-stu-id="7c695-474">Registration of the dependency in a service container.</span></span> <span data-ttu-id="7c695-475">ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="7c695-475">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="7c695-476">Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-476">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="7c695-477">Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* .</span><span class="sxs-lookup"><span data-stu-id="7c695-477">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="7c695-478">Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.</span><span class="sxs-lookup"><span data-stu-id="7c695-478">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="7c695-479">[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="7c695-479">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="7c695-480">Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="7c695-480">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="7c695-481">`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister.</span><span class="sxs-lookup"><span data-stu-id="7c695-481">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="7c695-482">Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir.</span><span class="sxs-lookup"><span data-stu-id="7c695-482">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="7c695-483">Her istenen bağımlılık, kendi bağımlılıklarını ister.</span><span class="sxs-lookup"><span data-stu-id="7c695-483">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="7c695-484">Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür.</span><span class="sxs-lookup"><span data-stu-id="7c695-484">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="7c695-485">Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.</span><span class="sxs-lookup"><span data-stu-id="7c695-485">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="7c695-486">`IMyDependency` ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-486">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="7c695-487">`IMyDependency` kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7c695-487">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7c695-488">`ILogger<TCategoryName>` günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .</span><span class="sxs-lookup"><span data-stu-id="7c695-488">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="7c695-489">Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="7c695-489">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="7c695-490">Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="7c695-490">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="7c695-491">Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar.</span><span class="sxs-lookup"><span data-stu-id="7c695-491">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="7c695-492">[Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="7c695-492">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="7c695-493">Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır).</span><span class="sxs-lookup"><span data-stu-id="7c695-493">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="7c695-494">Örneğin, `services.AddMvc()` hizmet Razor SAYFALARıNı ve MVC gerektir öğesini ekler.</span><span class="sxs-lookup"><span data-stu-id="7c695-494">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="7c695-495">Uygulamaların bu kuralı izlemesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="7c695-495">We recommended that apps follow this convention.</span></span> <span data-ttu-id="7c695-496">Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="7c695-496">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="7c695-497">Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="7c695-497">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="7c695-498">Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir.</span><span class="sxs-lookup"><span data-stu-id="7c695-498">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="7c695-499">Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7c695-499">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="7c695-500">Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :</span><span class="sxs-lookup"><span data-stu-id="7c695-500">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="7c695-501">Başlangıca eklenen hizmetler</span><span class="sxs-lookup"><span data-stu-id="7c695-501">Services injected into Startup</span></span>

<span data-ttu-id="7c695-502">`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="7c695-502">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="7c695-503">Hizmetler şu şekilde eklenebilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7c695-503">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="7c695-504">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="7c695-504">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="7c695-505">Framework tarafından sunulan hizmetler</span><span class="sxs-lookup"><span data-stu-id="7c695-505">Framework-provided services</span></span>

<span data-ttu-id="7c695-506">`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="7c695-506">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="7c695-507">Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7c695-507">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="7c695-508">Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="7c695-508">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="7c695-509">Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="7c695-509">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="7c695-510">Hizmet Türü</span><span class="sxs-lookup"><span data-stu-id="7c695-510">Service Type</span></span> | <span data-ttu-id="7c695-511">Ömür</span><span class="sxs-lookup"><span data-stu-id="7c695-511">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="7c695-512">Larsa</span><span class="sxs-lookup"><span data-stu-id="7c695-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="7c695-513">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="7c695-514">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="7c695-515">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="7c695-516">Larsa</span><span class="sxs-lookup"><span data-stu-id="7c695-516">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="7c695-517">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-517">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="7c695-518">Larsa</span><span class="sxs-lookup"><span data-stu-id="7c695-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="7c695-519">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="7c695-520">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="7c695-521">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-521">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="7c695-522">Larsa</span><span class="sxs-lookup"><span data-stu-id="7c695-522">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="7c695-523">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="7c695-524">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-524">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="7c695-525">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-525">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="7c695-526">Uzantı yöntemleriyle ek hizmetleri kaydetme</span><span class="sxs-lookup"><span data-stu-id="7c695-526">Register additional services with extension methods</span></span>

<span data-ttu-id="7c695-527">Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="7c695-527">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="7c695-528">Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="7c695-528">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="7c695-529">Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.</span><span class="sxs-lookup"><span data-stu-id="7c695-529">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="7c695-530">Hizmet yaşam süreleri</span><span class="sxs-lookup"><span data-stu-id="7c695-530">Service lifetimes</span></span>

<span data-ttu-id="7c695-531">Kayıtlı her hizmet için uygun bir yaşam süresi seçin.</span><span class="sxs-lookup"><span data-stu-id="7c695-531">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="7c695-532">ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="7c695-532">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="7c695-533">Larsa</span><span class="sxs-lookup"><span data-stu-id="7c695-533">Transient</span></span>

<span data-ttu-id="7c695-534">Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7c695-534">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="7c695-535">Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7c695-535">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="7c695-536">İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="7c695-536">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="7c695-537">Yayıl</span><span class="sxs-lookup"><span data-stu-id="7c695-537">Scoped</span></span>

<span data-ttu-id="7c695-538">Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).</span><span class="sxs-lookup"><span data-stu-id="7c695-538">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="7c695-539">İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.</span><span class="sxs-lookup"><span data-stu-id="7c695-539">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="7c695-540">Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7c695-540">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="7c695-541">[Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor.</span><span class="sxs-lookup"><span data-stu-id="7c695-541">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="7c695-542">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="7c695-542">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="7c695-543">Adet</span><span class="sxs-lookup"><span data-stu-id="7c695-543">Singleton</span></span>

<span data-ttu-id="7c695-544">Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde).</span><span class="sxs-lookup"><span data-stu-id="7c695-544">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="7c695-545">Her sonraki istek aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="7c695-545">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="7c695-546">Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-546">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="7c695-547">Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.</span><span class="sxs-lookup"><span data-stu-id="7c695-547">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="7c695-548">İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="7c695-548">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="7c695-549">Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir.</span><span class="sxs-lookup"><span data-stu-id="7c695-549">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="7c695-550">Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-550">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="7c695-551">Hizmet kayıt yöntemleri</span><span class="sxs-lookup"><span data-stu-id="7c695-551">Service registration methods</span></span>

<span data-ttu-id="7c695-552">Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.</span><span class="sxs-lookup"><span data-stu-id="7c695-552">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="7c695-553">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7c695-553">Method</span></span> | <span data-ttu-id="7c695-554">Automatic</span><span class="sxs-lookup"><span data-stu-id="7c695-554">Automatic</span></span><br><span data-ttu-id="7c695-555">object</span><span class="sxs-lookup"><span data-stu-id="7c695-555">object</span></span><br><span data-ttu-id="7c695-556">elden</span><span class="sxs-lookup"><span data-stu-id="7c695-556">disposal</span></span> | <span data-ttu-id="7c695-557">Birden çok</span><span class="sxs-lookup"><span data-stu-id="7c695-557">Multiple</span></span><br><span data-ttu-id="7c695-558">uygulamalar</span><span class="sxs-lookup"><span data-stu-id="7c695-558">implementations</span></span> | <span data-ttu-id="7c695-559">Geçiş bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="7c695-559">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="7c695-560">Örnek:</span><span class="sxs-lookup"><span data-stu-id="7c695-560">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="7c695-561">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-561">Yes</span></span> | <span data-ttu-id="7c695-562">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-562">Yes</span></span> | <span data-ttu-id="7c695-563">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-563">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="7c695-564">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="7c695-564">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="7c695-565">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-565">Yes</span></span> | <span data-ttu-id="7c695-566">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-566">Yes</span></span> | <span data-ttu-id="7c695-567">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-567">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="7c695-568">Örnek:</span><span class="sxs-lookup"><span data-stu-id="7c695-568">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="7c695-569">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-569">Yes</span></span> | <span data-ttu-id="7c695-570">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-570">No</span></span> | <span data-ttu-id="7c695-571">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-571">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="7c695-572">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="7c695-572">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="7c695-573">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-573">No</span></span> | <span data-ttu-id="7c695-574">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-574">Yes</span></span> | <span data-ttu-id="7c695-575">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-575">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="7c695-576">Örnekler:</span><span class="sxs-lookup"><span data-stu-id="7c695-576">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="7c695-577">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-577">No</span></span> | <span data-ttu-id="7c695-578">Hayır</span><span class="sxs-lookup"><span data-stu-id="7c695-578">No</span></span> | <span data-ttu-id="7c695-579">Yes</span><span class="sxs-lookup"><span data-stu-id="7c695-579">Yes</span></span> |

<span data-ttu-id="7c695-580">Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="7c695-580">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="7c695-581">Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="7c695-581">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="7c695-582">`TryAdd{LIFETIME}` Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7c695-582">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="7c695-583">Aşağıdaki örnekte, ilk satır `MyDependency` için kaydedilir `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="7c695-583">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="7c695-584">`IMyDependency`Zaten kayıtlı bir uygulamaya sahip olduğundan ikinci satır etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="7c695-584">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="7c695-585">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="7c695-585">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="7c695-586">[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7c695-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="7c695-587">Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="7c695-587">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="7c695-588">Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir.</span><span class="sxs-lookup"><span data-stu-id="7c695-588">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="7c695-589">Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7c695-589">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="7c695-590">Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="7c695-590">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="7c695-591">İkinci satır için kaydedilir `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="7c695-591">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="7c695-592">`IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="7c695-592">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="7c695-593">Oluşturucu Ekleme davranışı</span><span class="sxs-lookup"><span data-stu-id="7c695-593">Constructor injection behavior</span></span>

<span data-ttu-id="7c695-594">Hizmetler, iki mekanizma tarafından çözülebilir:</span><span class="sxs-lookup"><span data-stu-id="7c695-594">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="7c695-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="7c695-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="7c695-596">`ActivatorUtilities` Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7c695-596">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="7c695-597">Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7c695-597">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="7c695-598">Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7c695-598">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="7c695-599">Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7c695-599">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="7c695-600">Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-600">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="7c695-601">Entity Framework bağlamları</span><span class="sxs-lookup"><span data-stu-id="7c695-601">Entity Framework contexts</span></span>

<span data-ttu-id="7c695-602">Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="7c695-602">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="7c695-603">Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="7c695-603">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="7c695-604">Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-604">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="7c695-605">Ömür ve kayıt seçenekleri</span><span class="sxs-lookup"><span data-stu-id="7c695-605">Lifetime and registration options</span></span>

<span data-ttu-id="7c695-606">Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="7c695-606">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="7c695-607">Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="7c695-607">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="7c695-608">Arabirimler `Operation` sınıfında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="7c695-608">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="7c695-609">`Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7c695-609">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="7c695-610">, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` .</span><span class="sxs-lookup"><span data-stu-id="7c695-610">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="7c695-611">`OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.</span><span class="sxs-lookup"><span data-stu-id="7c695-611">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="7c695-612">Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="7c695-612">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="7c695-613">`OperationService` sınıfının yeni bir örneğini alır `IOperationTransient` .</span><span class="sxs-lookup"><span data-stu-id="7c695-613">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="7c695-614">Yeni örnek farklı bir şekilde oluşturur `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="7c695-614">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="7c695-615">İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="7c695-615">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="7c695-616">İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.</span><span class="sxs-lookup"><span data-stu-id="7c695-616">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="7c695-617">Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.</span><span class="sxs-lookup"><span data-stu-id="7c695-617">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="7c695-618">' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:</span><span class="sxs-lookup"><span data-stu-id="7c695-618">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="7c695-619">`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="7c695-619">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="7c695-620">Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.</span><span class="sxs-lookup"><span data-stu-id="7c695-620">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="7c695-621">Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7c695-621">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="7c695-622">Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="7c695-622">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="7c695-623">Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="7c695-623">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="7c695-624">Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="7c695-624">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="7c695-625">**İlk istek:**</span><span class="sxs-lookup"><span data-stu-id="7c695-625">**First request:**</span></span>

<span data-ttu-id="7c695-626">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="7c695-626">Controller operations:</span></span>

<span data-ttu-id="7c695-627">Geçici: d233e165-f417-469B-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="7c695-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="7c695-628">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="7c695-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="7c695-629">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7c695-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7c695-630">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7c695-630">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7c695-631">`OperationService` operasyonları</span><span class="sxs-lookup"><span data-stu-id="7c695-631">`OperationService` operations:</span></span>

<span data-ttu-id="7c695-632">Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="7c695-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="7c695-633">Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="7c695-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="7c695-634">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7c695-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7c695-635">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7c695-635">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7c695-636">**İkinci istek:**</span><span class="sxs-lookup"><span data-stu-id="7c695-636">**Second request:**</span></span>

<span data-ttu-id="7c695-637">Denetleyici işlemleri:</span><span class="sxs-lookup"><span data-stu-id="7c695-637">Controller operations:</span></span>

<span data-ttu-id="7c695-638">Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="7c695-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="7c695-639">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="7c695-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="7c695-640">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7c695-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7c695-641">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7c695-641">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7c695-642">`OperationService` operasyonları</span><span class="sxs-lookup"><span data-stu-id="7c695-642">`OperationService` operations:</span></span>

<span data-ttu-id="7c695-643">Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="7c695-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="7c695-644">Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="7c695-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="7c695-645">Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7c695-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7c695-646">Örnek: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7c695-646">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7c695-647">`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:</span><span class="sxs-lookup"><span data-stu-id="7c695-647">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="7c695-648">*Geçici* nesneler her zaman farklıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-648">*Transient* objects are always different.</span></span> <span data-ttu-id="7c695-649">Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-649">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="7c695-650">Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7c695-650">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="7c695-651">*Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-651">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="7c695-652">*Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7c695-652">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="7c695-653">Ana bilgisayardan Hizmetleri çağır</span><span class="sxs-lookup"><span data-stu-id="7c695-653">Call services from main</span></span>

<span data-ttu-id="7c695-654"><xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7c695-654">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="7c695-655">Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-655">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="7c695-656">Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="7c695-656">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="7c695-657">Kapsam doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7c695-657">Scope validation</span></span>

<span data-ttu-id="7c695-658">Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="7c695-658">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="7c695-659">Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="7c695-659">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="7c695-660">Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="7c695-660">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="7c695-661">Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7c695-661">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="7c695-662">Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="7c695-662">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="7c695-663">Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış.</span><span class="sxs-lookup"><span data-stu-id="7c695-663">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="7c695-664">Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="7c695-664">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="7c695-665">Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="7c695-665">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="7c695-666">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="7c695-666">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="7c695-667">İstek Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="7c695-667">Request Services</span></span>

<span data-ttu-id="7c695-668">ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.</span><span class="sxs-lookup"><span data-stu-id="7c695-668">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="7c695-669">İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="7c695-669">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="7c695-670">Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="7c695-670">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="7c695-671">Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-671">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="7c695-672">Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="7c695-672">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="7c695-673">Bu, test etmek daha kolay olan sınıfları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7c695-673">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="7c695-674">Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .</span><span class="sxs-lookup"><span data-stu-id="7c695-674">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="7c695-675">Bağımlılık ekleme için tasarım hizmetleri</span><span class="sxs-lookup"><span data-stu-id="7c695-675">Design services for dependency injection</span></span>

<span data-ttu-id="7c695-676">En iyi uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="7c695-676">Best practices are to:</span></span>

* <span data-ttu-id="7c695-677">Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="7c695-677">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="7c695-678">Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7c695-678">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="7c695-679">Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.</span><span class="sxs-lookup"><span data-stu-id="7c695-679">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="7c695-680">Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="7c695-680">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="7c695-681">Doğrudan örnekleme kodu belirli bir uygulamaya bağar.</span><span class="sxs-lookup"><span data-stu-id="7c695-681">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="7c695-682">Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.</span><span class="sxs-lookup"><span data-stu-id="7c695-682">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="7c695-683">Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır.</span><span class="sxs-lookup"><span data-stu-id="7c695-683">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="7c695-684">Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi.</span><span class="sxs-lookup"><span data-stu-id="7c695-684">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="7c695-685">RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="7c695-685">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="7c695-686">İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-686">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="7c695-687">Hizmetlerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="7c695-687">Disposal of services</span></span>

<span data-ttu-id="7c695-688">Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="7c695-688">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="7c695-689">Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.</span><span class="sxs-lookup"><span data-stu-id="7c695-689">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="7c695-690">Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:</span><span class="sxs-lookup"><span data-stu-id="7c695-690">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="7c695-691">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="7c695-691">In the following example:</span></span>

* <span data-ttu-id="7c695-692">Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="7c695-692">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="7c695-693">Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.</span><span class="sxs-lookup"><span data-stu-id="7c695-693">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="7c695-694">Framework Hizmetleri otomatik olarak atmaz.</span><span class="sxs-lookup"><span data-stu-id="7c695-694">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="7c695-695">Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.</span><span class="sxs-lookup"><span data-stu-id="7c695-695">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="7c695-696">Geçici ve paylaşılan örnekler için IDisposable Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="7c695-696">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="7c695-697">Geçici, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="7c695-697">Transient, limited lifetime</span></span>

<span data-ttu-id="7c695-698">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="7c695-698">**Scenario**</span></span>

<span data-ttu-id="7c695-699">Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:</span><span class="sxs-lookup"><span data-stu-id="7c695-699">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="7c695-700">Örnek, kök kapsamda çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="7c695-700">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="7c695-701">Kapsam bitmeden önce örnek atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-701">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="7c695-702">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="7c695-702">**Solution**</span></span>

<span data-ttu-id="7c695-703">Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7c695-703">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="7c695-704">Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur.</span><span class="sxs-lookup"><span data-stu-id="7c695-704">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="7c695-705">Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="7c695-705">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="7c695-706">Oluşturucusunda bir alın <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="7c695-706">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="7c695-707"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="7c695-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="7c695-708">Paylaşılan örnek, sınırlı ömür</span><span class="sxs-lookup"><span data-stu-id="7c695-708">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="7c695-709">**Senaryo**</span><span class="sxs-lookup"><span data-stu-id="7c695-709">**Scenario**</span></span>

<span data-ttu-id="7c695-710">Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-710">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="7c695-711">**Çözüm**</span><span class="sxs-lookup"><span data-stu-id="7c695-711">**Solution**</span></span>

<span data-ttu-id="7c695-712">Örneği kapsamlı bir ömür ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="7c695-712">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="7c695-713"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="7c695-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="7c695-714"><xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="7c695-714">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="7c695-715">Yaşam süresi sona erdirmek için kapsamı atın.</span><span class="sxs-lookup"><span data-stu-id="7c695-715">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="7c695-716">Genel Yönergeler</span><span class="sxs-lookup"><span data-stu-id="7c695-716">General Guidelines</span></span>

* <span data-ttu-id="7c695-717"><xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme.</span><span class="sxs-lookup"><span data-stu-id="7c695-717">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="7c695-718">Bunun yerine fabrika modelini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7c695-718">Use the factory pattern instead.</span></span>
* <span data-ttu-id="7c695-719">Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün.</span><span class="sxs-lookup"><span data-stu-id="7c695-719">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="7c695-720">Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.</span><span class="sxs-lookup"><span data-stu-id="7c695-720">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="7c695-721">Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="7c695-721">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="7c695-722"><xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-722">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="7c695-723">Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7c695-723">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="7c695-724">Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.</span><span class="sxs-lookup"><span data-stu-id="7c695-724">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="7c695-725">Varsayılan hizmet kapsayıcısı değiştirme</span><span class="sxs-lookup"><span data-stu-id="7c695-725">Default service container replacement</span></span>

<span data-ttu-id="7c695-726">Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="7c695-726">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="7c695-727">Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="7c695-727">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="7c695-728">Özellik ekleme</span><span class="sxs-lookup"><span data-stu-id="7c695-728">Property injection</span></span>
* <span data-ttu-id="7c695-729">Ada göre ekleme</span><span class="sxs-lookup"><span data-stu-id="7c695-729">Injection based on name</span></span>
* <span data-ttu-id="7c695-730">Alt kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="7c695-730">Child containers</span></span>
* <span data-ttu-id="7c695-731">Özel ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="7c695-731">Custom lifetime management</span></span>
* <span data-ttu-id="7c695-732">`Func<T>` yavaş başlatma desteği</span><span class="sxs-lookup"><span data-stu-id="7c695-732">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="7c695-733">Kural tabanlı kayıt</span><span class="sxs-lookup"><span data-stu-id="7c695-733">Convention-based registration</span></span>

<span data-ttu-id="7c695-734">Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7c695-734">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="7c695-735">Autofac</span><span class="sxs-lookup"><span data-stu-id="7c695-735">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="7c695-736">Drıioc</span><span class="sxs-lookup"><span data-stu-id="7c695-736">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="7c695-737">Yetkisiz kullanım</span><span class="sxs-lookup"><span data-stu-id="7c695-737">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="7c695-738">Açık Inject</span><span class="sxs-lookup"><span data-stu-id="7c695-738">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="7c695-739">E</span><span class="sxs-lookup"><span data-stu-id="7c695-739">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="7c695-740">Stakıbox</span><span class="sxs-lookup"><span data-stu-id="7c695-740">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="7c695-741">Unity</span><span class="sxs-lookup"><span data-stu-id="7c695-741">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="7c695-742">İş parçacığı güvenliği</span><span class="sxs-lookup"><span data-stu-id="7c695-742">Thread safety</span></span>

<span data-ttu-id="7c695-743">İş parçacığı güvenli Singleton Hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7c695-743">Create thread-safe singleton services.</span></span> <span data-ttu-id="7c695-744">Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-744">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="7c695-745">Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7c695-745">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="7c695-746">Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="7c695-746">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="7c695-747">Öneriler</span><span class="sxs-lookup"><span data-stu-id="7c695-747">Recommendations</span></span>

* <span data-ttu-id="7c695-748">`async/await` ve `Task` tabanlı hizmet çözümlemesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7c695-748">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="7c695-749">C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="7c695-749">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="7c695-750">Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7c695-750">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="7c695-751">Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="7c695-751">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="7c695-752">Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7c695-752">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7c695-753">Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının.</span><span class="sxs-lookup"><span data-stu-id="7c695-753">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="7c695-754">DI aracılığıyla gerçek öğe istemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="7c695-754">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="7c695-755">Hizmetlere statik erişimi önleyin.</span><span class="sxs-lookup"><span data-stu-id="7c695-755">Avoid static access to services.</span></span> <span data-ttu-id="7c695-756">Örneğin, bir başka yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik olarak yazılanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7c695-756">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="7c695-757">[Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin*kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7c695-757">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="7c695-758"><xref:System.IServiceProvider.GetService*>Bunun yerine, şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:</span><span class="sxs-lookup"><span data-stu-id="7c695-758">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="7c695-759">**Yanlış:**</span><span class="sxs-lookup"><span data-stu-id="7c695-759">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="7c695-760">**Doğru**:</span><span class="sxs-lookup"><span data-stu-id="7c695-760">**Correct**:</span></span>

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

* <span data-ttu-id="7c695-761">Kullanarak çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="7c695-761">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="7c695-762">Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="7c695-762">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="7c695-763">Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7c695-763">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="7c695-764">Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.</span><span class="sxs-lookup"><span data-stu-id="7c695-764">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="7c695-765">Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* .</span><span class="sxs-lookup"><span data-stu-id="7c695-765">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="7c695-766">Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7c695-766">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c695-767">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7c695-767">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="7c695-768">ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu</span><span class="sxs-lookup"><span data-stu-id="7c695-768">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="7c695-769">Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma</span><span class="sxs-lookup"><span data-stu-id="7c695-769">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="7c695-770">Açık bağımlılıklar Ilkesi</span><span class="sxs-lookup"><span data-stu-id="7c695-770">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="7c695-771">Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)</span><span class="sxs-lookup"><span data-stu-id="7c695-771">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="7c695-772">ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme</span><span class="sxs-lookup"><span data-stu-id="7c695-772">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
