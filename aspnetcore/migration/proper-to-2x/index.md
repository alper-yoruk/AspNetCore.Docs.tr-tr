---
title: ASP.NET'den ASP.NET Çekirdeğine geçiş
author: isaac2004
description: Varolan ASP.NET mvc veya Web API uygulamalarını core.web'ASP.NET geçirmek için kılavuz alın
ms.author: scaddie
ms.date: 10/18/2019
uid: migration/proper-to-2x/index
ms.openlocfilehash: 68a45dc50e00bead564500a12509b62a4a193ec4
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511099"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="fe950-103">ASP.NET'den ASP.NET Çekirdeğine geçiş</span><span class="sxs-lookup"><span data-stu-id="fe950-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="fe950-104">Yazar: [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="fe950-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="fe950-105">Bu makale, ASP.NET Core'a ASP.NET uygulamaları geçirmek için bir başvuru kılavuzu görevi ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="fe950-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe950-106">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="fe950-106">Prerequisites</span></span>

[<span data-ttu-id="fe950-107">.NET Core SDK 2.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="fe950-107">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="fe950-108">Hedef çerçeveler</span><span class="sxs-lookup"><span data-stu-id="fe950-108">Target frameworks</span></span>

<span data-ttu-id="fe950-109">ASP.NET Core projeleri geliştiricilere .NET Core, .NET Framework veya her ikisini birden hedefleme esnekliği sunar.</span><span class="sxs-lookup"><span data-stu-id="fe950-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="fe950-110">Hangi hedef çerçevenin en uygun olduğunu belirlemek [için sunucu uygulamaları için .NET Core ve .NET Framework arasında seçim](/dotnet/standard/choosing-core-framework-server) edersiniz.</span><span class="sxs-lookup"><span data-stu-id="fe950-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="fe950-111">.NET Framework hedeflediğinizde, projelerin tek tek NuGet paketlerine başvurması gerekir.</span><span class="sxs-lookup"><span data-stu-id="fe950-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="fe950-112">Hedefleme .NET Core, ASP.NET Core [metapaketi](xref:fundamentals/metapackage-app)sayesinde çok sayıda açık paket referansını ortadan kaldırmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="fe950-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="fe950-113">Projenizde `Microsoft.AspNetCore.App` meta paketi yükleyin:</span><span class="sxs-lookup"><span data-stu-id="fe950-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="fe950-114">Metapaket kullanıldığında, uygulamayla birlikte meta pakete atıfta bulunulan hiçbir paket dağıtılanmez.</span><span class="sxs-lookup"><span data-stu-id="fe950-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="fe950-115">.NET Core Runtime Store bu varlıkları içerir ve performansı artırmak için önceden derlenir.</span><span class="sxs-lookup"><span data-stu-id="fe950-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="fe950-116">Daha fazla ayrıntı [için ASP.NET Core için Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app) bakın.</span><span class="sxs-lookup"><span data-stu-id="fe950-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="fe950-117">Proje yapısı farklılıkları</span><span class="sxs-lookup"><span data-stu-id="fe950-117">Project structure differences</span></span>

<span data-ttu-id="fe950-118">*.csproj* dosya biçimi ASP.NET Core'da basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="fe950-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="fe950-119">Bazı önemli değişiklikler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="fe950-119">Some notable changes include:</span></span>

- <span data-ttu-id="fe950-120">Dosyaların açık bir şekilde eklenmesi, dosyaların projenin bir parçası olarak kabul edilmesi için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="fe950-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="fe950-121">Bu, büyük ekipler üzerinde çalışırken XML birleştirme çakışma riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="fe950-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="fe950-122">Dosya okunabilirliğini artıran diğer projelere GUID tabanlı başvuru lar yoktur.</span><span class="sxs-lookup"><span data-stu-id="fe950-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="fe950-123">Dosya Visual Studio'da boşaltılmadan düzenlenebilir:</span><span class="sxs-lookup"><span data-stu-id="fe950-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Visual Studio 2017'de CSPROJ bağlam menüsü seçeneğini edin](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="fe950-125">Global.asax dosya değiştirme</span><span class="sxs-lookup"><span data-stu-id="fe950-125">Global.asax file replacement</span></span>

<span data-ttu-id="fe950-126">ASP.NET Core bir uygulama bootstrapping için yeni bir mekanizma tanıttı.</span><span class="sxs-lookup"><span data-stu-id="fe950-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="fe950-127">ASP.NET başvurularıiçin giriş noktası *Global.asax* dosyasıdır.</span><span class="sxs-lookup"><span data-stu-id="fe950-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="fe950-128">Rota yapılandırması ve filtre ve alan kayıtları gibi görevler *Global.asax* dosyasında işlenir.</span><span class="sxs-lookup"><span data-stu-id="fe950-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="fe950-129">Bu yaklaşım, uygulamayı ve dağıtıldığı sunucuyu uygulamayı engelleyecek şekilde çiftleştirilir.</span><span class="sxs-lookup"><span data-stu-id="fe950-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="fe950-130">Ayırmak amacıyla, [OWIN](https://owin.org/) birlikte birden fazla çerçeve kullanmak için daha temiz bir yol sağlamak için tanıtıldı.</span><span class="sxs-lookup"><span data-stu-id="fe950-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="fe950-131">OWIN yalnızca gerekli modülleri eklemek için bir boru hattı sağlar.</span><span class="sxs-lookup"><span data-stu-id="fe950-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="fe950-132">Barındırma ortamı, hizmetleri ve uygulamanın istek ardışık hattını yapılandırmak için bir [Başlangıç](xref:fundamentals/startup) işlevi alır.</span><span class="sxs-lookup"><span data-stu-id="fe950-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="fe950-133">`Startup`uygulama ile ara yazılım kümesi kaydeder.</span><span class="sxs-lookup"><span data-stu-id="fe950-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="fe950-134">Her istek için uygulama, ara yazılım bileşenlerinin her birini, bağlı bir listenin baş işaretçisiyle varolan bir işleyici ler kümesine çağırır.</span><span class="sxs-lookup"><span data-stu-id="fe950-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="fe950-135">Her ara yazılım bileşeni, istek işleme ardışık hattına bir veya daha fazla işleyici ekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="fe950-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="fe950-136">Bu, listenin yeni başkanı olan işleyiciye bir başvuru döndürülerek gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="fe950-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="fe950-137">Her işleyici, listedeki bir sonraki işleyiciyi hatırlamak ve çağırmakla yükümlüdür.</span><span class="sxs-lookup"><span data-stu-id="fe950-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="fe950-138">ASP.NET Core ile bir uygulamaya giriş `Startup`noktası dır ve artık *Global.asax'a*bağımlı değildir.</span><span class="sxs-lookup"><span data-stu-id="fe950-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="fe950-139">OWIN'i .NET Framework ile kullanırken, aşağıdaki gibi bir şeyi bir boru hattı olarak kullanın:</span><span class="sxs-lookup"><span data-stu-id="fe950-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="fe950-140">Bu, varsayılan yollarınızı yapılandırır ve Varsayılan olarak Json üzerinden XmlSerialization olarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="fe950-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="fe950-141">Gerektiğinde bu ardışık yapıya diğer Ara yazılımları ekleyin (yükleme hizmetleri, yapılandırma ayarları, statik dosyalar, vb.).</span><span class="sxs-lookup"><span data-stu-id="fe950-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="fe950-142">ASP.NET Core benzer bir yaklaşım kullanır, ancak girişi işlemek için OWIN güvenmez.</span><span class="sxs-lookup"><span data-stu-id="fe950-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="fe950-143">Bunun yerine, *bu Program.cs* `Main` yöntemi (konsol uygulamalarına benzer) üzerinden yapılır ve `Startup` orada yüklenir.</span><span class="sxs-lookup"><span data-stu-id="fe950-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="fe950-144">`Startup`bir `Configure` yöntem içermelidir.</span><span class="sxs-lookup"><span data-stu-id="fe950-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="fe950-145">In `Configure`, boru hattına gerekli ara yazılımı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fe950-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="fe950-146">Aşağıdaki örnekte (varsayılan web sitesi şablonundan), uzantı yöntemleri ardışık alanı aşağıdakiler için destekle yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="fe950-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="fe950-147">Hata sayfaları</span><span class="sxs-lookup"><span data-stu-id="fe950-147">Error pages</span></span>
- <span data-ttu-id="fe950-148">HTTP Sıkı Ulaşım Güvenliği</span><span class="sxs-lookup"><span data-stu-id="fe950-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="fe950-149">HTTPS için HTTP yönlendirme</span><span class="sxs-lookup"><span data-stu-id="fe950-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="fe950-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="fe950-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="fe950-151">Ana bilgisayar ve uygulama, gelecekte farklı bir platforma geçme esnekliği sağlayan ayrılmıştır.</span><span class="sxs-lookup"><span data-stu-id="fe950-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="fe950-152">Core Startup ve Middleware'ASP.NET daha ayrıntılı bir başvuru için ASP.NET [Core'da Başlangıç'a](xref:fundamentals/startup) bakın</span><span class="sxs-lookup"><span data-stu-id="fe950-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="fe950-153">Mağaza yapılandırmaları</span><span class="sxs-lookup"><span data-stu-id="fe950-153">Store configurations</span></span>

<span data-ttu-id="fe950-154">ASP.NET depolama ayarlarını destekler.</span><span class="sxs-lookup"><span data-stu-id="fe950-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="fe950-155">Bu ayar, örneğin, uygulamaların dağıtıldığı ortamı desteklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fe950-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="fe950-156">Yaygın bir uygulama `<appSettings>` *Web.config* dosyasının bölümünde tüm özel anahtar değeri çiftleri depolamak oldu:</span><span class="sxs-lookup"><span data-stu-id="fe950-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="fe950-157">Uygulamalar `ConfigurationManager.AppSettings` `System.Configuration` ad alanında toplama kullanarak bu ayarları okuyun:</span><span class="sxs-lookup"><span data-stu-id="fe950-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="fe950-158">ASP.NET Core, uygulama için yapılandırma verilerini herhangi bir dosyada saklayabilir ve ara yazılım bootstrapping'in bir parçası olarak yükleyebilir.</span><span class="sxs-lookup"><span data-stu-id="fe950-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="fe950-159">Proje şablonlarında kullanılan varsayılan dosya *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="fe950-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="fe950-160">Bu dosyayı uygulamanızın `IConfiguration` içinde bir örneğin içine yüklemek *Startup.cs*yapılır:</span><span class="sxs-lookup"><span data-stu-id="fe950-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="fe950-161">Uygulama ayarları `Configuration` almak için okur:</span><span class="sxs-lookup"><span data-stu-id="fe950-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="fe950-162">Bu yaklaşımın, bir hizmeti bu değerlerle yüklemek için [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) (DI) kullanmak gibi daha sağlam hale getirmek için uzantıları vardır.</span><span class="sxs-lookup"><span data-stu-id="fe950-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="fe950-163">DI yaklaşımı, güçlü bir şekilde yazılmış bir yapılandırma nesnesi kümesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="fe950-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="fe950-164">ASP.NET Core yapılandırmasına daha ayrıntılı bir başvuru [için, ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="fe950-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="fe950-165">Yerel bağımlılık enjeksiyonu</span><span class="sxs-lookup"><span data-stu-id="fe950-165">Native dependency injection</span></span>

<span data-ttu-id="fe950-166">Büyük, ölçeklenebilir uygulamalar inşa ederken önemli bir hedef bileşenlerin ve hizmetlerin gevşek bağlantı olduğunu.</span><span class="sxs-lookup"><span data-stu-id="fe950-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="fe950-167">[Bağımlılık Enjeksiyon](xref:fundamentals/dependency-injection) bunu başarmak için popüler bir tekniktir, ve ASP.NET Core yerli bir bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="fe950-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="fe950-168">ASP.NET uygulamalarda, geliştiriciler Bağımlılık Enjeksiyonu uygulamak için üçüncü taraf kitaplığına güvenir.</span><span class="sxs-lookup"><span data-stu-id="fe950-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="fe950-169">Bu tür bir [kitaplık,](https://github.com/unitycontainer/unity)Microsoft Desenleri & Uygulamaları tarafından sağlanan Unity'dir.</span><span class="sxs-lookup"><span data-stu-id="fe950-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="fe950-170">Birlik ile Bağımlılık Enjeksiyon kurma bir örnek `IDependencyResolver` bir `UnityContainer`sarar uygulanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="fe950-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="fe950-171">Hizmetinizin `UnityContainer`bir örneğini oluşturun ve kapsayıcınızın yeni `HttpConfiguration` örneğine `UnityResolver` bağımlılık çözümleyicisini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="fe950-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="fe950-172">İhtiyaç `IProductRepository` duyulan yerlerde enjekte edin:</span><span class="sxs-lookup"><span data-stu-id="fe950-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="fe950-173">Bağımlılık Enjeksiyonu ASP.NET Çekirdek'in bir parçası olduğundan, `ConfigureServices` hizmetinizi *Startup.cs*yöntemine ekleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="fe950-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="fe950-174">Depo, Unity ile doğru olduğu gibi her yere enjekte edilebilir.</span><span class="sxs-lookup"><span data-stu-id="fe950-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="fe950-175">Bağımlılık enjeksiyonu hakkında daha fazla bilgi için [bkz.](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="fe950-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="fe950-176">Statik dosyalara hizmet</span><span class="sxs-lookup"><span data-stu-id="fe950-176">Serve static files</span></span>

<span data-ttu-id="fe950-177">Web geliştirmenin önemli bir parçası statik, istemci tarafı varlıklara hizmet edebilme yeteneğidir.</span><span class="sxs-lookup"><span data-stu-id="fe950-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="fe950-178">Statik dosyaların en yaygın örnekleri HTML, CSS, Javascript ve görüntülerdir.</span><span class="sxs-lookup"><span data-stu-id="fe950-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="fe950-179">Bu dosyaların uygulamanın (veya CDN) yayımlanmış konumuna kaydedilmesi ve bir istekle yüklenebilmeleri için başvurulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="fe950-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="fe950-180">Bu işlem ASP.NET Core'da değişti.</span><span class="sxs-lookup"><span data-stu-id="fe950-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="fe950-181">ASP.NET, statik dosyalar çeşitli dizinlerde depolanır ve görünümlerde başvurur.</span><span class="sxs-lookup"><span data-stu-id="fe950-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="fe950-182">ASP.NET Core'da statik dosyalar, aksi yapılandırılmadığı sürece "web root"*&lt;(içerik kökü&gt;/wwwroot)* olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="fe950-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="fe950-183">Dosyalar, aşağıdaki uzantı `UseStaticFiles` `Startup.Configure`yöntemini çağırarak istek ardışık hattına yüklenir:</span><span class="sxs-lookup"><span data-stu-id="fe950-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="fe950-184">.NET Framework hedefleniniyorsanız, `Microsoft.AspNetCore.StaticFiles`NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="fe950-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="fe950-185">Örneğin, *wwwroot/images* klasöründeki bir görüntü varlığı tarayıcı tarafından `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="fe950-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="fe950-186">ASP.NET Core'da statik dosyalara hizmet vermek için daha ayrıntılı bir başvuru için [Statik dosyalara](xref:fundamentals/static-files)bakın.</span><span class="sxs-lookup"><span data-stu-id="fe950-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-cookies"></a><span data-ttu-id="fe950-187">Çok değerli tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="fe950-187">Multi-value cookies</span></span>

<span data-ttu-id="fe950-188">[Çok değerli tanımlama bilgileri](xref:System.Web.HttpCookie.Values) ASP.NET Core'da desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="fe950-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="fe950-189">Değer başına bir çerez oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fe950-189">Create one cookie per value.</span></span>

## <a name="partial-app-migration"></a><span data-ttu-id="fe950-190">Kısmi uygulama geçişi</span><span class="sxs-lookup"><span data-stu-id="fe950-190">Partial app migration</span></span>

<span data-ttu-id="fe950-191">Kısmi uygulama geçişine bir yaklaşım, bir IIS alt uygulaması oluşturmak ve uygulamanın URL yapısını korurken yalnızca ASP.NET 4,x'ten ASP.NET Core'a belirli yolları taşımaktır.</span><span class="sxs-lookup"><span data-stu-id="fe950-191">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="fe950-192">Örneğin, *applicationHost.config* dosyasından uygulamanın URL yapısını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="fe950-192">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

<span data-ttu-id="fe950-193">Dizin yapısı:</span><span class="sxs-lookup"><span data-stu-id="fe950-193">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a><span data-ttu-id="fe950-194">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fe950-194">Additional resources</span></span>

- [<span data-ttu-id="fe950-195">Kütüphaneleri .NET Core'a Taşıma</span><span class="sxs-lookup"><span data-stu-id="fe950-195">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
