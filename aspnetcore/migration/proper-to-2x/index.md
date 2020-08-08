---
title: ASP.NET 'den ASP.NET Core 'e geçiş
author: isaac2004
description: Mevcut ASP.NET MVC veya Web API uygulamalarını ASP.NET Core. Web 'e geçirmeye yönelik rehberlik alın
ms.author: scaddie
ms.date: 10/18/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/index
ms.openlocfilehash: defdb190acf915eaa7c4aa8469ee47888f771526
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014925"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="719c9-103">ASP.NET 'den ASP.NET Core 'e geçiş</span><span class="sxs-lookup"><span data-stu-id="719c9-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="719c9-104">İle [Isaac Levi](https://isaaclevin.com) tarafından</span><span class="sxs-lookup"><span data-stu-id="719c9-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="719c9-105">Bu makale, ASP.NET uygulamalarının ASP.NET Core geçişine yönelik bir başvuru kılavuzu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="719c9-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="719c9-106">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="719c9-106">Prerequisites</span></span>

[<span data-ttu-id="719c9-107">.NET Core SDK 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="719c9-107">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="719c9-108">Hedef çerçeveler</span><span class="sxs-lookup"><span data-stu-id="719c9-108">Target frameworks</span></span>

<span data-ttu-id="719c9-109">ASP.NET Core projeler, geliştiricilere .NET Core, .NET Framework veya her ikisinin de hedeflenme esnekliği sunar.</span><span class="sxs-lookup"><span data-stu-id="719c9-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="719c9-110">En uygun hedef Framework 'ü belirlemek için, bkz. [sunucu uygulamaları için .NET Core ve .NET Framework arasından seçim yapma](/dotnet/standard/choosing-core-framework-server) .</span><span class="sxs-lookup"><span data-stu-id="719c9-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="719c9-111">.NET Framework hedeflenirken, projelerin ayrı NuGet paketlerine başvurması gerekir.</span><span class="sxs-lookup"><span data-stu-id="719c9-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="719c9-112">.NET Core 'u hedeflemek, ASP.NET Core [metapackage](xref:fundamentals/metapackage-app)sayesinde çok sayıda açık paket başvurularını ortadan kaldırmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="719c9-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="719c9-113">`Microsoft.AspNetCore.App`Meta paketini projenize yükler:</span><span class="sxs-lookup"><span data-stu-id="719c9-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="719c9-114">Metapackage kullanıldığında, metapackage içinde başvurulan hiçbir paket uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="719c9-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="719c9-115">.NET Core çalışma zamanı deposu bu varlıkları içerir ve performansı artırmak için önceden ön derlenmiş hale getiriyoruz.</span><span class="sxs-lookup"><span data-stu-id="719c9-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="719c9-116">Daha fazla ayrıntı için bkz. [Microsoft. AspNetCore. app metapackage ASP.NET Core](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="719c9-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="719c9-117">Proje yapısı farkları</span><span class="sxs-lookup"><span data-stu-id="719c9-117">Project structure differences</span></span>

<span data-ttu-id="719c9-118">*. Csproj* dosya biçimi ASP.NET Core basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="719c9-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="719c9-119">Bazı önemli değişiklikler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="719c9-119">Some notable changes include:</span></span>

- <span data-ttu-id="719c9-120">Dosyaların açıkça eklenmesi, projenin bir parçası olarak kabul edilmesi için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="719c9-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="719c9-121">Bu, büyük ekipler üzerinde çalışırken XML birleştirme çakışmalarının riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="719c9-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="719c9-122">Diğer projelere GUID tabanlı başvurular yoktur ve bu da dosya okunabilirliğini geliştirir.</span><span class="sxs-lookup"><span data-stu-id="719c9-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="719c9-123">Dosya Visual Studio 'da kaldırmadan düzenlenebilir:</span><span class="sxs-lookup"><span data-stu-id="719c9-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Visual Studio 2017 ' de CSPROJ bağlam menüsü seçeneğini Düzenle](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="719c9-125">Global. asax dosyası değiştirme</span><span class="sxs-lookup"><span data-stu-id="719c9-125">Global.asax file replacement</span></span>

<span data-ttu-id="719c9-126">ASP.NET Core, bir uygulamayı önyüklemeden yeni bir mekanizma getirmiştir.</span><span class="sxs-lookup"><span data-stu-id="719c9-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="719c9-127">ASP.NET uygulamaları için giriş noktası *Global. asax* dosyasıdır.</span><span class="sxs-lookup"><span data-stu-id="719c9-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="719c9-128">Rota yapılandırması ve filtre ve alan kayıtları gibi görevler, *Global. asax* dosyasında işlenir.</span><span class="sxs-lookup"><span data-stu-id="719c9-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="719c9-129">Bu yaklaşım, uygulamayı ve dağıtıldığı sunucuyu uygulamayı kesintiye uğratan bir şekilde bağar.</span><span class="sxs-lookup"><span data-stu-id="719c9-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="719c9-130">Bağımsız olarak, [Owin](https://owin.org/) , birden çok çerçeveyi birlikte kullanmanın bir temizleyici yolunu sağlamak için sunulmuştur.</span><span class="sxs-lookup"><span data-stu-id="719c9-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="719c9-131">OWIN yalnızca gereken modülleri eklemek için bir işlem hattı sağlar.</span><span class="sxs-lookup"><span data-stu-id="719c9-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="719c9-132">Barındırma ortamı, hizmetleri ve uygulamanın istek ardışık düzenini yapılandırmak için bir [Başlangıç](xref:fundamentals/startup) işlevi alır.</span><span class="sxs-lookup"><span data-stu-id="719c9-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="719c9-133">`Startup`uygulamayla bir ara yazılım kümesini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="719c9-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="719c9-134">Her istek için, uygulama bir ara yazılım bileşeninin her birini bağlantılı listenin baş işaretçisi ile mevcut bir işleyici kümesine çağırır.</span><span class="sxs-lookup"><span data-stu-id="719c9-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="719c9-135">Her bir ara yazılım bileşeni, istek işleme ardışık düzenine bir veya daha fazla işleyici ekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="719c9-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="719c9-136">Bu, listenin yeni başlığı olan işleyiciye bir başvuru döndürülerek gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="719c9-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="719c9-137">Her işleyici, listedeki bir sonraki işleyiciyi hatırlayıp çağırmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="719c9-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="719c9-138">ASP.NET Core, bir uygulamaya giriş noktası olur `Startup` ve artık *Global. asax*' a bağımlılığı yoktur.</span><span class="sxs-lookup"><span data-stu-id="719c9-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="719c9-139">.NET Framework ile OWIN kullanırken, işlem hattı olarak aşağıdaki gibi bir şey kullanın:</span><span class="sxs-lookup"><span data-stu-id="719c9-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="719c9-140">Bu, varsayılan rotalarınızı yapılandırır ve varsayılan olarak JSON üzerinden XmlSerialization olur.</span><span class="sxs-lookup"><span data-stu-id="719c9-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="719c9-141">Gerektiğinde bu işlem hattına başka bir ara yazılım ekleyin (Yükleme Hizmetleri, yapılandırma ayarları, statik dosyalar vb.).</span><span class="sxs-lookup"><span data-stu-id="719c9-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="719c9-142">ASP.NET Core benzer bir yaklaşım kullanır, ancak girişi işlemek için OWıN 'u kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="719c9-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="719c9-143">Bunun yerine, Program.cs yöntemi aracılığıyla yapılır *Program.cs* `Main` (konsol uygulamalarına benzer) ve bunlar `Startup` üzerinden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="719c9-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="719c9-144">`Startup`bir yöntem içermelidir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="719c9-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="719c9-145">' De `Configure` , gerekli ara yazılımı ardışık düzene ekleyin.</span><span class="sxs-lookup"><span data-stu-id="719c9-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="719c9-146">Aşağıdaki örnekte (varsayılan Web sitesi şablonundan), uzantı yöntemleri işlem hattını aşağıdakiler için desteğiyle yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="719c9-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="719c9-147">Hata sayfaları</span><span class="sxs-lookup"><span data-stu-id="719c9-147">Error pages</span></span>
- <span data-ttu-id="719c9-148">HTTP katı aktarım güvenliği</span><span class="sxs-lookup"><span data-stu-id="719c9-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="719c9-149">HTTPS 'ye HTTP yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="719c9-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="719c9-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="719c9-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="719c9-151">Ana bilgisayar ve uygulama, gelecekte farklı bir platforma geçme esnekliği sağlayan bir şekilde ayrılmış.</span><span class="sxs-lookup"><span data-stu-id="719c9-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="719c9-152">ASP.NET Core başlangıç ve ara yazılım için daha ayrıntılı bir başvuru için bkz. [ASP.NET Core 'de başlatma](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="719c9-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="719c9-153">Mağaza yapılandırması</span><span class="sxs-lookup"><span data-stu-id="719c9-153">Store configurations</span></span>

<span data-ttu-id="719c9-154">ASP.NET, ayarları depolamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="719c9-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="719c9-155">Bu ayar, örneğin, uygulamaların dağıtıldığı ortamı desteklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="719c9-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="719c9-156">Ortak bir uygulama, tüm özel anahtar-değer çiftlerini `<appSettings>` *Web.config* dosyasının bölümünde depolandı:</span><span class="sxs-lookup"><span data-stu-id="719c9-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="719c9-157">Uygulamalar `ConfigurationManager.AppSettings` ad alanındaki koleksiyonu kullanarak bu ayarları okur `System.Configuration` :</span><span class="sxs-lookup"><span data-stu-id="719c9-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="719c9-158">ASP.NET Core, uygulama için yapılandırma verilerini herhangi bir dosyada depolayıp ara yazılım önyükleme 'nin bir parçası olarak yükleyebilir.</span><span class="sxs-lookup"><span data-stu-id="719c9-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="719c9-159">Proje şablonlarında kullanılan varsayılan dosya *appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="719c9-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="719c9-160">Bu dosyayı uygulamanızın içindeki bir örneğine yüklemek `IConfiguration` *Startup.cs*içinde yapılır:</span><span class="sxs-lookup"><span data-stu-id="719c9-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="719c9-161">Uygulama, `Configuration` ayarları almak için öğesinden okur:</span><span class="sxs-lookup"><span data-stu-id="719c9-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="719c9-162">Bu şekilde, bu değerlere sahip bir hizmeti yüklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) kullanma gibi işlemleri daha sağlam hale getirmek için bu yaklaşımın uzantıları vardır.</span><span class="sxs-lookup"><span data-stu-id="719c9-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="719c9-163">Dı yaklaşımı, kesin türü belirtilmiş bir yapılandırma nesneleri kümesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="719c9-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="719c9-164">ASP.NET Core yapılandırmaya yönelik daha ayrıntılı bir başvuru için bkz. [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="719c9-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="719c9-165">Yerel bağımlılığı ekleme</span><span class="sxs-lookup"><span data-stu-id="719c9-165">Native dependency injection</span></span>

<span data-ttu-id="719c9-166">Büyük, ölçeklenebilir uygulamalar, bileşenlerin ve hizmetlerin gevşek bağlantısı olan önemli bir hedef.</span><span class="sxs-lookup"><span data-stu-id="719c9-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="719c9-167">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) , bunu elde etmek için popüler bir tekniktir ve ASP.NET Core yerel bir bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="719c9-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="719c9-168">ASP.NET uygulamalarında, geliştiriciler bağımlılık ekleme işlemini uygulamak için bir üçüncü taraf kitaplığı kullanır.</span><span class="sxs-lookup"><span data-stu-id="719c9-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="719c9-169">Bu tür bir kitaplık [, Microsoft](https://github.com/unitycontainer/unity)düzenleri & uygulamalar tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="719c9-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="719c9-170">Unity ile bağımlılık ekleme ayarlamayı bir örnek şunu `IDependencyResolver` sarmalayan bir örnektir `UnityContainer` :</span><span class="sxs-lookup"><span data-stu-id="719c9-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="719c9-171">Bir örneği oluşturun `UnityContainer` , hizmetinizi kaydedin ve bağımlılık çözümleyicisini `HttpConfiguration` kapsayıcının yeni örneğine ayarlayın `UnityResolver` :</span><span class="sxs-lookup"><span data-stu-id="719c9-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="719c9-172">`IProductRepository`Gerektiğinde ekle:</span><span class="sxs-lookup"><span data-stu-id="719c9-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="719c9-173">Bağımlılık ekleme ASP.NET Core bir parçası olduğu için hizmetinizi `ConfigureServices` *Startup.cs*yöntemine ekleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="719c9-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="719c9-174">Depo, Unity ile doğru olduğu için her yerde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="719c9-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="719c9-175">Bağımlılık ekleme hakkında daha fazla bilgi için bkz. [bağımlılık ekleme](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="719c9-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="719c9-176">Statik dosyaları sunma</span><span class="sxs-lookup"><span data-stu-id="719c9-176">Serve static files</span></span>

<span data-ttu-id="719c9-177">Web geliştirmenin önemli bir bölümü, statik, istemci tarafı varlıkları sunma olanağıdır.</span><span class="sxs-lookup"><span data-stu-id="719c9-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="719c9-178">Statik dosyaların en yaygın örnekleri HTML, CSS, JavaScript ve görüntülerdir.</span><span class="sxs-lookup"><span data-stu-id="719c9-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="719c9-179">Bu dosyaların, uygulamanın yayınlanan konumuna (veya CDN) kaydedilmesi gerekir ve bu dosyalar bir istek tarafından yüklenebilmeleri için başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="719c9-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="719c9-180">Bu işlem ASP.NET Core değişti.</span><span class="sxs-lookup"><span data-stu-id="719c9-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="719c9-181">ASP.NET ' de statik dosyalar çeşitli dizinlerde depolanır ve görünümlerde başvurulur.</span><span class="sxs-lookup"><span data-stu-id="719c9-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="719c9-182">ASP.NET Core, statik dosyalar, aksi belirtilmedikçe "Web kökünde" (\* &lt; içerik kökü &gt; /Wwwroot\*) içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="719c9-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="719c9-183">Dosyalar, şuradan genişletme yöntemi çağrılarak istek ardışık düzenine yüklenir `UseStaticFiles` `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="719c9-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="719c9-184">.NET Framework hedefliyorsanız, NuGet paketini yükler `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="719c9-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="719c9-185">Örneğin, *Wwwroot/görüntüler* klasöründeki bir görüntü varlığı, tarayıcı tarafından gibi bir konumda erişilebilir `http://<app>/images/<imageFileName>` .</span><span class="sxs-lookup"><span data-stu-id="719c9-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="719c9-186">ASP.NET Core içinde statik dosyalar sunma konusunda daha ayrıntılı bir başvuru için bkz. [statik dosyalar](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="719c9-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-no-loccookies"></a><span data-ttu-id="719c9-187">Çoklu değer cookie s</span><span class="sxs-lookup"><span data-stu-id="719c9-187">Multi-value cookies</span></span>

<span data-ttu-id="719c9-188">[Birden çok değerli cookie s](xref:System.Web.HttpCookie.Values) ASP.NET Core desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="719c9-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="719c9-189">Değer başına bir tane oluşturun cookie .</span><span class="sxs-lookup"><span data-stu-id="719c9-189">Create one cookie per value.</span></span>

## <a name="partial-app-migration"></a><span data-ttu-id="719c9-190">Kısmi uygulama geçişi</span><span class="sxs-lookup"><span data-stu-id="719c9-190">Partial app migration</span></span>

<span data-ttu-id="719c9-191">Kısmi uygulama geçişine yönelik bir yaklaşım, bir IIS alt uygulaması oluşturmaktır ve yalnızca ASP.NET 4. x adresinden ASP.NET Core, uygulamanın URL yapısını korurken yalnızca belirli yolları.</span><span class="sxs-lookup"><span data-stu-id="719c9-191">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="719c9-192">Örneğin, *applicationHost.config* DOSYASıNDAN uygulamanın URL yapısını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="719c9-192">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

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

<span data-ttu-id="719c9-193">Dizin yapısı:</span><span class="sxs-lookup"><span data-stu-id="719c9-193">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a><span data-ttu-id="719c9-194">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="719c9-194">Additional resources</span></span>

- [<span data-ttu-id="719c9-195">Kitaplıkları .NET Core 'a taşıma</span><span class="sxs-lookup"><span data-stu-id="719c9-195">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
