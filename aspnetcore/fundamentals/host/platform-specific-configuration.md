---
title: ASP.NET Core'da barındırma başlangıç derlemelerini kullanma
author: rick-anderson
description: IHostingStartup uygulamasını kullanarak harici bir derlemeden ASP.NET Core uygulamasını nasıl geliştireceğimi keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: ac667b0205f5daad395d86fbe129beb509a044a6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417616"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="857f0-103">ASP.NET Core'da barındırma başlangıç derlemelerini kullanma</span><span class="sxs-lookup"><span data-stu-id="857f0-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="857f0-104">Yazar: [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="857f0-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="857f0-105">(Barındırma <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> başlangıç) uygulaması, harici bir derlemeden başlangıçta bir uygulamaya geliştirmeler ekler.</span><span class="sxs-lookup"><span data-stu-id="857f0-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="857f0-106">Örneğin, harici bir kitaplık, bir uygulamaya ek yapılandırma sağlayıcıları veya hizmetleri sağlamak için barındırma başlangıç uygulamasını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="857f0-107">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="857f0-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="857f0-108">HostingBaşlangıç özniteliği</span><span class="sxs-lookup"><span data-stu-id="857f0-108">HostingStartup attribute</span></span>

<span data-ttu-id="857f0-109">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirmek üzere bir barındırma başlangıç derlemesinin varlığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="857f0-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="857f0-110">Giriş derlemesi veya `Startup` sınıfı içeren derleme öznitelik `HostingStartup` için otomatik olarak taranır.</span><span class="sxs-lookup"><span data-stu-id="857f0-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="857f0-111">Öznitelikleri aramak için `HostingStartup` derlemeler listesi [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)yapılandırmadan çalışma zamanında yüklenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="857f0-112">Keşif hariç tutmak için derlemeler listesi [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)yüklenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="857f0-113">Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad `StartupEnhancement`alanı .</span><span class="sxs-lookup"><span data-stu-id="857f0-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="857f0-114">Barındırma başlangıç kodunu içeren `StartupEnhancementHostingStartup`sınıf:</span><span class="sxs-lookup"><span data-stu-id="857f0-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="857f0-115">Öznitelik `HostingStartup` genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında yer alır.</span><span class="sxs-lookup"><span data-stu-id="857f0-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="857f0-116">Yüklü barındırma başlangıç derlemelerini keşfedin</span><span class="sxs-lookup"><span data-stu-id="857f0-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="857f0-117">Yüklü barındırma başlangıç derlemelerini keşfetmek için günlüğe kaydetmeyi etkinleştirin ve uygulamanın günlüklerini kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="857f0-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="857f0-118">Derlemeler günlüğe yüklenirken oluşan hatalar.</span><span class="sxs-lookup"><span data-stu-id="857f0-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="857f0-119">Yüklü barındırma başlangıç derlemeleri Hata Ayıklama düzeyinde günlüğe kaydedilir ve tüm hatalar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="857f0-120">Barındırma başlangıç montajlarının otomatik yüklemesini devre dışı</span><span class="sxs-lookup"><span data-stu-id="857f0-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="857f0-121">Barındırma başlangıç derlemelerinin otomatik yüklemesini devre dışı kullanabilirsiniz, aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="857f0-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="857f0-122">Tüm barındırma başlangıç derlemelerinin yüklenmesini önlemek için `true` `1`aşağıdakilerden birini ayarlayın veya:</span><span class="sxs-lookup"><span data-stu-id="857f0-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="857f0-123">Hosting Başlangıç ana bilgisayar yapılandırma ayarını önleyin:</span><span class="sxs-lookup"><span data-stu-id="857f0-123">Prevent Hosting Startup host configuration setting:</span></span>

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

  * <span data-ttu-id="857f0-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP`çevre değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="857f0-125">Belirli barındırma başlangıç derlemelerinin yüklenmesinden korunmak için, başlangıçta hariç tutmak için aşağıdakilerden birini yarı sütunlu sınırlı sayıda barındırma başlangıç derlemesi dizesine ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="857f0-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="857f0-126">Barındırma Başlangıç Dışlama Derlemeleri ana bilgisayar yapılandırma ayarı:</span><span class="sxs-lookup"><span data-stu-id="857f0-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

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

  * <span data-ttu-id="857f0-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`çevre değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="857f0-128">Hem ana bilgisayar yapılandırma ayarı hem de ortam değişkeni ayarlanırsa, ana bilgisayar ayarı davranışı denetler.</span><span class="sxs-lookup"><span data-stu-id="857f0-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="857f0-129">Ana bilgisayar ayarı veya ortam değişkenini kullanarak barındırma başlangıç derlemelerini devre dışı bırakmak, genel olarak montajı devre dışı bırakırsa ve bir uygulamanın çeşitli özelliklerini devre dışı bırakabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="857f0-130">Project</span><span class="sxs-lookup"><span data-stu-id="857f0-130">Project</span></span>

<span data-ttu-id="857f0-131">Aşağıdaki proje türlerinden birini içeren bir barındırma başlatma oluşturma:</span><span class="sxs-lookup"><span data-stu-id="857f0-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="857f0-132">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="857f0-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="857f0-133">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="857f0-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="857f0-134">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="857f0-134">Class library</span></span>

<span data-ttu-id="857f0-135">Bir barındırma başlangıç geliştirme bir sınıf kitaplığı sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="857f0-136">Kitaplık bir `HostingStartup` öznitelik içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="857f0-137">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) bir Razor Pages uygulaması, *HostingStartupApp*ve bir sınıf kütüphanesi, *HostingStartupLibrary*içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="857f0-138">Sınıf kitaplığı:</span><span class="sxs-lookup"><span data-stu-id="857f0-138">The class library:</span></span>

