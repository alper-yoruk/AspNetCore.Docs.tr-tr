---
title: ASP.NET Core statik dosyalar
author: rick-anderson
description: Statik dosyaları sunma ve güvenli hale getirme ve bir ASP.NET Core Web uygulamasındaki statik dosya barındırma ara yazılım davranışlarını yapılandırma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 3f4fc6f7d9d44d76d0504d9666df41571fd0b12c
ms.sourcegitcommit: d306407dc5bfe6fdfbac482214b3f59371b582bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/04/2020
ms.locfileid: "85951947"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="719a3-103">ASP.NET Core statik dosyalar</span><span class="sxs-lookup"><span data-stu-id="719a3-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="719a3-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="719a3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="719a3-105">HTML, CSS, resim ve JavaScript gibi statik dosyalar, ASP.NET Core bir uygulama varsayılan olarak doğrudan istemcilere hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="719a3-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="719a3-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="719a3-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="719a3-107">Statik dosyaları sunma</span><span class="sxs-lookup"><span data-stu-id="719a3-107">Serve static files</span></span>

<span data-ttu-id="719a3-108">Statik dosyalar projenin [Web kök](xref:fundamentals/index#web-root) dizininde depolanır.</span><span class="sxs-lookup"><span data-stu-id="719a3-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="719a3-109">Varsayılan dizin, `{content root}/wwwroot` ancak <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> yöntemiyle değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="719a3-110">Daha fazla bilgi için bkz. [içerik kökü](xref:fundamentals/index#content-root) ve [Web kök](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="719a3-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="719a3-111"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>Yöntemi, içerik kökünü geçerli dizine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="719a3-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="719a3-112">Yukarıdaki kod, Web uygulaması şablonuyla oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="719a3-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="719a3-113">Statik dosyalara, [Web köküne](xref:fundamentals/index#web-root)göre bir yol aracılığıyla erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="719a3-114">Örneğin, **Web uygulaması** proje şablonları, klasörü içinde birkaç klasör içerir `wwwroot` :</span><span class="sxs-lookup"><span data-stu-id="719a3-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="719a3-115">*Wwwroot/görüntüler* klasörünü oluşturmayı ve *Wwwroot/görüntüler/MyImage.jpg* dosyasını eklemeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="719a3-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="719a3-116">Klasöründeki bir dosyaya erişmek için URI biçimi `images` `https://<hostname>/images/<image_file_name>` .</span><span class="sxs-lookup"><span data-stu-id="719a3-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="719a3-117">Örneğin, `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="719a3-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="719a3-118">Web kökündeki dosyaları sunma</span><span class="sxs-lookup"><span data-stu-id="719a3-118">Serve files in web root</span></span>

<span data-ttu-id="719a3-119">Varsayılan Web uygulaması şablonları <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> içindeki yöntemini çağırır `Startup.Configure` , bu da statik dosyaların sunulmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="719a3-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="719a3-120">Parametresiz `UseStaticFiles` yöntemi aşırı yüklemesi, [Web kökündeki](xref:fundamentals/index#web-root) dosyaları servable olarak işaretler.</span><span class="sxs-lookup"><span data-stu-id="719a3-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="719a3-121">Aşağıdaki biçimlendirme *Wwwroot/görüntüler/MyImage.jpg*başvuruyor:</span><span class="sxs-lookup"><span data-stu-id="719a3-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="719a3-122">Yukarıdaki kodda, tilde karakteri `~/` [Web köküne](xref:fundamentals/index#web-root)işaret eder.</span><span class="sxs-lookup"><span data-stu-id="719a3-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="719a3-123">Dosyaları Web kökünün dışında sunma</span><span class="sxs-lookup"><span data-stu-id="719a3-123">Serve files outside of web root</span></span>

<span data-ttu-id="719a3-124">Sunulacak statik dosyaların [Web kökünün](xref:fundamentals/index#web-root)dışında yer aldığı bir dizin hiyerarşisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="719a3-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="719a3-125">Bir istek `red-rose.jpg` statik dosya ara yazılımını aşağıdaki şekilde yapılandırarak dosyaya erişebilir:</span><span class="sxs-lookup"><span data-stu-id="719a3-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="719a3-126">Yukarıdaki kodda, *mystaticfiles* dizin hiyerarşisi, *staticfiles* URI segmenti aracılığıyla herkese açıktır.</span><span class="sxs-lookup"><span data-stu-id="719a3-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="719a3-127">`https://<hostname>/StaticFiles/images/red-rose.jpg` *red-rose.jpg* dosyasına hizmet vermek için bir istek.</span><span class="sxs-lookup"><span data-stu-id="719a3-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="719a3-128">Aşağıdaki biçimlendirme *Mystaticfiles/Images/red-rose.jpg*başvurular:</span><span class="sxs-lookup"><span data-stu-id="719a3-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="719a3-129">HTTP yanıt üstbilgilerini ayarla</span><span class="sxs-lookup"><span data-stu-id="719a3-129">Set HTTP response headers</span></span>

<span data-ttu-id="719a3-130">Bir <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> nesne, http yanıt üst bilgilerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="719a3-131">[Web kökünden](xref:fundamentals/index#web-root)statik dosya sunma yapılandırmasına ek olarak, aşağıdaki kod `Cache-Control` üst bilgisini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="719a3-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="719a3-132">Statik dosyalar 600 saniye boyunca genel olarak önbelleklenebilir:</span><span class="sxs-lookup"><span data-stu-id="719a3-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Cache-Control üst bilgisini gösteren yanıt üstbilgileri eklendi](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="719a3-134">Statik dosya yetkilendirmesi</span><span class="sxs-lookup"><span data-stu-id="719a3-134">Static file authorization</span></span>

<span data-ttu-id="719a3-135">Statik dosya ara yazılımı yetkilendirme denetimleri sağlamıyor.</span><span class="sxs-lookup"><span data-stu-id="719a3-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="719a3-136">Tarafından sunulan tüm dosyalar, altında olanlar da dahil `wwwroot` herkese açık bir şekilde erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="719a3-137">Dosyalara yetkilendirme temelinde hizmeti sağlamak için:</span><span class="sxs-lookup"><span data-stu-id="719a3-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="719a3-138">Onları `wwwroot` ve statik dosya ara yazılımı tarafından erişilebilen herhangi bir dizini dışında saklayın.</span><span class="sxs-lookup"><span data-stu-id="719a3-138">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="719a3-139">Onlara yetkilendirme uygulanmış bir eylem yöntemiyle ve bir nesne döndüren bir eylem yöntemi aracılığıyla sunar <xref:Microsoft.AspNetCore.Mvc.FileResult> :</span><span class="sxs-lookup"><span data-stu-id="719a3-139">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="719a3-140">Dizin tarama</span><span class="sxs-lookup"><span data-stu-id="719a3-140">Directory browsing</span></span>

<span data-ttu-id="719a3-141">Dizin tarama belirtilen dizinler içinde dizin listelemesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="719a3-141">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="719a3-142">Dizin tarama, güvenlik nedenleriyle varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="719a3-142">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="719a3-143">Daha fazla bilgi için bkz. [hususlar](#sc).</span><span class="sxs-lookup"><span data-stu-id="719a3-143">For more information, see [Considerations](#sc).</span></span>

<span data-ttu-id="719a3-144">Dizin taramayı etkinleştir:</span><span class="sxs-lookup"><span data-stu-id="719a3-144">Enable directory browsing with:</span></span>

* <span data-ttu-id="719a3-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>içinde `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="719a3-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="719a3-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A>içinde `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="719a3-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="719a3-147">Yukarıdaki kod, URL 'YI kullanarak *Wwwroot/görüntüler* klasöründe `https://<hostname>/MyImages` , her bir dosya ve klasörün bağlantılarıyla Dizin taramasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="719a3-147">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![dizin tarama](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="719a3-149">Varsayılan belgelere hizmeti sunma</span><span class="sxs-lookup"><span data-stu-id="719a3-149">Serve default documents</span></span>

<span data-ttu-id="719a3-150">Varsayılan bir sayfanın ayarlanması, ziyaretçilerin bir sitede başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="719a3-150">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="719a3-151">Tam URI olmadan varsayılan bir sayfaya hizmeti sağlamak için `wwwroot` , <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="719a3-151">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="719a3-152">`UseDefaultFiles`Varsayılan dosyaya kullanılmadan önce çağrılmalıdır `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="719a3-152">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="719a3-153">`UseDefaultFiles`, dosyaya hizmeti olmayan bir URL yeniden yazar.</span><span class="sxs-lookup"><span data-stu-id="719a3-153">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="719a3-154">İle `UseDefaultFiles` , için arama içindeki bir klasöre yönelik istekler `wwwroot` :</span><span class="sxs-lookup"><span data-stu-id="719a3-154">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="719a3-155">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="719a3-155">*default.htm*</span></span>
* <span data-ttu-id="719a3-156">*default.html*</span><span class="sxs-lookup"><span data-stu-id="719a3-156">*default.html*</span></span>
* <span data-ttu-id="719a3-157">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="719a3-157">*index.htm*</span></span>
* <span data-ttu-id="719a3-158">*index.html*</span><span class="sxs-lookup"><span data-stu-id="719a3-158">*index.html*</span></span>

<span data-ttu-id="719a3-159">Listedeki ilk dosya, istek tam URI olmasına rağmen olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="719a3-159">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="719a3-160">Tarayıcı URL 'SI, istenen URI 'yi yansıtacak şekilde devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="719a3-160">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="719a3-161">Aşağıdaki kod, varsayılan dosya adını *mydefault.html*olarak değiştirir:</span><span class="sxs-lookup"><span data-stu-id="719a3-161">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="719a3-162">Aşağıdaki kod, `Startup.Configure` önceki kodla göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="719a3-162">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="719a3-163">Varsayılan belgeler için Usedosya sunucusu</span><span class="sxs-lookup"><span data-stu-id="719a3-163">UseFileServer for default documents</span></span>

<span data-ttu-id="719a3-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>`UseStaticFiles`, `UseDefaultFiles` ve isteğe bağlı olarak işlevselliğini birleştirir `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="719a3-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="719a3-165">`app.UseFileServer`Statik dosyaları ve varsayılan dosyayı sunma özelliğini etkinleştirmek için çağırın.</span><span class="sxs-lookup"><span data-stu-id="719a3-165">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="719a3-166">Dizin tarama etkin değil.</span><span class="sxs-lookup"><span data-stu-id="719a3-166">Directory browsing isn't enabled.</span></span> <span data-ttu-id="719a3-167">Aşağıdaki kod ile gösterilmektedir `Startup.Configure` `UseFileServer` :</span><span class="sxs-lookup"><span data-stu-id="719a3-167">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="719a3-168">Aşağıdaki kod statik dosyalar, varsayılan dosya ve dizin taramayı sunar:</span><span class="sxs-lookup"><span data-stu-id="719a3-168">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="719a3-169">Aşağıdaki kod, `Startup.Configure` önceki kodla göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="719a3-169">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="719a3-170">Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="719a3-170">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="719a3-171">Aşağıdaki kod statik dosyalar, varsayılan dosya ve dizin taramayı sunar `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="719a3-171">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="719a3-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>`EnableDirectoryBrowsing`özellik değeri olduğunda çağrılmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="719a3-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="719a3-173">Dosya hiyerarşisini ve önceki kodu kullanarak, URL 'Ler aşağıdaki şekilde çözümlenir:</span><span class="sxs-lookup"><span data-stu-id="719a3-173">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="719a3-174">URI</span><span class="sxs-lookup"><span data-stu-id="719a3-174">URI</span></span>            |      <span data-ttu-id="719a3-175">Yanıt</span><span class="sxs-lookup"><span data-stu-id="719a3-175">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="719a3-176">*MyStaticFiles/Images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="719a3-176">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="719a3-177">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="719a3-177">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="719a3-178">*Mystaticfiles* dizininde varsayılan adlı dosya yoksa, `https://<hostname>/StaticFiles` tıklatılabilir bağlantılarla dizin listesini döndürür:</span><span class="sxs-lookup"><span data-stu-id="719a3-178">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Statik dosyalar listesi](static-files/_static/db2.png)

<span data-ttu-id="719a3-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>ve hedef URI 'den sonuna kadar sondaki bir <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> istemci tarafı yeniden yönlendirmesi gerçekleştirin `/` `/` .</span><span class="sxs-lookup"><span data-stu-id="719a3-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="719a3-181">Örneğin, öğesinden `https://<hostname>/StaticFiles` `https://<hostname>/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="719a3-181">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="719a3-182">*Staticfiles* dizinindeki göreli URL 'ler, sondaki eğik çizgi () olmadan geçersizdir `/` .</span><span class="sxs-lookup"><span data-stu-id="719a3-182">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="719a3-183">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="719a3-183">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="719a3-184"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Sınıfı, `Mappings` MIME içerik türlerine dosya uzantılarının eşlemesi olarak hizmet veren bir özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="719a3-184">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="719a3-185">Aşağıdaki örnekte, bazı dosya uzantıları bilinen MIME türlerine eşlenir.</span><span class="sxs-lookup"><span data-stu-id="719a3-185">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="719a3-186">*. Rtf* uzantısı değiştirilmiştir ve *. mp4* kaldırılır:</span><span class="sxs-lookup"><span data-stu-id="719a3-186">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="719a3-187">Aşağıdaki kod, `Startup.Configure` önceki kodla göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="719a3-187">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="719a3-188">Bkz. [MIME içerik türleri](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="719a3-188">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="719a3-189">Standart olmayan içerik türleri</span><span class="sxs-lookup"><span data-stu-id="719a3-189">Non-standard content types</span></span>

<span data-ttu-id="719a3-190">Statik dosya ara yazılımı, neredeyse 400 bilinen dosya içerik türlerini anlamıştır.</span><span class="sxs-lookup"><span data-stu-id="719a3-190">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="719a3-191">Kullanıcı bilinmeyen bir dosya türüne sahip bir dosya isterse, statik dosya ara yazılımı isteği ardışık düzendeki bir sonraki ara yazılıma geçirir.</span><span class="sxs-lookup"><span data-stu-id="719a3-191">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="719a3-192">Bir ara yazılım, isteği işlediğinde, bir *404 bulunamadı* yanıtı döndürülür.</span><span class="sxs-lookup"><span data-stu-id="719a3-192">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="719a3-193">Dizin tarama etkinse, bir dizin listesinde dosyaya bir bağlantı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="719a3-193">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="719a3-194">Aşağıdaki kod, bilinmeyen türlere hizmet olarak bilinmeyen türler sunar ve bilinmeyen dosyayı görüntü olarak işler:</span><span class="sxs-lookup"><span data-stu-id="719a3-194">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="719a3-195">Aşağıdaki kod, `Startup.Configure` önceki kodla göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="719a3-195">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="719a3-196">Yukarıdaki kodla, bilinmeyen içerik türüne sahip bir dosya isteği görüntü olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="719a3-196">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="719a3-197">Etkinleştirme <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="719a3-197">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="719a3-198">Varsayılan olarak devre dışıdır ve kullanımı önerilmez.</span><span class="sxs-lookup"><span data-stu-id="719a3-198">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="719a3-199">[Fileextensioncontenttypeprovider](#fileextensioncontenttypeprovider) standart olmayan uzantılara sahip dosyalara hizmet vermeye yönelik daha güvenli bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="719a3-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="719a3-200">Birden çok konumdan dosyaları sunma</span><span class="sxs-lookup"><span data-stu-id="719a3-200">Serve files from multiple locations</span></span>

<span data-ttu-id="719a3-201">`UseStaticFiles`ve ' i `UseFileServer` işaret eden dosya sağlayıcısına varsayılan olarak `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="719a3-201">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="719a3-202">Diğer `UseStaticFiles` `UseFileServer` konumlardan dosya hizmeti sağlamak için diğer dosya sağlayıcılarıyla ek ve diğer örnekleri bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-202">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="719a3-203">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/15578)bakın.</span><span class="sxs-lookup"><span data-stu-id="719a3-203">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="719a3-204">Statik dosyalar için güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="719a3-204">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="719a3-205">`UseDirectoryBrowser`ve `UseStaticFiles` gizli dizileri sızdırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="719a3-205">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="719a3-206">Üretimde dizin taramayı devre dışı bırakmak önemle önerilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-206">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="719a3-207">Veya ile hangi dizinlerin etkinleştirildiğini dikkatle gözden `UseStaticFiles` geçirin `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="719a3-207">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="719a3-208">Tüm dizin ve alt dizinleri herkese açık şekilde erişilebilir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="719a3-208">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="719a3-209">Özel bir dizinde genel kullanıma sunma için uygun dosyaları (gibi) depolayın `<content_root>/wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="719a3-209">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="719a3-210">Bu dosyaları MVC görünümleri, Razor sayfaları, yapılandırma dosyaları vb. ile ayırın.</span><span class="sxs-lookup"><span data-stu-id="719a3-210">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="719a3-211">Ve ile sunulan içerik URL 'Leri `UseDirectoryBrowser` , `UseStaticFiles` temel dosya sisteminin büyük küçük harf duyarlılığı ve karakter kısıtlamalarına tabidir.</span><span class="sxs-lookup"><span data-stu-id="719a3-211">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="719a3-212">Örneğin, Windows büyük/küçük harfe duyarlıdır, ancak macOS ve Linux değildir.</span><span class="sxs-lookup"><span data-stu-id="719a3-212">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="719a3-213">IIS 'de barındırılan ASP.NET Core uygulamalar, statik dosya istekleri de dahil olmak üzere tüm istekleri uygulamaya iletmek için [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) kullanır.</span><span class="sxs-lookup"><span data-stu-id="719a3-213">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="719a3-214">IIS statik dosya işleyicisi kullanılmıyor ve istekleri işleme şansı yok.</span><span class="sxs-lookup"><span data-stu-id="719a3-214">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="719a3-215">Sunucu veya Web sitesi düzeyinde IIS statik dosya işleyicisini kaldırmak için IIS Yöneticisi ' nde aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="719a3-215">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="719a3-216">**Modüller** özelliğine gidin.</span><span class="sxs-lookup"><span data-stu-id="719a3-216">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="719a3-217">Listeden **StaticFileModule ' ü** seçin.</span><span class="sxs-lookup"><span data-stu-id="719a3-217">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="719a3-218">**Eylemler** kenar çubuğunda **Kaldır** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="719a3-218">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="719a3-219">IIS statik dosya işleyicisi etkinse **ve** ASP.NET Core modülü yanlış yapılandırılmışsa, statik dosyalar sunulur.</span><span class="sxs-lookup"><span data-stu-id="719a3-219">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="719a3-220">Bu, örneğin *web.config* dosyası dağıtılmamışsa oluşur.</span><span class="sxs-lookup"><span data-stu-id="719a3-220">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="719a3-221">*. Cs* ve *. cshtml*dahil olmak üzere kod dosyalarını uygulama projesinin [Web kökünün](xref:fundamentals/index#web-root)dışına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="719a3-221">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="719a3-222">Bu nedenle, uygulamanın istemci tarafı içeriği ile sunucu tabanlı kod arasında bir mantıksal ayrım oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="719a3-222">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="719a3-223">Bu, sunucu tarafı kodun sızmasını önler.</span><span class="sxs-lookup"><span data-stu-id="719a3-223">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="719a3-224">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="719a3-224">Additional resources</span></span>

* [<span data-ttu-id="719a3-225">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="719a3-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="719a3-226">ASP.NET Core’a Giriş</span><span class="sxs-lookup"><span data-stu-id="719a3-226">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="719a3-227">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Scott Ade](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="719a3-227">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="719a3-228">HTML, CSS, resim ve JavaScript gibi statik dosyalar, ASP.NET Core bir uygulamanın doğrudan istemcilere hizmet verdiği varlıklardır.</span><span class="sxs-lookup"><span data-stu-id="719a3-228">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="719a3-229">Bu dosyalara hizmet sunma özelliğini etkinleştirmek için bazı yapılandırmalar gerekir.</span><span class="sxs-lookup"><span data-stu-id="719a3-229">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="719a3-230">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="719a3-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="719a3-231">Statik dosyaları sunma</span><span class="sxs-lookup"><span data-stu-id="719a3-231">Serve static files</span></span>

<span data-ttu-id="719a3-232">Statik dosyalar projenin [Web kök](xref:fundamentals/index#web-root) dizininde depolanır.</span><span class="sxs-lookup"><span data-stu-id="719a3-232">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="719a3-233">Varsayılan dizin *{Content root}/Wwwroot*' dır, ancak yöntemi aracılığıyla değiştirilebilir <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> .</span><span class="sxs-lookup"><span data-stu-id="719a3-233">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="719a3-234">Daha fazla bilgi için bkz. [içerik kökü](xref:fundamentals/index#content-root) ve [Web kök](xref:fundamentals/index#web-root) .</span><span class="sxs-lookup"><span data-stu-id="719a3-234">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="719a3-235">Uygulamanın Web ana bilgisayarı, içerik kök dizininden haberdar olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="719a3-235">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="719a3-236">`WebHost.CreateDefaultBuilder`Yöntemi, içerik kökünü geçerli dizine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="719a3-236">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="719a3-237">Statik dosyalara, [Web köküne](xref:fundamentals/index#web-root)göre bir yol aracılığıyla erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-237">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="719a3-238">Örneğin, **Web uygulaması** proje şablonu, klasörü içinde birkaç klasör içerir `wwwroot` :</span><span class="sxs-lookup"><span data-stu-id="719a3-238">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="719a3-239">*Görüntüler* alt klasöründeki bir dosyaya erışmek için urı biçimi \*http:// \<server_address> /images/ \<image_file_name> \*olur.</span><span class="sxs-lookup"><span data-stu-id="719a3-239">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="719a3-240">Örneğin, *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="719a3-240">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="719a3-241">.NET Framework hedefliyorsanız, [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) paketini projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="719a3-241">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="719a3-242">.NET Core hedefleniyorsa, [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) bu paketi içerir.</span><span class="sxs-lookup"><span data-stu-id="719a3-242">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="719a3-243">Statik dosyaları sunmaya izin veren [ara yazılımı](xref:fundamentals/middleware/index)yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="719a3-243">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="719a3-244">Web kökünün içindeki dosyaları sunma</span><span class="sxs-lookup"><span data-stu-id="719a3-244">Serve files inside of web root</span></span>

<span data-ttu-id="719a3-245"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>Yöntemi içinde çağırın `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="719a3-245">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="719a3-246">Parametresiz `UseStaticFiles` yöntemi aşırı yüklemesi, [Web kökündeki](xref:fundamentals/index#web-root) dosyaları servable olarak işaretler.</span><span class="sxs-lookup"><span data-stu-id="719a3-246">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="719a3-247">Aşağıdaki biçimlendirme *Wwwroot/Images/banner1. SVG*öğesine başvuruyor:</span><span class="sxs-lookup"><span data-stu-id="719a3-247">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="719a3-248">Yukarıdaki kodda, tilde karakteri `~/` [Web köküne](xref:fundamentals/index#web-root)işaret eder.</span><span class="sxs-lookup"><span data-stu-id="719a3-248">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="719a3-249">Dosyaları Web kökünün dışında sunma</span><span class="sxs-lookup"><span data-stu-id="719a3-249">Serve files outside of web root</span></span>

<span data-ttu-id="719a3-250">Sunulacak statik dosyaların [Web kökünün](xref:fundamentals/index#web-root)dışında yer aldığı bir dizin hiyerarşisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="719a3-250">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="719a3-251">Bir istek statik dosya ara yazılımını aşağıdaki şekilde yapılandırarak *banner1. SVG* dosyasına erişebilir:</span><span class="sxs-lookup"><span data-stu-id="719a3-251">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="719a3-252">Yukarıdaki kodda, *mystaticfiles* dizin hiyerarşisi, *staticfiles* URI segmenti aracılığıyla herkese açıktır.</span><span class="sxs-lookup"><span data-stu-id="719a3-252">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="719a3-253">*Http:// \<server_address> /StaticFiles/images/banner1.SVG* için bir istek *banner1. SVG* dosyasına hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="719a3-253">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="719a3-254">Aşağıdaki biçimlendirme *Mystaticfiles/Images/banner1. SVG*' ye başvurur:</span><span class="sxs-lookup"><span data-stu-id="719a3-254">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="719a3-255">HTTP yanıt üstbilgilerini ayarla</span><span class="sxs-lookup"><span data-stu-id="719a3-255">Set HTTP response headers</span></span>

<span data-ttu-id="719a3-256">Bir <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> nesne, http yanıt üst bilgilerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-256">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="719a3-257">[Web kökünden](xref:fundamentals/index#web-root)statik dosya sunma yapılandırmasına ek olarak, aşağıdaki kod `Cache-Control` üst bilgisini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="719a3-257">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="719a3-258"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>Yöntemi, [Microsoft. Aspnetcore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="719a3-258">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="719a3-259">Dosyalar, geliştirme ortamında 10 dakika (600 saniye) için genel olarak önbelleklenebilir hale getirilir:</span><span class="sxs-lookup"><span data-stu-id="719a3-259">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Cache-Control üst bilgisini gösteren yanıt üstbilgileri eklendi](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="719a3-261">Statik dosya yetkilendirmesi</span><span class="sxs-lookup"><span data-stu-id="719a3-261">Static file authorization</span></span>

<span data-ttu-id="719a3-262">Statik dosya ara yazılımı yetkilendirme denetimleri sağlamıyor.</span><span class="sxs-lookup"><span data-stu-id="719a3-262">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="719a3-263">*Wwwroot*altındakiler de dahil olmak üzere hizmet tarafından sunulan tüm dosyalar herkese açık olarak erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-263">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="719a3-264">Dosyalara yetkilendirme temelinde hizmeti sağlamak için:</span><span class="sxs-lookup"><span data-stu-id="719a3-264">To serve files based on authorization:</span></span>

* <span data-ttu-id="719a3-265">Onları *Wwwroot* dışında ve statik dosya ara yazılımı tarafından erişilebilen herhangi bir dizinle saklayın.</span><span class="sxs-lookup"><span data-stu-id="719a3-265">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="719a3-266">Yetkilendirmeyi uygulanan bir eylem yöntemi aracılığıyla onlara sunar.</span><span class="sxs-lookup"><span data-stu-id="719a3-266">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="719a3-267">Bir <xref:Microsoft.AspNetCore.Mvc.FileResult> nesne döndürün:</span><span class="sxs-lookup"><span data-stu-id="719a3-267">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="719a3-268">Dizin taramayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="719a3-268">Enable directory browsing</span></span>

<span data-ttu-id="719a3-269">Dizin tarama, Web uygulamanızın kullanıcılarına belirtilen bir dizin içindeki bir dizin listesini ve dosyalarını görmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="719a3-269">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="719a3-270">Dizin tarama, güvenlik nedenleriyle varsayılan olarak devre dışıdır (bkz. [hususlar](#sc)).</span><span class="sxs-lookup"><span data-stu-id="719a3-270">Directory browsing is disabled by default for security reasons (see [Considerations](#sc)).</span></span> <span data-ttu-id="719a3-271">İçindeki yöntemi çağırarak dizin taramayı etkinleştir <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="719a3-271">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="719a3-272">Yöntemi şuradan çağırarak gerekli hizmetleri ekleyin <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="719a3-272">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="719a3-273">Yukarıdaki kod, her bir dosya ve klasörün bağlantılarıyla birlikte *http:// \<server_address> /MYıMAGES*URL 'sini kullanarak *Wwwroot/görüntüler* klasöründe Dizin taramasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="719a3-273">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![dizin tarama](static-files/_static/dir-browse.png)

<span data-ttu-id="719a3-275">Göz atmayı etkinleştirirken güvenlik riskleri hakkındaki [noktalara](#considerations) göz atın.</span><span class="sxs-lookup"><span data-stu-id="719a3-275">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="719a3-276">Aşağıdaki örnekteki iki çağrının olduğunu aklınızda edin `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="719a3-276">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="719a3-277">İlk çağrı *Wwwroot* klasöründeki statik dosyaları sunmaya izin veriyor.</span><span class="sxs-lookup"><span data-stu-id="719a3-277">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="719a3-278">İkinci çağrı, *http:// \<server_address> /MYıMAGES*URL 'sini kullanarak *Wwwroot/görüntüler* klasöründe dizin taramayı mümkün bir şekilde sunar:</span><span class="sxs-lookup"><span data-stu-id="719a3-278">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="719a3-279">Varsayılan bir belge sunar</span><span class="sxs-lookup"><span data-stu-id="719a3-279">Serve a default document</span></span>

<span data-ttu-id="719a3-280">Varsayılan ana sayfanın ayarlanması, ziyaretçi sitenizi ziyaret ederken mantıksal bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="719a3-280">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="719a3-281">Kullanıcı URI 'yi tamamen nitelemeden varsayılan bir sayfaya hizmeti sağlamak için, <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> yöntemi şuradan çağırın `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="719a3-281">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="719a3-282">`UseDefaultFiles`Varsayılan dosyaya kullanılmadan önce çağrılmalıdır `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="719a3-282">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="719a3-283">`UseDefaultFiles`, dosyayı gerçekten sunan bir URL yeniden yazar.</span><span class="sxs-lookup"><span data-stu-id="719a3-283">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="719a3-284">Dosya hizmeti sağlamak için statik dosya ara yazılımını etkinleştirin `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="719a3-284">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="719a3-285">İle `UseDefaultFiles` , bir klasör için arama istekleri:</span><span class="sxs-lookup"><span data-stu-id="719a3-285">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="719a3-286">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="719a3-286">*default.htm*</span></span>
* <span data-ttu-id="719a3-287">*default.html*</span><span class="sxs-lookup"><span data-stu-id="719a3-287">*default.html*</span></span>
* <span data-ttu-id="719a3-288">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="719a3-288">*index.htm*</span></span>
* <span data-ttu-id="719a3-289">*index.html*</span><span class="sxs-lookup"><span data-stu-id="719a3-289">*index.html*</span></span>

<span data-ttu-id="719a3-290">Listedeki ilk dosya, istek tam URI olmasına rağmen olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="719a3-290">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="719a3-291">Tarayıcı URL 'SI, istenen URI 'yi yansıtacak şekilde devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="719a3-291">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="719a3-292">Aşağıdaki kod, varsayılan dosya adını *mydefault.html*olarak değiştirir:</span><span class="sxs-lookup"><span data-stu-id="719a3-292">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="719a3-293">Usedosya sunucusu</span><span class="sxs-lookup"><span data-stu-id="719a3-293">UseFileServer</span></span>

<span data-ttu-id="719a3-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>`UseStaticFiles`, `UseDefaultFiles` ve isteğe bağlı olarak işlevselliğini birleştirir `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="719a3-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="719a3-295">Aşağıdaki kod, statik dosyaların ve varsayılan dosyanın kullanılmasına izin veriyor.</span><span class="sxs-lookup"><span data-stu-id="719a3-295">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="719a3-296">Dizin tarama etkin değil.</span><span class="sxs-lookup"><span data-stu-id="719a3-296">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="719a3-297">Aşağıdaki kod, dizin taramayı etkinleştirerek Parametresiz aşırı yüklemeden sonra oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="719a3-297">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="719a3-298">Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="719a3-298">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="719a3-299">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="719a3-299">**wwwroot**</span></span>
  * <span data-ttu-id="719a3-300">**Self**</span><span class="sxs-lookup"><span data-stu-id="719a3-300">**css**</span></span>
  * <span data-ttu-id="719a3-301">**yansımasını**</span><span class="sxs-lookup"><span data-stu-id="719a3-301">**images**</span></span>
  * <span data-ttu-id="719a3-302">**JS**</span><span class="sxs-lookup"><span data-stu-id="719a3-302">**js**</span></span>
* <span data-ttu-id="719a3-303">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="719a3-303">**MyStaticFiles**</span></span>
  * <span data-ttu-id="719a3-304">**yansımasını**</span><span class="sxs-lookup"><span data-stu-id="719a3-304">**images**</span></span>
    * <span data-ttu-id="719a3-305">*banner1. SVG*</span><span class="sxs-lookup"><span data-stu-id="719a3-305">*banner1.svg*</span></span>
  * <span data-ttu-id="719a3-306">*default.html*</span><span class="sxs-lookup"><span data-stu-id="719a3-306">*default.html*</span></span>

<span data-ttu-id="719a3-307">Aşağıdaki kod statik dosyaları, varsayılan dosyaları ve dizin taramayı mümkün bir şekilde sunar `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="719a3-307">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="719a3-308">`AddDirectoryBrowser``EnableDirectoryBrowsing`özellik değeri şu olduğunda çağrılmalıdır `true` :</span><span class="sxs-lookup"><span data-stu-id="719a3-308">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="719a3-309">Dosya hiyerarşisini ve önceki kodu kullanarak, URL 'Ler aşağıdaki şekilde çözümlenir:</span><span class="sxs-lookup"><span data-stu-id="719a3-309">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="719a3-310">URI</span><span class="sxs-lookup"><span data-stu-id="719a3-310">URI</span></span>            |                             <span data-ttu-id="719a3-311">Yanıt</span><span class="sxs-lookup"><span data-stu-id="719a3-311">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="719a3-312">*http:// \<server_address> /StaticFiles/images/banner1.SVG*</span><span class="sxs-lookup"><span data-stu-id="719a3-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="719a3-313">MyStaticFiles/Images/banner1. SVG</span><span class="sxs-lookup"><span data-stu-id="719a3-313">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="719a3-314">*http:// \<server_address> /Staticfiles*</span><span class="sxs-lookup"><span data-stu-id="719a3-314">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="719a3-315">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="719a3-315">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="719a3-316">*Mystaticfiles* dizininde varsayılan adlı dosya yoksa *http:// \<server_address> /staticfiles* , tıklatılabilir bağlantılarla dizin listesini döndürür:</span><span class="sxs-lookup"><span data-stu-id="719a3-316">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Statik dosyalar listesi](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="719a3-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>ve <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> `http://{SERVER ADDRESS}/StaticFiles` (sonunda eğik çizgi olmadan) ile (sonunda eğik çizgiyle) bir istemci tarafı yeniden yönlendirmesi gerçekleştirin `http://{SERVER ADDRESS}/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="719a3-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="719a3-319">*Staticfiles* dizinindeki göreli URL 'ler, sondaki eğik çizgi olmadan geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="719a3-319">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="719a3-320">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="719a3-320">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="719a3-321"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Sınıfı, `Mappings` MIME içerik türlerine dosya uzantılarının eşlemesi olarak hizmet veren bir özellik içerir.</span><span class="sxs-lookup"><span data-stu-id="719a3-321">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="719a3-322">Aşağıdaki örnekte, bazı dosya uzantıları bilinen MIME türlerine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-322">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="719a3-323">*. Rtf* uzantısı değiştirilmiştir ve *. mp4* kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="719a3-323">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="719a3-324">Bkz. [MIME içerik türleri](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="719a3-324">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="719a3-325">Standart olmayan içerik türleri</span><span class="sxs-lookup"><span data-stu-id="719a3-325">Non-standard content types</span></span>

<span data-ttu-id="719a3-326">Statik dosya ara yazılımı, neredeyse 400 bilinen dosya içerik türlerini anlamıştır.</span><span class="sxs-lookup"><span data-stu-id="719a3-326">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="719a3-327">Kullanıcı bilinmeyen bir dosya türüne sahip bir dosya isterse, statik dosya ara yazılımı isteği ardışık düzendeki bir sonraki ara yazılıma geçirir.</span><span class="sxs-lookup"><span data-stu-id="719a3-327">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="719a3-328">Bir ara yazılım, isteği işlediğinde, bir *404 bulunamadı* yanıtı döndürülür.</span><span class="sxs-lookup"><span data-stu-id="719a3-328">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="719a3-329">Dizin tarama etkinse, bir dizin listesinde dosyaya bir bağlantı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="719a3-329">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="719a3-330">Aşağıdaki kod, bilinmeyen türlere hizmet olarak bilinmeyen türler sunar ve bilinmeyen dosyayı görüntü olarak işler:</span><span class="sxs-lookup"><span data-stu-id="719a3-330">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="719a3-331">Yukarıdaki kodla, bilinmeyen içerik türüne sahip bir dosya isteği görüntü olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="719a3-331">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="719a3-332">Etkinleştirme <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="719a3-332">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="719a3-333">Varsayılan olarak devre dışıdır ve kullanımı önerilmez.</span><span class="sxs-lookup"><span data-stu-id="719a3-333">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="719a3-334">[Fileextensioncontenttypeprovider](#fileextensioncontenttypeprovider) standart olmayan uzantılara sahip dosyalara hizmet vermeye yönelik daha güvenli bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="719a3-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="719a3-335">Birden çok konumdan dosyaları sunma</span><span class="sxs-lookup"><span data-stu-id="719a3-335">Serve files from multiple locations</span></span>

<span data-ttu-id="719a3-336">`UseStaticFiles`ve `UseFileServer` Varsayılan olarak *Wwwroot*' a işaret eden dosya sağlayıcısına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="719a3-336">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="719a3-337">`UseStaticFiles` `UseFileServer` Diğer konumlardan dosya sunmak için diğer dosya sağlayıcılarının yanı sıra ek örnekler de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="719a3-337">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="719a3-338">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/15578)bakın.</span><span class="sxs-lookup"><span data-stu-id="719a3-338">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="719a3-339">Dikkat edilmesi gerekenler</span><span class="sxs-lookup"><span data-stu-id="719a3-339">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="719a3-340">`UseDirectoryBrowser`ve `UseStaticFiles` gizli dizileri sızdırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="719a3-340">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="719a3-341">Üretimde dizin taramayı devre dışı bırakmak önemle önerilir.</span><span class="sxs-lookup"><span data-stu-id="719a3-341">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="719a3-342">Veya ile hangi dizinlerin etkinleştirildiğini dikkatle gözden `UseStaticFiles` geçirin `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="719a3-342">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="719a3-343">Tüm dizin ve alt dizinleri herkese açık şekilde erişilebilir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="719a3-343">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="719a3-344">Özel bir dizinde genel kullanıma sunma için uygun dosyaları, örneğin \* \<content_root> /Wwwroot\*gibi depolayın.</span><span class="sxs-lookup"><span data-stu-id="719a3-344">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="719a3-345">Bu dosyaları MVC görünümlerini, Razor sayfaları (yalnızca 2. x), yapılandırma dosyalarını vb. ayırın.</span><span class="sxs-lookup"><span data-stu-id="719a3-345">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="719a3-346">Ve ile sunulan içerik URL 'Leri `UseDirectoryBrowser` , `UseStaticFiles` temel dosya sisteminin büyük küçük harf duyarlılığı ve karakter kısıtlamalarına tabidir.</span><span class="sxs-lookup"><span data-stu-id="719a3-346">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="719a3-347">Örneğin, Windows büyük/küçük harfe duyarsız &mdash; MacOS ve Linux değildir.</span><span class="sxs-lookup"><span data-stu-id="719a3-347">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="719a3-348">IIS 'de barındırılan ASP.NET Core uygulamalar, statik dosya istekleri de dahil olmak üzere tüm istekleri uygulamaya iletmek için [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) kullanır.</span><span class="sxs-lookup"><span data-stu-id="719a3-348">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="719a3-349">IIS statik dosya işleyicisi kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="719a3-349">The IIS static file handler isn't used.</span></span> <span data-ttu-id="719a3-350">Modül tarafından işlenmek üzere istekleri işleme şansı yoktur.</span><span class="sxs-lookup"><span data-stu-id="719a3-350">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="719a3-351">Sunucu veya Web sitesi düzeyinde IIS statik dosya işleyicisini kaldırmak için IIS Yöneticisi ' nde aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="719a3-351">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="719a3-352">**Modüller** özelliğine gidin.</span><span class="sxs-lookup"><span data-stu-id="719a3-352">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="719a3-353">Listeden **StaticFileModule ' ü** seçin.</span><span class="sxs-lookup"><span data-stu-id="719a3-353">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="719a3-354">**Eylemler** kenar çubuğunda **Kaldır** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="719a3-354">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="719a3-355">IIS statik dosya işleyicisi etkinse **ve** ASP.NET Core modülü yanlış yapılandırılmışsa, statik dosyalar sunulur.</span><span class="sxs-lookup"><span data-stu-id="719a3-355">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="719a3-356">Bu, örneğin *web.config* dosyası dağıtılmamışsa oluşur.</span><span class="sxs-lookup"><span data-stu-id="719a3-356">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="719a3-357">Kod dosyalarını ( *. cs* ve *. cshtml*dahil) uygulama projesinin [Web kökünün](xref:fundamentals/index#web-root)dışına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="719a3-357">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="719a3-358">Bu nedenle, uygulamanın istemci tarafı içeriği ile sunucu tabanlı kod arasında bir mantıksal ayrım oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="719a3-358">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="719a3-359">Bu, sunucu tarafı kodun sızmasını önler.</span><span class="sxs-lookup"><span data-stu-id="719a3-359">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="719a3-360">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="719a3-360">Additional resources</span></span>

* [<span data-ttu-id="719a3-361">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="719a3-361">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="719a3-362">ASP.NET Core’a Giriş</span><span class="sxs-lookup"><span data-stu-id="719a3-362">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
