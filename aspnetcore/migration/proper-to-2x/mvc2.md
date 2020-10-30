---
title: ASP.NET 'den ASP.NET Core 2,0 'ye geçiş
author: isaac2004
description: Mevcut ASP.NET MVC veya Web API uygulamalarını ASP.NET Core 2,0 ' ye geçirmeye yönelik rehberlik alın.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: migration/mvc2
ms.openlocfilehash: cf7d2e3a94c14fb752180d9349536d17b4557e0a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051336"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="9b015-103">ASP.NET 'den ASP.NET Core 2,0 'ye geçiş</span><span class="sxs-lookup"><span data-stu-id="9b015-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="9b015-104">İle [Isaac Levi](https://isaaclevin.com) tarafından</span><span class="sxs-lookup"><span data-stu-id="9b015-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="9b015-105">Bu makale, ASP.NET uygulamalarını ASP.NET Core 2,0 ' ye geçirmeye yönelik bir başvuru kılavuzu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="9b015-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b015-106">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="9b015-106">Prerequisites</span></span>

<span data-ttu-id="9b015-107">.Net downloads 'lerden **aşağıdakilerden birini** yükler [: Windows](https://dotnet.microsoft.com/download):</span><span class="sxs-lookup"><span data-stu-id="9b015-107">Install **one** of the following from [.NET Downloads: Windows](https://dotnet.microsoft.com/download):</span></span>

* <span data-ttu-id="9b015-108">.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="9b015-108">.NET Core SDK</span></span>
* <span data-ttu-id="9b015-109">Windows için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b015-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="9b015-110">**ASP.net ve Web geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="9b015-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="9b015-111">**.NET Core platformlar arası geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="9b015-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="9b015-112">Hedef çerçeveler</span><span class="sxs-lookup"><span data-stu-id="9b015-112">Target frameworks</span></span>

<span data-ttu-id="9b015-113">ASP.NET Core 2,0 projeleri, geliştiricilere .NET Core, .NET Framework veya her ikisinin de hedeflenme esnekliği sunar.</span><span class="sxs-lookup"><span data-stu-id="9b015-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="9b015-114">En uygun hedef Framework 'ü belirlemek için, bkz. [sunucu uygulamaları için .NET Core ve .NET Framework arasından seçim yapma](/dotnet/standard/choosing-core-framework-server) .</span><span class="sxs-lookup"><span data-stu-id="9b015-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="9b015-115">.NET Framework hedeflenirken, projelerin ayrı NuGet paketlerine başvurması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9b015-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="9b015-116">.NET Core 'u hedeflemek, ASP.NET Core 2,0 [metapackage](xref:fundamentals/metapackage)sayesinde çok sayıda açık paket başvurularını ortadan kaldırmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="9b015-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="9b015-117">`Microsoft.AspNetCore.All`Meta paketini projenize yükler:</span><span class="sxs-lookup"><span data-stu-id="9b015-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="9b015-118">Metapackage kullanıldığında, metapackage içinde başvurulan hiçbir paket uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="9b015-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="9b015-119">.NET Core çalışma zamanı deposu bu varlıkları içerir ve performansı artırmak için önceden ön derlenmiş hale getiriyoruz.</span><span class="sxs-lookup"><span data-stu-id="9b015-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="9b015-120"><xref:fundamentals/metapackage>Daha fazla ayrıntı için bkz..</span><span class="sxs-lookup"><span data-stu-id="9b015-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="9b015-121">Proje yapısı farkları</span><span class="sxs-lookup"><span data-stu-id="9b015-121">Project structure differences</span></span>

<span data-ttu-id="9b015-122">*. Csproj* dosya biçimi ASP.NET Core basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="9b015-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="9b015-123">Bazı önemli değişiklikler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="9b015-123">Some notable changes include:</span></span>

* <span data-ttu-id="9b015-124">Dosyaların açıkça eklenmesi, projenin bir parçası olarak kabul edilmesi için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="9b015-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="9b015-125">Bu, büyük ekipler üzerinde çalışırken XML birleştirme çakışmalarının riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="9b015-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="9b015-126">Diğer projelere GUID tabanlı başvurular yoktur ve bu da dosya okunabilirliğini geliştirir.</span><span class="sxs-lookup"><span data-stu-id="9b015-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="9b015-127">Dosya Visual Studio 'da kaldırmadan düzenlenebilir:</span><span class="sxs-lookup"><span data-stu-id="9b015-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Visual Studio 2017 ' de CSPROJ bağlam menüsü seçeneğini Düzenle](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="9b015-129">Global. asax dosyası değiştirme</span><span class="sxs-lookup"><span data-stu-id="9b015-129">Global.asax file replacement</span></span>

<span data-ttu-id="9b015-130">ASP.NET Core, bir uygulamayı önyüklemeden yeni bir mekanizma getirmiştir.</span><span class="sxs-lookup"><span data-stu-id="9b015-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="9b015-131">ASP.NET uygulamaları için giriş noktası *Global. asax* dosyasıdır.</span><span class="sxs-lookup"><span data-stu-id="9b015-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="9b015-132">Rota yapılandırması ve filtre ve alan kayıtları gibi görevler, *Global. asax* dosyasında işlenir.</span><span class="sxs-lookup"><span data-stu-id="9b015-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="9b015-133">Bu yaklaşım, uygulamayı ve dağıtıldığı sunucuyu uygulamayı kesintiye uğratan bir şekilde bağar.</span><span class="sxs-lookup"><span data-stu-id="9b015-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="9b015-134">Bağımsız olarak, [Owin](https://owin.org/) , birden çok çerçeveyi birlikte kullanmanın bir temizleyici yolunu sağlamak için sunulmuştur.</span><span class="sxs-lookup"><span data-stu-id="9b015-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="9b015-135">OWIN yalnızca gereken modülleri eklemek için bir işlem hattı sağlar.</span><span class="sxs-lookup"><span data-stu-id="9b015-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="9b015-136">Barındırma ortamı, hizmetleri ve uygulamanın istek ardışık düzenini yapılandırmak için bir [Başlangıç](xref:fundamentals/startup) işlevi alır.</span><span class="sxs-lookup"><span data-stu-id="9b015-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="9b015-137">`Startup` uygulamayla bir ara yazılım kümesini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="9b015-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="9b015-138">Her istek için, uygulama bir ara yazılım bileşeninin her birini bağlantılı listenin baş işaretçisi ile mevcut bir işleyici kümesine çağırır.</span><span class="sxs-lookup"><span data-stu-id="9b015-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="9b015-139">Her bir ara yazılım bileşeni, istek işleme ardışık düzenine bir veya daha fazla işleyici ekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="9b015-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="9b015-140">Bu, listenin yeni başlığı olan işleyiciye bir başvuru döndürülerek gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9b015-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="9b015-141">Her işleyici, listedeki bir sonraki işleyiciyi hatırlayıp çağırmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="9b015-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="9b015-142">ASP.NET Core, bir uygulamaya giriş noktası olur `Startup` ve artık *Global. asax* ' a bağımlılığı yoktur.</span><span class="sxs-lookup"><span data-stu-id="9b015-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax* .</span></span> <span data-ttu-id="9b015-143">.NET Framework ile OWIN kullanırken, işlem hattı olarak aşağıdaki gibi bir şey kullanın:</span><span class="sxs-lookup"><span data-stu-id="9b015-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="9b015-144">Bu, varsayılan rotalarınızı yapılandırır ve varsayılan olarak JSON üzerinden XmlSerialization olur.</span><span class="sxs-lookup"><span data-stu-id="9b015-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="9b015-145">Gerektiğinde bu işlem hattına başka bir ara yazılım ekleyin (Yükleme Hizmetleri, yapılandırma ayarları, statik dosyalar vb.).</span><span class="sxs-lookup"><span data-stu-id="9b015-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="9b015-146">ASP.NET Core benzer bir yaklaşım kullanır, ancak girişi işlemek için OWıN 'u kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="9b015-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="9b015-147">Bunun yerine, Program.cs yöntemi aracılığıyla yapılır *Program.cs* `Main` (konsol uygulamalarına benzer) ve bunlar `Startup` üzerinden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="9b015-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="9b015-148">`Startup` bir yöntem içermelidir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="9b015-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="9b015-149">' De `Configure` , gerekli ara yazılımı ardışık düzene ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9b015-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="9b015-150">Aşağıdaki örnekte (varsayılan Web sitesi şablonundan), işlem hattını desteğiyle yapılandırmak için birkaç uzantı yöntemi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="9b015-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="9b015-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="9b015-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="9b015-152">Hata sayfaları</span><span class="sxs-lookup"><span data-stu-id="9b015-152">Error pages</span></span>
* <span data-ttu-id="9b015-153">Statik dosyalar</span><span class="sxs-lookup"><span data-stu-id="9b015-153">Static files</span></span>
* <span data-ttu-id="9b015-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="9b015-154">ASP.NET Core MVC</span></span>
* :::no-loc(Identity):::

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="9b015-155">Ana bilgisayar ve uygulama, gelecekte farklı bir platforma geçme esnekliği sağlayan bir şekilde ayrılmış.</span><span class="sxs-lookup"><span data-stu-id="9b015-155">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="9b015-156">ASP.NET Core başlangıç ve ara yazılım için daha ayrıntılı bir başvuru için bkz <xref:fundamentals/startup> ..</span><span class="sxs-lookup"><span data-stu-id="9b015-156">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="9b015-157">Yapılandırma depolama</span><span class="sxs-lookup"><span data-stu-id="9b015-157">Storing configurations</span></span>

<span data-ttu-id="9b015-158">ASP.NET, ayarları depolamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="9b015-158">ASP.NET supports storing settings.</span></span> <span data-ttu-id="9b015-159">Bu ayar, örneğin, uygulamaların dağıtıldığı ortamı desteklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9b015-159">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="9b015-160">Ortak bir uygulama, tüm özel anahtar-değer çiftlerini `<appSettings>` *Web.config* dosyasının bölümünde depolandı:</span><span class="sxs-lookup"><span data-stu-id="9b015-160">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="9b015-161">Uygulamalar `ConfigurationManager.AppSettings` ad alanındaki koleksiyonu kullanarak bu ayarları okur `System.Configuration` :</span><span class="sxs-lookup"><span data-stu-id="9b015-161">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="9b015-162">ASP.NET Core, uygulama için yapılandırma verilerini herhangi bir dosyada depolayıp ara yazılım önyükleme 'nin bir parçası olarak yükleyebilir.</span><span class="sxs-lookup"><span data-stu-id="9b015-162">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="9b015-163">Proje şablonlarında kullanılan varsayılan dosya *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="9b015-163">The default file used in the project templates is *:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="9b015-164">Bu dosyayı uygulamanızın içindeki bir örneğine yüklemek `IConfiguration` *Startup.cs* içinde yapılır:</span><span class="sxs-lookup"><span data-stu-id="9b015-164">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs* :</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="9b015-165">Uygulama, `Configuration` ayarları almak için öğesinden okur:</span><span class="sxs-lookup"><span data-stu-id="9b015-165">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="9b015-166">Bu şekilde, bu değerlere sahip bir hizmeti yüklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) kullanma gibi işlemleri daha sağlam hale getirmek için bu yaklaşımın uzantıları vardır.</span><span class="sxs-lookup"><span data-stu-id="9b015-166">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="9b015-167">Dı yaklaşımı, kesin türü belirtilmiş bir yapılandırma nesneleri kümesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="9b015-167">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="9b015-168">**Note:** ASP.NET Core yapılandırmaya yönelik daha ayrıntılı bir başvuru için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="9b015-168">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="9b015-169">Yerel bağımlılığı ekleme</span><span class="sxs-lookup"><span data-stu-id="9b015-169">Native dependency injection</span></span>

<span data-ttu-id="9b015-170">Büyük, ölçeklenebilir uygulamalar, bileşenlerin ve hizmetlerin gevşek bağlantısı olan önemli bir hedef.</span><span class="sxs-lookup"><span data-stu-id="9b015-170">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="9b015-171">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) , bunu elde etmek için popüler bir tekniktir ve ASP.NET Core yerel bir bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="9b015-171">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="9b015-172">ASP.NET uygulamalarında, geliştiriciler bağımlılık ekleme işlemini uygulamak için bir üçüncü taraf kitaplığı kullanır.</span><span class="sxs-lookup"><span data-stu-id="9b015-172">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="9b015-173">Bu tür bir kitaplık [, Microsoft](https://github.com/unitycontainer/unity)düzenleri & uygulamalar tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9b015-173">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="9b015-174">Unity ile bağımlılık ekleme ayarlamayı bir örnek şunu `IDependencyResolver` sarmalayan bir örnektir `UnityContainer` :</span><span class="sxs-lookup"><span data-stu-id="9b015-174">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="9b015-175">Bir örneği oluşturun `UnityContainer` , hizmetinizi kaydedin ve bağımlılık çözümleyicisini `HttpConfiguration` kapsayıcının yeni örneğine ayarlayın `UnityResolver` :</span><span class="sxs-lookup"><span data-stu-id="9b015-175">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="9b015-176">`IProductRepository`Gerektiğinde ekle:</span><span class="sxs-lookup"><span data-stu-id="9b015-176">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="9b015-177">Bağımlılık ekleme ASP.NET Core bir parçası olduğundan, hizmetinizi şu şekilde ekleyebilirsiniz `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9b015-177">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="9b015-178">Depo, Unity ile doğru olduğu için her yerde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="9b015-178">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="9b015-179">ASP.NET Core bağımlılık ekleme hakkında daha fazla bilgi için bkz <xref:fundamentals/dependency-injection> ..</span><span class="sxs-lookup"><span data-stu-id="9b015-179">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="9b015-180">Statik dosyaları sunma</span><span class="sxs-lookup"><span data-stu-id="9b015-180">Serving static files</span></span>

<span data-ttu-id="9b015-181">Web geliştirmenin önemli bir bölümü, statik, istemci tarafı varlıkları sunma olanağıdır.</span><span class="sxs-lookup"><span data-stu-id="9b015-181">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="9b015-182">Statik dosyaların en yaygın örnekleri HTML, CSS, JavaScript ve görüntülerdir.</span><span class="sxs-lookup"><span data-stu-id="9b015-182">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="9b015-183">Bu dosyaların, uygulamanın yayınlanan konumuna (veya CDN) kaydedilmesi gerekir ve bu dosyalar bir istek tarafından yüklenebilmeleri için başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9b015-183">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="9b015-184">Bu işlem ASP.NET Core değişti.</span><span class="sxs-lookup"><span data-stu-id="9b015-184">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="9b015-185">ASP.NET ' de statik dosyalar çeşitli dizinlerde depolanır ve görünümlerde başvurulur.</span><span class="sxs-lookup"><span data-stu-id="9b015-185">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="9b015-186">ASP.NET Core, statik dosyalar, aksi belirtilmedikçe "Web kökünde" ( *&lt; içerik kökü &gt; /Wwwroot* ) içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="9b015-186">In ASP.NET Core, static files are stored in the "web root" ( *&lt;content root&gt;/wwwroot* ), unless configured otherwise.</span></span> <span data-ttu-id="9b015-187">Dosyalar, şuradan genişletme yöntemi çağrılarak istek ardışık düzenine yüklenir `UseStaticFiles` `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9b015-187">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="9b015-188">**Note:** .NET Framework hedefliyorsanız, NuGet paketini yükler `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="9b015-188">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="9b015-189">Örneğin, *Wwwroot/görüntüler* klasöründeki bir görüntü varlığı, tarayıcı tarafından gibi bir konumda erişilebilir `http://<app>/images/<imageFileName>` .</span><span class="sxs-lookup"><span data-stu-id="9b015-189">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="9b015-190">**Note:** ASP.NET Core içinde statik dosyaları sunmaya yönelik daha ayrıntılı bir başvuru için bkz <xref:fundamentals/static-files> ..</span><span class="sxs-lookup"><span data-stu-id="9b015-190">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b015-191">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9b015-191">Additional resources</span></span>

* [<span data-ttu-id="9b015-192">Kitaplıkları .NET Core 'a taşıma</span><span class="sxs-lookup"><span data-stu-id="9b015-192">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