* <span data-ttu-id="857f0-139">Bir barındırma başlangıç `ServiceKeyInjection`sınıfı içerir, `IHostingStartup`hangi uygular.</span><span class="sxs-lookup"><span data-stu-id="857f0-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="857f0-140">`ServiceKeyInjection`bellek içi yapılandırma sağlayıcısını[(AddInMemoryCollection)](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)kullanarak uygulamanın yapılandırmasına bir çift hizmet dizeleri ekler.</span><span class="sxs-lookup"><span data-stu-id="857f0-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="857f0-141">Barındırma `HostingStartup` başlangıç adı alanını ve sınıfını tanımlayan bir öznitelik içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="857f0-142">Sınıfın `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi, bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirme eklemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="857f0-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="857f0-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="857f0-144">Uygulamanın Dizin sayfası, sınıf kitaplığı barındırma başlangıç derlemesi tarafından ayarlanan iki anahtar için yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="857f0-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="857f0-145">*HostingStartupApp/Sayfalar/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="857f0-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="857f0-146">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) da ayrı bir hosting başlangıç sağlayan bir NuGet paket projesi içerir, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="857f0-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="857f0-147">Paket, daha önce açıklanan sınıf kitaplığıyla aynı özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="857f0-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="857f0-148">Paket:</span><span class="sxs-lookup"><span data-stu-id="857f0-148">The package:</span></span>

* <span data-ttu-id="857f0-149">Bir barındırma başlangıç `ServiceKeyInjection`sınıfı içerir, `IHostingStartup`hangi uygular.</span><span class="sxs-lookup"><span data-stu-id="857f0-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="857f0-150">`ServiceKeyInjection`uygulamanın yapılandırmasına bir çift hizmet dizeleri ekler.</span><span class="sxs-lookup"><span data-stu-id="857f0-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="857f0-151">Bir `HostingStartup` öznitelik içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="857f0-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="857f0-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="857f0-153">Uygulamanın Dizin sayfası, paketin barındırma başlangıç derlemesi tarafından ayarlanan iki anahtar için yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="857f0-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="857f0-154">*HostingStartupApp/Sayfalar/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="857f0-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="857f0-155">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="857f0-155">Console app without an entry point</span></span>

<span data-ttu-id="857f0-156">*Bu yaklaşım ,.NET Framework için değil, yalnızca .NET Core uygulamaları için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="857f0-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="857f0-157">Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik bir `HostingStartup` barındırma başlatma geliştirmesi, öznitelik içeren bir giriş noktası olmayan bir konsol uygulamasında sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="857f0-158">Konsol uygulamasının yayımedilmesi, çalışma zamanı deposundan tüketilebilen bir barındırma başlangıç derlemesi üretir.</span><span class="sxs-lookup"><span data-stu-id="857f0-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="857f0-159">Giriş noktası olmayan bir konsol uygulaması bu işlemde kullanılır, çünkü:</span><span class="sxs-lookup"><span data-stu-id="857f0-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="857f0-160">Barındırma başlangıç derlemesinde barındırma başlangıç ını tüketmek için bir bağımlılık lar dosyası gereklidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="857f0-161">Bağımlılıklar dosyası, kitaplık değil, bir uygulamayayımlayarak üretilen çalıştırılabilir bir uygulama varlığıdır.</span><span class="sxs-lookup"><span data-stu-id="857f0-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="857f0-162">Kitaplık, paylaşılan çalışma süresini hedefleyen çalıştırılabilir bir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)doğrudan ekilemez.</span><span class="sxs-lookup"><span data-stu-id="857f0-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="857f0-163">Dinamik bir barındırma başlangıç oluşturulmasında:</span><span class="sxs-lookup"><span data-stu-id="857f0-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="857f0-164">Konsol uygulamasından bir giriş noktası olmadan bir barındırma başlangıç derlemesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="857f0-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="857f0-165">`IHostingStartup` Uygulamayı içeren bir sınıf içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="857f0-166">Uygulama sınıfını `IHostingStartup` tanımlamak için bir [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="857f0-167">Konsol uygulaması, barındırma başlatmanın bağımlılıklarını elde etmek için yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="857f0-168">Konsol uygulamasını yayımlamanın bir sonucu, kullanılmayan bağımlılıkların bağımlılıklar dosyasından kırpılmış olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="857f0-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="857f0-169">Bağımlılıklar dosyası, barındırma başlangıç derlemesinin çalışma zamanı konumunu ayarlamak üzere değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="857f0-170">Barındırma başlangıç derlemesi ve bağımlılıkları dosyası çalışma zamanı paket deposuna yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="857f0-171">Barındırma başlangıç derlemesini ve bağımlılıkları dosyasını keşfetmek için, bunlar bir çift ortam değişkeninde listelenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="857f0-172">Konsol uygulaması [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:</span><span class="sxs-lookup"><span data-stu-id="857f0-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="857f0-173">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfı yükleme `IHostingStartup` ve yürütme için <xref:Microsoft.AspNetCore.Hosting.IWebHost>bir uygulama olarak tanımlar.</span><span class="sxs-lookup"><span data-stu-id="857f0-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="857f0-174">Aşağıdaki örnekte, ad alanı `StartupEnhancement`ve sınıf: `StartupEnhancementHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="857f0-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="857f0-175">Bir sınıf `IHostingStartup`uygular.</span><span class="sxs-lookup"><span data-stu-id="857f0-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="857f0-176">Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi, bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirme eklemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="857f0-177">`IHostingStartup.Configure`barındırma başlangıç derlemesinde, kullanıcı `Startup.Configure` kodunda önceki çalışma süresine göre çağrılır ve bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından sağlanan herhangi bir yapılandırmanın üzerine yazmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="857f0-178">Bir `IHostingStartup` proje inşa ederken, bağımlılıklar dosyası (*.deps.json*) derlemenin `runtime` konumunu *çöp* kutusu klasörüne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="857f0-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="857f0-179">Dosyanın yalnızca bir bölümü gösterilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-179">Only part of the file is shown.</span></span> <span data-ttu-id="857f0-180">Örnekteki montaj `StartupEnhancement`adı.</span><span class="sxs-lookup"><span data-stu-id="857f0-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="857f0-181">Barındırma başlatma tarafından sağlanan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="857f0-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="857f0-182">Barındırma başlangıç yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak yapılandırmayı işlemek için iki yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="857f0-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="857f0-183">Uygulamanın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri çalıştırdıktan sonra yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın.</span><span class="sxs-lookup"><span data-stu-id="857f0-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="857f0-184">Başlangıç yapılandırmasını barındırma, bu yaklaşımı kullanarak uygulamanın yapılandırması üzerinde öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="857f0-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="857f0-185">Uygulamanın <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri yürütmeden önce yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın.</span><span class="sxs-lookup"><span data-stu-id="857f0-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="857f0-186">Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="857f0-187">Barındırma başlangıç derlemesini belirtin</span><span class="sxs-lookup"><span data-stu-id="857f0-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="857f0-188">Sınıf kitaplığı veya konsol uygulaması yla sağlanan barındırma başlatma için, ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="857f0-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="857f0-189">Ortam değişkeni, yarı kolonlu sınırlı bir derleme listesidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="857f0-190">Öznitelik için `HostingStartup` yalnızca barındırma başlangıç derlemeleri taranır.</span><span class="sxs-lookup"><span data-stu-id="857f0-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="857f0-191">Örnek uygulama, *HostingStartupApp*için, daha önce açıklanan barındırma başlangıçları keşfetmek için, ortam değişkeni aşağıdaki değere ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="857f0-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="857f0-192">Barındırma başlangıç derlemesi, Barındırma Başlangıç Derlemeleri ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="857f0-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

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

<span data-ttu-id="857f0-193">Birden çok barındırma başlangıç derlemesi olduğunda, yöntemleri <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> derlemelerin listelenme sırasına göre yürütülür.</span><span class="sxs-lookup"><span data-stu-id="857f0-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="857f0-194">Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="857f0-194">Activation</span></span>

<span data-ttu-id="857f0-195">Başlangıç etkinleştirme barındırma seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="857f0-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="857f0-196">[Runtime store](#runtime-store) &ndash; Etkinleştirme etkinleştirme için derleme zamanı başvurusu gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="857f0-196">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="857f0-197">Örnek uygulama, barındırma başlatma başlatma ve bağımlılık dosyalarını çok makineli bir ortamda barındırma başlatmanın dağıtımını kolaylaştırmak için bir klasöre, *dağıtıma*yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="857f0-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="857f0-198">*Dağıtım* klasörü, barındırma başlatmayı etkinleştirmek için dağıtım sisteminde ortam değişkenleri oluşturan veya değiştiren bir PowerShell komut dosyası da içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="857f0-199">Etkinleştirme için gerekli zaman başvurularını derleme</span><span class="sxs-lookup"><span data-stu-id="857f0-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="857f0-200">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="857f0-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="857f0-201">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="857f0-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="857f0-202">Runtime deposu</span><span class="sxs-lookup"><span data-stu-id="857f0-202">Runtime store</span></span>

