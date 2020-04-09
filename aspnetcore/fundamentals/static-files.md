---
title: ASP.NET Core'daki statik dosyalar
author: rick-anderson
description: Statik dosyalara nasıl hizmet edinip güvenli hale getirmek ve ASP.NET Core web uygulamasında ara yazılım barındırma statik dosyayı yapılandırmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 95a77defc7e98328e1f4e3615648b1d14485e51e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660127"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="37989-103">ASP.NET Core'daki statik dosyalar</span><span class="sxs-lookup"><span data-stu-id="37989-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="37989-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="37989-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="37989-105">HTML, CSS, görüntüler ve JavaScript gibi statik dosyalar, ASP.NET Core uygulamasının doğrudan istemcilere sunduğu varlıklardır.</span><span class="sxs-lookup"><span data-stu-id="37989-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="37989-106">Bu dosyaların sunulmasını etkinleştirmek için bazı yapılandırmalar gereklidir.</span><span class="sxs-lookup"><span data-stu-id="37989-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="37989-107">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37989-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="37989-108">Statik dosyalara hizmet</span><span class="sxs-lookup"><span data-stu-id="37989-108">Serve static files</span></span>

<span data-ttu-id="37989-109">Statik dosyalar projenin [web kök](xref:fundamentals/index#web-root) dizini içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="37989-109">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="37989-110">Varsayılan dizin *{içerik kökü}/wwwroot'dur,* ancak [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) yöntemi yle değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="37989-110">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="37989-111">Daha fazla bilgi için [İçerik kökü](xref:fundamentals/index#content-root) ve [Web köküne](xref:fundamentals/index#web-root) bakın.</span><span class="sxs-lookup"><span data-stu-id="37989-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="37989-112">Uygulamanın web barındırma içeriği kök dizininden haberdar edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="37989-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="37989-113">Yöntem, `WebHost.CreateDefaultBuilder` içerik kökünü geçerli dizine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="37989-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="37989-114">[İçindeuseContentRoot'u](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) çağırarak içerik kökünü geçerli dizine `Program.Main`ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="37989-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="37989-115">Statik dosyalara [web köküne](xref:fundamentals/index#web-root)göre bir yol üzerinden erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="37989-115">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="37989-116">Örneğin, **Web Uygulaması** proje şablonu *wwwroot* klasöründe birkaç klasör içerir:</span><span class="sxs-lookup"><span data-stu-id="37989-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="37989-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="37989-117">**wwwroot**</span></span>
  * <span data-ttu-id="37989-118">**Css**</span><span class="sxs-lookup"><span data-stu-id="37989-118">**css**</span></span>
  * <span data-ttu-id="37989-119">**Görüntü**</span><span class="sxs-lookup"><span data-stu-id="37989-119">**images**</span></span>
  * <span data-ttu-id="37989-120">**Js**</span><span class="sxs-lookup"><span data-stu-id="37989-120">**js**</span></span>

<span data-ttu-id="37989-121">*Resimler* alt klasöründeki bir dosyaya erişmek için URI \*biçimi,\<>/resim/\<image_file_name>server_address http://. \*</span><span class="sxs-lookup"><span data-stu-id="37989-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="37989-122">Örneğin, *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="37989-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="37989-123">.NET Framework'u hedefliyorsanız, projeye [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="37989-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="37989-124">.NET Core hedefalıyorsanız, [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) bu paketi içerir.</span><span class="sxs-lookup"><span data-stu-id="37989-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="37989-125">.NET Framework'u hedefliyorsanız, projeye [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="37989-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="37989-126">.NET Core hedefalıyorsanız, [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) bu paketi içerir.</span><span class="sxs-lookup"><span data-stu-id="37989-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="37989-127">Projeye [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="37989-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span>

::: moniker-end

<span data-ttu-id="37989-128">Statik dosyaların sunulmasını sağlayan [ara yazılımı](xref:fundamentals/middleware/index) yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="37989-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="37989-129">Dosyaları web kökü içinde servis edin</span><span class="sxs-lookup"><span data-stu-id="37989-129">Serve files inside of web root</span></span>

<span data-ttu-id="37989-130">[UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) yöntemini `Startup.Configure`aşağıdakiler içinde çağırır:</span><span class="sxs-lookup"><span data-stu-id="37989-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="37989-131">Parametresiz `UseStaticFiles` yöntem aşırı [yük, web kökündeki](xref:fundamentals/index#web-root) dosyaları servis edilebilir olarak işaretler.</span><span class="sxs-lookup"><span data-stu-id="37989-131">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="37989-132">Aşağıdaki biçimlendirme referansları *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="37989-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="37989-133">Önceki kodda, tilde karakteri `~/` web [köküne](xref:fundamentals/index#web-root)işaret ediyor.</span><span class="sxs-lookup"><span data-stu-id="37989-133">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="37989-134">Dosyaları web kökü dışında servis edin</span><span class="sxs-lookup"><span data-stu-id="37989-134">Serve files outside of web root</span></span>

<span data-ttu-id="37989-135">Hizmet verilecek statik dosyaların [web kökünün](xref:fundamentals/index#web-root)dışında bulunduğu bir dizin hiyerarşisi düşünün:</span><span class="sxs-lookup"><span data-stu-id="37989-135">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* <span data-ttu-id="37989-136">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="37989-136">**wwwroot**</span></span>
  * <span data-ttu-id="37989-137">**Css**</span><span class="sxs-lookup"><span data-stu-id="37989-137">**css**</span></span>
  * <span data-ttu-id="37989-138">**Görüntü**</span><span class="sxs-lookup"><span data-stu-id="37989-138">**images**</span></span>
  * <span data-ttu-id="37989-139">**Js**</span><span class="sxs-lookup"><span data-stu-id="37989-139">**js**</span></span>
* <span data-ttu-id="37989-140">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="37989-140">**MyStaticFiles**</span></span>
  * <span data-ttu-id="37989-141">**Görüntü**</span><span class="sxs-lookup"><span data-stu-id="37989-141">**images**</span></span>
    * <span data-ttu-id="37989-142">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="37989-142">*banner1.svg*</span></span>

<span data-ttu-id="37989-143">Bir istek aşağıdaki gibi Statik Dosya Middleware yapılandırarak *banner1.svg* dosyasına erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="37989-143">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="37989-144">Önceki kodda, *MyStaticFiles* dizin hiyerarşisi *StaticFiles* URI segmenti aracılığıyla herkese açık olarak açıklanır.</span><span class="sxs-lookup"><span data-stu-id="37989-144">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="37989-145">*>/StaticFiles/images/banner1.svg server_address http://\<* için bir istek *banner1.svg* dosya hizmet vermektedir.</span><span class="sxs-lookup"><span data-stu-id="37989-145">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="37989-146">Aşağıdaki biçimlendirme *referansları MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="37989-146">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="37989-147">HTTP yanıt üstbilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="37989-147">Set HTTP response headers</span></span>

<span data-ttu-id="37989-148">[Bir StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) nesnesi HTTP yanıt üstbilgilerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="37989-148">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="37989-149">[Web kökünden](xref:fundamentals/index#web-root)hizmet veren statik dosyayı yapılandırmaya ek `Cache-Control` olarak, aşağıdaki kod üstbilgi yi ayarlar:</span><span class="sxs-lookup"><span data-stu-id="37989-149">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="37989-150">[HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) yöntemi [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="37989-150">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="37989-151">Dosyalar Geliştirme ortamında 10 dakika (600 saniye) kamuya açık hale getirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="37989-151">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Önbellek Denetimi üstbilgisini gösteren yanıt üstbilgisi eklendi](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="37989-153">Statik dosya yetkilendirmesi</span><span class="sxs-lookup"><span data-stu-id="37989-153">Static file authorization</span></span>

<span data-ttu-id="37989-154">Statik Dosya Middleware yetkilendirme denetimleri sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="37989-154">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="37989-155">*Wwwroot*altında olanlar da dahil olmak üzere, tarafından sunulan tüm dosyalar, genel olarak erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="37989-155">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="37989-156">Yetkilendirmeye dayalı dosyaları sunmak için:</span><span class="sxs-lookup"><span data-stu-id="37989-156">To serve files based on authorization:</span></span>

* <span data-ttu-id="37989-157">*Bunları wwwroot'un* ve Statik Dosya Middleware'in erişebileceği herhangi bir dizinin dışında saklayın.</span><span class="sxs-lookup"><span data-stu-id="37989-157">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="37989-158">Yetkilendirmenin uygulandığı bir eylem yöntemi yle bunları servis edin.</span><span class="sxs-lookup"><span data-stu-id="37989-158">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="37989-159">[FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) nesnesi döndürme:</span><span class="sxs-lookup"><span data-stu-id="37989-159">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="37989-160">Dizin taramasını etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="37989-160">Enable directory browsing</span></span>

<span data-ttu-id="37989-161">Dizin tarama web uygulamanızın kullanıcılarının belirli bir dizin içinde bir dizin listesi ve dosyaları görmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="37989-161">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="37989-162">Dizin tarama güvenlik nedenleriyle varsayılan olarak devre dışı bırakılır [(bkz.](#considerations)</span><span class="sxs-lookup"><span data-stu-id="37989-162">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="37989-163">[UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) yöntemini şu şekilde çağırarak dizin taramasını `Startup.Configure`etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="37989-163">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="37989-164">[AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) yöntemini çağırarak gerekli hizmetleri `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="37989-164">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="37989-165">Önceki kod, her dosya ve klasöre bağlantılar la *>/MyImages server_address\<* server_address URL http:// kullanarak *wwwroot/images* klasörünün dizin le gezinmesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="37989-165">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![dizin tarama](static-files/_static/dir-browse.png)

<span data-ttu-id="37989-167">Bkz. Göz atma etkinleştirirken güvenlik riskleri hakkında [dikkat](#considerations) edilmesi gerekenler.</span><span class="sxs-lookup"><span data-stu-id="37989-167">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="37989-168">Aşağıdaki örnekte iki `UseStaticFiles` çağrıya dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="37989-168">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="37989-169">İlk *arama, wwwroot* klasöründe statik dosyaların sunulmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="37989-169">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="37989-170">İkinci arama, *>/MyImages\<* server_address URL http:// kullanarak *wwwroot/images* klasörünün dizin le gezinmesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="37989-170">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="37989-171">Varsayılan bir belgeye hizmet</span><span class="sxs-lookup"><span data-stu-id="37989-171">Serve a default document</span></span>

<span data-ttu-id="37989-172">Varsayılan bir giriş sayfası ayarlamak, ziyaretçilerin sitenizi ziyaret ederken mantıksal bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="37989-172">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="37989-173">Kullanıcı URI'yi tam olarak nitelemeden varsayılan bir sayfa sunmak `Startup.Configure`için [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) yöntemini şu telefondan arayın:</span><span class="sxs-lookup"><span data-stu-id="37989-173">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="37989-174">`UseDefaultFiles`varsayılan dosyayı `UseStaticFiles` sunmak için önce çağrılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="37989-174">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="37989-175">`UseDefaultFiles`dosyaya gerçekte hizmet vermeyen bir URL rewriter'ıdır.</span><span class="sxs-lookup"><span data-stu-id="37989-175">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="37989-176">Dosyaya hizmet etmek `UseStaticFiles` için Statik Dosya Middleware'i etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="37989-176">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="37989-177">Ile `UseDefaultFiles`, bir klasör arama istekleri için:</span><span class="sxs-lookup"><span data-stu-id="37989-177">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="37989-178">*Default.htm*</span><span class="sxs-lookup"><span data-stu-id="37989-178">*default.htm*</span></span>
* <span data-ttu-id="37989-179">*default.html*</span><span class="sxs-lookup"><span data-stu-id="37989-179">*default.html*</span></span>
* <span data-ttu-id="37989-180">*Index.htm*</span><span class="sxs-lookup"><span data-stu-id="37989-180">*index.htm*</span></span>
* <span data-ttu-id="37989-181">*Index.html*</span><span class="sxs-lookup"><span data-stu-id="37989-181">*index.html*</span></span>

<span data-ttu-id="37989-182">Listeden bulunan ilk dosya, istek tam nitelikli URI'ymiş gibi sunulur.</span><span class="sxs-lookup"><span data-stu-id="37989-182">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="37989-183">Tarayıcı URL'si URI'nin istediği yansıtmaya devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="37989-183">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="37989-184">Aşağıdaki kod varsayılan dosya adını *mydefault.html*olarak değiştirir:</span><span class="sxs-lookup"><span data-stu-id="37989-184">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="37989-185">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="37989-185">UseFileServer</span></span>

<span data-ttu-id="37989-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>işlevselliğini birleştirir `UseStaticFiles` `UseDefaultFiles`, ve `UseDirectoryBrowser`isteğe bağlı olarak .</span><span class="sxs-lookup"><span data-stu-id="37989-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="37989-187">Aşağıdaki kod statik dosyaların ve varsayılan dosyanın sunulmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="37989-187">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="37989-188">Dizin tarama sı etkinleştirildi.</span><span class="sxs-lookup"><span data-stu-id="37989-188">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="37989-189">Aşağıdaki kod, dizin taramasını etkinleştirerek parametresiz aşırı yükleme üzerine oluşturur:</span><span class="sxs-lookup"><span data-stu-id="37989-189">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="37989-190">Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="37989-190">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="37989-191">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="37989-191">**wwwroot**</span></span>
  * <span data-ttu-id="37989-192">**Css**</span><span class="sxs-lookup"><span data-stu-id="37989-192">**css**</span></span>
  * <span data-ttu-id="37989-193">**Görüntü**</span><span class="sxs-lookup"><span data-stu-id="37989-193">**images**</span></span>
  * <span data-ttu-id="37989-194">**Js**</span><span class="sxs-lookup"><span data-stu-id="37989-194">**js**</span></span>
* <span data-ttu-id="37989-195">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="37989-195">**MyStaticFiles**</span></span>
  * <span data-ttu-id="37989-196">**Görüntü**</span><span class="sxs-lookup"><span data-stu-id="37989-196">**images**</span></span>
    * <span data-ttu-id="37989-197">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="37989-197">*banner1.svg*</span></span>
  * <span data-ttu-id="37989-198">*default.html*</span><span class="sxs-lookup"><span data-stu-id="37989-198">*default.html*</span></span>

<span data-ttu-id="37989-199">Aşağıdaki kod statik dosyaları, varsayılan dosyaları ve dizin `MyStaticFiles`tarama sağlar:</span><span class="sxs-lookup"><span data-stu-id="37989-199">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="37989-200">`AddDirectoryBrowser``EnableDirectoryBrowsing` özellik değeri olduğunda `true`çağrılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="37989-200">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="37989-201">Dosya hiyerarşisi ve önceki kodu kullanarak URL'ler aşağıdaki gibi çözer:</span><span class="sxs-lookup"><span data-stu-id="37989-201">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="37989-202">URI</span><span class="sxs-lookup"><span data-stu-id="37989-202">URI</span></span>            |                             <span data-ttu-id="37989-203">Yanıt</span><span class="sxs-lookup"><span data-stu-id="37989-203">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="37989-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="37989-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="37989-205">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="37989-205">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="37989-206">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="37989-206">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="37989-207">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="37989-207">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="37989-208">*MyStaticFiles* dizininde varsayılan adlandırılmış dosya yoksa, *http://\<server_address server_address>/StaticFiles* dizin listesini tıklanabilir bağlantılarla döndürür:</span><span class="sxs-lookup"><span data-stu-id="37989-208">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Statik dosya listesi](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="37989-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>ve <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> istemci tarafı yönlendirme `http://{SERVER ADDRESS}/StaticFiles` (bir iz kesme olmadan) `http://{SERVER ADDRESS}/StaticFiles/` gerçekleştirmek (bir izleme eğik çizgi ile).</span><span class="sxs-lookup"><span data-stu-id="37989-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="37989-211">*StaticFiles* dizinindeki göreli URL'ler, sondalı bir eğik çizgi olmadan geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="37989-211">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="37989-212">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="37989-212">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="37989-213">[FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) sınıfı, `Mappings` MIME içerik türlerine dosya uzantılarının eşlemeolarak hizmet veren bir özellik içerir.</span><span class="sxs-lookup"><span data-stu-id="37989-213">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="37989-214">Aşağıdaki örnekte, bilinen MIME türlerine birkaç dosya uzantısı kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="37989-214">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="37989-215">*.rtf* uzantısı değiştirilir ve *.mp4* kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="37989-215">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="37989-216">Bakınız [MIME içerik türleri.](https://www.iana.org/assignments/media-types/media-types.xhtml)</span><span class="sxs-lookup"><span data-stu-id="37989-216">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="37989-217">Standart olmayan içerik türleri</span><span class="sxs-lookup"><span data-stu-id="37989-217">Non-standard content types</span></span>

<span data-ttu-id="37989-218">Statik Dosya Middleware neredeyse 400 bilinen dosya içeriği türleri anlar.</span><span class="sxs-lookup"><span data-stu-id="37989-218">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="37989-219">Kullanıcı bilinmeyen bir dosya türüne sahip bir dosya isterse, Statik Dosya Middleware isteği ardışık ardışık alandaki bir sonraki ara yazılıma geçirir.</span><span class="sxs-lookup"><span data-stu-id="37989-219">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="37989-220">Hiçbir ara yazılım isteği işlemezse, *404 Bulunamadı* yanıtı döndürülür.</span><span class="sxs-lookup"><span data-stu-id="37989-220">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="37989-221">Dizin taraması etkinleştirilmişse, dosyanın bağlantısı bir dizin listesinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="37989-221">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="37989-222">Aşağıdaki kod bilinmeyen türlere hizmet edilmesini sağlar ve bilinmeyen dosyayı görüntü olarak işler:</span><span class="sxs-lookup"><span data-stu-id="37989-222">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="37989-223">Önceki kodla, bilinmeyen içerik türüne sahip bir dosya isteği görüntü olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="37989-223">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="37989-224">[ServeUnknownFileTypes'yi](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) etkinleştirmek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="37989-224">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="37989-225">Varsayılan olarak devre dışı bırakılır ve kullanımı önerilmez.</span><span class="sxs-lookup"><span data-stu-id="37989-225">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="37989-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) standart olmayan uzantıları ile dosyaları hizmet için daha güvenli bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="37989-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="37989-227">Dosyaları birden çok konumdan servis edin</span><span class="sxs-lookup"><span data-stu-id="37989-227">Serve files from multiple locations</span></span>

<span data-ttu-id="37989-228">`UseStaticFiles`ve `UseFileServer` *wwwroot'u*işaret eden dosya sağlayıcısına varsayılan dır.</span><span class="sxs-lookup"><span data-stu-id="37989-228">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="37989-229">Diğer konumlardan dosyaları `UseStaticFiles` sunmak `UseFileServer` için ek örnekleri ve diğer dosya sağlayıcıları ile sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="37989-229">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="37989-230">Daha fazla bilgi için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/15578)bakın.</span><span class="sxs-lookup"><span data-stu-id="37989-230">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="37989-231">Dikkat edilmesi gerekenler</span><span class="sxs-lookup"><span data-stu-id="37989-231">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="37989-232">`UseDirectoryBrowser`ve `UseStaticFiles` sırları sızdırabilir.</span><span class="sxs-lookup"><span data-stu-id="37989-232">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="37989-233">Üretimde dizin tarama devre dışı bırakılması şiddetle tavsiye edilir.</span><span class="sxs-lookup"><span data-stu-id="37989-233">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="37989-234">Hangi dizinlerin veya `UseStaticFiles` '' `UseDirectoryBrowser`aracılığıyla etkinleştirilmiş olduğunu dikkatle inceleyin.</span><span class="sxs-lookup"><span data-stu-id="37989-234">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="37989-235">Tüm dizin ve alt dizinleri genel olarak erişilebilir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="37989-235">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="37989-236">\* \<content_root>/wwwroot\*gibi özel bir dizinde halka hizmet vermeye uygun dosyaları saklayın.</span><span class="sxs-lookup"><span data-stu-id="37989-236">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="37989-237">Bu dosyaları MVC görünümlerinden, Razor Pages'dan (yalnızca 2.x), yapılandırma dosyalarından vb. ayırın.</span><span class="sxs-lookup"><span data-stu-id="37989-237">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="37989-238">İçerikle açıkta `UseDirectoryBrowser` kalan ve `UseStaticFiles` altta yatan dosya sisteminin durum hassasiyeti ve karakter kısıtlamalarına tabi olan URL'ler.</span><span class="sxs-lookup"><span data-stu-id="37989-238">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="37989-239">Örneğin, Windows büyük bir&mdash;durumda duyarsız macOS ve Linux değildir.</span><span class="sxs-lookup"><span data-stu-id="37989-239">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="37989-240">iIS'de barındırılan ASP.NET Core uygulamaları, statik dosya istekleri de dahil olmak üzere tüm istekleri uygulamaya iletmek için [ASP.NET Çekirdek Modülü'ni](xref:host-and-deploy/aspnet-core-module) kullanır.</span><span class="sxs-lookup"><span data-stu-id="37989-240">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="37989-241">IIS statik dosya işleyicisi kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="37989-241">The IIS static file handler isn't used.</span></span> <span data-ttu-id="37989-242">Modül tarafından ele alınamadan önce istekleri işleme şansı yoktur.</span><span class="sxs-lookup"><span data-stu-id="37989-242">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="37989-243">Sunucu veya web sitesi düzeyinde IIS statik dosya işleyicisini kaldırmak için IIS Manager'da aşağıdaki adımları tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="37989-243">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="37989-244">**Modüller** özelliğine gidin.</span><span class="sxs-lookup"><span data-stu-id="37989-244">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="37989-245">Listede **StaticFileModule'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="37989-245">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="37989-246">**Eylemler** kenar çubuğunda **Kaldır'ı** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="37989-246">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="37989-247">IIS statik dosya işleyicisi **etkinleştirilmişse ve** ASP.NET Çekirdek Modülü yanlış yapılandırılırsa, statik dosyalar servis edilir.</span><span class="sxs-lookup"><span data-stu-id="37989-247">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="37989-248">Bu, örneğin, *web.config* dosyası dağıtılmazsa olur.</span><span class="sxs-lookup"><span data-stu-id="37989-248">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="37989-249">Kod dosyalarını *(.cs* ve *.cshtml*dahil) uygulama projesinin [web kökünün](xref:fundamentals/index#web-root)dışına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="37989-249">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="37989-250">Bu nedenle, uygulamanın istemci tarafındaki içeriği yle sunucu tabanlı kodu arasında mantıksal bir ayrım oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="37989-250">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="37989-251">Bu, sunucu tarafındaki kodun sızdırılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="37989-251">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37989-252">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="37989-252">Additional resources</span></span>

* [<span data-ttu-id="37989-253">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="37989-253">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="37989-254">ASP.NET Core’a Giriş</span><span class="sxs-lookup"><span data-stu-id="37989-254">Introduction to ASP.NET Core</span></span>](xref:index)
