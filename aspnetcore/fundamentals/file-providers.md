---
<span data-ttu-id="9d650-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-102">'Blazor'</span></span>
- <span data-ttu-id="9d650-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-103">'Identity'</span></span>
- <span data-ttu-id="9d650-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-105">'Razor'</span></span>
- <span data-ttu-id="9d650-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="9d650-107">ASP.NET Core dosya sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="9d650-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="9d650-108">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="9d650-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9d650-109">ASP.NET Core dosya sistemi erişimini dosya sağlayıcılarının kullanımı üzerinden soyutlar.</span><span class="sxs-lookup"><span data-stu-id="9d650-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="9d650-110">Dosya sağlayıcıları ASP.NET Core Framework boyunca kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="9d650-111">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9d650-111">For example:</span></span>

* <span data-ttu-id="9d650-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>uygulamanın [içerik kökünü](xref:fundamentals/index#content-root) ve [Web kökünü](xref:fundamentals/index#web-root) türler olarak gösterir `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="9d650-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="9d650-113">[Statik dosya ara yazılımı](xref:fundamentals/static-files) , statik dosyaları bulmak Için dosya sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="9d650-114">[Razor](xref:mvc/views/razor)sayfa ve görünümleri bulmak için dosya sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="9d650-115">.NET Core araçları, hangi dosyaların yayımlanacak olduğunu belirlemek için dosya sağlayıcılarını ve glob düzenlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="9d650-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9d650-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="9d650-117">Dosya sağlayıcısı arabirimleri</span><span class="sxs-lookup"><span data-stu-id="9d650-117">File Provider interfaces</span></span>

<span data-ttu-id="9d650-118">Birincil arabirim <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="9d650-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="9d650-119">`IFileProvider`şunları yapmak için yöntemler sunar:</span><span class="sxs-lookup"><span data-stu-id="9d650-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="9d650-120">Dosya bilgilerini edinin ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).</span><span class="sxs-lookup"><span data-stu-id="9d650-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="9d650-121">Dizin bilgilerini ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ) alın.</span><span class="sxs-lookup"><span data-stu-id="9d650-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="9d650-122">Değişiklik bildirimlerini ayarlayın (bir kullanarak <xref:Microsoft.Extensions.Primitives.IChangeToken> ).</span><span class="sxs-lookup"><span data-stu-id="9d650-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="9d650-123">`IFileInfo`dosyalarla çalışma için yöntemler ve özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="9d650-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="9d650-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(bayt cinsinden)</span><span class="sxs-lookup"><span data-stu-id="9d650-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="9d650-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>güncel</span><span class="sxs-lookup"><span data-stu-id="9d650-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="9d650-126">Yöntemini kullanarak dosyasından okuma yapabilirsiniz <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="9d650-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="9d650-127">*Fileprovidersample* örnek uygulaması, uygulamasında `Startup.ConfigureServices` [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama genelinde kullanılmak üzere bir dosya sağlayıcısının nasıl yapılandırılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="9d650-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="9d650-128">Dosya sağlayıcısı uygulamaları</span><span class="sxs-lookup"><span data-stu-id="9d650-128">File Provider implementations</span></span>