<span data-ttu-id="857f0-203">Barındırma başlangıç uygulaması çalışma [zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="857f0-204">Gelişmiş uygulama tarafından derleme zamanı derleme süresi başvurusu gerekmez.</span><span class="sxs-lookup"><span data-stu-id="857f0-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="857f0-205">Barındırma başlatma oluşturulduktan sonra, manifest proje dosyası ve [dotnet deposu](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir runtime deposu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="857f0-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="857f0-206">Örnek uygulamada *(RuntimeStore* projesi) aşağıdaki komut kullanılır:</span><span class="sxs-lookup"><span data-stu-id="857f0-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="857f0-207">Çalışma zamanı deposunu keşfetmek için, çalışma zamanı mağazasının konumu ortam `DOTNET_SHARED_STORE` değişkenine eklenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="857f0-208">**Barındırma başlatmanın bağımlılıklar dosyasını değiştirin ve yerleştirin**</span><span class="sxs-lookup"><span data-stu-id="857f0-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="857f0-209">Geliştirmeye paket başvurusu yapmadan geliştirmeyi etkinleştirmek için, çalışma süresine ek bağımlılıklar belirtin. `additionalDeps`</span><span class="sxs-lookup"><span data-stu-id="857f0-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="857f0-210">`additionalDeps`şunları yapmanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="857f0-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="857f0-211">Başlangıçta uygulamanın kendi *.deps.json* dosyasıyla birleştirmek için bir dizi ek *.deps.json* dosyası sağlayarak uygulamanın kitaplık grafiğini genişletin.</span><span class="sxs-lookup"><span data-stu-id="857f0-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="857f0-212">Barındırma başlangıç tertibatını kullanılabilir ve yüklenebilir hale getirin.</span><span class="sxs-lookup"><span data-stu-id="857f0-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="857f0-213">Ek bağımlılıklar dosyasını oluşturmak için önerilen yaklaşım:</span><span class="sxs-lookup"><span data-stu-id="857f0-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="857f0-214">Önceki `dotnet publish` bölümde başvurulan çalışma zamanı deposu bildirimi dosyasında yürütün.</span><span class="sxs-lookup"><span data-stu-id="857f0-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="857f0-215">Açıktan başvuruyu kitaplıklardan ve ortaya çıkan `runtime` *.deps.json* dosyasının bölümünden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="857f0-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="857f0-216">Örnek projede, `store.manifest/1.0.0` özellik `targets` ve `libraries` bölüm kaldırılır:</span><span class="sxs-lookup"><span data-stu-id="857f0-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="857f0-217">*.deps.json* dosyasını aşağıdaki konuma yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="857f0-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="857f0-218">`{ADDITIONAL DEPENDENCIES PATH}`&ndash; Ortam değişkenine `DOTNET_ADDITIONAL_DEPS` eklenen konum.</span><span class="sxs-lookup"><span data-stu-id="857f0-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="857f0-219">`{SHARED FRAMEWORK NAME}`&ndash; Bu ek bağımlılıklar dosyası için paylaşılan çerçeve gereklidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-219">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="857f0-220">`{SHARED FRAMEWORK VERSION}`&ndash; Minimum paylaşılan çerçeve sürümü.</span><span class="sxs-lookup"><span data-stu-id="857f0-220">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="857f0-221">`{ENHANCEMENT ASSEMBLY NAME}`&ndash; Geliştirmenin montaj adı.</span><span class="sxs-lookup"><span data-stu-id="857f0-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="857f0-222">Örnek uygulamada *(RuntimeStore* projesi), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:</span><span class="sxs-lookup"><span data-stu-id="857f0-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="857f0-223">Çalışma zamanı deposu konumunu bulmak için çalışma zamanı için ek bağımlılıklar dosya konumu ortam değişkenine `DOTNET_ADDITIONAL_DEPS` eklenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="857f0-224">Örnek uygulamada *(RuntimeStore* projesi), çalışma zamanı deposunu oluşturmak ve ek bağımlılıklar dosyası oluşturmak [powershell](/powershell/scripting/powershell-scripting) komut dosyası kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="857f0-225">Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanabildiğini örnekler için [bkz.](xref:fundamentals/environments)</span><span class="sxs-lookup"><span data-stu-id="857f0-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="857f0-226">**Dağıtım**</span><span class="sxs-lookup"><span data-stu-id="857f0-226">**Deployment**</span></span>

<span data-ttu-id="857f0-227">Çok makineli bir ortamda barındırma başlatma dağıtımını kolaylaştırmak için, örnek uygulama yayımlanmış çıktıda aşağıdakileri içeren bir *dağıtım* klasörü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="857f0-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="857f0-228">Barındırma başlangıç çalışma zamanı mağazası.</span><span class="sxs-lookup"><span data-stu-id="857f0-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="857f0-229">Barındırma başlangıç bağımlılıkları dosyası.</span><span class="sxs-lookup"><span data-stu-id="857f0-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="857f0-230">Bir PowerShell komut dosyası oluşturur veya `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_SHARED_STORE`değiştirir `DOTNET_ADDITIONAL_DEPS` , , , ve barındırma başlangıç etkinleştirme desteklemek için.</span><span class="sxs-lookup"><span data-stu-id="857f0-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="857f0-231">Komut dosyasını dağıtım sistemindeki yönetimpowershell komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="857f0-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="857f0-232">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="857f0-232">NuGet package</span></span>

<span data-ttu-id="857f0-233">Bir barındırma başlangıç geliştirme bir NuGet paketinde sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="857f0-234">Paketin bir `HostingStartup` özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="857f0-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="857f0-235">Paket tarafından sağlanan barındırma başlangıç türleri aşağıdaki yaklaşımlardan biri kullanılarak uygulamaya sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="857f0-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="857f0-236">Geliştirilmiş uygulamanın proje dosyası, uygulamanın proje dosyasındaki barındırma başlatma için bir paket başvurusu yapar (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="857f0-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="857f0-237">Derleme zamanı başvurusu yerinde olduğundan, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına *(.deps.json)* dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="857f0-238">Bu yaklaşım, [nuget.org](https://www.nuget.org/)için yayınlanan bir barındırma başlangıç montaj paketi için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="857f0-239">Barındırma başlatmanın bağımlılıklar dosyası, [Runtime store](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="857f0-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="857f0-240">NuGet paketleri ve çalışma zamanı mağazası hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="857f0-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="857f0-241">Çapraz Platform Araçları ile NuGet Paketi Nasıl Oluşturulur?</span><span class="sxs-lookup"><span data-stu-id="857f0-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="857f0-242">Yayımlama paketleri</span><span class="sxs-lookup"><span data-stu-id="857f0-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="857f0-243">Çalışma zamanı paket deposu</span><span class="sxs-lookup"><span data-stu-id="857f0-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="857f0-244">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="857f0-244">Project bin folder</span></span>

<span data-ttu-id="857f0-245">Barındırma başlatma geliştirmesi, geliştirilmiş uygulamada depo *gözü*tarafından dağıtılan bir derleme tarafından sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="857f0-246">Derleme tarafından sağlanan barındırma başlangıç türleri aşağıdaki yaklaşımlardan biri kullanılarak uygulamaya sunulmuştur:</span><span class="sxs-lookup"><span data-stu-id="857f0-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="857f0-247">Geliştirilmiş uygulamanın proje dosyası, barındırma başlatma (derleme zamanı başvurusu) için bir derleme başvurusu yapar.</span><span class="sxs-lookup"><span data-stu-id="857f0-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="857f0-248">Derleme zamanı başvurusu yerinde olduğundan, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına *(.deps.json)* dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="857f0-249">Bu yaklaşım, dağıtım senaryosu barındırma başlangıç derlemesi *(.dll* dosyası) için derleme zamanı başvurusu yapılması ve derlemenin aşağıdakilerden herhangi biri için taşınması gerektiğinde geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="857f0-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="857f0-250">Tüketen proje.</span><span class="sxs-lookup"><span data-stu-id="857f0-250">The consuming project.</span></span>
  * <span data-ttu-id="857f0-251">Tüketen proje tarafından erişilebilen bir konum.</span><span class="sxs-lookup"><span data-stu-id="857f0-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="857f0-252">Barındırma başlatmanın bağımlılıklar dosyası, [Runtime store](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="857f0-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="857f0-253">.NET Framework hedeflenirken, derleme varsayılan yük bağlamında yüklenebilir, bu da .NET Framework'de derlemenin aşağıdaki konumlardan herhangi birinde bulunduğu anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="857f0-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="857f0-254">Uygulama temel &ndash; yolu Uygulamanın yürütülebileceği kutu *klasörü* (*.exe*) bulunur.</span><span class="sxs-lookup"><span data-stu-id="857f0-254">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="857f0-255">Küresel Derleme Önbelleği &ndash; (GAC) GAC, birkaç .NET Framework uygulamasının paylaştığı derlemeleri depolar.</span><span class="sxs-lookup"><span data-stu-id="857f0-255">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="857f0-256">Daha fazla bilgi için bkz: .NET Framework belgelerindeki [genel derleme önbelleğine montaj yükleme.](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)</span><span class="sxs-lookup"><span data-stu-id="857f0-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="857f0-257">Örnek kod</span><span class="sxs-lookup"><span data-stu-id="857f0-257">Sample code</span></span>

<span data-ttu-id="857f0-258">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(nasıl indirilir)](xref:index#how-to-download-a-sample)başlangıç uygulama senaryolarını barındırmayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="857f0-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="857f0-259">İki barındırma başlangıç derlemesi (sınıf kitaplıkları) her biri bellek içi yapılandırma anahtar değeri çiftleri kümesi:</span><span class="sxs-lookup"><span data-stu-id="857f0-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="857f0-260">NuGet paketi (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="857f0-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="857f0-261">Sınıf kitaplığı (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="857f0-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="857f0-262">Barındırma başlatma, depotarafından dağıtılan bir derlemeden *(Başlangıç Tanılama)* etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="857f0-263">Derleme, başlangıçta uygulamaya tanılama bilgileri sağlayan iki ara yazılım ekler:</span><span class="sxs-lookup"><span data-stu-id="857f0-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="857f0-264">Kayıtlı hizmetler</span><span class="sxs-lookup"><span data-stu-id="857f0-264">Registered services</span></span>
  * <span data-ttu-id="857f0-265">Adres (şema, ana bilgisayar, yol tabanı, yol, sorgu dizesi)</span><span class="sxs-lookup"><span data-stu-id="857f0-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="857f0-266">Bağlantı (uzak IP, uzak bağlantı noktası, yerel IP, yerel bağlantı noktası, istemci sertifikası)</span><span class="sxs-lookup"><span data-stu-id="857f0-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="857f0-267">İstek üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="857f0-267">Request headers</span></span>
  * <span data-ttu-id="857f0-268">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="857f0-268">Environment variables</span></span>

<span data-ttu-id="857f0-269">Örneği çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="857f0-269">To run the sample:</span></span>

<span data-ttu-id="857f0-270">**NuGet paketinden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="857f0-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="857f0-271">*HostingStartupPackage* paketini [dotnet paketi](/dotnet/core/tools/dotnet-pack) komutu ile derle.</span><span class="sxs-lookup"><span data-stu-id="857f0-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="857f0-272">*HostingStartupPackage* paketinin montaj adını ortam değişkenine `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ekleyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="857f0-273">Uygulamayı derle ve çalıştır.</span><span class="sxs-lookup"><span data-stu-id="857f0-273">Compile and run the app.</span></span> <span data-ttu-id="857f0-274">Geliştirilmiş uygulamada bir paket başvurusu bulunur (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="857f0-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="857f0-275">Uygulamanın proje dosyasında a, `<PropertyGroup>` paket projenin çıktısını belirtir (*... /HostingStartupPackage/bin/Debug*) paket kaynağı olarak.</span><span class="sxs-lookup"><span data-stu-id="857f0-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="857f0-276">Bu, uygulamanın paketi [nuget.org](https://www.nuget.org/)yüklemeden paketini kullanmasına olanak tanır. Daha fazla bilgi için HostingStartupApp'ın proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="857f0-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="857f0-277">Dizin sayfası tarafından işlenen hizmet yapılandırma anahtar değerlerinin paket `ServiceKeyInjection.Configure` yöntemitarafından ayarlanan değerlerle eşleştirdiğini gözlendirin.</span><span class="sxs-lookup"><span data-stu-id="857f0-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="857f0-278">*HostingStartupPackage* projesinde değişiklik yapar ve yeniden derlerseniz, *HostingStartupApp'ın* yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="857f0-279">Yerel NuGet önbelleklerini temizlemek için aşağıdaki [dotnet nuget yerel komutunu](/dotnet/core/tools/dotnet-nuget-locals) uygulayın:</span><span class="sxs-lookup"><span data-stu-id="857f0-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="857f0-280">**Sınıf kitaplığından etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="857f0-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="857f0-281">*HostingStartupLibrary* sınıf kitaplığını [dotnet build](/dotnet/core/tools/dotnet-build) komutuyla derle.</span><span class="sxs-lookup"><span data-stu-id="857f0-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="857f0-282">Sınıf kitaplığı *derleme* sinin derleme adını çevre `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="857f0-283">*bin*-sınıf kitaplığı derlemesi'nin derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne *HostingStartupLibrary.dll* dosyasını kopyalayarak sınıf kitaplığı derlemesini uygulamaya dağıtın.</span><span class="sxs-lookup"><span data-stu-id="857f0-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="857f0-284">Uygulamayı derle ve çalıştır.</span><span class="sxs-lookup"><span data-stu-id="857f0-284">Compile and run the app.</span></span> <span data-ttu-id="857f0-285">Uygulamanın proje `<ItemGroup>` dosyasında sınıf kitaplığı derlemesi *(.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll)*(derleme zamanı başvurusu) başvurur.</span><span class="sxs-lookup"><span data-stu-id="857f0-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="857f0-286">Daha fazla bilgi için HostingStartupApp'ın proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="857f0-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="857f0-287">Dizin sayfası tarafından işlenen hizmet yapılandırma anahtar değerlerinin sınıf kitaplığı `ServiceKeyInjection.Configure` yöntemitarafından ayarlanan değerlerle eşleştirdiğini gözlendirin.</span><span class="sxs-lookup"><span data-stu-id="857f0-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="857f0-288">**Çalışma zamanı depotarafından dağıtılan bir derlemeden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="857f0-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="857f0-289">*StartupDiagnostics* *projesi, StartupDiagnostics.deps.json* dosyasını değiştirmek için [PowerShell'i](/powershell/scripting/powershell-scripting) kullanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="857f0-290">PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak Windows'da varsayılan olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="857f0-291">PowerShell'i diğer platformlarda elde etmek için [PowerShell'in çeşitli sürümlerini yükleme bölümüne](/powershell/scripting/install/installing-powershell)bakın.</span><span class="sxs-lookup"><span data-stu-id="857f0-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="857f0-292">*RuntimeStore* klasöründe *build.ps1* komut dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="857f0-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="857f0-293">Komut dosyası:</span><span class="sxs-lookup"><span data-stu-id="857f0-293">The script:</span></span>
   * <span data-ttu-id="857f0-294">`StartupDiagnostics` Paketi *obj\packages* klasöründe oluşturur.</span><span class="sxs-lookup"><span data-stu-id="857f0-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="857f0-295">`StartupDiagnostics` *Mağaza* klasöründe çalışma zamanı deposunu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="857f0-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="857f0-296">Komut `dotnet store` dosyasındaki komut, `win7-x64` Windows'a dağıtılan bir barındırma başlatma için [çalışma zamanı tanımlayıcısını (RID)](/dotnet/core/rid-catalog) kullanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="857f0-297">Barındırma başlatmayı farklı bir çalışma süresi için sağlarken, komut dosyasının 37.</span><span class="sxs-lookup"><span data-stu-id="857f0-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="857f0-298">Çalışma zamanı deposu `StartupDiagnostics` daha sonra montajın tüketileceği makinede kullanıcının veya sistemin çalışma zamanı deposuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="857f0-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="857f0-299">Montaj için `StartupDiagnostics` kullanıcı runtime mağaza yükleme yeri *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="857f0-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="857f0-300">*Ek Deps* klasöründe `additionalDeps` for `StartupDiagnostics` oluşturur.</span><span class="sxs-lookup"><span data-stu-id="857f0-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="857f0-301">Ek bağımlılıklar daha sonra kullanıcının veya sistemin ek bağımlılıklarına taşınır.</span><span class="sxs-lookup"><span data-stu-id="857f0-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="857f0-302">Kullanıcı `StartupDiagnostics` ek bağımlılıkları yükleme konumu *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="857f0-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="857f0-303">*deploy.ps1* dosyasını *dağıtım* klasörüne yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="857f0-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="857f0-304">*Dağıtım* klasöründe *deploy.ps1* komut dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="857f0-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="857f0-305">Komut dosyası ekler:</span><span class="sxs-lookup"><span data-stu-id="857f0-305">The script appends:</span></span>
   * <span data-ttu-id="857f0-306">`StartupDiagnostics`çevre `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` değişkenine.</span><span class="sxs-lookup"><span data-stu-id="857f0-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="857f0-307">Barındırma başlangıç bağımlılıkları yolu (RuntimeStore projesinin *dağıtım* klasöründe) ortam değişkenine. `DOTNET_ADDITIONAL_DEPS`</span><span class="sxs-lookup"><span data-stu-id="857f0-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="857f0-308">Çalışma zamanı deposu yolu (RuntimeStore projesinin *dağıtım* klasöründe) ortam değişkenine. `DOTNET_SHARED_STORE`</span><span class="sxs-lookup"><span data-stu-id="857f0-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="857f0-309">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="857f0-309">Run the sample app.</span></span>
1. <span data-ttu-id="857f0-310">Uygulamanın `/services` kayıtlı hizmetlerini görmek için bitiş noktasını isteyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="857f0-311">Tanılama `/diag` bilgilerini görmek için bitiş noktasını isteyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="857f0-312">(Barındırma <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> başlangıç) uygulaması, harici bir derlemeden başlangıçta bir uygulamaya geliştirmeler ekler.</span><span class="sxs-lookup"><span data-stu-id="857f0-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="857f0-313">Örneğin, harici bir kitaplık, bir uygulamaya ek yapılandırma sağlayıcıları veya hizmetleri sağlamak için barındırma başlangıç uygulamasını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="857f0-314">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="857f0-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="857f0-315">HostingBaşlangıç özniteliği</span><span class="sxs-lookup"><span data-stu-id="857f0-315">HostingStartup attribute</span></span>

<span data-ttu-id="857f0-316">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirmek üzere bir barındırma başlangıç derlemesinin varlığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="857f0-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="857f0-317">Giriş derlemesi veya `Startup` sınıfı içeren derleme öznitelik `HostingStartup` için otomatik olarak taranır.</span><span class="sxs-lookup"><span data-stu-id="857f0-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="857f0-318">Öznitelikleri aramak için `HostingStartup` derlemeler listesi [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)yapılandırmadan çalışma zamanında yüklenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="857f0-319">Keşif hariç tutmak için derlemeler listesi [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)yüklenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="857f0-320">Daha fazla bilgi için [Bkz. Web Host: Başlangıç Derlemelerini Barındırma](xref:fundamentals/host/web-host#hosting-startup-assemblies) ve [Web Barındırma: Başlangıç Hariç Derlemeleri Barındırma.](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)</span><span class="sxs-lookup"><span data-stu-id="857f0-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="857f0-321">Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad `StartupEnhancement`alanı .</span><span class="sxs-lookup"><span data-stu-id="857f0-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="857f0-322">Barındırma başlangıç kodunu içeren `StartupEnhancementHostingStartup`sınıf:</span><span class="sxs-lookup"><span data-stu-id="857f0-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="857f0-323">Öznitelik `HostingStartup` genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında yer alır.</span><span class="sxs-lookup"><span data-stu-id="857f0-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="857f0-324">Yüklü barındırma başlangıç derlemelerini keşfedin</span><span class="sxs-lookup"><span data-stu-id="857f0-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="857f0-325">Yüklü barındırma başlangıç derlemelerini keşfetmek için günlüğe kaydetmeyi etkinleştirin ve uygulamanın günlüklerini kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="857f0-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="857f0-326">Derlemeler günlüğe yüklenirken oluşan hatalar.</span><span class="sxs-lookup"><span data-stu-id="857f0-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="857f0-327">Yüklü barındırma başlangıç derlemeleri Hata Ayıklama düzeyinde günlüğe kaydedilir ve tüm hatalar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="857f0-328">Barındırma başlangıç montajlarının otomatik yüklemesini devre dışı</span><span class="sxs-lookup"><span data-stu-id="857f0-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="857f0-329">Barındırma başlangıç derlemelerinin otomatik yüklemesini devre dışı kullanabilirsiniz, aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="857f0-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="857f0-330">Tüm barındırma başlangıç derlemelerinin yüklenmesini önlemek için `true` `1`aşağıdakilerden birini ayarlayın veya:</span><span class="sxs-lookup"><span data-stu-id="857f0-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="857f0-331">[Hosting Başlangıç](xref:fundamentals/host/web-host#prevent-hosting-startup) ana bilgisayar yapılandırma ayarını engelleyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="857f0-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP`çevre değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="857f0-333">Belirli barındırma başlangıç derlemelerinin yüklenmesinden korunmak için, başlangıçta hariç tutmak için aşağıdakilerden birini yarı sütunlu sınırlı sayıda barındırma başlangıç derlemesi dizesine ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="857f0-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="857f0-334">[Barındırma Başlangıç Dışlama Derlemeleri](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) ana bilgisayar yapılandırma ayarı.</span><span class="sxs-lookup"><span data-stu-id="857f0-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="857f0-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`çevre değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="857f0-336">Hem ana bilgisayar yapılandırma ayarı hem de ortam değişkeni ayarlanırsa, ana bilgisayar ayarı davranışı denetler.</span><span class="sxs-lookup"><span data-stu-id="857f0-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="857f0-337">Ana bilgisayar ayarı veya ortam değişkenini kullanarak barındırma başlangıç derlemelerini devre dışı bırakmak, genel olarak montajı devre dışı bırakırsa ve bir uygulamanın çeşitli özelliklerini devre dışı bırakabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="857f0-338">Project</span><span class="sxs-lookup"><span data-stu-id="857f0-338">Project</span></span>

<span data-ttu-id="857f0-339">Aşağıdaki proje türlerinden birini içeren bir barındırma başlatma oluşturma:</span><span class="sxs-lookup"><span data-stu-id="857f0-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="857f0-340">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="857f0-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="857f0-341">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="857f0-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="857f0-342">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="857f0-342">Class library</span></span>

<span data-ttu-id="857f0-343">Bir barındırma başlangıç geliştirme bir sınıf kitaplığı sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="857f0-344">Kitaplık bir `HostingStartup` öznitelik içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="857f0-345">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) bir Razor Pages uygulaması, *HostingStartupApp*ve bir sınıf kütüphanesi, *HostingStartupLibrary*içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="857f0-346">Sınıf kitaplığı:</span><span class="sxs-lookup"><span data-stu-id="857f0-346">The class library:</span></span>

* <span data-ttu-id="857f0-347">Bir barındırma başlangıç `ServiceKeyInjection`sınıfı içerir, `IHostingStartup`hangi uygular.</span><span class="sxs-lookup"><span data-stu-id="857f0-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="857f0-348">`ServiceKeyInjection`bellek içi yapılandırma sağlayıcısını[(AddInMemoryCollection)](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)kullanarak uygulamanın yapılandırmasına bir çift hizmet dizeleri ekler.</span><span class="sxs-lookup"><span data-stu-id="857f0-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="857f0-349">Barındırma `HostingStartup` başlangıç adı alanını ve sınıfını tanımlayan bir öznitelik içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="857f0-350">Sınıfın `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi, bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirme eklemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="857f0-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="857f0-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="857f0-352">Uygulamanın Dizin sayfası, sınıf kitaplığı barındırma başlangıç derlemesi tarafından ayarlanan iki anahtar için yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="857f0-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="857f0-353">*HostingStartupApp/Sayfalar/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="857f0-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="857f0-354">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) da ayrı bir hosting başlangıç sağlayan bir NuGet paket projesi içerir, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="857f0-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="857f0-355">Paket, daha önce açıklanan sınıf kitaplığıyla aynı özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="857f0-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="857f0-356">Paket:</span><span class="sxs-lookup"><span data-stu-id="857f0-356">The package:</span></span>

* <span data-ttu-id="857f0-357">Bir barındırma başlangıç `ServiceKeyInjection`sınıfı içerir, `IHostingStartup`hangi uygular.</span><span class="sxs-lookup"><span data-stu-id="857f0-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="857f0-358">`ServiceKeyInjection`uygulamanın yapılandırmasına bir çift hizmet dizeleri ekler.</span><span class="sxs-lookup"><span data-stu-id="857f0-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="857f0-359">Bir `HostingStartup` öznitelik içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="857f0-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="857f0-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="857f0-361">Uygulamanın Dizin sayfası, paketin barındırma başlangıç derlemesi tarafından ayarlanan iki anahtar için yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="857f0-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="857f0-362">*HostingStartupApp/Sayfalar/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="857f0-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="857f0-363">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="857f0-363">Console app without an entry point</span></span>

<span data-ttu-id="857f0-364">*Bu yaklaşım ,.NET Framework için değil, yalnızca .NET Core uygulamaları için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="857f0-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="857f0-365">Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik bir `HostingStartup` barındırma başlatma geliştirmesi, öznitelik içeren bir giriş noktası olmayan bir konsol uygulamasında sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="857f0-366">Konsol uygulamasının yayımedilmesi, çalışma zamanı deposundan tüketilebilen bir barındırma başlangıç derlemesi üretir.</span><span class="sxs-lookup"><span data-stu-id="857f0-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="857f0-367">Giriş noktası olmayan bir konsol uygulaması bu işlemde kullanılır, çünkü:</span><span class="sxs-lookup"><span data-stu-id="857f0-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="857f0-368">Barındırma başlangıç derlemesinde barındırma başlangıç ını tüketmek için bir bağımlılık lar dosyası gereklidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="857f0-369">Bağımlılıklar dosyası, kitaplık değil, bir uygulamayayımlayarak üretilen çalıştırılabilir bir uygulama varlığıdır.</span><span class="sxs-lookup"><span data-stu-id="857f0-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="857f0-370">Kitaplık, paylaşılan çalışma süresini hedefleyen çalıştırılabilir bir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)doğrudan ekilemez.</span><span class="sxs-lookup"><span data-stu-id="857f0-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="857f0-371">Dinamik bir barındırma başlangıç oluşturulmasında:</span><span class="sxs-lookup"><span data-stu-id="857f0-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="857f0-372">Konsol uygulamasından bir giriş noktası olmadan bir barındırma başlangıç derlemesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="857f0-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="857f0-373">`IHostingStartup` Uygulamayı içeren bir sınıf içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="857f0-374">Uygulama sınıfını `IHostingStartup` tanımlamak için bir [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="857f0-375">Konsol uygulaması, barındırma başlatmanın bağımlılıklarını elde etmek için yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="857f0-376">Konsol uygulamasını yayımlamanın bir sonucu, kullanılmayan bağımlılıkların bağımlılıklar dosyasından kırpılmış olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="857f0-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="857f0-377">Bağımlılıklar dosyası, barındırma başlangıç derlemesinin çalışma zamanı konumunu ayarlamak üzere değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="857f0-378">Barındırma başlangıç derlemesi ve bağımlılıkları dosyası çalışma zamanı paket deposuna yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="857f0-379">Barındırma başlangıç derlemesini ve bağımlılıkları dosyasını keşfetmek için, bunlar bir çift ortam değişkeninde listelenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="857f0-380">Konsol uygulaması [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:</span><span class="sxs-lookup"><span data-stu-id="857f0-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="857f0-381">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfı yükleme `IHostingStartup` ve yürütme için <xref:Microsoft.AspNetCore.Hosting.IWebHost>bir uygulama olarak tanımlar.</span><span class="sxs-lookup"><span data-stu-id="857f0-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="857f0-382">Aşağıdaki örnekte, ad alanı `StartupEnhancement`ve sınıf: `StartupEnhancementHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="857f0-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="857f0-383">Bir sınıf `IHostingStartup`uygular.</span><span class="sxs-lookup"><span data-stu-id="857f0-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="857f0-384">Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi, bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirme eklemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="857f0-385">`IHostingStartup.Configure`barındırma başlangıç derlemesinde, kullanıcı `Startup.Configure` kodunda önceki çalışma süresine göre çağrılır ve bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından sağlanan herhangi bir yapılandırmanın üzerine yazmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="857f0-386">Bir `IHostingStartup` proje inşa ederken, bağımlılıklar dosyası (*.deps.json*) derlemenin `runtime` konumunu *çöp* kutusu klasörüne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="857f0-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="857f0-387">Dosyanın yalnızca bir bölümü gösterilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-387">Only part of the file is shown.</span></span> <span data-ttu-id="857f0-388">Örnekteki montaj `StartupEnhancement`adı.</span><span class="sxs-lookup"><span data-stu-id="857f0-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="857f0-389">Barındırma başlatma tarafından sağlanan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="857f0-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="857f0-390">Barındırma başlangıç yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak yapılandırmayı işlemek için iki yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="857f0-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="857f0-391">Uygulamanın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri çalıştırdıktan sonra yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın.</span><span class="sxs-lookup"><span data-stu-id="857f0-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="857f0-392">Başlangıç yapılandırmasını barındırma, bu yaklaşımı kullanarak uygulamanın yapılandırması üzerinde öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="857f0-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="857f0-393">Uygulamanın <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri yürütmeden önce yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın.</span><span class="sxs-lookup"><span data-stu-id="857f0-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="857f0-394">Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="857f0-395">Barındırma başlangıç derlemesini belirtin</span><span class="sxs-lookup"><span data-stu-id="857f0-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="857f0-396">Sınıf kitaplığı veya konsol uygulaması yla sağlanan barındırma başlatma için, ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="857f0-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="857f0-397">Ortam değişkeni, yarı kolonlu sınırlı bir derleme listesidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="857f0-398">Öznitelik için `HostingStartup` yalnızca barındırma başlangıç derlemeleri taranır.</span><span class="sxs-lookup"><span data-stu-id="857f0-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="857f0-399">Örnek uygulama, *HostingStartupApp*için, daha önce açıklanan barındırma başlangıçları keşfetmek için, ortam değişkeni aşağıdaki değere ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="857f0-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="857f0-400">Barındırma başlangıç derlemesi, [Barındırma Başlangıç Derlemeleri](xref:fundamentals/host/web-host#hosting-startup-assemblies) ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="857f0-401">Birden çok barındırma başlangıç derlemesi olduğunda, yöntemleri <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> derlemelerin listelenme sırasına göre yürütülür.</span><span class="sxs-lookup"><span data-stu-id="857f0-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="857f0-402">Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="857f0-402">Activation</span></span>

<span data-ttu-id="857f0-403">Başlangıç etkinleştirme barındırma seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="857f0-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="857f0-404">[Runtime store](#runtime-store) &ndash; Etkinleştirme etkinleştirme için derleme zamanı başvurusu gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="857f0-404">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="857f0-405">Örnek uygulama, barındırma başlatma başlatma ve bağımlılık dosyalarını çok makineli bir ortamda barındırma başlatmanın dağıtımını kolaylaştırmak için bir klasöre, *dağıtıma*yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="857f0-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="857f0-406">*Dağıtım* klasörü, barındırma başlatmayı etkinleştirmek için dağıtım sisteminde ortam değişkenleri oluşturan veya değiştiren bir PowerShell komut dosyası da içerir.</span><span class="sxs-lookup"><span data-stu-id="857f0-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="857f0-407">Etkinleştirme için gerekli zaman başvurularını derleme</span><span class="sxs-lookup"><span data-stu-id="857f0-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="857f0-408">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="857f0-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="857f0-409">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="857f0-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="857f0-410">Runtime deposu</span><span class="sxs-lookup"><span data-stu-id="857f0-410">Runtime store</span></span>

<span data-ttu-id="857f0-411">Barındırma başlangıç uygulaması çalışma [zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="857f0-412">Gelişmiş uygulama tarafından derleme zamanı derleme süresi başvurusu gerekmez.</span><span class="sxs-lookup"><span data-stu-id="857f0-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="857f0-413">Barındırma başlatma oluşturulduktan sonra, manifest proje dosyası ve [dotnet deposu](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir runtime deposu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="857f0-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="857f0-414">Örnek uygulamada *(RuntimeStore* projesi) aşağıdaki komut kullanılır:</span><span class="sxs-lookup"><span data-stu-id="857f0-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="857f0-415">Çalışma zamanı deposunu keşfetmek için, çalışma zamanı mağazasının konumu ortam `DOTNET_SHARED_STORE` değişkenine eklenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="857f0-416">**Barındırma başlatmanın bağımlılıklar dosyasını değiştirin ve yerleştirin**</span><span class="sxs-lookup"><span data-stu-id="857f0-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="857f0-417">Geliştirmeye paket başvurusu yapmadan geliştirmeyi etkinleştirmek için, çalışma süresine ek bağımlılıklar belirtin. `additionalDeps`</span><span class="sxs-lookup"><span data-stu-id="857f0-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="857f0-418">`additionalDeps`şunları yapmanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="857f0-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="857f0-419">Başlangıçta uygulamanın kendi *.deps.json* dosyasıyla birleştirmek için bir dizi ek *.deps.json* dosyası sağlayarak uygulamanın kitaplık grafiğini genişletin.</span><span class="sxs-lookup"><span data-stu-id="857f0-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="857f0-420">Barındırma başlangıç tertibatını kullanılabilir ve yüklenebilir hale getirin.</span><span class="sxs-lookup"><span data-stu-id="857f0-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="857f0-421">Ek bağımlılıklar dosyasını oluşturmak için önerilen yaklaşım:</span><span class="sxs-lookup"><span data-stu-id="857f0-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="857f0-422">Önceki `dotnet publish` bölümde başvurulan çalışma zamanı deposu bildirimi dosyasında yürütün.</span><span class="sxs-lookup"><span data-stu-id="857f0-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="857f0-423">Açıktan başvuruyu kitaplıklardan ve ortaya çıkan `runtime` *.deps.json* dosyasının bölümünden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="857f0-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="857f0-424">Örnek projede, `store.manifest/1.0.0` özellik `targets` ve `libraries` bölüm kaldırılır:</span><span class="sxs-lookup"><span data-stu-id="857f0-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="857f0-425">*.deps.json* dosyasını aşağıdaki konuma yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="857f0-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="857f0-426">`{ADDITIONAL DEPENDENCIES PATH}`&ndash; Ortam değişkenine `DOTNET_ADDITIONAL_DEPS` eklenen konum.</span><span class="sxs-lookup"><span data-stu-id="857f0-426">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="857f0-427">`{SHARED FRAMEWORK NAME}`&ndash; Bu ek bağımlılıklar dosyası için paylaşılan çerçeve gereklidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-427">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="857f0-428">`{SHARED FRAMEWORK VERSION}`&ndash; Minimum paylaşılan çerçeve sürümü.</span><span class="sxs-lookup"><span data-stu-id="857f0-428">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="857f0-429">`{ENHANCEMENT ASSEMBLY NAME}`&ndash; Geliştirmenin montaj adı.</span><span class="sxs-lookup"><span data-stu-id="857f0-429">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="857f0-430">Örnek uygulamada *(RuntimeStore* projesi), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:</span><span class="sxs-lookup"><span data-stu-id="857f0-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="857f0-431">Çalışma zamanı deposu konumunu bulmak için çalışma zamanı için ek bağımlılıklar dosya konumu ortam değişkenine `DOTNET_ADDITIONAL_DEPS` eklenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="857f0-432">Örnek uygulamada *(RuntimeStore* projesi), çalışma zamanı deposunu oluşturmak ve ek bağımlılıklar dosyası oluşturmak [powershell](/powershell/scripting/powershell-scripting) komut dosyası kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="857f0-433">Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanabildiğini örnekler için [bkz.](xref:fundamentals/environments)</span><span class="sxs-lookup"><span data-stu-id="857f0-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="857f0-434">**Dağıtım**</span><span class="sxs-lookup"><span data-stu-id="857f0-434">**Deployment**</span></span>

<span data-ttu-id="857f0-435">Çok makineli bir ortamda barındırma başlatma dağıtımını kolaylaştırmak için, örnek uygulama yayımlanmış çıktıda aşağıdakileri içeren bir *dağıtım* klasörü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="857f0-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="857f0-436">Barındırma başlangıç çalışma zamanı mağazası.</span><span class="sxs-lookup"><span data-stu-id="857f0-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="857f0-437">Barındırma başlangıç bağımlılıkları dosyası.</span><span class="sxs-lookup"><span data-stu-id="857f0-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="857f0-438">Bir PowerShell komut dosyası oluşturur veya `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_SHARED_STORE`değiştirir `DOTNET_ADDITIONAL_DEPS` , , , ve barındırma başlangıç etkinleştirme desteklemek için.</span><span class="sxs-lookup"><span data-stu-id="857f0-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="857f0-439">Komut dosyasını dağıtım sistemindeki yönetimpowershell komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="857f0-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="857f0-440">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="857f0-440">NuGet package</span></span>

<span data-ttu-id="857f0-441">Bir barındırma başlangıç geliştirme bir NuGet paketinde sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="857f0-442">Paketin bir `HostingStartup` özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="857f0-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="857f0-443">Paket tarafından sağlanan barındırma başlangıç türleri aşağıdaki yaklaşımlardan biri kullanılarak uygulamaya sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="857f0-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="857f0-444">Geliştirilmiş uygulamanın proje dosyası, uygulamanın proje dosyasındaki barındırma başlatma için bir paket başvurusu yapar (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="857f0-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="857f0-445">Derleme zamanı başvurusu yerinde olduğundan, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına *(.deps.json)* dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="857f0-446">Bu yaklaşım, [nuget.org](https://www.nuget.org/)için yayınlanan bir barındırma başlangıç montaj paketi için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="857f0-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="857f0-447">Barındırma başlatmanın bağımlılıklar dosyası, [Runtime store](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="857f0-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="857f0-448">NuGet paketleri ve çalışma zamanı mağazası hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="857f0-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="857f0-449">Çapraz Platform Araçları ile NuGet Paketi Nasıl Oluşturulur?</span><span class="sxs-lookup"><span data-stu-id="857f0-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="857f0-450">Yayımlama paketleri</span><span class="sxs-lookup"><span data-stu-id="857f0-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="857f0-451">Çalışma zamanı paket deposu</span><span class="sxs-lookup"><span data-stu-id="857f0-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="857f0-452">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="857f0-452">Project bin folder</span></span>

<span data-ttu-id="857f0-453">Barındırma başlatma geliştirmesi, geliştirilmiş uygulamada depo *gözü*tarafından dağıtılan bir derleme tarafından sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="857f0-454">Derleme tarafından sağlanan barındırma başlangıç türleri aşağıdaki yaklaşımlardan biri kullanılarak uygulamaya sunulmuştur:</span><span class="sxs-lookup"><span data-stu-id="857f0-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="857f0-455">Geliştirilmiş uygulamanın proje dosyası, barındırma başlatma (derleme zamanı başvurusu) için bir derleme başvurusu yapar.</span><span class="sxs-lookup"><span data-stu-id="857f0-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="857f0-456">Derleme zamanı başvurusu yerinde olduğundan, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına *(.deps.json)* dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="857f0-457">Bu yaklaşım, dağıtım senaryosu barındırma başlangıç derlemesi *(.dll* dosyası) için derleme zamanı başvurusu yapılması ve derlemenin aşağıdakilerden herhangi biri için taşınması gerektiğinde geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="857f0-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="857f0-458">Tüketen proje.</span><span class="sxs-lookup"><span data-stu-id="857f0-458">The consuming project.</span></span>
  * <span data-ttu-id="857f0-459">Tüketen proje tarafından erişilebilen bir konum.</span><span class="sxs-lookup"><span data-stu-id="857f0-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="857f0-460">Barındırma başlatmanın bağımlılıklar dosyası, [Runtime store](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="857f0-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="857f0-461">.NET Framework hedeflenirken, derleme varsayılan yük bağlamında yüklenebilir, bu da .NET Framework'de derlemenin aşağıdaki konumlardan herhangi birinde bulunduğu anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="857f0-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="857f0-462">Uygulama temel &ndash; yolu Uygulamanın yürütülebileceği kutu *klasörü* (*.exe*) bulunur.</span><span class="sxs-lookup"><span data-stu-id="857f0-462">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="857f0-463">Küresel Derleme Önbelleği &ndash; (GAC) GAC, birkaç .NET Framework uygulamasının paylaştığı derlemeleri depolar.</span><span class="sxs-lookup"><span data-stu-id="857f0-463">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="857f0-464">Daha fazla bilgi için bkz: .NET Framework belgelerindeki [genel derleme önbelleğine montaj yükleme.](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)</span><span class="sxs-lookup"><span data-stu-id="857f0-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="857f0-465">Örnek kod</span><span class="sxs-lookup"><span data-stu-id="857f0-465">Sample code</span></span>

<span data-ttu-id="857f0-466">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(nasıl indirilir)](xref:index#how-to-download-a-sample)başlangıç uygulama senaryolarını barındırmayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="857f0-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="857f0-467">İki barındırma başlangıç derlemesi (sınıf kitaplıkları) her biri bellek içi yapılandırma anahtar değeri çiftleri kümesi:</span><span class="sxs-lookup"><span data-stu-id="857f0-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="857f0-468">NuGet paketi (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="857f0-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="857f0-469">Sınıf kitaplığı (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="857f0-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="857f0-470">Barındırma başlatma, depotarafından dağıtılan bir derlemeden *(Başlangıç Tanılama)* etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="857f0-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="857f0-471">Derleme, başlangıçta uygulamaya tanılama bilgileri sağlayan iki ara yazılım ekler:</span><span class="sxs-lookup"><span data-stu-id="857f0-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="857f0-472">Kayıtlı hizmetler</span><span class="sxs-lookup"><span data-stu-id="857f0-472">Registered services</span></span>
  * <span data-ttu-id="857f0-473">Adres (şema, ana bilgisayar, yol tabanı, yol, sorgu dizesi)</span><span class="sxs-lookup"><span data-stu-id="857f0-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="857f0-474">Bağlantı (uzak IP, uzak bağlantı noktası, yerel IP, yerel bağlantı noktası, istemci sertifikası)</span><span class="sxs-lookup"><span data-stu-id="857f0-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="857f0-475">İstek üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="857f0-475">Request headers</span></span>
  * <span data-ttu-id="857f0-476">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="857f0-476">Environment variables</span></span>

<span data-ttu-id="857f0-477">Örneği çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="857f0-477">To run the sample:</span></span>

<span data-ttu-id="857f0-478">**NuGet paketinden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="857f0-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="857f0-479">*HostingStartupPackage* paketini [dotnet paketi](/dotnet/core/tools/dotnet-pack) komutu ile derle.</span><span class="sxs-lookup"><span data-stu-id="857f0-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="857f0-480">*HostingStartupPackage* paketinin montaj adını ortam değişkenine `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ekleyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="857f0-481">Uygulamayı derle ve çalıştır.</span><span class="sxs-lookup"><span data-stu-id="857f0-481">Compile and run the app.</span></span> <span data-ttu-id="857f0-482">Geliştirilmiş uygulamada bir paket başvurusu bulunur (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="857f0-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="857f0-483">Uygulamanın proje dosyasında a, `<PropertyGroup>` paket projenin çıktısını belirtir (*... /HostingStartupPackage/bin/Debug*) paket kaynağı olarak.</span><span class="sxs-lookup"><span data-stu-id="857f0-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="857f0-484">Bu, uygulamanın paketi [nuget.org](https://www.nuget.org/)yüklemeden paketini kullanmasına olanak tanır. Daha fazla bilgi için HostingStartupApp'ın proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="857f0-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="857f0-485">Dizin sayfası tarafından işlenen hizmet yapılandırma anahtar değerlerinin paket `ServiceKeyInjection.Configure` yöntemitarafından ayarlanan değerlerle eşleştirdiğini gözlendirin.</span><span class="sxs-lookup"><span data-stu-id="857f0-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="857f0-486">*HostingStartupPackage* projesinde değişiklik yapar ve yeniden derlerseniz, *HostingStartupApp'ın* yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="857f0-487">Yerel NuGet önbelleklerini temizlemek için aşağıdaki [dotnet nuget yerel komutunu](/dotnet/core/tools/dotnet-nuget-locals) uygulayın:</span><span class="sxs-lookup"><span data-stu-id="857f0-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="857f0-488">**Sınıf kitaplığından etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="857f0-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="857f0-489">*HostingStartupLibrary* sınıf kitaplığını [dotnet build](/dotnet/core/tools/dotnet-build) komutuyla derle.</span><span class="sxs-lookup"><span data-stu-id="857f0-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="857f0-490">Sınıf kitaplığı *derleme* sinin derleme adını çevre `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="857f0-491">*bin*-sınıf kitaplığı derlemesi'nin derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne *HostingStartupLibrary.dll* dosyasını kopyalayarak sınıf kitaplığı derlemesini uygulamaya dağıtın.</span><span class="sxs-lookup"><span data-stu-id="857f0-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="857f0-492">Uygulamayı derle ve çalıştır.</span><span class="sxs-lookup"><span data-stu-id="857f0-492">Compile and run the app.</span></span> <span data-ttu-id="857f0-493">Uygulamanın proje `<ItemGroup>` dosyasında sınıf kitaplığı derlemesi *(.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll)*(derleme zamanı başvurusu) başvurur.</span><span class="sxs-lookup"><span data-stu-id="857f0-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="857f0-494">Daha fazla bilgi için HostingStartupApp'ın proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="857f0-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="857f0-495">Dizin sayfası tarafından işlenen hizmet yapılandırma anahtar değerlerinin sınıf kitaplığı `ServiceKeyInjection.Configure` yöntemitarafından ayarlanan değerlerle eşleştirdiğini gözlendirin.</span><span class="sxs-lookup"><span data-stu-id="857f0-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="857f0-496">**Çalışma zamanı depotarafından dağıtılan bir derlemeden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="857f0-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="857f0-497">*StartupDiagnostics* *projesi, StartupDiagnostics.deps.json* dosyasını değiştirmek için [PowerShell'i](/powershell/scripting/powershell-scripting) kullanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="857f0-498">PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak Windows'da varsayılan olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="857f0-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="857f0-499">PowerShell'i diğer platformlarda elde etmek için [PowerShell'in çeşitli sürümlerini yükleme bölümüne](/powershell/scripting/install/installing-powershell)bakın.</span><span class="sxs-lookup"><span data-stu-id="857f0-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="857f0-500">*RuntimeStore* klasöründe *build.ps1* komut dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="857f0-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="857f0-501">Komut dosyası:</span><span class="sxs-lookup"><span data-stu-id="857f0-501">The script:</span></span>
   * <span data-ttu-id="857f0-502">`StartupDiagnostics` Paketi *obj\packages* klasöründe oluşturur.</span><span class="sxs-lookup"><span data-stu-id="857f0-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="857f0-503">`StartupDiagnostics` *Mağaza* klasöründe çalışma zamanı deposunu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="857f0-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="857f0-504">Komut `dotnet store` dosyasındaki komut, `win7-x64` Windows'a dağıtılan bir barındırma başlatma için [çalışma zamanı tanımlayıcısını (RID)](/dotnet/core/rid-catalog) kullanır.</span><span class="sxs-lookup"><span data-stu-id="857f0-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="857f0-505">Barındırma başlatmayı farklı bir çalışma süresi için sağlarken, komut dosyasının 37.</span><span class="sxs-lookup"><span data-stu-id="857f0-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="857f0-506">Çalışma zamanı deposu `StartupDiagnostics` daha sonra montajın tüketileceği makinede kullanıcının veya sistemin çalışma zamanı deposuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="857f0-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="857f0-507">Montaj için `StartupDiagnostics` kullanıcı runtime mağaza yükleme yeri *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="857f0-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="857f0-508">*Ek Deps* klasöründe `additionalDeps` for `StartupDiagnostics` oluşturur.</span><span class="sxs-lookup"><span data-stu-id="857f0-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="857f0-509">Ek bağımlılıklar daha sonra kullanıcının veya sistemin ek bağımlılıklarına taşınır.</span><span class="sxs-lookup"><span data-stu-id="857f0-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="857f0-510">Kullanıcı `StartupDiagnostics` ek bağımlılıkları yükleme konumu *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="857f0-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="857f0-511">*deploy.ps1* dosyasını *dağıtım* klasörüne yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="857f0-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="857f0-512">*Dağıtım* klasöründe *deploy.ps1* komut dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="857f0-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="857f0-513">Komut dosyası ekler:</span><span class="sxs-lookup"><span data-stu-id="857f0-513">The script appends:</span></span>
   * <span data-ttu-id="857f0-514">`StartupDiagnostics`çevre `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` değişkenine.</span><span class="sxs-lookup"><span data-stu-id="857f0-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="857f0-515">Barındırma başlangıç bağımlılıkları yolu (RuntimeStore projesinin *dağıtım* klasöründe) ortam değişkenine. `DOTNET_ADDITIONAL_DEPS`</span><span class="sxs-lookup"><span data-stu-id="857f0-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="857f0-516">Çalışma zamanı deposu yolu (RuntimeStore projesinin *dağıtım* klasöründe) ortam değişkenine. `DOTNET_SHARED_STORE`</span><span class="sxs-lookup"><span data-stu-id="857f0-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="857f0-517">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="857f0-517">Run the sample app.</span></span>
1. <span data-ttu-id="857f0-518">Uygulamanın `/services` kayıtlı hizmetlerini görmek için bitiş noktasını isteyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="857f0-519">Tanılama `/diag` bilgilerini görmek için bitiş noktasını isteyin.</span><span class="sxs-lookup"><span data-stu-id="857f0-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
