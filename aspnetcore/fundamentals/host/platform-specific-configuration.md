---
<span data-ttu-id="f1103-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f1103-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f1103-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f1103-102">'Blazor'</span></span>
- <span data-ttu-id="f1103-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f1103-103">'Identity'</span></span>
- <span data-ttu-id="f1103-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f1103-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f1103-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f1103-105">'Razor'</span></span>
- <span data-ttu-id="f1103-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f1103-106">'SignalR' uid:</span></span> 

---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="f1103-107">ASP.NET Core 'de barındırma başlangıç derlemelerini kullanın</span><span class="sxs-lookup"><span data-stu-id="f1103-107">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="f1103-108">[Palete kroni](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="f1103-108">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1103-109"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(Barındırma başlatma) uygulaması, bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler ekler.</span><span class="sxs-lookup"><span data-stu-id="f1103-109">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="f1103-110">Örneğin, bir dış kitaplık, bir uygulamaya ek yapılandırma sağlayıcıları veya hizmetler sağlamak için barındırma başlangıç uygulamasını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-110">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="f1103-111">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1103-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="f1103-112">HostingStartup özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1103-112">HostingStartup attribute</span></span>

<span data-ttu-id="f1103-113">[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirilecek bir barındırma başlangıç derlemesinin varlığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f1103-113">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="f1103-114">Giriş derlemesi veya sınıfı içeren derleme `Startup` öznitelik için otomatik olarak taranır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-114">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f1103-115">Öznitelikleri aramak için derlemelerin listesi `HostingStartup` [Webhostdefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)içindeki yapılandırmadan çalışma zamanında yüklenir.</span><span class="sxs-lookup"><span data-stu-id="f1103-115">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="f1103-116">Bulmadan dışlanacak derlemelerin listesi [Webhostdefaults. Hostingstartupexcludederlemelieskey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)öğesinden yüklendi.</span><span class="sxs-lookup"><span data-stu-id="f1103-116">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="f1103-117">Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad alanı `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="f1103-117">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="f1103-118">Barındırma başlangıç kodunu içeren sınıf `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="f1103-118">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f1103-119">`HostingStartup`Özniteliği genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında bulunur.</span><span class="sxs-lookup"><span data-stu-id="f1103-119">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="f1103-120">Yüklü barındırma başlangıç derlemelerini bul</span><span class="sxs-lookup"><span data-stu-id="f1103-120">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="f1103-121">Yüklü barındırma başlangıç derlemelerini öğrenmek için, günlüğü etkinleştirin ve uygulamanın günlüklerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-121">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="f1103-122">Derlemeler yüklenirken oluşan hatalar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-122">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="f1103-123">Yüklenen barındırma başlangıç derlemeleri hata ayıklama düzeyinde günlüğe kaydedilir ve tüm hatalar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-123">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="f1103-124">Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="f1103-124">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="f1103-125">Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırakmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f1103-125">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="f1103-126">Tüm barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini veya olarak ayarlayın `true` `1` :</span><span class="sxs-lookup"><span data-stu-id="f1103-126">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="f1103-127">Barındırma başlangıç ana bilgisayar yapılandırma ayarını önle:</span><span class="sxs-lookup"><span data-stu-id="f1103-127">Prevent Hosting Startup host configuration setting:</span></span>

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

  * <span data-ttu-id="f1103-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP`ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="f1103-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="f1103-129">Belirli barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini, başlangıçta dışlamak üzere, bir barındırma başlangıç bütünleştirilmiş kodlarının noktalı virgülle ayrılmış dizesine ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="f1103-129">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="f1103-130">Barındırma başlatma derlemeleri dışlama ana bilgisayar yapılandırma ayarı:</span><span class="sxs-lookup"><span data-stu-id="f1103-130">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

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

  * <span data-ttu-id="f1103-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="f1103-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="f1103-132">Ana bilgisayar yapılandırma ayarı ve ortam değişkeni ayarlanırsa, konak ayarı davranışı denetler.</span><span class="sxs-lookup"><span data-stu-id="f1103-132">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="f1103-133">Konak ayarını veya ortam değişkenini kullanarak başlatma derlemelerinin barındırılmasını devre dışı bırakmak, derlemeyi küresel olarak devre dışı bırakabilir ve bir uygulamanın çeşitli özelliklerini devre dışı bırakabilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-133">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="f1103-134">Project</span><span class="sxs-lookup"><span data-stu-id="f1103-134">Project</span></span>

<span data-ttu-id="f1103-135">Aşağıdaki proje türlerinden birini kullanarak bir barındırma başlatması oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f1103-135">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="f1103-136">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="f1103-136">Class library</span></span>](#class-library)
* [<span data-ttu-id="f1103-137">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="f1103-137">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="f1103-138">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="f1103-138">Class library</span></span>

<span data-ttu-id="f1103-139">Bir barındırma başlatma geliştirmesi, bir sınıf kitaplığında bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-139">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="f1103-140">Kitaplık bir özniteliği içerir `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-140">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f1103-141">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) , bir Razor Pages uygulaması, *Hostingstartupapp*ve bir sınıf kitaplığı, *hostingstartuplibrary*içerir.</span><span class="sxs-lookup"><span data-stu-id="f1103-141">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="f1103-142">Sınıf kitaplığı:</span><span class="sxs-lookup"><span data-stu-id="f1103-142">The class library:</span></span>

* <span data-ttu-id="f1103-143">Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-143">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f1103-144">`ServiceKeyInjection`bellek içi yapılandırma sağlayıcısını ([Addınmemorycollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) kullanarak uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.</span><span class="sxs-lookup"><span data-stu-id="f1103-144">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="f1103-145">`HostingStartup`Barındırma başlatmasının ad alanını ve sınıfını tanımlayan bir özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="f1103-145">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="f1103-146">`ServiceKeyInjection`Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-146">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="f1103-147">*Hostingstartuplibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f1103-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f1103-148">Uygulamanın dizin sayfası, sınıf kitaplığının barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="f1103-148">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="f1103-149">*Hostingstartupapp/Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f1103-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="f1103-150">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ayrıca ayrı bir barındırma başlatma, *Hostingstartuppackage*sağlayan bir NuGet paket projesi içerir.</span><span class="sxs-lookup"><span data-stu-id="f1103-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="f1103-151">Paket, daha önce açıklanan sınıf kitaplığıyla aynı özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f1103-151">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="f1103-152">Paket:</span><span class="sxs-lookup"><span data-stu-id="f1103-152">The package:</span></span>

* <span data-ttu-id="f1103-153">Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-153">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f1103-154">`ServiceKeyInjection`uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.</span><span class="sxs-lookup"><span data-stu-id="f1103-154">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="f1103-155">Bir `HostingStartup` özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="f1103-155">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f1103-156">*Hostingstartuppackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f1103-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f1103-157">Uygulamanın dizin sayfası, paketin barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="f1103-157">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="f1103-158">*Hostingstartupapp/Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f1103-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="f1103-159">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="f1103-159">Console app without an entry point</span></span>

<span data-ttu-id="f1103-160">*Bu yaklaşım, .NET Framework değil yalnızca .NET Core uygulamaları için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="f1103-160">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="f1103-161">Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik barındırma başlatma geliştirmesi, bir öznitelik içeren giriş noktası olmadan bir konsol uygulamasında sağlanabilmesi `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-161">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="f1103-162">Konsol uygulamasını yayımlamak, çalışma zamanı deposundan tüketilebilen bir barındırma başlangıç derlemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1103-162">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="f1103-163">Bu işlemde giriş noktası olmayan bir konsol uygulaması kullanılmıştır çünkü:</span><span class="sxs-lookup"><span data-stu-id="f1103-163">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="f1103-164">Barındırma başlangıç derlemesinde barındırma başlangıcını kullanmak için bir bağımlılıklar dosyası gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f1103-164">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="f1103-165">Bağımlılıklar dosyası, bir kitaplık değil bir uygulama yayımlamayla üretilen çalıştırılabilir bir uygulama varlıktır.</span><span class="sxs-lookup"><span data-stu-id="f1103-165">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="f1103-166">Bir kitaplık, paylaşılan çalışma zamanını hedefleyen bir çalıştırılabilir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)eklenemez.</span><span class="sxs-lookup"><span data-stu-id="f1103-166">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="f1103-167">Dinamik barındırma başlatma oluşturma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="f1103-167">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="f1103-168">Bir barındırma başlangıç derlemesi, konsol uygulamasından bir giriş noktası olmadan oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f1103-168">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="f1103-169">, Uygulamayı içeren bir sınıf içerir `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-169">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="f1103-170">Uygulama sınıfını tanımlamak için bir [Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-170">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="f1103-171">Konsol uygulaması, barındırma başlatmasının bağımlılıklarını almak için yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-171">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="f1103-172">Konsol uygulamasını yayımlamanın bir sonucu, kullanılmayan bağımlılıkların bağımlılıklar dosyasından kırpıllarıdır.</span><span class="sxs-lookup"><span data-stu-id="f1103-172">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="f1103-173">Bağımlılıklar dosyası, barındırma başlangıç derlemesinin çalışma zamanı konumunu ayarlamak için değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-173">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="f1103-174">Barındırma başlangıç derlemesi ve onun bağımlılıklar dosyası çalışma zamanı paket deposuna yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-174">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="f1103-175">Barındırma başlangıç derlemesini ve onun bağımlılıklar dosyasını öğrenmek için, ortam değişkenleri çiftinde listelenir.</span><span class="sxs-lookup"><span data-stu-id="f1103-175">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="f1103-176">Konsol uygulaması [Microsoft. AspNetCore. Hosting. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:</span><span class="sxs-lookup"><span data-stu-id="f1103-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="f1103-177">[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfını `IHostingStartup` oluştururken yükleme ve yürütme için uygulamasının bir uygulamasını tanımlar <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="f1103-177">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="f1103-178">Aşağıdaki örnekte, ad alanı `StartupEnhancement` ve sınıfı `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="f1103-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f1103-179">Bir sınıf uygular `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="f1103-180">Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-180">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="f1103-181">`IHostingStartup.Configure`barındırma başlangıç derlemesinde kullanıcı kodundan önce çalışma zamanı tarafından çağrılır `Startup.Configure` , bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından verilen yapılandırmanın üzerine yazılmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="f1103-182">Bir proje oluştururken `IHostingStartup` , bağımlılıklar dosyası (*. Deps. JSON*) `runtime` derlemenin konumunu *bin* klasörüne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f1103-182">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="f1103-183">Yalnızca dosyanın bir kısmı gösterilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-183">Only part of the file is shown.</span></span> <span data-ttu-id="f1103-184">Örnekteki derleme adı `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="f1103-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="f1103-185">Barındırma başlatma tarafından belirtilen yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f1103-185">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="f1103-186">Yapılandırma işlemi, barındırma başlatmasının yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak iki yaklaşımdan yararlanabilir:</span><span class="sxs-lookup"><span data-stu-id="f1103-186">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="f1103-187"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>Uygulamanın temsilcileri çalıştırıldıktan sonra yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="f1103-187">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f1103-188">Barındırma başlangıç yapılandırması, uygulamanın yapılandırmasına bu yaklaşımı kullanarak öncelik kazanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-188">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="f1103-189"><xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>Uygulamanın temsilcileri yürütmeden önce yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="f1103-189">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f1103-190">Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="f1103-190">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="f1103-191">Barındırma başlangıç derlemesini belirtin</span><span class="sxs-lookup"><span data-stu-id="f1103-191">Specify the hosting startup assembly</span></span>

<span data-ttu-id="f1103-192">Bir sınıf kitaplığı ya da konsol uygulaması tarafından sağlanan bir barındırma başlatması için, ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` .</span><span class="sxs-lookup"><span data-stu-id="f1103-192">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="f1103-193">Ortam değişkeni, derlemelerin noktalı virgülle ayrılmış listesidir.</span><span class="sxs-lookup"><span data-stu-id="f1103-193">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="f1103-194">Özniteliği için yalnızca barındırma başlangıç derlemeleri taranır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-194">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f1103-195">Daha önce açıklanan barındırma başlangıç pencerelerini öğrenmek için *Hostingstartupapp*örnek uygulaması için, ortam değişkeni aşağıdaki değere ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="f1103-195">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="f1103-196">Barındırma başlangıç bütünleştirilmiş kodları, barındırma başlangıç derlemeleri ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="f1103-196">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

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

<span data-ttu-id="f1103-197">Birden çok barındırma başlatması varsa, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemleri derlemelerin listelendiği sırada yürütülür.</span><span class="sxs-lookup"><span data-stu-id="f1103-197">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="f1103-198">Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f1103-198">Activation</span></span>

<span data-ttu-id="f1103-199">Başlatma başlangıç etkinleştirme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="f1103-199">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="f1103-200">[Çalışma zamanı deposu](#runtime-store): etkinleştirme etkinleştirme için derleme zamanı başvurusu gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f1103-200">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="f1103-201">Örnek uygulama, çok makineli bir ortamda barındırma başlatmasının dağıtımını kolaylaştırmak için barındırma başlangıç derlemesini ve bağımlılıklar dosyalarını bir klasöre, *dağıtımına*koyar.</span><span class="sxs-lookup"><span data-stu-id="f1103-201">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="f1103-202">*Dağıtım* klasörü Ayrıca, barındırma başlangıcını etkinleştirmek için dağıtım sistemindeki ortam değişkenlerini oluşturan veya değiştiren bir PowerShell betiği içerir.</span><span class="sxs-lookup"><span data-stu-id="f1103-202">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="f1103-203">Etkinleştirme için derleme zamanı başvurusu gerekiyor</span><span class="sxs-lookup"><span data-stu-id="f1103-203">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="f1103-204">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="f1103-204">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="f1103-205">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="f1103-205">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="f1103-206">Çalışma zamanı deposu</span><span class="sxs-lookup"><span data-stu-id="f1103-206">Runtime store</span></span>

<span data-ttu-id="f1103-207">Barındırma başlangıç uygulamasının [çalışma zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilmesi.</span><span class="sxs-lookup"><span data-stu-id="f1103-207">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="f1103-208">Derleme zamanı başvurusu, Gelişmiş uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="f1103-208">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="f1103-209">Barındırma başlatma oluşturulduktan sonra, bildirim proje dosyası ve [DotNet Store](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir çalışma zamanı deposu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f1103-209">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="f1103-210">Örnek uygulamada (*Runtimesstore* Projesi) aşağıdaki komut kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f1103-210">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="f1103-211">Çalışma zamanının çalışma zamanı deposunu bulması için, çalışma zamanı deposunun konumu `DOTNET_SHARED_STORE` ortam değişkenine eklenir.</span><span class="sxs-lookup"><span data-stu-id="f1103-211">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="f1103-212">**Barındırma başlatmasının bağımlılıklar dosyasını değiştirme ve yerleştirme**</span><span class="sxs-lookup"><span data-stu-id="f1103-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="f1103-213">Geliştirmede bir paket başvurusu olmadan geliştirmeyi etkinleştirmek için, ile çalışma zamanına ek bağımlılıklar belirtin `additionalDeps` .</span><span class="sxs-lookup"><span data-stu-id="f1103-213">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="f1103-214">`additionalDeps`şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="f1103-214">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="f1103-215">Başlangıçta uygulamanın kendi *. Deps* . JSON dosyasıyla birleştirilecek bir dizi ek *. Deps. JSON* dosyası sağlayarak uygulamanın kitaplık grafiğini genişletin.</span><span class="sxs-lookup"><span data-stu-id="f1103-215">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="f1103-216">Barındırma başlangıç derlemesini bulunabilir ve yüklenebilir hale getirin.</span><span class="sxs-lookup"><span data-stu-id="f1103-216">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="f1103-217">Ek bağımlılıklar dosyası oluşturmak için önerilen yaklaşım şunlardır:</span><span class="sxs-lookup"><span data-stu-id="f1103-217">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="f1103-218">`dotnet publish`Önceki bölümde başvurulan çalışma zamanı deposu bildirim dosyasında yürütün.</span><span class="sxs-lookup"><span data-stu-id="f1103-218">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="f1103-219">Bildirimlerin bildirim başvurusunu ve `runtime` sonuçta elde edilen *. Deps. JSON* dosyasının bölümünü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-219">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="f1103-220">Örnek projede, `store.manifest/1.0.0` özelliği `targets` ve `libraries` bölümünden kaldırılır:</span><span class="sxs-lookup"><span data-stu-id="f1103-220">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="f1103-221">*. Deps. JSON* dosyasını şu konuma yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1103-221">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="f1103-222">`{ADDITIONAL DEPENDENCIES PATH}`: Ortam değişkenine konum eklendi `DOTNET_ADDITIONAL_DEPS` .</span><span class="sxs-lookup"><span data-stu-id="f1103-222">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="f1103-223">`{SHARED FRAMEWORK NAME}`: Bu ek bağımlılıklar dosyası için paylaşılan çerçeve gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f1103-223">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="f1103-224">`{SHARED FRAMEWORK VERSION}`: En düşük paylaşılan çerçeve sürümü.</span><span class="sxs-lookup"><span data-stu-id="f1103-224">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="f1103-225">`{ENHANCEMENT ASSEMBLY NAME}`: Geliştirmesinin derleme adı.</span><span class="sxs-lookup"><span data-stu-id="f1103-225">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="f1103-226">Örnek uygulamada (*Runtimesbir* proje), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:</span><span class="sxs-lookup"><span data-stu-id="f1103-226">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="f1103-227">Çalışma zamanı depo konumunu bulması için, ek bağımlılıklar dosya konumu `DOTNET_ADDITIONAL_DEPS` ortam değişkenine eklenir.</span><span class="sxs-lookup"><span data-stu-id="f1103-227">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="f1103-228">Örnek uygulamada (*Runtimesbir* proje), çalışma zamanı deposunun oluşturulması ve ek bağımlılıklar dosyası oluşturulması bir [PowerShell](/powershell/scripting/powershell-scripting) betiği kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-228">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="f1103-229">Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanbileceğine ilişkin örnekler için, bkz. [birden çok ortam kullanma](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f1103-229">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f1103-230">**Dağıtım**</span><span class="sxs-lookup"><span data-stu-id="f1103-230">**Deployment**</span></span>

<span data-ttu-id="f1103-231">Çoklu makine ortamında bir barındırma başlatmasının dağıtımını kolaylaştırmak için, örnek uygulama, yayımlanan çıktıda şunları içeren bir *dağıtım* klasörü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f1103-231">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="f1103-232">Barındırma başlangıç çalışma zamanı deposu.</span><span class="sxs-lookup"><span data-stu-id="f1103-232">The hosting startup runtime store.</span></span>
* <span data-ttu-id="f1103-233">Barındırma başlangıç bağımlılıkları dosyası.</span><span class="sxs-lookup"><span data-stu-id="f1103-233">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="f1103-234">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_SHARED_STORE` `DOTNET_ADDITIONAL_DEPS` Barındırma başlangıcını etkinleştirmeyi desteklemek için, ve ' ı oluşturan veya değiştiren bir PowerShell betiği.</span><span class="sxs-lookup"><span data-stu-id="f1103-234">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="f1103-235">Betiği dağıtım sistemindeki bir yönetim PowerShell komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-235">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="f1103-236">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="f1103-236">NuGet package</span></span>

<span data-ttu-id="f1103-237">Bir NuGet paketinde barındırma başlatma geliştirmesi sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f1103-237">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="f1103-238">Pakette bir öznitelik vardır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-238">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="f1103-239">Paket tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="f1103-239">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="f1103-240">Gelişmiş uygulamanın proje dosyası, uygulamanın proje dosyasında (derleme zamanı başvurusu) barındırma başlatması için bir paket başvurusu yapar.</span><span class="sxs-lookup"><span data-stu-id="f1103-240">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="f1103-241">Derleme zamanı başvurusu yerinde olduğunda, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*. Deps. JSON*) dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-241">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f1103-242">Bu yaklaşım, [NuGet.org](https://www.nuget.org/)'e yayınlanan bir barındırma başlangıç derleme paketi için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f1103-242">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="f1103-243">Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="f1103-243">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="f1103-244">NuGet paketleri ve çalışma zamanı deposu hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="f1103-244">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="f1103-245">Platformlar arası araçlarla bir NuGet paketi oluşturma</span><span class="sxs-lookup"><span data-stu-id="f1103-245">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="f1103-246">Paketler yayımlanıyor</span><span class="sxs-lookup"><span data-stu-id="f1103-246">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="f1103-247">Çalışma zamanı paket deposu</span><span class="sxs-lookup"><span data-stu-id="f1103-247">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="f1103-248">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="f1103-248">Project bin folder</span></span>

<span data-ttu-id="f1103-249">Bir barındırma başlatma geliştirmesi, gelişmiş uygulamada, *bin*ile dağıtılan bir derleme tarafından sağlanıyor.</span><span class="sxs-lookup"><span data-stu-id="f1103-249">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="f1103-250">Derleme tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="f1103-250">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="f1103-251">Gelişmiş uygulamanın proje dosyası, barındırma başlatmaya bir derleme başvurusu yapar (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="f1103-251">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="f1103-252">Derleme zamanı başvurusu yerinde olduğunda, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*. Deps. JSON*) dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-252">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f1103-253">Bu yaklaşım, dağıtım senaryosu barındırma başlatmasının derlemesine (*. dll* dosyası) bir derleme zamanı başvurusu yapmak ve derlemeyi şu şekilde taşımak için çağırdığında geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="f1103-253">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="f1103-254">Tüketen proje.</span><span class="sxs-lookup"><span data-stu-id="f1103-254">The consuming project.</span></span>
  * <span data-ttu-id="f1103-255">Tüketim Projesi tarafından erişilebilen bir konum.</span><span class="sxs-lookup"><span data-stu-id="f1103-255">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="f1103-256">Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="f1103-256">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="f1103-257">.NET Framework hedeflenirken, derleme varsayılan yükleme bağlamında yüklenebilir olur; bu, .NET Framework, derlemenin aşağıdaki konumlardan birinde bulunduğu anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="f1103-257">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="f1103-258">Uygulama temel yolu: uygulamanın yürütülebilir dosyasının (*. exe*) bulunduğu *bin* klasörü.</span><span class="sxs-lookup"><span data-stu-id="f1103-258">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="f1103-259">Genel bütünleştirilmiş kod önbelleği (GAC): GAC, birkaç .NET Framework uygulamanın paylaştığı derlemeleri depolar.</span><span class="sxs-lookup"><span data-stu-id="f1103-259">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="f1103-260">Daha fazla bilgi için, bkz. [nasıl yapılır: bir derlemeyi genel derleme önbelleğine yüklemek](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) .NET Framework belgeleri.</span><span class="sxs-lookup"><span data-stu-id="f1103-260">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="f1103-261">Örnek kod</span><span class="sxs-lookup"><span data-stu-id="f1103-261">Sample code</span></span>

<span data-ttu-id="f1103-262">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample)), başlangıç uygulama senaryolarını barındırma gösterir:</span><span class="sxs-lookup"><span data-stu-id="f1103-262">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="f1103-263">İki barındırma başlangıç derlemesi (sınıf kitaplıkları) her biri bellek içi yapılandırma anahtar-değer çiftleri çiftini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f1103-263">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="f1103-264">NuGet paketi (*Hostingstartuppackage*)</span><span class="sxs-lookup"><span data-stu-id="f1103-264">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="f1103-265">Sınıf kitaplığı (*Hostingstartuplibrary*)</span><span class="sxs-lookup"><span data-stu-id="f1103-265">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="f1103-266">Bir barındırma başlatması, çalışma zamanı deposu tarafından dağıtılan bir derlemeden (*Startupdiagnostics*) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-266">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="f1103-267">Derleme, üzerinde tanılama bilgileri sağlayan, başlangıçta uygulamaya iki middlewares ekler:</span><span class="sxs-lookup"><span data-stu-id="f1103-267">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="f1103-268">Kayıtlı hizmetler</span><span class="sxs-lookup"><span data-stu-id="f1103-268">Registered services</span></span>
  * <span data-ttu-id="f1103-269">Adres (düzen, ana bilgisayar, yol tabanı, yol, sorgu dizesi)</span><span class="sxs-lookup"><span data-stu-id="f1103-269">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="f1103-270">Bağlantı (uzak IP, uzak bağlantı noktası, yerel IP, yerel bağlantı noktası, istemci sertifikası)</span><span class="sxs-lookup"><span data-stu-id="f1103-270">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="f1103-271">İstek üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="f1103-271">Request headers</span></span>
  * <span data-ttu-id="f1103-272">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="f1103-272">Environment variables</span></span>

<span data-ttu-id="f1103-273">Örneği çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="f1103-273">To run the sample:</span></span>

<span data-ttu-id="f1103-274">**NuGet paketinden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="f1103-274">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="f1103-275">,, [DotNet paketi](/dotnet/core/tools/dotnet-pack) komutuyla *hostingstartuppackage* paketini derleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-275">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="f1103-276">Paketin *Hostingstartuppackage* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-276">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f1103-277">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-277">Compile and run the app.</span></span> <span data-ttu-id="f1103-278">Gelişmiş uygulamada bir paket başvurusu vardır (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="f1103-278">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="f1103-279">`<PropertyGroup>`Uygulamanın proje dosyasındaki bir paket projenin çıkışını belirtir (*.. /HostingStartupPackage/bin/Debug*) bir paket kaynağı olarak.</span><span class="sxs-lookup"><span data-stu-id="f1103-279">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="f1103-280">Bu, uygulamanın paketi [NuGet.org](https://www.nuget.org/)'e yüklemeden paketi kullanmasına izin verir. Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="f1103-280">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f1103-281">Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, paketin yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f1103-281">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f1103-282">*Hostingstartuppackage* projesinde değişiklik yaparsanız ve yeniden derleyseniz, *Hostingstartupapp* ' ın yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-282">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="f1103-283">Yerel NuGet önbelleklerini temizlemek için aşağıdaki [DotNet NuGet Yereller](/dotnet/core/tools/dotnet-nuget-locals) komutunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="f1103-283">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="f1103-284">**Bir sınıf kitaplığından etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="f1103-284">**Activation from a class library**</span></span>

1. <span data-ttu-id="f1103-285">, [DotNet Build](/dotnet/core/tools/dotnet-build) komutuyla *hostingstartuplibrary* sınıf kitaplığını derleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-285">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="f1103-286">Sınıf kitaplığının *Hostingstartuplibrary* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-286">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f1103-287">*bin*- *hostingstartuplibrary. dll* dosyasını sınıf kitaplığının derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne kopyalayarak uygulamaya sınıf kitaplığının derlemesini dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f1103-287">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="f1103-288">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-288">Compile and run the app.</span></span> <span data-ttu-id="f1103-289">`<ItemGroup>`Uygulamanın proje dosyasındaki bir, sınıf kitaplığının derlemesine (*.\Bin\debug\netcoreapp3,\hostingstartuplibrary.dll*) (bir derleme zamanı başvurusu) başvurur.</span><span class="sxs-lookup"><span data-stu-id="f1103-289">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="f1103-290">Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="f1103-290">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="f1103-291">Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, sınıf kitaplığının yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f1103-291">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f1103-292">**Çalışma zamanı deposu tarafından dağıtılan bir derlemeden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="f1103-292">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="f1103-293">*Startupdiagnostics* projesi, *startupdiagnostics. Deps. JSON* dosyasını değiştirmek için [PowerShell](/powershell/scripting/powershell-scripting) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-293">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="f1103-294">PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak varsayılan olarak yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="f1103-294">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="f1103-295">Diğer platformlarda PowerShell 'i almak için bkz. [PowerShell 'in çeşitli sürümlerini yükleme](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="f1103-295">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="f1103-296">*Runtimesyürüme* klasöründe *Build. ps1* betiğini yürütün.</span><span class="sxs-lookup"><span data-stu-id="f1103-296">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="f1103-297">Betik:</span><span class="sxs-lookup"><span data-stu-id="f1103-297">The script:</span></span>
   * <span data-ttu-id="f1103-298">, `StartupDiagnostics` *Obj\packages* klasöründe paketi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1103-298">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="f1103-299">İçin çalışma zamanı deposunu `StartupDiagnostics` *Mağaza* klasöründe oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1103-299">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="f1103-300">`dotnet store`Betikteki komut, `win7-x64` Windows 'a dağıtılan bir barındırma başlatması için [çalışma zamanı tanımlayıcısı 'nı (RID)](/dotnet/core/rid-catalog) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-300">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="f1103-301">Farklı bir çalışma zamanı için barındırma başlangıcını sağlarken, betiğin 37. satırındaki doğru RID 'yi yerine koyun.</span><span class="sxs-lookup"><span data-stu-id="f1103-301">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="f1103-302">Çalışma zamanı deposu `StartupDiagnostics` daha sonra derlemenin tüketilebileceği makinede kullanıcının veya sisteminin çalışma zamanı deposuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="f1103-302">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="f1103-303">Derlemenin Kullanıcı çalışma zamanı deposu yüklemesi konumu `StartupDiagnostics` *. DotNet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/LIB/netcoreapp 3.0/startupdiagnostics. dll*' dir.</span><span class="sxs-lookup"><span data-stu-id="f1103-303">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="f1103-304">, `additionalDeps` `StartupDiagnostics` *Additionaldeps* klasöründe için öğesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1103-304">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="f1103-305">Ek bağımlılıklar daha sonra kullanıcının veya sistem ek bağımlılıklarına taşınır.</span><span class="sxs-lookup"><span data-stu-id="f1103-305">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="f1103-306">Kullanıcı `StartupDiagnostics` ek bağımlılıkları yüklemesi konumu *. DotNet/x64/additionalDeps/startupdiagnostics/Shared/Microsoft. Netcore. app/3.0.0/startupdiagnostics. Deps. JSON*' dır.</span><span class="sxs-lookup"><span data-stu-id="f1103-306">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="f1103-307">*Dağıtım* klasörüne *Deploy. ps1* dosyasını koyar.</span><span class="sxs-lookup"><span data-stu-id="f1103-307">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="f1103-308">*Dağıtım* klasöründe *Deploy. ps1* betiğini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-308">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="f1103-309">Betik şunu ekler:</span><span class="sxs-lookup"><span data-stu-id="f1103-309">The script appends:</span></span>
   * <span data-ttu-id="f1103-310">`StartupDiagnostics``ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="f1103-310">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="f1103-311">Barındırma başlangıç bağımlılıkları yolu (Runtimessımında projenin *dağıtım* klasöründe) `DOTNET_ADDITIONAL_DEPS` ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="f1103-311">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="f1103-312">Çalışma zamanı depolama yolu (Runtimes, projenin *dağıtım* klasöründe) `DOTNET_SHARED_STORE` ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="f1103-312">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="f1103-313">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-313">Run the sample app.</span></span>
1. <span data-ttu-id="f1103-314">`/services`Uygulamanın kayıtlı hizmetlerini görmek için uç nokta isteyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-314">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="f1103-315">`/diag`Tanılama bilgilerini görmek için uç nokta isteyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-315">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f1103-316"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(Barındırma başlatma) uygulaması, bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler ekler.</span><span class="sxs-lookup"><span data-stu-id="f1103-316">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="f1103-317">Örneğin, bir dış kitaplık, bir uygulamaya ek yapılandırma sağlayıcıları veya hizmetler sağlamak için barındırma başlangıç uygulamasını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-317">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="f1103-318">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1103-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="f1103-319">HostingStartup özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1103-319">HostingStartup attribute</span></span>

<span data-ttu-id="f1103-320">[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirilecek bir barındırma başlangıç derlemesinin varlığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f1103-320">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="f1103-321">Giriş derlemesi veya sınıfı içeren derleme `Startup` öznitelik için otomatik olarak taranır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-321">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f1103-322">Öznitelikleri aramak için derlemelerin listesi `HostingStartup` [Webhostdefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)içindeki yapılandırmadan çalışma zamanında yüklenir.</span><span class="sxs-lookup"><span data-stu-id="f1103-322">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="f1103-323">Bulmadan dışlanacak derlemelerin listesi [Webhostdefaults. Hostingstartupexcludederlemelieskey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)öğesinden yüklendi.</span><span class="sxs-lookup"><span data-stu-id="f1103-323">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="f1103-324">Daha fazla bilgi için bkz. [Web Konağı: barındırma başlangıç derlemeleri](xref:fundamentals/host/web-host#hosting-startup-assemblies) ve [Web Konağı: barındırma başlatma derlemeleri çıkarma](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="f1103-324">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="f1103-325">Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad alanı `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="f1103-325">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="f1103-326">Barındırma başlangıç kodunu içeren sınıf `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="f1103-326">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f1103-327">`HostingStartup`Özniteliği genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında bulunur.</span><span class="sxs-lookup"><span data-stu-id="f1103-327">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="f1103-328">Yüklü barındırma başlangıç derlemelerini bul</span><span class="sxs-lookup"><span data-stu-id="f1103-328">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="f1103-329">Yüklü barındırma başlangıç derlemelerini öğrenmek için, günlüğü etkinleştirin ve uygulamanın günlüklerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-329">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="f1103-330">Derlemeler yüklenirken oluşan hatalar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-330">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="f1103-331">Yüklenen barındırma başlangıç derlemeleri hata ayıklama düzeyinde günlüğe kaydedilir ve tüm hatalar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-331">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="f1103-332">Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="f1103-332">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="f1103-333">Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırakmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f1103-333">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="f1103-334">Tüm barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini veya olarak ayarlayın `true` `1` :</span><span class="sxs-lookup"><span data-stu-id="f1103-334">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="f1103-335">[Barındırma başlangıç](xref:fundamentals/host/web-host#prevent-hosting-startup) ana bilgisayar yapılandırma ayarını önleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-335">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="f1103-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP`ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="f1103-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="f1103-337">Belirli barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini, başlangıçta dışlamak üzere, bir barındırma başlangıç bütünleştirilmiş kodlarının noktalı virgülle ayrılmış dizesine ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="f1103-337">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="f1103-338">[Barındırma başlatma derlemeleri](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) ana bilgisayar yapılandırma ayarı.</span><span class="sxs-lookup"><span data-stu-id="f1103-338">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="f1103-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="f1103-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="f1103-340">Ana bilgisayar yapılandırma ayarı ve ortam değişkeni ayarlanırsa, konak ayarı davranışı denetler.</span><span class="sxs-lookup"><span data-stu-id="f1103-340">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="f1103-341">Konak ayarını veya ortam değişkenini kullanarak başlatma derlemelerinin barındırılmasını devre dışı bırakmak, derlemeyi küresel olarak devre dışı bırakabilir ve bir uygulamanın çeşitli özelliklerini devre dışı bırakabilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-341">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="f1103-342">Project</span><span class="sxs-lookup"><span data-stu-id="f1103-342">Project</span></span>

<span data-ttu-id="f1103-343">Aşağıdaki proje türlerinden birini kullanarak bir barındırma başlatması oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f1103-343">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="f1103-344">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="f1103-344">Class library</span></span>](#class-library)
* [<span data-ttu-id="f1103-345">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="f1103-345">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="f1103-346">Sınıf kitaplığı</span><span class="sxs-lookup"><span data-stu-id="f1103-346">Class library</span></span>

<span data-ttu-id="f1103-347">Bir barındırma başlatma geliştirmesi, bir sınıf kitaplığında bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-347">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="f1103-348">Kitaplık bir özniteliği içerir `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-348">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f1103-349">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) , bir Razor Pages uygulaması, *Hostingstartupapp*ve bir sınıf kitaplığı, *hostingstartuplibrary*içerir.</span><span class="sxs-lookup"><span data-stu-id="f1103-349">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="f1103-350">Sınıf kitaplığı:</span><span class="sxs-lookup"><span data-stu-id="f1103-350">The class library:</span></span>

* <span data-ttu-id="f1103-351">Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-351">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f1103-352">`ServiceKeyInjection`bellek içi yapılandırma sağlayıcısını ([Addınmemorycollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) kullanarak uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.</span><span class="sxs-lookup"><span data-stu-id="f1103-352">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="f1103-353">`HostingStartup`Barındırma başlatmasının ad alanını ve sınıfını tanımlayan bir özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="f1103-353">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="f1103-354">`ServiceKeyInjection`Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-354">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="f1103-355">*Hostingstartuplibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f1103-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f1103-356">Uygulamanın dizin sayfası, sınıf kitaplığının barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="f1103-356">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="f1103-357">*Hostingstartupapp/Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f1103-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="f1103-358">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ayrıca ayrı bir barındırma başlatma, *Hostingstartuppackage*sağlayan bir NuGet paket projesi içerir.</span><span class="sxs-lookup"><span data-stu-id="f1103-358">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="f1103-359">Paket, daha önce açıklanan sınıf kitaplığıyla aynı özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f1103-359">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="f1103-360">Paket:</span><span class="sxs-lookup"><span data-stu-id="f1103-360">The package:</span></span>

* <span data-ttu-id="f1103-361">Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-361">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f1103-362">`ServiceKeyInjection`uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.</span><span class="sxs-lookup"><span data-stu-id="f1103-362">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="f1103-363">Bir `HostingStartup` özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="f1103-363">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f1103-364">*Hostingstartuppackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="f1103-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f1103-365">Uygulamanın dizin sayfası, paketin barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:</span><span class="sxs-lookup"><span data-stu-id="f1103-365">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="f1103-366">*Hostingstartupapp/Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f1103-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="f1103-367">Giriş noktası olmayan konsol uygulaması</span><span class="sxs-lookup"><span data-stu-id="f1103-367">Console app without an entry point</span></span>

<span data-ttu-id="f1103-368">*Bu yaklaşım, .NET Framework değil yalnızca .NET Core uygulamaları için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="f1103-368">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="f1103-369">Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik barındırma başlatma geliştirmesi, bir öznitelik içeren giriş noktası olmadan bir konsol uygulamasında sağlanabilmesi `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-369">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="f1103-370">Konsol uygulamasını yayımlamak, çalışma zamanı deposundan tüketilebilen bir barındırma başlangıç derlemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1103-370">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="f1103-371">Bu işlemde giriş noktası olmayan bir konsol uygulaması kullanılmıştır çünkü:</span><span class="sxs-lookup"><span data-stu-id="f1103-371">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="f1103-372">Barındırma başlangıç derlemesinde barındırma başlangıcını kullanmak için bir bağımlılıklar dosyası gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f1103-372">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="f1103-373">Bağımlılıklar dosyası, bir kitaplık değil bir uygulama yayımlamayla üretilen çalıştırılabilir bir uygulama varlıktır.</span><span class="sxs-lookup"><span data-stu-id="f1103-373">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="f1103-374">Bir kitaplık, paylaşılan çalışma zamanını hedefleyen bir çalıştırılabilir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)eklenemez.</span><span class="sxs-lookup"><span data-stu-id="f1103-374">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="f1103-375">Dinamik barındırma başlatma oluşturma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="f1103-375">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="f1103-376">Bir barındırma başlangıç derlemesi, konsol uygulamasından bir giriş noktası olmadan oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f1103-376">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="f1103-377">, Uygulamayı içeren bir sınıf içerir `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-377">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="f1103-378">Uygulama sınıfını tanımlamak için bir [Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-378">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="f1103-379">Konsol uygulaması, barındırma başlatmasının bağımlılıklarını almak için yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-379">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="f1103-380">Konsol uygulamasını yayımlamanın bir sonucu, kullanılmayan bağımlılıkların bağımlılıklar dosyasından kırpıllarıdır.</span><span class="sxs-lookup"><span data-stu-id="f1103-380">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="f1103-381">Bağımlılıklar dosyası, barındırma başlangıç derlemesinin çalışma zamanı konumunu ayarlamak için değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-381">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="f1103-382">Barındırma başlangıç derlemesi ve onun bağımlılıklar dosyası çalışma zamanı paket deposuna yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-382">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="f1103-383">Barındırma başlangıç derlemesini ve onun bağımlılıklar dosyasını öğrenmek için, ortam değişkenleri çiftinde listelenir.</span><span class="sxs-lookup"><span data-stu-id="f1103-383">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="f1103-384">Konsol uygulaması [Microsoft. AspNetCore. Hosting. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:</span><span class="sxs-lookup"><span data-stu-id="f1103-384">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="f1103-385">[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfını `IHostingStartup` oluştururken yükleme ve yürütme için uygulamasının bir uygulamasını tanımlar <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="f1103-385">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="f1103-386">Aşağıdaki örnekte, ad alanı `StartupEnhancement` ve sınıfı `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="f1103-386">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f1103-387">Bir sınıf uygular `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-387">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="f1103-388">Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-388">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="f1103-389">`IHostingStartup.Configure`barındırma başlangıç derlemesinde kullanıcı kodundan önce çalışma zamanı tarafından çağrılır `Startup.Configure` , bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından verilen yapılandırmanın üzerine yazılmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-389">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="f1103-390">Bir proje oluştururken `IHostingStartup` , bağımlılıklar dosyası (*. Deps. JSON*) `runtime` derlemenin konumunu *bin* klasörüne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f1103-390">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="f1103-391">Yalnızca dosyanın bir kısmı gösterilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-391">Only part of the file is shown.</span></span> <span data-ttu-id="f1103-392">Örnekteki derleme adı `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="f1103-392">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="f1103-393">Barındırma başlatma tarafından belirtilen yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f1103-393">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="f1103-394">Yapılandırma işlemi, barındırma başlatmasının yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak iki yaklaşımdan yararlanabilir:</span><span class="sxs-lookup"><span data-stu-id="f1103-394">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="f1103-395"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>Uygulamanın temsilcileri çalıştırıldıktan sonra yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="f1103-395">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f1103-396">Barındırma başlangıç yapılandırması, uygulamanın yapılandırmasına bu yaklaşımı kullanarak öncelik kazanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-396">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="f1103-397"><xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>Uygulamanın temsilcileri yürütmeden önce yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="f1103-397">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f1103-398">Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="f1103-398">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="f1103-399">Barındırma başlangıç derlemesini belirtin</span><span class="sxs-lookup"><span data-stu-id="f1103-399">Specify the hosting startup assembly</span></span>

<span data-ttu-id="f1103-400">Bir sınıf kitaplığı ya da konsol uygulaması tarafından sağlanan bir barındırma başlatması için, ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` .</span><span class="sxs-lookup"><span data-stu-id="f1103-400">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="f1103-401">Ortam değişkeni, derlemelerin noktalı virgülle ayrılmış listesidir.</span><span class="sxs-lookup"><span data-stu-id="f1103-401">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="f1103-402">Özniteliği için yalnızca barındırma başlangıç derlemeleri taranır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-402">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f1103-403">Daha önce açıklanan barındırma başlangıç pencerelerini öğrenmek için *Hostingstartupapp*örnek uygulaması için, ortam değişkeni aşağıdaki değere ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="f1103-403">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="f1103-404">Barındırma başlangıç bütünleştirilmiş [kodları barındırma başlangıç derlemeleri](xref:fundamentals/host/web-host#hosting-startup-assemblies) ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-404">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="f1103-405">Birden çok barındırma başlatması varsa, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemleri derlemelerin listelendiği sırada yürütülür.</span><span class="sxs-lookup"><span data-stu-id="f1103-405">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="f1103-406">Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f1103-406">Activation</span></span>

<span data-ttu-id="f1103-407">Başlatma başlangıç etkinleştirme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="f1103-407">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="f1103-408">[Çalışma zamanı deposu](#runtime-store): etkinleştirme etkinleştirme için derleme zamanı başvurusu gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f1103-408">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="f1103-409">Örnek uygulama, çok makineli bir ortamda barındırma başlatmasının dağıtımını kolaylaştırmak için barındırma başlangıç derlemesini ve bağımlılıklar dosyalarını bir klasöre, *dağıtımına*koyar.</span><span class="sxs-lookup"><span data-stu-id="f1103-409">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="f1103-410">*Dağıtım* klasörü Ayrıca, barındırma başlangıcını etkinleştirmek için dağıtım sistemindeki ortam değişkenlerini oluşturan veya değiştiren bir PowerShell betiği içerir.</span><span class="sxs-lookup"><span data-stu-id="f1103-410">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="f1103-411">Etkinleştirme için derleme zamanı başvurusu gerekiyor</span><span class="sxs-lookup"><span data-stu-id="f1103-411">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="f1103-412">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="f1103-412">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="f1103-413">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="f1103-413">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="f1103-414">Çalışma zamanı deposu</span><span class="sxs-lookup"><span data-stu-id="f1103-414">Runtime store</span></span>

<span data-ttu-id="f1103-415">Barındırma başlangıç uygulamasının [çalışma zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilmesi.</span><span class="sxs-lookup"><span data-stu-id="f1103-415">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="f1103-416">Derleme zamanı başvurusu, Gelişmiş uygulama için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="f1103-416">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="f1103-417">Barındırma başlatma oluşturulduktan sonra, bildirim proje dosyası ve [DotNet Store](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir çalışma zamanı deposu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f1103-417">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="f1103-418">Örnek uygulamada (*Runtimesstore* Projesi) aşağıdaki komut kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f1103-418">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="f1103-419">Çalışma zamanının çalışma zamanı deposunu bulması için, çalışma zamanı deposunun konumu `DOTNET_SHARED_STORE` ortam değişkenine eklenir.</span><span class="sxs-lookup"><span data-stu-id="f1103-419">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="f1103-420">**Barındırma başlatmasının bağımlılıklar dosyasını değiştirme ve yerleştirme**</span><span class="sxs-lookup"><span data-stu-id="f1103-420">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="f1103-421">Geliştirmede bir paket başvurusu olmadan geliştirmeyi etkinleştirmek için, ile çalışma zamanına ek bağımlılıklar belirtin `additionalDeps` .</span><span class="sxs-lookup"><span data-stu-id="f1103-421">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="f1103-422">`additionalDeps`şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="f1103-422">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="f1103-423">Başlangıçta uygulamanın kendi *. Deps* . JSON dosyasıyla birleştirilecek bir dizi ek *. Deps. JSON* dosyası sağlayarak uygulamanın kitaplık grafiğini genişletin.</span><span class="sxs-lookup"><span data-stu-id="f1103-423">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="f1103-424">Barındırma başlangıç derlemesini bulunabilir ve yüklenebilir hale getirin.</span><span class="sxs-lookup"><span data-stu-id="f1103-424">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="f1103-425">Ek bağımlılıklar dosyası oluşturmak için önerilen yaklaşım şunlardır:</span><span class="sxs-lookup"><span data-stu-id="f1103-425">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="f1103-426">`dotnet publish`Önceki bölümde başvurulan çalışma zamanı deposu bildirim dosyasında yürütün.</span><span class="sxs-lookup"><span data-stu-id="f1103-426">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="f1103-427">Bildirimlerin bildirim başvurusunu ve `runtime` sonuçta elde edilen *. Deps. JSON* dosyasının bölümünü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-427">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="f1103-428">Örnek projede, `store.manifest/1.0.0` özelliği `targets` ve `libraries` bölümünden kaldırılır:</span><span class="sxs-lookup"><span data-stu-id="f1103-428">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="f1103-429">*. Deps. JSON* dosyasını şu konuma yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1103-429">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="f1103-430">`{ADDITIONAL DEPENDENCIES PATH}`: Ortam değişkenine konum eklendi `DOTNET_ADDITIONAL_DEPS` .</span><span class="sxs-lookup"><span data-stu-id="f1103-430">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="f1103-431">`{SHARED FRAMEWORK NAME}`: Bu ek bağımlılıklar dosyası için paylaşılan çerçeve gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f1103-431">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="f1103-432">`{SHARED FRAMEWORK VERSION}`: En düşük paylaşılan çerçeve sürümü.</span><span class="sxs-lookup"><span data-stu-id="f1103-432">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="f1103-433">`{ENHANCEMENT ASSEMBLY NAME}`: Geliştirmesinin derleme adı.</span><span class="sxs-lookup"><span data-stu-id="f1103-433">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="f1103-434">Örnek uygulamada (*Runtimesbir* proje), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:</span><span class="sxs-lookup"><span data-stu-id="f1103-434">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="f1103-435">Çalışma zamanı depo konumunu bulması için, ek bağımlılıklar dosya konumu `DOTNET_ADDITIONAL_DEPS` ortam değişkenine eklenir.</span><span class="sxs-lookup"><span data-stu-id="f1103-435">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="f1103-436">Örnek uygulamada (*Runtimesbir* proje), çalışma zamanı deposunun oluşturulması ve ek bağımlılıklar dosyası oluşturulması bir [PowerShell](/powershell/scripting/powershell-scripting) betiği kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-436">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="f1103-437">Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanbileceğine ilişkin örnekler için, bkz. [birden çok ortam kullanma](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f1103-437">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f1103-438">**Dağıtım**</span><span class="sxs-lookup"><span data-stu-id="f1103-438">**Deployment**</span></span>

<span data-ttu-id="f1103-439">Çoklu makine ortamında bir barındırma başlatmasının dağıtımını kolaylaştırmak için, örnek uygulama, yayımlanan çıktıda şunları içeren bir *dağıtım* klasörü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f1103-439">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="f1103-440">Barındırma başlangıç çalışma zamanı deposu.</span><span class="sxs-lookup"><span data-stu-id="f1103-440">The hosting startup runtime store.</span></span>
* <span data-ttu-id="f1103-441">Barındırma başlangıç bağımlılıkları dosyası.</span><span class="sxs-lookup"><span data-stu-id="f1103-441">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="f1103-442">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_SHARED_STORE` `DOTNET_ADDITIONAL_DEPS` Barındırma başlangıcını etkinleştirmeyi desteklemek için, ve ' ı oluşturan veya değiştiren bir PowerShell betiği.</span><span class="sxs-lookup"><span data-stu-id="f1103-442">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="f1103-443">Betiği dağıtım sistemindeki bir yönetim PowerShell komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-443">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="f1103-444">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="f1103-444">NuGet package</span></span>

<span data-ttu-id="f1103-445">Bir NuGet paketinde barındırma başlatma geliştirmesi sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f1103-445">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="f1103-446">Pakette bir öznitelik vardır `HostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="f1103-446">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="f1103-447">Paket tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="f1103-447">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="f1103-448">Gelişmiş uygulamanın proje dosyası, uygulamanın proje dosyasında (derleme zamanı başvurusu) barındırma başlatması için bir paket başvurusu yapar.</span><span class="sxs-lookup"><span data-stu-id="f1103-448">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="f1103-449">Derleme zamanı başvurusu yerinde olduğunda, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*. Deps. JSON*) dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-449">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f1103-450">Bu yaklaşım, [NuGet.org](https://www.nuget.org/)'e yayınlanan bir barındırma başlangıç derleme paketi için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f1103-450">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="f1103-451">Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="f1103-451">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="f1103-452">NuGet paketleri ve çalışma zamanı deposu hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="f1103-452">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="f1103-453">Platformlar arası araçlarla bir NuGet paketi oluşturma</span><span class="sxs-lookup"><span data-stu-id="f1103-453">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="f1103-454">Paketler yayımlanıyor</span><span class="sxs-lookup"><span data-stu-id="f1103-454">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="f1103-455">Çalışma zamanı paket deposu</span><span class="sxs-lookup"><span data-stu-id="f1103-455">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="f1103-456">Proje bin klasörü</span><span class="sxs-lookup"><span data-stu-id="f1103-456">Project bin folder</span></span>

<span data-ttu-id="f1103-457">Bir barındırma başlatma geliştirmesi, gelişmiş uygulamada, *bin*ile dağıtılan bir derleme tarafından sağlanıyor.</span><span class="sxs-lookup"><span data-stu-id="f1103-457">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="f1103-458">Derleme tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="f1103-458">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="f1103-459">Gelişmiş uygulamanın proje dosyası, barındırma başlatmaya bir derleme başvurusu yapar (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="f1103-459">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="f1103-460">Derleme zamanı başvurusu yerinde olduğunda, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*. Deps. JSON*) dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-460">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f1103-461">Bu yaklaşım, dağıtım senaryosu barındırma başlatmasının derlemesine (*. dll* dosyası) bir derleme zamanı başvurusu yapmak ve derlemeyi şu şekilde taşımak için çağırdığında geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="f1103-461">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="f1103-462">Tüketen proje.</span><span class="sxs-lookup"><span data-stu-id="f1103-462">The consuming project.</span></span>
  * <span data-ttu-id="f1103-463">Tüketim Projesi tarafından erişilebilen bir konum.</span><span class="sxs-lookup"><span data-stu-id="f1103-463">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="f1103-464">Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).</span><span class="sxs-lookup"><span data-stu-id="f1103-464">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="f1103-465">.NET Framework hedeflenirken, derleme varsayılan yükleme bağlamında yüklenebilir olur; bu, .NET Framework, derlemenin aşağıdaki konumlardan birinde bulunduğu anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="f1103-465">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="f1103-466">Uygulama temel yolu: uygulamanın yürütülebilir dosyasının (*. exe*) bulunduğu *bin* klasörü.</span><span class="sxs-lookup"><span data-stu-id="f1103-466">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="f1103-467">Genel bütünleştirilmiş kod önbelleği (GAC): GAC, birkaç .NET Framework uygulamanın paylaştığı derlemeleri depolar.</span><span class="sxs-lookup"><span data-stu-id="f1103-467">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="f1103-468">Daha fazla bilgi için, bkz. [nasıl yapılır: bir derlemeyi genel derleme önbelleğine yüklemek](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) .NET Framework belgeleri.</span><span class="sxs-lookup"><span data-stu-id="f1103-468">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="f1103-469">Örnek kod</span><span class="sxs-lookup"><span data-stu-id="f1103-469">Sample code</span></span>

<span data-ttu-id="f1103-470">[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample)), başlangıç uygulama senaryolarını barındırma gösterir:</span><span class="sxs-lookup"><span data-stu-id="f1103-470">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="f1103-471">İki barındırma başlangıç derlemesi (sınıf kitaplıkları) her biri bellek içi yapılandırma anahtar-değer çiftleri çiftini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f1103-471">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="f1103-472">NuGet paketi (*Hostingstartuppackage*)</span><span class="sxs-lookup"><span data-stu-id="f1103-472">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="f1103-473">Sınıf kitaplığı (*Hostingstartuplibrary*)</span><span class="sxs-lookup"><span data-stu-id="f1103-473">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="f1103-474">Bir barındırma başlatması, çalışma zamanı deposu tarafından dağıtılan bir derlemeden (*Startupdiagnostics*) etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1103-474">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="f1103-475">Derleme, üzerinde tanılama bilgileri sağlayan, başlangıçta uygulamaya iki middlewares ekler:</span><span class="sxs-lookup"><span data-stu-id="f1103-475">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="f1103-476">Kayıtlı hizmetler</span><span class="sxs-lookup"><span data-stu-id="f1103-476">Registered services</span></span>
  * <span data-ttu-id="f1103-477">Adres (düzen, ana bilgisayar, yol tabanı, yol, sorgu dizesi)</span><span class="sxs-lookup"><span data-stu-id="f1103-477">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="f1103-478">Bağlantı (uzak IP, uzak bağlantı noktası, yerel IP, yerel bağlantı noktası, istemci sertifikası)</span><span class="sxs-lookup"><span data-stu-id="f1103-478">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="f1103-479">İstek üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="f1103-479">Request headers</span></span>
  * <span data-ttu-id="f1103-480">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="f1103-480">Environment variables</span></span>

<span data-ttu-id="f1103-481">Örneği çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="f1103-481">To run the sample:</span></span>

<span data-ttu-id="f1103-482">**NuGet paketinden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="f1103-482">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="f1103-483">,, [DotNet paketi](/dotnet/core/tools/dotnet-pack) komutuyla *hostingstartuppackage* paketini derleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-483">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="f1103-484">Paketin *Hostingstartuppackage* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-484">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f1103-485">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-485">Compile and run the app.</span></span> <span data-ttu-id="f1103-486">Gelişmiş uygulamada bir paket başvurusu vardır (derleme zamanı başvurusu).</span><span class="sxs-lookup"><span data-stu-id="f1103-486">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="f1103-487">`<PropertyGroup>`Uygulamanın proje dosyasındaki bir paket projenin çıkışını belirtir (*.. /HostingStartupPackage/bin/Debug*) bir paket kaynağı olarak.</span><span class="sxs-lookup"><span data-stu-id="f1103-487">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="f1103-488">Bu, uygulamanın paketi [NuGet.org](https://www.nuget.org/)'e yüklemeden paketi kullanmasına izin verir. Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="f1103-488">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f1103-489">Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, paketin yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f1103-489">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f1103-490">*Hostingstartuppackage* projesinde değişiklik yaparsanız ve yeniden derleyseniz, *Hostingstartupapp* ' ın yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-490">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="f1103-491">Yerel NuGet önbelleklerini temizlemek için aşağıdaki [DotNet NuGet Yereller](/dotnet/core/tools/dotnet-nuget-locals) komutunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="f1103-491">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="f1103-492">**Bir sınıf kitaplığından etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="f1103-492">**Activation from a class library**</span></span>

1. <span data-ttu-id="f1103-493">, [DotNet Build](/dotnet/core/tools/dotnet-build) komutuyla *hostingstartuplibrary* sınıf kitaplığını derleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-493">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="f1103-494">Sınıf kitaplığının *Hostingstartuplibrary* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-494">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f1103-495">*bin*- *hostingstartuplibrary. dll* dosyasını sınıf kitaplığının derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne kopyalayarak uygulamaya sınıf kitaplığının derlemesini dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f1103-495">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="f1103-496">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-496">Compile and run the app.</span></span> <span data-ttu-id="f1103-497">`<ItemGroup>`Uygulamanın proje dosyasındaki bir, sınıf kitaplığının derlemesine (*.\Bin\debug\netcoreapp2,\hostingstartuplibrary.dll*) (bir derleme zamanı başvurusu) başvurur.</span><span class="sxs-lookup"><span data-stu-id="f1103-497">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="f1103-498">Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.</span><span class="sxs-lookup"><span data-stu-id="f1103-498">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="f1103-499">Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, sınıf kitaplığının yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f1103-499">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f1103-500">**Çalışma zamanı deposu tarafından dağıtılan bir derlemeden etkinleştirme**</span><span class="sxs-lookup"><span data-stu-id="f1103-500">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="f1103-501">*Startupdiagnostics* projesi, *startupdiagnostics. Deps. JSON* dosyasını değiştirmek için [PowerShell](/powershell/scripting/powershell-scripting) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-501">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="f1103-502">PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak varsayılan olarak yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="f1103-502">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="f1103-503">Diğer platformlarda PowerShell 'i almak için bkz. [PowerShell 'in çeşitli sürümlerini yükleme](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="f1103-503">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="f1103-504">*Runtimesyürüme* klasöründe *Build. ps1* betiğini yürütün.</span><span class="sxs-lookup"><span data-stu-id="f1103-504">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="f1103-505">Betik:</span><span class="sxs-lookup"><span data-stu-id="f1103-505">The script:</span></span>
   * <span data-ttu-id="f1103-506">, `StartupDiagnostics` *Obj\packages* klasöründe paketi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1103-506">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="f1103-507">İçin çalışma zamanı deposunu `StartupDiagnostics` *Mağaza* klasöründe oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1103-507">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="f1103-508">`dotnet store`Betikteki komut, `win7-x64` Windows 'a dağıtılan bir barındırma başlatması için [çalışma zamanı tanımlayıcısı 'nı (RID)](/dotnet/core/rid-catalog) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1103-508">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="f1103-509">Farklı bir çalışma zamanı için barındırma başlangıcını sağlarken, betiğin 37. satırındaki doğru RID 'yi yerine koyun.</span><span class="sxs-lookup"><span data-stu-id="f1103-509">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="f1103-510">Çalışma zamanı deposu `StartupDiagnostics` daha sonra derlemenin tüketilebileceği makinede kullanıcının veya sisteminin çalışma zamanı deposuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="f1103-510">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="f1103-511">Derlemenin Kullanıcı çalışma zamanı deposu yüklemesi konumu `StartupDiagnostics` *. DotNet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/LIB/netcoreapp 2.2/startupdiagnostics. dll*' dir.</span><span class="sxs-lookup"><span data-stu-id="f1103-511">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="f1103-512">, `additionalDeps` `StartupDiagnostics` *Additionaldeps* klasöründe için öğesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1103-512">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="f1103-513">Ek bağımlılıklar daha sonra kullanıcının veya sistem ek bağımlılıklarına taşınır.</span><span class="sxs-lookup"><span data-stu-id="f1103-513">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="f1103-514">Kullanıcı `StartupDiagnostics` ek bağımlılıkları yüklemesi konumu *. DotNet/x64/additionalDeps/startupdiagnostics/Shared/Microsoft. Netcore. App/2.2.0/startupdiagnostics. Deps. JSON*' dır.</span><span class="sxs-lookup"><span data-stu-id="f1103-514">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="f1103-515">*Dağıtım* klasörüne *Deploy. ps1* dosyasını koyar.</span><span class="sxs-lookup"><span data-stu-id="f1103-515">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="f1103-516">*Dağıtım* klasöründe *Deploy. ps1* betiğini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-516">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="f1103-517">Betik şunu ekler:</span><span class="sxs-lookup"><span data-stu-id="f1103-517">The script appends:</span></span>
   * <span data-ttu-id="f1103-518">`StartupDiagnostics``ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="f1103-518">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="f1103-519">Barındırma başlangıç bağımlılıkları yolu (Runtimessımında projenin *dağıtım* klasöründe) `DOTNET_ADDITIONAL_DEPS` ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="f1103-519">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="f1103-520">Çalışma zamanı depolama yolu (Runtimes, projenin *dağıtım* klasöründe) `DOTNET_SHARED_STORE` ortam değişkenine.</span><span class="sxs-lookup"><span data-stu-id="f1103-520">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="f1103-521">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1103-521">Run the sample app.</span></span>
1. <span data-ttu-id="f1103-522">`/services`Uygulamanın kayıtlı hizmetlerini görmek için uç nokta isteyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-522">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="f1103-523">`/diag`Tanılama bilgilerini görmek için uç nokta isteyin.</span><span class="sxs-lookup"><span data-stu-id="f1103-523">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