<span data-ttu-id="9d650-129">Aşağıdaki tablo uygulamasının uygulamalarını listelemektedir `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="9d650-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="9d650-130">Uygulama</span><span class="sxs-lookup"><span data-stu-id="9d650-130">Implementation</span></span> | <span data-ttu-id="9d650-131">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9d650-131">Description</span></span> |
| ---
<span data-ttu-id="9d650-132">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-133">'Blazor'</span></span>
- <span data-ttu-id="9d650-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-134">'Identity'</span></span>
- <span data-ttu-id="9d650-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-136">'Razor'</span></span>
- <span data-ttu-id="9d650-137">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-138">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-139">'Blazor'</span></span>
- <span data-ttu-id="9d650-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-140">'Identity'</span></span>
- <span data-ttu-id="9d650-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-142">'Razor'</span></span>
- <span data-ttu-id="9d650-143">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-144">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-145">'Blazor'</span></span>
- <span data-ttu-id="9d650-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-146">'Identity'</span></span>
- <span data-ttu-id="9d650-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-148">'Razor'</span></span>
- <span data-ttu-id="9d650-149">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-150">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-151">'Blazor'</span></span>
- <span data-ttu-id="9d650-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-152">'Identity'</span></span>
- <span data-ttu-id="9d650-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-154">'Razor'</span></span>
- <span data-ttu-id="9d650-155">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-156">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-157">'Blazor'</span></span>
- <span data-ttu-id="9d650-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-158">'Identity'</span></span>
- <span data-ttu-id="9d650-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-160">'Razor'</span></span>
- <span data-ttu-id="9d650-161">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-161">'SignalR' uid:</span></span> 

<span data-ttu-id="9d650-162">------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-163">'Blazor'</span></span>
- <span data-ttu-id="9d650-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-164">'Identity'</span></span>
- <span data-ttu-id="9d650-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-166">'Razor'</span></span>
- <span data-ttu-id="9d650-167">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-168">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-169">'Blazor'</span></span>
- <span data-ttu-id="9d650-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-170">'Identity'</span></span>
- <span data-ttu-id="9d650-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-172">'Razor'</span></span>
- <span data-ttu-id="9d650-173">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-174">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-175">'Blazor'</span></span>
- <span data-ttu-id="9d650-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-176">'Identity'</span></span>
- <span data-ttu-id="9d650-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-178">'Razor'</span></span>
- <span data-ttu-id="9d650-179">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-179">'SignalR' uid:</span></span> 

<span data-ttu-id="9d650-180">------ | | [Compositefileprovider](#compositefileprovider) | Bir veya daha fazla sağlayıcıdan dosya ve dizinlere Birleşik erişim sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="9d650-181">| | [Bildirimli Estembeddedfileprovider](#manifestembeddedfileprovider) | Derlemelerde yerleşik dosyalara erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="9d650-182">| | [Physicalfileprovider](#physicalfileprovider) | Sistemin fiziksel dosyalarına erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="9d650-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="9d650-183">PhysicalFileProvider</span></span>

<span data-ttu-id="9d650-184"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Fiziksel dosya sistemine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="9d650-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="9d650-185">`PhysicalFileProvider`, <xref:System.IO.File?displayProperty=fullName> türü (fiziksel sağlayıcı için) ve tüm yolları bir dizine ve alt öğelerine kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="9d650-186">Bu kapsam, belirtilen dizin ve alt öğeleri dışındaki dosya sistemine erişimi engeller.</span><span class="sxs-lookup"><span data-stu-id="9d650-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="9d650-187">Oluşturma ve kullanma için en yaygın senaryo, `PhysicalFileProvider` `IFileProvider` [bağımlılık ekleme](xref:fundamentals/dependency-injection)aracılığıyla bir Oluşturucu içinde istekte bulunur.</span><span class="sxs-lookup"><span data-stu-id="9d650-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="9d650-188">Bu sağlayıcıyı doğrudan örnekledikten sonra, mutlak bir dizin yolu gereklidir ve sağlayıcı kullanılarak yapılan tüm isteklerin temel yolu olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="9d650-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="9d650-189">Glob desenleri dizin yolunda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="9d650-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="9d650-190">Aşağıdaki kod, `PhysicalFileProvider` dizin içeriğini ve dosya bilgilerini elde etmek için nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="9d650-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="9d650-191">Önceki örnekteki türler:</span><span class="sxs-lookup"><span data-stu-id="9d650-191">Types in the preceding example:</span></span>

* <span data-ttu-id="9d650-192">`provider`bir `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="9d650-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="9d650-193">`contents`bir `IDirectoryContents` .</span><span class="sxs-lookup"><span data-stu-id="9d650-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="9d650-194">`fileInfo`bir `IFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="9d650-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="9d650-195">Dosya sağlayıcısı, tarafından belirtilen dizin üzerinden yinelemek `applicationRoot` veya `GetFileInfo` bir dosyanın bilgilerini almak için çağırmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d650-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="9d650-196">Glob desenleri yönteme geçirilememelidir `GetFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="9d650-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="9d650-197">Dosya sağlayıcısı, dizin dışında bir erişime sahip değil `applicationRoot` .</span><span class="sxs-lookup"><span data-stu-id="9d650-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="9d650-198">*Fileprovidersample* örnek uygulaması, şunu kullanarak sağlayıcıyı oluşturur `Startup.ConfigureServices` <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="9d650-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="9d650-199">Bildirimli Estembeddedfileprovider</span><span class="sxs-lookup"><span data-stu-id="9d650-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="9d650-200">, <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Derlemeler içine katıştırılmış dosyalara erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="9d650-201">, `ManifestEmbeddedFileProvider` Gömülü dosyaların özgün yollarını yeniden oluşturmak için derlemeye derlenen bir bildirim kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="9d650-202">Katıştırılmış dosyaların bir bildirimini oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="9d650-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="9d650-203">Projenize [Microsoft. Extensions. FileProviders. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9d650-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="9d650-204">`<GenerateEmbeddedFilesManifest>`Özelliğini olarak ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="9d650-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="9d650-205">Eklenecek dosyaları belirtin [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :</span><span class="sxs-lookup"><span data-stu-id="9d650-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="9d650-206">Derlemeye eklemek üzere bir veya daha fazla dosya belirtmek için [Glob desenlerini](#glob-patterns) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9d650-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="9d650-207">*Fileprovidersample* örnek uygulaması bir oluşturur `ManifestEmbeddedFileProvider` ve şu anda yürütülmekte olan derlemeyi oluşturucusuna geçirir.</span><span class="sxs-lookup"><span data-stu-id="9d650-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="9d650-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9d650-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="9d650-209">Ek aşırı yüklemeler şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="9d650-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="9d650-210">Göreli bir dosya yolu belirtin.</span><span class="sxs-lookup"><span data-stu-id="9d650-210">Specify a relative file path.</span></span>
* <span data-ttu-id="9d650-211">Dosya kapsamını son değiştirilme tarihine kadar.</span><span class="sxs-lookup"><span data-stu-id="9d650-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="9d650-212">Katıştırılmış dosya bildirimini içeren gömülü kaynağı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="9d650-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="9d650-213">Yüklemek</span><span class="sxs-lookup"><span data-stu-id="9d650-213">Overload</span></span> | <span data-ttu-id="9d650-214">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9d650-214">Description</span></span> |
| ---
<span data-ttu-id="9d650-215">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-216">'Blazor'</span></span>
- <span data-ttu-id="9d650-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-217">'Identity'</span></span>
- <span data-ttu-id="9d650-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-219">'Razor'</span></span>
- <span data-ttu-id="9d650-220">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-221">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-222">'Blazor'</span></span>
- <span data-ttu-id="9d650-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-223">'Identity'</span></span>
- <span data-ttu-id="9d650-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-225">'Razor'</span></span>
- <span data-ttu-id="9d650-226">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-226">'SignalR' uid:</span></span> 

<span data-ttu-id="9d650-227">---- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-228">'Blazor'</span></span>
- <span data-ttu-id="9d650-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-229">'Identity'</span></span>
- <span data-ttu-id="9d650-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-231">'Razor'</span></span>
- <span data-ttu-id="9d650-232">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-233">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-234">'Blazor'</span></span>
- <span data-ttu-id="9d650-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-235">'Identity'</span></span>
- <span data-ttu-id="9d650-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-237">'Razor'</span></span>
- <span data-ttu-id="9d650-238">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-239">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-240">'Blazor'</span></span>
- <span data-ttu-id="9d650-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-241">'Identity'</span></span>
- <span data-ttu-id="9d650-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-243">'Razor'</span></span>
- <span data-ttu-id="9d650-244">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-244">'SignalR' uid:</span></span> 

<span data-ttu-id="9d650-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | İsteğe bağlı bir `root` göreli yol parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9d650-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="9d650-246">`root` <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> Belirtilen yol altında bu kaynaklara yönelik olarak yapılan çağrıları belirtin.</span><span class="sxs-lookup"><span data-stu-id="9d650-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="9d650-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | İsteğe bağlı `root` göreli yol parametresini ve bir `lastModified` date ( <xref:System.DateTimeOffset> ) parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9d650-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="9d650-248">`lastModified`Tarih kapsamları <xref:Microsoft.Extensions.FileProviders.IFileInfo> tarafından döndürülen örneklerin son değiştirilme tarihidir <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="9d650-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="9d650-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | İsteğe bağlı bir `root` göreli yolu, `lastModified` tarihi ve parametreleri kabul eder `manifestName` .</span><span class="sxs-lookup"><span data-stu-id="9d650-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="9d650-250">, `manifestName` Bildirimi içeren gömülü kaynağın adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9d650-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="9d650-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="9d650-251">CompositeFileProvider</span></span>

<span data-ttu-id="9d650-252">, <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` Birden çok sağlayıcıdan dosyalarla çalışmak için tek bir arabirim ortaya çıkaran örnekleri birleştirir.</span><span class="sxs-lookup"><span data-stu-id="9d650-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="9d650-253">Oluştururken `CompositeFileProvider` bir veya daha fazla `IFileProvider` örneği oluşturucuya geçirin.</span><span class="sxs-lookup"><span data-stu-id="9d650-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="9d650-254">*Fileprovidersample* örnek uygulamasında bir `PhysicalFileProvider` ve, `ManifestEmbeddedFileProvider` `CompositeFileProvider` uygulamanın hizmet kapsayıcısında kayıtlı bir dosya sağlayın.</span><span class="sxs-lookup"><span data-stu-id="9d650-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="9d650-255">Aşağıdaki kod proje `Startup.ConfigureServices` yönteminde bulunur:</span><span class="sxs-lookup"><span data-stu-id="9d650-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="9d650-256">Değişiklikleri izle</span><span class="sxs-lookup"><span data-stu-id="9d650-256">Watch for changes</span></span>

<span data-ttu-id="9d650-257"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType>Yöntemi, değişiklikler için bir veya daha fazla dosya veya dizini izlemek üzere bir senaryo sağlar.</span><span class="sxs-lookup"><span data-stu-id="9d650-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="9d650-258">`Watch`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9d650-258">The `Watch` method:</span></span>

* <span data-ttu-id="9d650-259">Birden çok dosya belirtmek için [Glob desenlerini](#glob-patterns) içerebilen bir dosya yolu dizesi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9d650-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="9d650-260">Döndürür <xref:Microsoft.Extensions.Primitives.IChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="9d650-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="9d650-261">Ortaya çıkan değişiklik belirteci şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="9d650-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="9d650-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Bir değişikliğin oluşup oluşmadığını tespit etmek için incelenebilir bir özellik.</span><span class="sxs-lookup"><span data-stu-id="9d650-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="9d650-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Belirtilen yol dizesinde değişiklikler algılandığında çağırılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="9d650-264">Her değişiklik belirteci yalnızca, ilişkili geri çağırma işlemini tek bir değişikliğe yanıt olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="9d650-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="9d650-265">Sabit izlemeyi etkinleştirmek için bir <xref:System.Threading.Tasks.TaskCompletionSource`1> (aşağıda gösterilmiştir) kullanın veya `IChangeToken` değişiklikleri yanıt olarak yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9d650-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="9d650-266">*WatchConsole* örnek uygulaması, *TextFiles* dizinindeki bir *. txt* dosyası her değiştirildiğinde bir ileti yazar:</span><span class="sxs-lookup"><span data-stu-id="9d650-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="9d650-267">Docker kapsayıcıları ve ağ paylaşımları gibi bazı dosya sistemleri, değişiklik bildirimlerini güvenilir bir şekilde gönderemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="9d650-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="9d650-268">`DOTNET_USE_POLLING_FILE_WATCHER`Ortam değişkenini, `1` `true` her dört saniyede bir değişiklikler için dosya sistemini yoklamak üzere veya olarak ayarlayın (yapılandırılamaz).</span><span class="sxs-lookup"><span data-stu-id="9d650-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="9d650-269">Glob desenleri</span><span class="sxs-lookup"><span data-stu-id="9d650-269">Glob patterns</span></span>

<span data-ttu-id="9d650-270">Dosya sistemi yolları, *Glob (veya glob) desenleri*adlı joker karakter desenleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="9d650-271">Bu desenlerle dosya gruplarını belirtin.</span><span class="sxs-lookup"><span data-stu-id="9d650-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="9d650-272">İki joker karakter şunlardır `*` `**` :</span><span class="sxs-lookup"><span data-stu-id="9d650-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="9d650-273">Geçerli klasör düzeyindeki her şeyi, dosya adını veya herhangi bir dosya uzantısını eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="9d650-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="9d650-274">Eşleşmeler, `/` `.` dosya yolundaki karakterler ile sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="9d650-275">Birden çok dizin düzeyindeki tüm öğeleri eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="9d650-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="9d650-276">, Bir Dizin hiyerarşisinde birçok dosya yinelemeli olarak eşleşmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d650-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="9d650-277">Aşağıdaki tabloda, glob desenlerinin yaygın örnekleri verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="9d650-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="9d650-278">Desen</span><span class="sxs-lookup"><span data-stu-id="9d650-278">Pattern</span></span>  |<span data-ttu-id="9d650-279">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9d650-279">Description</span></span>  |
|---
<span data-ttu-id="9d650-280">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-281">'Blazor'</span></span>
- <span data-ttu-id="9d650-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-282">'Identity'</span></span>
- <span data-ttu-id="9d650-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-284">'Razor'</span></span>
- <span data-ttu-id="9d650-285">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-286">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-287">'Blazor'</span></span>
- <span data-ttu-id="9d650-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-288">'Identity'</span></span>
- <span data-ttu-id="9d650-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-290">'Razor'</span></span>
- <span data-ttu-id="9d650-291">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-291">'SignalR' uid:</span></span> 

<span data-ttu-id="9d650-292">-----|---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-293">'Blazor'</span></span>
- <span data-ttu-id="9d650-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-294">'Identity'</span></span>
- <span data-ttu-id="9d650-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-296">'Razor'</span></span>
- <span data-ttu-id="9d650-297">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-298">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-299">'Blazor'</span></span>
- <span data-ttu-id="9d650-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-300">'Identity'</span></span>
- <span data-ttu-id="9d650-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-302">'Razor'</span></span>
- <span data-ttu-id="9d650-303">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-303">'SignalR' uid:</span></span> 

<span data-ttu-id="9d650-304">-----|
|`directory/file.txt`| Belirli bir dizindeki belirli bir dosyayla eşleşir. | |`directory/*.txt`| Belirli bir dizinde *. txt* uzantılı tüm dosyaları eşleştirir. | |`directory/*/appsettings.json`| Dizinler içindeki tüm *appSettings. JSON* dosyalarıyla *Dizin* klasörünün altında tam olarak bir düzey eşleşir. | |`directory/**/*.txt`| *. Txt* uzantılı tüm dosyaları, *Dizin* klasörünün altında herhangi bir yerde buldu. |</span><span class="sxs-lookup"><span data-stu-id="9d650-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9d650-305">ASP.NET Core dosya sistemi erişimini dosya sağlayıcılarının kullanımı üzerinden soyutlar.</span><span class="sxs-lookup"><span data-stu-id="9d650-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="9d650-306">Dosya sağlayıcıları ASP.NET Core Framework boyunca kullanılır:</span><span class="sxs-lookup"><span data-stu-id="9d650-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="9d650-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın [içerik kökünü](xref:fundamentals/index#content-root) ve [Web kökünü](xref:fundamentals/index#web-root) türler olarak gösterir `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="9d650-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="9d650-308">[Statik dosya ara yazılımı](xref:fundamentals/static-files) , statik dosyaları bulmak Için dosya sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="9d650-309">[Razor](xref:mvc/views/razor)sayfa ve görünümleri bulmak için dosya sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="9d650-310">.NET Core araçları, hangi dosyaların yayımlanacak olduğunu belirlemek için dosya sağlayıcılarını ve glob düzenlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="9d650-311">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9d650-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="9d650-312">Dosya sağlayıcısı arabirimleri</span><span class="sxs-lookup"><span data-stu-id="9d650-312">File Provider interfaces</span></span>

<span data-ttu-id="9d650-313">Birincil arabirim <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="9d650-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="9d650-314">`IFileProvider`şunları yapmak için yöntemler sunar:</span><span class="sxs-lookup"><span data-stu-id="9d650-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="9d650-315">Dosya bilgilerini edinin ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).</span><span class="sxs-lookup"><span data-stu-id="9d650-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="9d650-316">Dizin bilgilerini ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ) alın.</span><span class="sxs-lookup"><span data-stu-id="9d650-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="9d650-317">Değişiklik bildirimlerini ayarlayın (bir kullanarak <xref:Microsoft.Extensions.Primitives.IChangeToken> ).</span><span class="sxs-lookup"><span data-stu-id="9d650-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="9d650-318">`IFileInfo`dosyalarla çalışma için yöntemler ve özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="9d650-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="9d650-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(bayt cinsinden)</span><span class="sxs-lookup"><span data-stu-id="9d650-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="9d650-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>güncel</span><span class="sxs-lookup"><span data-stu-id="9d650-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="9d650-321">[Ifileınfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) yöntemini kullanarak dosyadan okuma yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9d650-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="9d650-322">Örnek uygulama, uygulamasında `Startup.ConfigureServices` [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama genelinde kullanılmak üzere bir dosya sağlayıcısının nasıl yapılandırılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="9d650-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="9d650-323">Dosya sağlayıcısı uygulamaları</span><span class="sxs-lookup"><span data-stu-id="9d650-323">File Provider implementations</span></span>

<span data-ttu-id="9d650-324">Üç uygulaması `IFileProvider` mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="9d650-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="9d650-325">Uygulama</span><span class="sxs-lookup"><span data-stu-id="9d650-325">Implementation</span></span> | <span data-ttu-id="9d650-326">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9d650-326">Description</span></span> |
| ---
<span data-ttu-id="9d650-327">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-328">'Blazor'</span></span>
- <span data-ttu-id="9d650-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-329">'Identity'</span></span>
- <span data-ttu-id="9d650-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-331">'Razor'</span></span>
- <span data-ttu-id="9d650-332">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-333">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-334">'Blazor'</span></span>
- <span data-ttu-id="9d650-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-335">'Identity'</span></span>
- <span data-ttu-id="9d650-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-337">'Razor'</span></span>
- <span data-ttu-id="9d650-338">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-339">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-340">'Blazor'</span></span>
- <span data-ttu-id="9d650-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-341">'Identity'</span></span>
- <span data-ttu-id="9d650-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-343">'Razor'</span></span>
- <span data-ttu-id="9d650-344">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-345">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-346">'Blazor'</span></span>
- <span data-ttu-id="9d650-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-347">'Identity'</span></span>
- <span data-ttu-id="9d650-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-349">'Razor'</span></span>
- <span data-ttu-id="9d650-350">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-351">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-352">'Blazor'</span></span>
- <span data-ttu-id="9d650-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-353">'Identity'</span></span>
- <span data-ttu-id="9d650-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-355">'Razor'</span></span>
- <span data-ttu-id="9d650-356">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-356">'SignalR' uid:</span></span> 

<span data-ttu-id="9d650-357">------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-358">'Blazor'</span></span>
- <span data-ttu-id="9d650-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-359">'Identity'</span></span>
- <span data-ttu-id="9d650-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-361">'Razor'</span></span>
- <span data-ttu-id="9d650-362">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-363">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-364">'Blazor'</span></span>
- <span data-ttu-id="9d650-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-365">'Identity'</span></span>
- <span data-ttu-id="9d650-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-367">'Razor'</span></span>
- <span data-ttu-id="9d650-368">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-369">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-370">'Blazor'</span></span>
- <span data-ttu-id="9d650-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-371">'Identity'</span></span>
- <span data-ttu-id="9d650-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-373">'Razor'</span></span>
- <span data-ttu-id="9d650-374">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-374">'SignalR' uid:</span></span> 

<span data-ttu-id="9d650-375">------ | | [Physicalfileprovider](#physicalfileprovider) | Fiziksel sağlayıcı, sistemin fiziksel dosyalarına erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="9d650-376">| | [Bildirimli Estembeddedfileprovider](#manifestembeddedfileprovider) | Bildirimde yerleşik olarak bulunan dosyalara erişmek için bildirim eklenmiş sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="9d650-377">| | [Compositefileprovider](#compositefileprovider) | Bileşik sağlayıcı, bir veya daha fazla sağlayıcıdan gelen dosyalara ve dizinlere Birleşik erişim sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="9d650-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="9d650-378">PhysicalFileProvider</span></span>

<span data-ttu-id="9d650-379"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Fiziksel dosya sistemine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="9d650-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="9d650-380">`PhysicalFileProvider`, <xref:System.IO.File?displayProperty=fullName> türü (fiziksel sağlayıcı için) ve tüm yolları bir dizine ve alt öğelerine kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="9d650-381">Bu kapsam, belirtilen dizin ve alt öğeleri dışındaki dosya sistemine erişimi engeller.</span><span class="sxs-lookup"><span data-stu-id="9d650-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="9d650-382">Oluşturma ve kullanma için en yaygın senaryo, `PhysicalFileProvider` `IFileProvider` [bağımlılık ekleme](xref:fundamentals/dependency-injection)aracılığıyla bir Oluşturucu içinde istekte bulunur.</span><span class="sxs-lookup"><span data-stu-id="9d650-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="9d650-383">Bu sağlayıcıyı doğrudan örnekleyen bir dizin yolu gereklidir ve sağlayıcı kullanılarak yapılan tüm isteklerin temel yolu olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="9d650-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="9d650-384">Aşağıdaki kod, nasıl oluşturulacağını `PhysicalFileProvider` ve dizin içeriğini ve dosya bilgilerini almak için nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="9d650-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="9d650-385">Önceki örnekteki türler:</span><span class="sxs-lookup"><span data-stu-id="9d650-385">Types in the preceding example:</span></span>

* <span data-ttu-id="9d650-386">`provider`bir `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="9d650-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="9d650-387">`contents`bir `IDirectoryContents` .</span><span class="sxs-lookup"><span data-stu-id="9d650-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="9d650-388">`fileInfo`bir `IFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="9d650-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="9d650-389">Dosya sağlayıcısı, tarafından belirtilen dizin üzerinden yinelemek `applicationRoot` veya `GetFileInfo` bir dosyanın bilgilerini almak için çağırmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d650-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="9d650-390">Dosya sağlayıcısı, dizin dışında bir erişime sahip değil `applicationRoot` .</span><span class="sxs-lookup"><span data-stu-id="9d650-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="9d650-391">Örnek uygulama, sağlayıcıyı `Startup.ConfigureServices` [ıhostingenvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)kullanarak uygulamanın sınıfında oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9d650-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="9d650-392">Bildirimli Estembeddedfileprovider</span><span class="sxs-lookup"><span data-stu-id="9d650-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="9d650-393">, <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Derlemeler içine katıştırılmış dosyalara erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="9d650-394">, `ManifestEmbeddedFileProvider` Gömülü dosyaların özgün yollarını yeniden oluşturmak için derlemeye derlenen bir bildirim kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="9d650-395">Gömülü dosyaların bir bildirimini oluşturmak için `<GenerateEmbeddedFilesManifest>` özelliğini olarak ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="9d650-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="9d650-396">[ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)ile eklenecek dosyaları belirtin:</span><span class="sxs-lookup"><span data-stu-id="9d650-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="9d650-397">Derlemeye eklemek üzere bir veya daha fazla dosya belirtmek için [Glob desenlerini](#glob-patterns) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9d650-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="9d650-398">Örnek uygulama bir oluşturur `ManifestEmbeddedFileProvider` ve şu anda yürütülmekte olan derlemeyi oluşturucusuna geçirir.</span><span class="sxs-lookup"><span data-stu-id="9d650-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="9d650-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9d650-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="9d650-400">Ek aşırı yüklemeler şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="9d650-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="9d650-401">Göreli bir dosya yolu belirtin.</span><span class="sxs-lookup"><span data-stu-id="9d650-401">Specify a relative file path.</span></span>
* <span data-ttu-id="9d650-402">Dosya kapsamını son değiştirilme tarihine kadar.</span><span class="sxs-lookup"><span data-stu-id="9d650-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="9d650-403">Katıştırılmış dosya bildirimini içeren gömülü kaynağı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="9d650-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="9d650-404">Yüklemek</span><span class="sxs-lookup"><span data-stu-id="9d650-404">Overload</span></span> | <span data-ttu-id="9d650-405">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9d650-405">Description</span></span> |
| ---
<span data-ttu-id="9d650-406">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-407">'Blazor'</span></span>
- <span data-ttu-id="9d650-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-408">'Identity'</span></span>
- <span data-ttu-id="9d650-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-410">'Razor'</span></span>
- <span data-ttu-id="9d650-411">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-412">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-413">'Blazor'</span></span>
- <span data-ttu-id="9d650-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-414">'Identity'</span></span>
- <span data-ttu-id="9d650-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-416">'Razor'</span></span>
- <span data-ttu-id="9d650-417">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-417">'SignalR' uid:</span></span> 

<span data-ttu-id="9d650-418">---- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-419">'Blazor'</span></span>
- <span data-ttu-id="9d650-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-420">'Identity'</span></span>
- <span data-ttu-id="9d650-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-422">'Razor'</span></span>
- <span data-ttu-id="9d650-423">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-424">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-425">'Blazor'</span></span>
- <span data-ttu-id="9d650-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-426">'Identity'</span></span>
- <span data-ttu-id="9d650-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-428">'Razor'</span></span>
- <span data-ttu-id="9d650-429">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9d650-430">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9d650-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9d650-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9d650-431">'Blazor'</span></span>
- <span data-ttu-id="9d650-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9d650-432">'Identity'</span></span>
- <span data-ttu-id="9d650-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9d650-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="9d650-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9d650-434">'Razor'</span></span>
- <span data-ttu-id="9d650-435">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9d650-435">'SignalR' uid:</span></span> 

<span data-ttu-id="9d650-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | İsteğe bağlı bir `root` göreli yol parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9d650-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="9d650-437">`root` <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> Belirtilen yol altında bu kaynaklara yönelik olarak yapılan çağrıları belirtin.</span><span class="sxs-lookup"><span data-stu-id="9d650-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="9d650-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | İsteğe bağlı `root` göreli yol parametresini ve bir `lastModified` date ( <xref:System.DateTimeOffset> ) parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9d650-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="9d650-439">`lastModified`Tarih kapsamları <xref:Microsoft.Extensions.FileProviders.IFileInfo> tarafından döndürülen örneklerin son değiştirilme tarihidir <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="9d650-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="9d650-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | İsteğe bağlı bir `root` göreli yolu, `lastModified` tarihi ve parametreleri kabul eder `manifestName` .</span><span class="sxs-lookup"><span data-stu-id="9d650-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="9d650-441">, `manifestName` Bildirimi içeren gömülü kaynağın adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9d650-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="9d650-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="9d650-442">CompositeFileProvider</span></span>

<span data-ttu-id="9d650-443">, <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` Birden çok sağlayıcıdan dosyalarla çalışmak için tek bir arabirim ortaya çıkaran örnekleri birleştirir.</span><span class="sxs-lookup"><span data-stu-id="9d650-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="9d650-444">Oluştururken `CompositeFileProvider` bir veya daha fazla `IFileProvider` örneği oluşturucuya geçirin.</span><span class="sxs-lookup"><span data-stu-id="9d650-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="9d650-445">Örnek uygulamada, `PhysicalFileProvider` ve, `ManifestEmbeddedFileProvider` `CompositeFileProvider` uygulamanın hizmet kapsayıcısında kayıtlı bir dosya sağlar:</span><span class="sxs-lookup"><span data-stu-id="9d650-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="9d650-446">Değişiklikleri izle</span><span class="sxs-lookup"><span data-stu-id="9d650-446">Watch for changes</span></span>

<span data-ttu-id="9d650-447">[IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) yöntemi, değişiklikler için bir veya daha fazla dosya ya da dizin izlemek üzere bir senaryo sağlar.</span><span class="sxs-lookup"><span data-stu-id="9d650-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="9d650-448">`Watch`birden çok dosya belirtmek için [Glob desenlerini](#glob-patterns) içerebilen bir yol dizesi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9d650-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="9d650-449">`Watch`döndürür <xref:Microsoft.Extensions.Primitives.IChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="9d650-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="9d650-450">Değişiklik belirteci şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="9d650-450">The change token exposes:</span></span>

* <span data-ttu-id="9d650-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Bir değişikliğin oluşup oluşmadığını tespit etmek için incelenebilir bir özellik.</span><span class="sxs-lookup"><span data-stu-id="9d650-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="9d650-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Belirtilen yol dizesinde değişiklikler algılandığında çağırılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="9d650-453">Her değişiklik belirteci yalnızca, ilişkili geri çağırma işlemini tek bir değişikliğe yanıt olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="9d650-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="9d650-454">Sabit izlemeyi etkinleştirmek için bir <xref:System.Threading.Tasks.TaskCompletionSource`1> (aşağıda gösterilmiştir) kullanın veya `IChangeToken` değişiklikleri yanıt olarak yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9d650-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="9d650-455">Örnek uygulamada, *WatchConsole* konsol uygulaması bir metin dosyası her değiştirildiğinde bir ileti görüntüleyecek şekilde yapılandırılmıştır:</span><span class="sxs-lookup"><span data-stu-id="9d650-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="9d650-456">Docker kapsayıcıları ve ağ paylaşımları gibi bazı dosya sistemleri, değişiklik bildirimlerini güvenilir bir şekilde gönderemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="9d650-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="9d650-457">`DOTNET_USE_POLLING_FILE_WATCHER`Ortam değişkenini, `1` `true` her dört saniyede bir değişiklikler için dosya sistemini yoklamak üzere veya olarak ayarlayın (yapılandırılamaz).</span><span class="sxs-lookup"><span data-stu-id="9d650-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="9d650-458">Glob desenleri</span><span class="sxs-lookup"><span data-stu-id="9d650-458">Glob patterns</span></span>

<span data-ttu-id="9d650-459">Dosya sistemi yolları, *Glob (veya glob) desenleri*adlı joker karakter desenleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="9d650-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="9d650-460">Bu desenlerle dosya gruplarını belirtin.</span><span class="sxs-lookup"><span data-stu-id="9d650-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="9d650-461">İki joker karakter şunlardır `*` `**` :</span><span class="sxs-lookup"><span data-stu-id="9d650-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="9d650-462">Geçerli klasör düzeyindeki her şeyi, dosya adını veya herhangi bir dosya uzantısını eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="9d650-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="9d650-463">Eşleşmeler, `/` `.` dosya yolundaki karakterler ile sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9d650-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="9d650-464">Birden çok dizin düzeyindeki tüm öğeleri eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="9d650-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="9d650-465">, Bir Dizin hiyerarşisinde birçok dosya yinelemeli olarak eşleşmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9d650-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="9d650-466">**Glob deseninin örnekleri**</span><span class="sxs-lookup"><span data-stu-id="9d650-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="9d650-467">Belirli bir dizindeki belirli bir dosyayla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="9d650-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="9d650-468">Belirli bir dizinde *. txt* uzantılı tüm dosyaları eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="9d650-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="9d650-469">`appsettings.json`Dizinteki tüm dosyaları, *Dizin* klasörünün altında tam olarak bir düzey eşler.</span><span class="sxs-lookup"><span data-stu-id="9d650-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="9d650-470">*. Txt* uzantılı tüm dosyaları, *Dizin* klasörünün altında herhangi bir yerde buldu.</span><span class="sxs-lookup"><span data-stu-id="9d650-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
