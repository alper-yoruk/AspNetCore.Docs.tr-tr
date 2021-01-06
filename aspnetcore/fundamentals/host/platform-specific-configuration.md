---
title: ASP.NET Core 'de barındırma başlangıç derlemelerini kullanın
author: rick-anderson
description: Bir ASP.NET Core uygulamasının bir dış derlemeden bir ıhostingstartup uygulaması kullanarak nasıl geliştirileceğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: c12487875db69472ee328dfc7a611ee99974c770
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061060"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="02dd5-103">ASP.NET Core 'de barındırma başlangıç derlemelerini kullanın</span><span class="sxs-lookup"><span data-stu-id="02dd5-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="02dd5-104">[Palete kroni](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="02dd5-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02dd5-105"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(Barındırma başlatma) uygulaması, bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler ekler.</span><span class="sxs-lookup"><span data-stu-id="02dd5-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="02dd5-106">Örneğin, bir dış kitaplık, bir uygulamaya ek yapılandırma sağlayıcıları veya hizmetler sağlamak için barındırma başlangıç uygulamasını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="02dd5-107">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02dd5-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="02dd5-108">HostingStartup özniteliği</span><span class="sxs-lookup"><span data-stu-id="02dd5-108">HostingStartup attribute</span></span>

<span data-ttu-id="02dd5-109">[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirilecek bir barındırma başlangıç derlemesinin varlığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="02dd5-110">Giriş derlemesi veya sınıfı içeren derleme `Startup` öznitelik için otomatik olarak taranır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="02dd5-111">Öznitelikleri aramak için derlemelerin listesi `HostingStartup` [Webhostdefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)içindeki yapılandırmadan çalışma zamanında yüklenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="02dd5-112">Bulmadan dışlanacak derlemelerin listesi [Webhostdefaults. Hostingstartupexcludederlemelieskey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)öğesinden yüklendi.</span><span class="sxs-lookup"><span data-stu-id="02dd5-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="02dd5-113">Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad alanı `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="02dd5-114">Barındırma başlangıç kodunu içeren sınıf `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="02dd5-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="02dd5-115">`HostingStartup`Özniteliği genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında bulunur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="02dd5-116">Yüklü barındırma başlangıç derlemelerini bul</span><span class="sxs-lookup"><span data-stu-id="02dd5-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="02dd5-117">Yüklü barındırma başlangıç derlemelerini öğrenmek için, günlüğü etkinleştirin ve uygulamanın günlüklerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="02dd5-118">Derlemeler yüklenirken oluşan hatalar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="02dd5-119">Yüklenen barındırma başlangıç derlemeleri hata ayıklama düzeyinde günlüğe kaydedilir ve tüm hatalar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="02dd5-120">Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="02dd5-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="02dd5-121">Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırakmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="02dd5-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="02dd5-122">Tüm barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini veya olarak ayarlayın `true` `1` :</span><span class="sxs-lookup"><span data-stu-id="02dd5-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="02dd5-123">Barındırma başlangıç ana bilgisayar yapılandırma ayarını önle:</span><span class="sxs-lookup"><span data-stu-id="02dd5-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="02dd5-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="02dd5-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="02dd5-125">Belirli barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini, başlangıçta dışlamak üzere, bir barındırma başlangıç bütünleştirilmiş kodlarının noktalı virgülle ayrılmış dizesine ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="02dd5-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="02dd5-126">Barındırma başlatma derlemeleri dışlama ana bilgisayar yapılandırma ayarı:</span><span class="sxs-lookup"><span data-stu-id="02dd5-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="02dd5-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="02dd5-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="02dd5-128">Ana bilgisayar yapılandırma ayarı ve ortam değişkeni ayarlanırsa, konak ayarı davranışı denetler.</span><span class="sxs-lookup"><span data-stu-id="02dd5-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="02dd5-129">Konak ayarını veya ortam değişkenini kullanarak başlatma derlemelerinin barındırılmasını devre dışı bırakmak, derlemeyi küresel olarak devre dışı bırakabilir ve bir uygulamanın çeşitli özelliklerini devre dışı bırakabilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="02dd5-130">Project</span><span class="sxs-lookup"><span data-stu-id="02dd5-130">Project</span></span>

<span data-ttu-id="02dd5-131">Aşağıdaki proje türlerinden birini kullanarak bir barındırma başlatması oluşturun:</span><span class="sxs-lookup"><span data-stu-id="02dd5-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="02dd5-132">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="02dd5-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="02dd5-133">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="02dd5-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="02dd5-134">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="02dd5-134">Class library</span></span>

<span data-ttu-id="02dd5-135">Bir barındırma başlatma geliştirmesi, bir sınıf kitaplığında bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="02dd5-136">Kitaplık bir özniteliği içerir `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="02dd5-137">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) , bir Razor Pages uygulaması, *Hostingstartupapp* ve bir sınıf kitaplığı, *hostingstartuplibrary* içerir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="02dd5-138">Sınıf kitaplığı:</span><span class="sxs-lookup"><span data-stu-id="02dd5-138">The class library:</span></span>

* <span data-ttu-id="02dd5-139">Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="02dd5-140">`ServiceKeyInjection` bellek içi yapılandırma sağlayıcısını ([Addınmemorycollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) kullanarak uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.</span><span class="sxs-lookup"><span data-stu-id="02dd5-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="02dd5-141">`HostingStartup`Barındırma başlatmasının ad alanını ve sınıfını tanımlayan bir özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="02dd5-142">`ServiceKeyInjection`Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="02dd5-143">*Hostingstartuplibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="02dd5-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="02dd5-144">Uygulamanın dizin sayfası, sınıf kitaplığının barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="02dd5-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="02dd5-145">*Hostingstartupapp/Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="02dd5-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="02dd5-146">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ayrıca ayrı bir barındırma başlatma, *Hostingstartuppackage* sağlayan bir NuGet paket projesi içerir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="02dd5-147">Paket, daha önce açıklanan sınıf kitaplığıyla aynı özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="02dd5-148">Paket:</span><span class="sxs-lookup"><span data-stu-id="02dd5-148">The package:</span></span>

* <span data-ttu-id="02dd5-149">Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="02dd5-150">`ServiceKeyInjection` uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.</span><span class="sxs-lookup"><span data-stu-id="02dd5-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="02dd5-151">Bir `HostingStartup` özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="02dd5-152">*Hostingstartuppackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="02dd5-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="02dd5-153">Uygulamanın dizin sayfası, paketin barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="02dd5-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="02dd5-154">*Hostingstartupapp/Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="02dd5-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="02dd5-155">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="02dd5-155">Console app without an entry point</span></span>

<span data-ttu-id="02dd5-156">*Bu yaklaşım, .NET Framework değil yalnızca .NET Core uygulamaları için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="02dd5-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="02dd5-157">Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik barındırma başlatma geliştirmesi, bir öznitelik içeren giriş noktası olmadan bir konsol uygulamasında sağlanabilmesi `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="02dd5-158">Konsol uygulamasını yayımlamak, çalışma zamanı deposundan tüketilebilen bir barındırma başlangıç derlemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="02dd5-159">Bu işlemde giriş noktası olmayan bir konsol uygulaması kullanılmıştır çünkü:</span><span class="sxs-lookup"><span data-stu-id="02dd5-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="02dd5-160">Barındırma başlangıç derlemesinde barındırma başlangıcını kullanmak için bir bağımlılıklar dosyası gereklidir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="02dd5-161">Bağımlılıklar dosyası, bir kitaplık değil bir uygulama yayımlamayla üretilen çalıştırılabilir bir uygulama varlıktır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="02dd5-162">Bir kitaplık, paylaşılan çalışma zamanını hedefleyen bir çalıştırılabilir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)eklenemez.</span><span class="sxs-lookup"><span data-stu-id="02dd5-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="02dd5-163">Dinamik barındırma başlatma oluşturma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="02dd5-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="02dd5-164">Bir barındırma başlangıç derlemesi, konsol uygulamasından bir giriş noktası olmadan oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="02dd5-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="02dd5-165">, Uygulamayı içeren bir sınıf içerir `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="02dd5-166">Uygulama sınıfını tanımlamak için bir [Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="02dd5-167">Konsol uygulaması, barındırma başlatmasının bağımlılıklarını almak için yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="02dd5-168">Konsol uygulamasını yayımlamanın bir sonucu, kullanılmayan bağımlılıkların bağımlılıklar dosyasından kırpıllarıdır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="02dd5-169">Bağımlılıklar dosyası, barındırma başlangıç derlemesinin çalışma zamanı konumunu ayarlamak için değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="02dd5-170">Barındırma başlangıç derlemesi ve onun bağımlılıklar dosyası çalışma zamanı paket deposuna yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="02dd5-171">Barındırma başlangıç derlemesini ve onun bağımlılıklar dosyasını öğrenmek için, ortam değişkenleri çiftinde listelenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="02dd5-172">Konsol uygulaması [Microsoft. AspNetCore. Hosting. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:</span><span class="sxs-lookup"><span data-stu-id="02dd5-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="02dd5-173">[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfını `IHostingStartup` oluştururken yükleme ve yürütme için uygulamasının bir uygulamasını tanımlar <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="02dd5-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="02dd5-174">Aşağıdaki örnekte, ad alanı `StartupEnhancement` ve sınıfı `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="02dd5-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="02dd5-175">Bir sınıf uygular `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="02dd5-176">Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="02dd5-177">`IHostingStartup.Configure` barındırma başlangıç derlemesinde kullanıcı kodundan önce çalışma zamanı tarafından çağrılır `Startup.Configure` , bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından verilen yapılandırmanın üzerine yazılmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="02dd5-178">Bir proje oluştururken `IHostingStartup` , bağımlılıklar dosyası (*.deps.js*), `runtime` derlemenin konumunu *bin* klasörüne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="02dd5-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="02dd5-179">Yalnızca dosyanın bir kısmı gösterilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-179">Only part of the file is shown.</span></span> <span data-ttu-id="02dd5-180">Örnekteki derleme adı `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="02dd5-181">Barındırma başlatma tarafından belirtilen yapılandırma</span><span class="sxs-lookup"><span data-stu-id="02dd5-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="02dd5-182">Yapılandırma işlemi, barındırma başlatmasının yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak iki yaklaşımdan yararlanabilir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="02dd5-183"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>Uygulamanın temsilcileri çalıştırıldıktan sonra yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="02dd5-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="02dd5-184">Barındırma başlangıç yapılandırması, uygulamanın yapılandırmasına bu yaklaşımı kullanarak öncelik kazanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="02dd5-185"><xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>Uygulamanın temsilcileri yürütmeden önce yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="02dd5-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="02dd5-186">Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="02dd5-187">Barındırma başlangıç derlemesini belirtin</span><span class="sxs-lookup"><span data-stu-id="02dd5-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="02dd5-188">Bir sınıf kitaplığı ya da konsol uygulaması tarafından sağlanan bir barındırma başlatması için, ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="02dd5-189">Ortam değişkeni, derlemelerin noktalı virgülle ayrılmış listesidir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="02dd5-190">Özniteliği için yalnızca barındırma başlangıç derlemeleri taranır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="02dd5-191">Daha önce açıklanan barındırma başlangıç pencerelerini öğrenmek için *Hostingstartupapp* örnek uygulaması için, ortam değişkeni aşağıdaki değere ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="02dd5-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="02dd5-192">Barındırma başlangıç bütünleştirilmiş kodları, barındırma başlangıç derlemeleri ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="02dd5-193">Birden çok barındırma başlatması varsa, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemleri derlemelerin listelendiği sırada yürütülür.</span><span class="sxs-lookup"><span data-stu-id="02dd5-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="02dd5-194">Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="02dd5-194">Activation</span></span>

<span data-ttu-id="02dd5-195">Başlatma başlangıç etkinleştirme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="02dd5-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="02dd5-196">[Çalışma zamanı deposu](#runtime-store): etkinleştirme etkinleştirme için derleme zamanı başvurusu gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="02dd5-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="02dd5-197">Örnek uygulama, çok makineli bir ortamda barındırma başlatmasının dağıtımını kolaylaştırmak için barındırma başlangıç derlemesini ve bağımlılıklar dosyalarını bir klasöre, *dağıtımına* koyar.</span><span class="sxs-lookup"><span data-stu-id="02dd5-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="02dd5-198">*Dağıtım* klasörü Ayrıca, barındırma başlangıcını etkinleştirmek için dağıtım sistemindeki ortam değişkenlerini oluşturan veya değiştiren bir PowerShell betiği içerir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="02dd5-199">Etkinleştirme için derleme zamanı başvurusu gerekiyor</span><span class="sxs-lookup"><span data-stu-id="02dd5-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="02dd5-200">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="02dd5-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="02dd5-201">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="02dd5-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="02dd5-202">Çalışma zamanı deposu</span><span class="sxs-lookup"><span data-stu-id="02dd5-202">Runtime store</span></span>

<span data-ttu-id="02dd5-203">Barındırma başlangıç uygulamasının [çalışma zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilmesi.</span><span class="sxs-lookup"><span data-stu-id="02dd5-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="02dd5-204">Derleme zamanı başvurusu, Gelişmiş uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="02dd5-205">Barındırma başlatma oluşturulduktan sonra, bildirim proje dosyası ve [DotNet Store](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir çalışma zamanı deposu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="02dd5-206">Örnek uygulamada (*Runtimesstore* Projesi) aşağıdaki komut kullanılır:</span><span class="sxs-lookup"><span data-stu-id="02dd5-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="02dd5-207">Çalışma zamanının çalışma zamanı deposunu bulması için, çalışma zamanı deposunun konumu `DOTNET_SHARED_STORE` ortam değişkenine eklenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="02dd5-208">**Barındırma başlatmasının bağımlılıklar dosyasını değiştirme ve yerleştirme**</span><span class="sxs-lookup"><span data-stu-id="02dd5-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="02dd5-209">Geliştirmede bir paket başvurusu olmadan geliştirmeyi etkinleştirmek için, ile çalışma zamanına ek bağımlılıklar belirtin `additionalDeps` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="02dd5-210">`additionalDeps` şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="02dd5-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="02dd5-211">Dosya başlangıcında uygulamanın kendi *.deps.js* birlikte birleştirilecek dosyalara bir dizi ek *.deps.js* sağlayarak uygulamanın kitaplık grafiğini genişletin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="02dd5-212">Barındırma başlangıç derlemesini bulunabilir ve yüklenebilir hale getirin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="02dd5-213">Ek bağımlılıklar dosyası oluşturmak için önerilen yaklaşım şunlardır:</span><span class="sxs-lookup"><span data-stu-id="02dd5-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="02dd5-214">`dotnet publish`Önceki bölümde başvurulan çalışma zamanı deposu bildirim dosyasında yürütün.</span><span class="sxs-lookup"><span data-stu-id="02dd5-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="02dd5-215">Bildirimlerin bildirim başvurusunu ve `runtime` sonuçta elde edilen *.deps.js* dosyanın bölümünü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="02dd5-216">Örnek projede, `store.manifest/1.0.0` özelliği `targets` ve `libraries` bölümünden kaldırılır:</span><span class="sxs-lookup"><span data-stu-id="02dd5-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="02dd5-217">*.deps.js* dosyasını aşağıdaki konuma yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="02dd5-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="02dd5-218">`{ADDITIONAL DEPENDENCIES PATH}`: Ortam değişkenine konum eklendi `DOTNET_ADDITIONAL_DEPS` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="02dd5-219">`{SHARED FRAMEWORK NAME}`: Bu ek bağımlılıklar dosyası için paylaşılan çerçeve gereklidir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="02dd5-220">`{SHARED FRAMEWORK VERSION}`: En düşük paylaşılan çerçeve sürümü.</span><span class="sxs-lookup"><span data-stu-id="02dd5-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="02dd5-221">`{ENHANCEMENT ASSEMBLY NAME}`: Geliştirmesinin derleme adı.</span><span class="sxs-lookup"><span data-stu-id="02dd5-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="02dd5-222">Örnek uygulamada (*Runtimesbir* proje), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="02dd5-223">Çalışma zamanı depo konumunu bulması için, ek bağımlılıklar dosya konumu `DOTNET_ADDITIONAL_DEPS` ortam değişkenine eklenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="02dd5-224">Örnek uygulamada (*Runtimesbir* proje), çalışma zamanı deposunun oluşturulması ve ek bağımlılıklar dosyası oluşturulması bir [PowerShell](/powershell/scripting/powershell-scripting) betiği kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="02dd5-225">Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanbileceğine ilişkin örnekler için, bkz. [birden çok ortam kullanma](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="02dd5-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="02dd5-226">**Dağıtım**</span><span class="sxs-lookup"><span data-stu-id="02dd5-226">**Deployment**</span></span>

<span data-ttu-id="02dd5-227">Çoklu makine ortamında bir barındırma başlatmasının dağıtımını kolaylaştırmak için, örnek uygulama, yayımlanan çıktıda şunları içeren bir *dağıtım* klasörü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="02dd5-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="02dd5-228">Barındırma başlangıç çalışma zamanı deposu.</span><span class="sxs-lookup"><span data-stu-id="02dd5-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="02dd5-229">Barındırma başlangıç bağımlılıkları dosyası.</span><span class="sxs-lookup"><span data-stu-id="02dd5-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="02dd5-230">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_SHARED_STORE` `DOTNET_ADDITIONAL_DEPS` Barındırma başlangıcını etkinleştirmeyi desteklemek için, ve ' ı oluşturan veya değiştiren bir PowerShell betiği.</span><span class="sxs-lookup"><span data-stu-id="02dd5-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="02dd5-231">Betiği dağıtım sistemindeki bir yönetim PowerShell komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="02dd5-232">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="02dd5-232">NuGet package</span></span>

<span data-ttu-id="02dd5-233">Bir NuGet paketinde barındırma başlatma geliştirmesi sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="02dd5-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="02dd5-234">Pakette bir öznitelik vardır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="02dd5-235">Paket tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="02dd5-236">Gelişmiş uygulamanın proje dosyası, uygulamanın proje dosyasında (derleme zamanı başvurusu) barındırma başlatması için bir paket başvurusu yapar.</span><span class="sxs-lookup"><span data-stu-id="02dd5-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="02dd5-237">Derleme zamanı başvurusuyla birlikte, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*.deps.js*) eklenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="02dd5-238">Bu yaklaşım, [NuGet.org](https://www.nuget.org/)'e yayınlanan bir barındırma başlangıç derleme paketi için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="02dd5-239">Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="02dd5-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="02dd5-240">NuGet paketleri ve çalışma zamanı deposu hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="02dd5-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="02dd5-241">Platformlar arası araçlarla bir NuGet paketi oluşturma</span><span class="sxs-lookup"><span data-stu-id="02dd5-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="02dd5-242">Paketler yayımlanıyor</span><span class="sxs-lookup"><span data-stu-id="02dd5-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="02dd5-243">Çalışma zamanı paket deposu</span><span class="sxs-lookup"><span data-stu-id="02dd5-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="02dd5-244">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="02dd5-244">Project bin folder</span></span>

<span data-ttu-id="02dd5-245">Bir barındırma başlatma geliştirmesi, gelişmiş uygulamada, *bin* ile dağıtılan bir derleme tarafından sağlanıyor.</span><span class="sxs-lookup"><span data-stu-id="02dd5-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="02dd5-246">Derleme tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="02dd5-247">Gelişmiş uygulamanın proje dosyası, barındırma başlatmaya bir derleme başvurusu yapar (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="02dd5-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="02dd5-248">Derleme zamanı başvurusuyla birlikte, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*.deps.js*) eklenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="02dd5-249">Bu yaklaşım, dağıtım senaryosu barındırma başlatmasının derlemesine (*. dll* dosyası) bir derleme zamanı başvurusu yapmak ve derlemeyi şu şekilde taşımak için çağırdığında geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="02dd5-250">Tüketen proje.</span><span class="sxs-lookup"><span data-stu-id="02dd5-250">The consuming project.</span></span>
  * <span data-ttu-id="02dd5-251">Tüketim Projesi tarafından erişilebilen bir konum.</span><span class="sxs-lookup"><span data-stu-id="02dd5-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="02dd5-252">Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="02dd5-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="02dd5-253">.NET Framework hedeflenirken, derleme varsayılan yükleme bağlamında yüklenebilir olur; bu, .NET Framework, derlemenin aşağıdaki konumlardan birinde bulunduğu anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="02dd5-254">Uygulama temel yolu: uygulamanın yürütülebilir dosyasının (*. exe*) bulunduğu *bin* klasörü.</span><span class="sxs-lookup"><span data-stu-id="02dd5-254">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="02dd5-255">Genel bütünleştirilmiş kod önbelleği (GAC): GAC, birkaç .NET Framework uygulamanın paylaştığı derlemeleri depolar.</span><span class="sxs-lookup"><span data-stu-id="02dd5-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="02dd5-256">Daha fazla bilgi için, bkz. [nasıl yapılır: bir derlemeyi genel derleme önbelleğine yüklemek](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) .NET Framework belgeleri.</span><span class="sxs-lookup"><span data-stu-id="02dd5-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="02dd5-257">Örnek kod</span><span class="sxs-lookup"><span data-stu-id="02dd5-257">Sample code</span></span>

<span data-ttu-id="02dd5-258">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample)), başlangıç uygulama senaryolarını barındırma gösterir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="02dd5-259">İki barındırma başlangıç derlemesi (sınıf kitaplıkları) her biri bellek içi yapılandırma anahtar-değer çiftleri çiftini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="02dd5-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="02dd5-260">NuGet paketi (*Hostingstartuppackage*)</span><span class="sxs-lookup"><span data-stu-id="02dd5-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="02dd5-261">Sınıf kitaplığı (*Hostingstartuplibrary*)</span><span class="sxs-lookup"><span data-stu-id="02dd5-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="02dd5-262">Bir barındırma başlatması, çalışma zamanı deposu tarafından dağıtılan bir derlemeden (*Startupdiagnostics*) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="02dd5-263">Derleme, üzerinde tanılama bilgileri sağlayan, başlangıçta uygulamaya iki middlewares ekler:</span><span class="sxs-lookup"><span data-stu-id="02dd5-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="02dd5-264">Kayıtlı hizmetler</span><span class="sxs-lookup"><span data-stu-id="02dd5-264">Registered services</span></span>
  * <span data-ttu-id="02dd5-265">Adres (düzen, ana bilgisayar, yol tabanı, yol, sorgu dizesi)</span><span class="sxs-lookup"><span data-stu-id="02dd5-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="02dd5-266">Bağlantı (uzak IP, uzak bağlantı noktası, yerel IP, yerel bağlantı noktası, istemci sertifikası)</span><span class="sxs-lookup"><span data-stu-id="02dd5-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="02dd5-267">İstek üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="02dd5-267">Request headers</span></span>
  * <span data-ttu-id="02dd5-268">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="02dd5-268">Environment variables</span></span>

<span data-ttu-id="02dd5-269">Örneği çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="02dd5-269">To run the sample:</span></span>

<span data-ttu-id="02dd5-270">**NuGet paketinden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="02dd5-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="02dd5-271">,, [DotNet paketi](/dotnet/core/tools/dotnet-pack) komutuyla *hostingstartuppackage* paketini derleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="02dd5-272">Paketin *Hostingstartuppackage* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="02dd5-273">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-273">Compile and run the app.</span></span> <span data-ttu-id="02dd5-274">Gelişmiş uygulamada bir paket başvurusu vardır (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="02dd5-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="02dd5-275">`<PropertyGroup>`Uygulamanın proje dosyasındaki bir paket projenin çıkışını belirtir (*.. /HostingStartupPackage/bin/Debug*) bir paket kaynağı olarak.</span><span class="sxs-lookup"><span data-stu-id="02dd5-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="02dd5-276">Bu, uygulamanın paketi [NuGet.org](https://www.nuget.org/)'e yüklemeden paketi kullanmasına izin verir. Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="02dd5-277">Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, paketin yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="02dd5-278">*Hostingstartuppackage* projesinde değişiklik yaparsanız ve yeniden derleyseniz, *Hostingstartupapp* ' ın yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="02dd5-279">Yerel NuGet önbelleklerini temizlemek için aşağıdaki [DotNet NuGet Yereller](/dotnet/core/tools/dotnet-nuget-locals) komutunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="02dd5-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="02dd5-280">**Bir sınıf kitaplığından etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="02dd5-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="02dd5-281">, [DotNet Build](/dotnet/core/tools/dotnet-build) komutuyla *hostingstartuplibrary* sınıf kitaplığını derleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="02dd5-282">Sınıf kitaplığının *Hostingstartuplibrary* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="02dd5-283">*bin*- *HostingStartupLibrary.dll* dosyasını sınıf kitaplığının derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne kopyalayarak, sınıf kitaplığının derlemesini uygulamaya dağıtın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="02dd5-284">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-284">Compile and run the app.</span></span> <span data-ttu-id="02dd5-285">`<ItemGroup>`Uygulamanın proje dosyasındaki bir, sınıf kitaplığının derlemesine (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (derleme zamanı başvurusu) başvurur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="02dd5-286">Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="02dd5-287">Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, sınıf kitaplığının yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="02dd5-288">**Çalışma zamanı deposu tarafından dağıtılan bir derlemeden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="02dd5-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="02dd5-289">*Startupdiagnostics* projesi dosyada *StartupDiagnostics.deps.js* değiştirmek için [PowerShell](/powershell/scripting/powershell-scripting) kullanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="02dd5-290">PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak varsayılan olarak yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="02dd5-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="02dd5-291">Diğer platformlarda PowerShell 'i almak için bkz. [PowerShell 'in çeşitli sürümlerini yükleme](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="02dd5-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="02dd5-292">*Runtimesyürüme* klasöründe *build.ps1* betiğini yürütün.</span><span class="sxs-lookup"><span data-stu-id="02dd5-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="02dd5-293">Betik şunları yapar:</span><span class="sxs-lookup"><span data-stu-id="02dd5-293">The script:</span></span>
   * <span data-ttu-id="02dd5-294">, `StartupDiagnostics` *Obj\packages* klasöründe paketi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="02dd5-295">İçin çalışma zamanı deposunu `StartupDiagnostics` *Mağaza* klasöründe oluşturur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="02dd5-296">`dotnet store`Betikteki komut, `win7-x64` Windows 'a dağıtılan bir barındırma başlatması için [çalışma zamanı tanımlayıcısı 'nı (RID)](/dotnet/core/rid-catalog) kullanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="02dd5-297">Farklı bir çalışma zamanı için barındırma başlangıcını sağlarken, betiğin 37. satırındaki doğru RID 'yi yerine koyun.</span><span class="sxs-lookup"><span data-stu-id="02dd5-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="02dd5-298">Çalışma zamanı deposu `StartupDiagnostics` daha sonra derlemenin tüketilebileceği makinede kullanıcının veya sisteminin çalışma zamanı deposuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="02dd5-299">Derlemenin Kullanıcı çalışma zamanı deposu yüklemesi konumu `StartupDiagnostics` *. DotNet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="02dd5-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="02dd5-300">, `additionalDeps` `StartupDiagnostics` *Additionaldeps* klasöründe için öğesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="02dd5-301">Ek bağımlılıklar daha sonra kullanıcının veya sistem ek bağımlılıklarına taşınır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="02dd5-302">Kullanıcı `StartupDiagnostics` ek bağımlılıkları yüklemesi konumu *. DotNet/x64/additionalDeps/startupdiagnostics/Shared/Microsoft. netcore. app/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="02dd5-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="02dd5-303">*deploy.ps1* dosyasını *dağıtım* klasörüne koyar.</span><span class="sxs-lookup"><span data-stu-id="02dd5-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="02dd5-304">*Dağıtım* klasöründe *deploy.ps1* betiğini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="02dd5-305">Betik şunu ekler:</span><span class="sxs-lookup"><span data-stu-id="02dd5-305">The script appends:</span></span>
   * <span data-ttu-id="02dd5-306">`StartupDiagnostics``ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="02dd5-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="02dd5-307">Barındırma başlangıç bağımlılıkları yolu (Runtimessımında projenin *dağıtım* klasöründe) `DOTNET_ADDITIONAL_DEPS` ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="02dd5-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="02dd5-308">Çalışma zamanı depolama yolu (Runtimes, projenin *dağıtım* klasöründe) `DOTNET_SHARED_STORE` ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="02dd5-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="02dd5-309">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-309">Run the sample app.</span></span>
1. <span data-ttu-id="02dd5-310">`/services`Uygulamanın kayıtlı hizmetlerini görmek için uç nokta isteyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="02dd5-311">`/diag`Tanılama bilgilerini görmek için uç nokta isteyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="02dd5-312"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(Barındırma başlatma) uygulaması, bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler ekler.</span><span class="sxs-lookup"><span data-stu-id="02dd5-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="02dd5-313">Örneğin, bir dış kitaplık, bir uygulamaya ek yapılandırma sağlayıcıları veya hizmetler sağlamak için barındırma başlangıç uygulamasını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="02dd5-314">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02dd5-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="02dd5-315">HostingStartup özniteliği</span><span class="sxs-lookup"><span data-stu-id="02dd5-315">HostingStartup attribute</span></span>

<span data-ttu-id="02dd5-316">[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirilecek bir barındırma başlangıç derlemesinin varlığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="02dd5-317">Giriş derlemesi veya sınıfı içeren derleme `Startup` öznitelik için otomatik olarak taranır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="02dd5-318">Öznitelikleri aramak için derlemelerin listesi `HostingStartup` [Webhostdefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)içindeki yapılandırmadan çalışma zamanında yüklenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="02dd5-319">Bulmadan dışlanacak derlemelerin listesi [Webhostdefaults. Hostingstartupexcludederlemelieskey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)öğesinden yüklendi.</span><span class="sxs-lookup"><span data-stu-id="02dd5-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="02dd5-320">Daha fazla bilgi için bkz. [Web Konağı: barındırma başlangıç derlemeleri](xref:fundamentals/host/web-host#hosting-startup-assemblies) ve [Web Konağı: barındırma başlatma derlemeleri çıkarma](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="02dd5-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="02dd5-321">Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad alanı `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="02dd5-322">Barındırma başlangıç kodunu içeren sınıf `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="02dd5-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="02dd5-323">`HostingStartup`Özniteliği genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında bulunur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="02dd5-324">Yüklü barındırma başlangıç derlemelerini bul</span><span class="sxs-lookup"><span data-stu-id="02dd5-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="02dd5-325">Yüklü barındırma başlangıç derlemelerini öğrenmek için, günlüğü etkinleştirin ve uygulamanın günlüklerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="02dd5-326">Derlemeler yüklenirken oluşan hatalar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="02dd5-327">Yüklenen barındırma başlangıç derlemeleri hata ayıklama düzeyinde günlüğe kaydedilir ve tüm hatalar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="02dd5-328">Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="02dd5-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="02dd5-329">Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırakmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="02dd5-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="02dd5-330">Tüm barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini veya olarak ayarlayın `true` `1` :</span><span class="sxs-lookup"><span data-stu-id="02dd5-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="02dd5-331">[Barındırma başlangıç](xref:fundamentals/host/web-host#prevent-hosting-startup) ana bilgisayar yapılandırma ayarını önleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="02dd5-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="02dd5-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="02dd5-333">Belirli barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini, başlangıçta dışlamak üzere, bir barındırma başlangıç bütünleştirilmiş kodlarının noktalı virgülle ayrılmış dizesine ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="02dd5-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="02dd5-334">[Barındırma başlatma derlemeleri](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) ana bilgisayar yapılandırma ayarı.</span><span class="sxs-lookup"><span data-stu-id="02dd5-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="02dd5-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="02dd5-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="02dd5-336">Ana bilgisayar yapılandırma ayarı ve ortam değişkeni ayarlanırsa, konak ayarı davranışı denetler.</span><span class="sxs-lookup"><span data-stu-id="02dd5-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="02dd5-337">Konak ayarını veya ortam değişkenini kullanarak başlatma derlemelerinin barındırılmasını devre dışı bırakmak, derlemeyi küresel olarak devre dışı bırakabilir ve bir uygulamanın çeşitli özelliklerini devre dışı bırakabilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="02dd5-338">Project</span><span class="sxs-lookup"><span data-stu-id="02dd5-338">Project</span></span>

<span data-ttu-id="02dd5-339">Aşağıdaki proje türlerinden birini kullanarak bir barındırma başlatması oluşturun:</span><span class="sxs-lookup"><span data-stu-id="02dd5-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="02dd5-340">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="02dd5-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="02dd5-341">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="02dd5-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="02dd5-342">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="02dd5-342">Class library</span></span>

<span data-ttu-id="02dd5-343">Bir barındırma başlatma geliştirmesi, bir sınıf kitaplığında bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="02dd5-344">Kitaplık bir özniteliği içerir `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="02dd5-345">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) , bir Razor Pages uygulaması, *Hostingstartupapp* ve bir sınıf kitaplığı, *hostingstartuplibrary* içerir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="02dd5-346">Sınıf kitaplığı:</span><span class="sxs-lookup"><span data-stu-id="02dd5-346">The class library:</span></span>

* <span data-ttu-id="02dd5-347">Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="02dd5-348">`ServiceKeyInjection` bellek içi yapılandırma sağlayıcısını ([Addınmemorycollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) kullanarak uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.</span><span class="sxs-lookup"><span data-stu-id="02dd5-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="02dd5-349">`HostingStartup`Barındırma başlatmasının ad alanını ve sınıfını tanımlayan bir özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="02dd5-350">`ServiceKeyInjection`Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="02dd5-351">*Hostingstartuplibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="02dd5-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="02dd5-352">Uygulamanın dizin sayfası, sınıf kitaplığının barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="02dd5-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="02dd5-353">*Hostingstartupapp/Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="02dd5-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="02dd5-354">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ayrıca ayrı bir barındırma başlatma, *Hostingstartuppackage* sağlayan bir NuGet paket projesi içerir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="02dd5-355">Paket, daha önce açıklanan sınıf kitaplığıyla aynı özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="02dd5-356">Paket:</span><span class="sxs-lookup"><span data-stu-id="02dd5-356">The package:</span></span>

* <span data-ttu-id="02dd5-357">Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="02dd5-358">`ServiceKeyInjection` uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.</span><span class="sxs-lookup"><span data-stu-id="02dd5-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="02dd5-359">Bir `HostingStartup` özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="02dd5-360">*Hostingstartuppackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="02dd5-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="02dd5-361">Uygulamanın dizin sayfası, paketin barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="02dd5-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="02dd5-362">*Hostingstartupapp/Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="02dd5-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="02dd5-363">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="02dd5-363">Console app without an entry point</span></span>

<span data-ttu-id="02dd5-364">*Bu yaklaşım, .NET Framework değil yalnızca .NET Core uygulamaları için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="02dd5-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="02dd5-365">Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik barındırma başlatma geliştirmesi, bir öznitelik içeren giriş noktası olmadan bir konsol uygulamasında sağlanabilmesi `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="02dd5-366">Konsol uygulamasını yayımlamak, çalışma zamanı deposundan tüketilebilen bir barındırma başlangıç derlemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="02dd5-367">Bu işlemde giriş noktası olmayan bir konsol uygulaması kullanılmıştır çünkü:</span><span class="sxs-lookup"><span data-stu-id="02dd5-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="02dd5-368">Barındırma başlangıç derlemesinde barındırma başlangıcını kullanmak için bir bağımlılıklar dosyası gereklidir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="02dd5-369">Bağımlılıklar dosyası, bir kitaplık değil bir uygulama yayımlamayla üretilen çalıştırılabilir bir uygulama varlıktır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="02dd5-370">Bir kitaplık, paylaşılan çalışma zamanını hedefleyen bir çalıştırılabilir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)eklenemez.</span><span class="sxs-lookup"><span data-stu-id="02dd5-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="02dd5-371">Dinamik barındırma başlatma oluşturma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="02dd5-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="02dd5-372">Bir barındırma başlangıç derlemesi, konsol uygulamasından bir giriş noktası olmadan oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="02dd5-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="02dd5-373">, Uygulamayı içeren bir sınıf içerir `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="02dd5-374">Uygulama sınıfını tanımlamak için bir [Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="02dd5-375">Konsol uygulaması, barındırma başlatmasının bağımlılıklarını almak için yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="02dd5-376">Konsol uygulamasını yayımlamanın bir sonucu, kullanılmayan bağımlılıkların bağımlılıklar dosyasından kırpıllarıdır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="02dd5-377">Bağımlılıklar dosyası, barındırma başlangıç derlemesinin çalışma zamanı konumunu ayarlamak için değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="02dd5-378">Barındırma başlangıç derlemesi ve onun bağımlılıklar dosyası çalışma zamanı paket deposuna yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="02dd5-379">Barındırma başlangıç derlemesini ve onun bağımlılıklar dosyasını öğrenmek için, ortam değişkenleri çiftinde listelenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="02dd5-380">Konsol uygulaması [Microsoft. AspNetCore. Hosting. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:</span><span class="sxs-lookup"><span data-stu-id="02dd5-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="02dd5-381">[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfını `IHostingStartup` oluştururken yükleme ve yürütme için uygulamasının bir uygulamasını tanımlar <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="02dd5-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="02dd5-382">Aşağıdaki örnekte, ad alanı `StartupEnhancement` ve sınıfı `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="02dd5-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="02dd5-383">Bir sınıf uygular `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="02dd5-384">Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="02dd5-385">`IHostingStartup.Configure` barındırma başlangıç derlemesinde kullanıcı kodundan önce çalışma zamanı tarafından çağrılır `Startup.Configure` , bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından verilen yapılandırmanın üzerine yazılmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="02dd5-386">Bir proje oluştururken `IHostingStartup` , bağımlılıklar dosyası (*.deps.js*), `runtime` derlemenin konumunu *bin* klasörüne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="02dd5-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="02dd5-387">Yalnızca dosyanın bir kısmı gösterilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-387">Only part of the file is shown.</span></span> <span data-ttu-id="02dd5-388">Örnekteki derleme adı `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="02dd5-389">Barındırma başlatma tarafından belirtilen yapılandırma</span><span class="sxs-lookup"><span data-stu-id="02dd5-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="02dd5-390">Yapılandırma işlemi, barındırma başlatmasının yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak iki yaklaşımdan yararlanabilir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="02dd5-391"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>Uygulamanın temsilcileri çalıştırıldıktan sonra yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="02dd5-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="02dd5-392">Barındırma başlangıç yapılandırması, uygulamanın yapılandırmasına bu yaklaşımı kullanarak öncelik kazanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="02dd5-393"><xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>Uygulamanın temsilcileri yürütmeden önce yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="02dd5-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="02dd5-394">Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="02dd5-395">Barındırma başlangıç derlemesini belirtin</span><span class="sxs-lookup"><span data-stu-id="02dd5-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="02dd5-396">Bir sınıf kitaplığı ya da konsol uygulaması tarafından sağlanan bir barındırma başlatması için, ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="02dd5-397">Ortam değişkeni, derlemelerin noktalı virgülle ayrılmış listesidir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="02dd5-398">Özniteliği için yalnızca barındırma başlangıç derlemeleri taranır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="02dd5-399">Daha önce açıklanan barındırma başlangıç pencerelerini öğrenmek için *Hostingstartupapp* örnek uygulaması için, ortam değişkeni aşağıdaki değere ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="02dd5-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="02dd5-400">Barındırma başlangıç bütünleştirilmiş [kodları barındırma başlangıç derlemeleri](xref:fundamentals/host/web-host#hosting-startup-assemblies) ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="02dd5-401">Birden çok barındırma başlatması varsa, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemleri derlemelerin listelendiği sırada yürütülür.</span><span class="sxs-lookup"><span data-stu-id="02dd5-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="02dd5-402">Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="02dd5-402">Activation</span></span>

<span data-ttu-id="02dd5-403">Başlatma başlangıç etkinleştirme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="02dd5-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="02dd5-404">[Çalışma zamanı deposu](#runtime-store): etkinleştirme etkinleştirme için derleme zamanı başvurusu gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="02dd5-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="02dd5-405">Örnek uygulama, çok makineli bir ortamda barındırma başlatmasının dağıtımını kolaylaştırmak için barındırma başlangıç derlemesini ve bağımlılıklar dosyalarını bir klasöre, *dağıtımına* koyar.</span><span class="sxs-lookup"><span data-stu-id="02dd5-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="02dd5-406">*Dağıtım* klasörü Ayrıca, barındırma başlangıcını etkinleştirmek için dağıtım sistemindeki ortam değişkenlerini oluşturan veya değiştiren bir PowerShell betiği içerir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="02dd5-407">Etkinleştirme için derleme zamanı başvurusu gerekiyor</span><span class="sxs-lookup"><span data-stu-id="02dd5-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="02dd5-408">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="02dd5-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="02dd5-409">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="02dd5-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="02dd5-410">Çalışma zamanı deposu</span><span class="sxs-lookup"><span data-stu-id="02dd5-410">Runtime store</span></span>

<span data-ttu-id="02dd5-411">Barındırma başlangıç uygulamasının [çalışma zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilmesi.</span><span class="sxs-lookup"><span data-stu-id="02dd5-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="02dd5-412">Derleme zamanı başvurusu, Gelişmiş uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="02dd5-413">Barındırma başlatma oluşturulduktan sonra, bildirim proje dosyası ve [DotNet Store](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir çalışma zamanı deposu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="02dd5-414">Örnek uygulamada (*Runtimesstore* Projesi) aşağıdaki komut kullanılır:</span><span class="sxs-lookup"><span data-stu-id="02dd5-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="02dd5-415">Çalışma zamanının çalışma zamanı deposunu bulması için, çalışma zamanı deposunun konumu `DOTNET_SHARED_STORE` ortam değişkenine eklenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="02dd5-416">**Barındırma başlatmasının bağımlılıklar dosyasını değiştirme ve yerleştirme**</span><span class="sxs-lookup"><span data-stu-id="02dd5-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="02dd5-417">Geliştirmede bir paket başvurusu olmadan geliştirmeyi etkinleştirmek için, ile çalışma zamanına ek bağımlılıklar belirtin `additionalDeps` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="02dd5-418">`additionalDeps` şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="02dd5-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="02dd5-419">Dosya başlangıcında uygulamanın kendi *.deps.js* birlikte birleştirilecek dosyalara bir dizi ek *.deps.js* sağlayarak uygulamanın kitaplık grafiğini genişletin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="02dd5-420">Barındırma başlangıç derlemesini bulunabilir ve yüklenebilir hale getirin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="02dd5-421">Ek bağımlılıklar dosyası oluşturmak için önerilen yaklaşım şunlardır:</span><span class="sxs-lookup"><span data-stu-id="02dd5-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="02dd5-422">`dotnet publish`Önceki bölümde başvurulan çalışma zamanı deposu bildirim dosyasında yürütün.</span><span class="sxs-lookup"><span data-stu-id="02dd5-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="02dd5-423">Bildirimlerin bildirim başvurusunu ve `runtime` sonuçta elde edilen *.deps.js* dosyanın bölümünü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="02dd5-424">Örnek projede, `store.manifest/1.0.0` özelliği `targets` ve `libraries` bölümünden kaldırılır:</span><span class="sxs-lookup"><span data-stu-id="02dd5-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="02dd5-425">*.deps.js* dosyasını aşağıdaki konuma yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="02dd5-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="02dd5-426">`{ADDITIONAL DEPENDENCIES PATH}`: Ortam değişkenine konum eklendi `DOTNET_ADDITIONAL_DEPS` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="02dd5-427">`{SHARED FRAMEWORK NAME}`: Bu ek bağımlılıklar dosyası için paylaşılan çerçeve gereklidir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="02dd5-428">`{SHARED FRAMEWORK VERSION}`: En düşük paylaşılan çerçeve sürümü.</span><span class="sxs-lookup"><span data-stu-id="02dd5-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="02dd5-429">`{ENHANCEMENT ASSEMBLY NAME}`: Geliştirmesinin derleme adı.</span><span class="sxs-lookup"><span data-stu-id="02dd5-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="02dd5-430">Örnek uygulamada (*Runtimesbir* proje), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="02dd5-431">Çalışma zamanı depo konumunu bulması için, ek bağımlılıklar dosya konumu `DOTNET_ADDITIONAL_DEPS` ortam değişkenine eklenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="02dd5-432">Örnek uygulamada (*Runtimesbir* proje), çalışma zamanı deposunun oluşturulması ve ek bağımlılıklar dosyası oluşturulması bir [PowerShell](/powershell/scripting/powershell-scripting) betiği kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="02dd5-433">Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanbileceğine ilişkin örnekler için, bkz. [birden çok ortam kullanma](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="02dd5-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="02dd5-434">**Dağıtım**</span><span class="sxs-lookup"><span data-stu-id="02dd5-434">**Deployment**</span></span>

<span data-ttu-id="02dd5-435">Çoklu makine ortamında bir barındırma başlatmasının dağıtımını kolaylaştırmak için, örnek uygulama, yayımlanan çıktıda şunları içeren bir *dağıtım* klasörü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="02dd5-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="02dd5-436">Barındırma başlangıç çalışma zamanı deposu.</span><span class="sxs-lookup"><span data-stu-id="02dd5-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="02dd5-437">Barındırma başlangıç bağımlılıkları dosyası.</span><span class="sxs-lookup"><span data-stu-id="02dd5-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="02dd5-438">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_SHARED_STORE` `DOTNET_ADDITIONAL_DEPS` Barındırma başlangıcını etkinleştirmeyi desteklemek için, ve ' ı oluşturan veya değiştiren bir PowerShell betiği.</span><span class="sxs-lookup"><span data-stu-id="02dd5-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="02dd5-439">Betiği dağıtım sistemindeki bir yönetim PowerShell komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="02dd5-440">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="02dd5-440">NuGet package</span></span>

<span data-ttu-id="02dd5-441">Bir NuGet paketinde barındırma başlatma geliştirmesi sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="02dd5-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="02dd5-442">Pakette bir öznitelik vardır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="02dd5-443">Paket tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="02dd5-444">Gelişmiş uygulamanın proje dosyası, uygulamanın proje dosyasında (derleme zamanı başvurusu) barındırma başlatması için bir paket başvurusu yapar.</span><span class="sxs-lookup"><span data-stu-id="02dd5-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="02dd5-445">Derleme zamanı başvurusuyla birlikte, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*.deps.js*) eklenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="02dd5-446">Bu yaklaşım, [NuGet.org](https://www.nuget.org/)'e yayınlanan bir barındırma başlangıç derleme paketi için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="02dd5-447">Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="02dd5-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="02dd5-448">NuGet paketleri ve çalışma zamanı deposu hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="02dd5-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="02dd5-449">Platformlar arası araçlarla bir NuGet paketi oluşturma</span><span class="sxs-lookup"><span data-stu-id="02dd5-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="02dd5-450">Paketler yayımlanıyor</span><span class="sxs-lookup"><span data-stu-id="02dd5-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="02dd5-451">Çalışma zamanı paket deposu</span><span class="sxs-lookup"><span data-stu-id="02dd5-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="02dd5-452">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="02dd5-452">Project bin folder</span></span>

<span data-ttu-id="02dd5-453">Bir barındırma başlatma geliştirmesi, gelişmiş uygulamada, *bin* ile dağıtılan bir derleme tarafından sağlanıyor.</span><span class="sxs-lookup"><span data-stu-id="02dd5-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="02dd5-454">Derleme tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="02dd5-455">Gelişmiş uygulamanın proje dosyası, barındırma başlatmaya bir derleme başvurusu yapar (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="02dd5-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="02dd5-456">Derleme zamanı başvurusuyla birlikte, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*.deps.js*) eklenir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="02dd5-457">Bu yaklaşım, dağıtım senaryosu barındırma başlatmasının derlemesine (*. dll* dosyası) bir derleme zamanı başvurusu yapmak ve derlemeyi şu şekilde taşımak için çağırdığında geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="02dd5-458">Tüketen proje.</span><span class="sxs-lookup"><span data-stu-id="02dd5-458">The consuming project.</span></span>
  * <span data-ttu-id="02dd5-459">Tüketim Projesi tarafından erişilebilen bir konum.</span><span class="sxs-lookup"><span data-stu-id="02dd5-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="02dd5-460">Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="02dd5-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="02dd5-461">.NET Framework hedeflenirken, derleme varsayılan yükleme bağlamında yüklenebilir olur; bu, .NET Framework, derlemenin aşağıdaki konumlardan birinde bulunduğu anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="02dd5-462">Uygulama temel yolu: uygulamanın yürütülebilir dosyasının (*. exe*) bulunduğu *bin* klasörü.</span><span class="sxs-lookup"><span data-stu-id="02dd5-462">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="02dd5-463">Genel bütünleştirilmiş kod önbelleği (GAC): GAC, birkaç .NET Framework uygulamanın paylaştığı derlemeleri depolar.</span><span class="sxs-lookup"><span data-stu-id="02dd5-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="02dd5-464">Daha fazla bilgi için, bkz. [nasıl yapılır: bir derlemeyi genel derleme önbelleğine yüklemek](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) .NET Framework belgeleri.</span><span class="sxs-lookup"><span data-stu-id="02dd5-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="02dd5-465">Örnek kod</span><span class="sxs-lookup"><span data-stu-id="02dd5-465">Sample code</span></span>

<span data-ttu-id="02dd5-466">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample)), başlangıç uygulama senaryolarını barındırma gösterir:</span><span class="sxs-lookup"><span data-stu-id="02dd5-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="02dd5-467">İki barındırma başlangıç derlemesi (sınıf kitaplıkları) her biri bellek içi yapılandırma anahtar-değer çiftleri çiftini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="02dd5-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="02dd5-468">NuGet paketi (*Hostingstartuppackage*)</span><span class="sxs-lookup"><span data-stu-id="02dd5-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="02dd5-469">Sınıf kitaplığı (*Hostingstartuplibrary*)</span><span class="sxs-lookup"><span data-stu-id="02dd5-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="02dd5-470">Bir barındırma başlatması, çalışma zamanı deposu tarafından dağıtılan bir derlemeden (*Startupdiagnostics*) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="02dd5-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="02dd5-471">Derleme, üzerinde tanılama bilgileri sağlayan, başlangıçta uygulamaya iki middlewares ekler:</span><span class="sxs-lookup"><span data-stu-id="02dd5-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="02dd5-472">Kayıtlı hizmetler</span><span class="sxs-lookup"><span data-stu-id="02dd5-472">Registered services</span></span>
  * <span data-ttu-id="02dd5-473">Adres (düzen, ana bilgisayar, yol tabanı, yol, sorgu dizesi)</span><span class="sxs-lookup"><span data-stu-id="02dd5-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="02dd5-474">Bağlantı (uzak IP, uzak bağlantı noktası, yerel IP, yerel bağlantı noktası, istemci sertifikası)</span><span class="sxs-lookup"><span data-stu-id="02dd5-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="02dd5-475">İstek üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="02dd5-475">Request headers</span></span>
  * <span data-ttu-id="02dd5-476">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="02dd5-476">Environment variables</span></span>

<span data-ttu-id="02dd5-477">Örneği çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="02dd5-477">To run the sample:</span></span>

<span data-ttu-id="02dd5-478">**NuGet paketinden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="02dd5-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="02dd5-479">,, [DotNet paketi](/dotnet/core/tools/dotnet-pack) komutuyla *hostingstartuppackage* paketini derleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="02dd5-480">Paketin *Hostingstartuppackage* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="02dd5-481">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-481">Compile and run the app.</span></span> <span data-ttu-id="02dd5-482">Gelişmiş uygulamada bir paket başvurusu vardır (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="02dd5-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="02dd5-483">`<PropertyGroup>`Uygulamanın proje dosyasındaki bir paket projenin çıkışını belirtir (*.. /HostingStartupPackage/bin/Debug*) bir paket kaynağı olarak.</span><span class="sxs-lookup"><span data-stu-id="02dd5-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="02dd5-484">Bu, uygulamanın paketi [NuGet.org](https://www.nuget.org/)'e yüklemeden paketi kullanmasına izin verir. Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="02dd5-485">Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, paketin yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="02dd5-486">*Hostingstartuppackage* projesinde değişiklik yaparsanız ve yeniden derleyseniz, *Hostingstartupapp* ' ın yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="02dd5-487">Yerel NuGet önbelleklerini temizlemek için aşağıdaki [DotNet NuGet Yereller](/dotnet/core/tools/dotnet-nuget-locals) komutunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="02dd5-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="02dd5-488">**Bir sınıf kitaplığından etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="02dd5-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="02dd5-489">, [DotNet Build](/dotnet/core/tools/dotnet-build) komutuyla *hostingstartuplibrary* sınıf kitaplığını derleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="02dd5-490">Sınıf kitaplığının *Hostingstartuplibrary* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="02dd5-491">*bin*- *HostingStartupLibrary.dll* dosyasını sınıf kitaplığının derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne kopyalayarak, sınıf kitaplığının derlemesini uygulamaya dağıtın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="02dd5-492">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-492">Compile and run the app.</span></span> <span data-ttu-id="02dd5-493">`<ItemGroup>`Uygulamanın proje dosyasındaki bir, sınıf kitaplığının derlemesine (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (derleme zamanı başvurusu) başvurur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="02dd5-494">Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="02dd5-495">Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, sınıf kitaplığının yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="02dd5-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="02dd5-496">**Çalışma zamanı deposu tarafından dağıtılan bir derlemeden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="02dd5-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="02dd5-497">*Startupdiagnostics* projesi dosyada *StartupDiagnostics.deps.js* değiştirmek için [PowerShell](/powershell/scripting/powershell-scripting) kullanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="02dd5-498">PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak varsayılan olarak yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="02dd5-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="02dd5-499">Diğer platformlarda PowerShell 'i almak için bkz. [PowerShell 'in çeşitli sürümlerini yükleme](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="02dd5-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="02dd5-500">*Runtimesyürüme* klasöründe *build.ps1* betiğini yürütün.</span><span class="sxs-lookup"><span data-stu-id="02dd5-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="02dd5-501">Betik şunları yapar:</span><span class="sxs-lookup"><span data-stu-id="02dd5-501">The script:</span></span>
   * <span data-ttu-id="02dd5-502">, `StartupDiagnostics` *Obj\packages* klasöründe paketi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="02dd5-503">İçin çalışma zamanı deposunu `StartupDiagnostics` *Mağaza* klasöründe oluşturur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="02dd5-504">`dotnet store`Betikteki komut, `win7-x64` Windows 'a dağıtılan bir barındırma başlatması için [çalışma zamanı tanımlayıcısı 'nı (RID)](/dotnet/core/rid-catalog) kullanır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="02dd5-505">Farklı bir çalışma zamanı için barındırma başlangıcını sağlarken, betiğin 37. satırındaki doğru RID 'yi yerine koyun.</span><span class="sxs-lookup"><span data-stu-id="02dd5-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="02dd5-506">Çalışma zamanı deposu `StartupDiagnostics` daha sonra derlemenin tüketilebileceği makinede kullanıcının veya sisteminin çalışma zamanı deposuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="02dd5-507">Derlemenin Kullanıcı çalışma zamanı deposu yüklemesi konumu `StartupDiagnostics` *. DotNet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="02dd5-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="02dd5-508">, `additionalDeps` `StartupDiagnostics` *Additionaldeps* klasöründe için öğesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="02dd5-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="02dd5-509">Ek bağımlılıklar daha sonra kullanıcının veya sistem ek bağımlılıklarına taşınır.</span><span class="sxs-lookup"><span data-stu-id="02dd5-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="02dd5-510">Kullanıcı `StartupDiagnostics` ek bağımlılıkları yüklemesi konumu *. DotNet/x64/additionalDeps/startupdiagnostics/Shared/Microsoft. netcore. App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="02dd5-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="02dd5-511">*deploy.ps1* dosyasını *dağıtım* klasörüne koyar.</span><span class="sxs-lookup"><span data-stu-id="02dd5-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="02dd5-512">*Dağıtım* klasöründe *deploy.ps1* betiğini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="02dd5-513">Betik şunu ekler:</span><span class="sxs-lookup"><span data-stu-id="02dd5-513">The script appends:</span></span>
   * <span data-ttu-id="02dd5-514">`StartupDiagnostics``ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="02dd5-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="02dd5-515">Barındırma başlangıç bağımlılıkları yolu (Runtimessımında projenin *dağıtım* klasöründe) `DOTNET_ADDITIONAL_DEPS` ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="02dd5-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="02dd5-516">Çalışma zamanı depolama yolu (Runtimes, projenin *dağıtım* klasöründe) `DOTNET_SHARED_STORE` ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="02dd5-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="02dd5-517">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02dd5-517">Run the sample app.</span></span>
1. <span data-ttu-id="02dd5-518">`/services`Uygulamanın kayıtlı hizmetlerini görmek için uç nokta isteyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="02dd5-519">`/diag`Tanılama bilgilerini görmek için uç nokta isteyin.</span><span class="sxs-lookup"><span data-stu-id="02dd5-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
