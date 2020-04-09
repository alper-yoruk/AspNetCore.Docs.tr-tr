---
title: ASP.NET Core'daki Dosya Sağlayıcıları
author: rick-anderson
description: ASP.NET Core özetleri dosya sistemine Dosya Sağlayıcıları'nın kullanımı yoluyla nasıl erişebildiğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/file-providers
ms.openlocfilehash: 25607bd534cae05a6c6b11fa6d8902faa3c0684c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751095"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="a561f-103">ASP.NET Core'daki Dosya Sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="a561f-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="a561f-104">Yazar: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a561f-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a561f-105">ASP.NET Core özetleri dosya sistemi erişim Dosya Sağlayıcıları kullanımı yoluyla.</span><span class="sxs-lookup"><span data-stu-id="a561f-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="a561f-106">Dosya Sağlayıcıları ASP.NET Core çerçevesi boyunca kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="a561f-107">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="a561f-107">For example:</span></span>

* <span data-ttu-id="a561f-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>uygulamanın [içerik kökünü](xref:fundamentals/index#content-root) ve web `IFileProvider` [kökünü](xref:fundamentals/index#web-root) tür olarak ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="a561f-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="a561f-109">[Statik Dosya Middleware](xref:fundamentals/static-files) statik dosyaları bulmak için Dosya Sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="a561f-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="a561f-110">[Razor,](xref:mvc/views/razor) sayfaları ve görünümleri bulmak için Dosya Sağlayıcıları'nı kullanır.</span><span class="sxs-lookup"><span data-stu-id="a561f-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="a561f-111">.NET Core araçlama, hangi dosyaların yayımlanması gerektiğini belirtmek için Dosya Sağlayıcıları ve glob desenlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="a561f-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="a561f-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a561f-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="a561f-113">Dosya Sağlayıcı arabirimleri</span><span class="sxs-lookup"><span data-stu-id="a561f-113">File Provider interfaces</span></span>

<span data-ttu-id="a561f-114">Birincil <xref:Microsoft.Extensions.FileProviders.IFileProvider>arabirim.</span><span class="sxs-lookup"><span data-stu-id="a561f-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="a561f-115">`IFileProvider`yöntemleri ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="a561f-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="a561f-116">Dosya bilgilerini<xref:Microsoft.Extensions.FileProviders.IFileInfo>alma ( ).</span><span class="sxs-lookup"><span data-stu-id="a561f-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="a561f-117">Dizin bilgileri<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>edinin ( ).</span><span class="sxs-lookup"><span data-stu-id="a561f-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="a561f-118">Değişiklik bildirimlerini ayarlama (bir <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="a561f-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="a561f-119">`IFileInfo`dosyalarla çalışmak için yöntem ve özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="a561f-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="a561f-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(bayt olarak)</span><span class="sxs-lookup"><span data-stu-id="a561f-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="a561f-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Tarih</span><span class="sxs-lookup"><span data-stu-id="a561f-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="a561f-122"><xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> Yöntemi kullanarak dosyadan okuyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a561f-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="a561f-123">*FileProviderSample* örnek [uygulaması, bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla `Startup.ConfigureServices` uygulama boyunca kullanılmak üzere bir Dosya Sağlayıcısının nasıl yapılandırılabildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="a561f-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="a561f-124">Dosya Sağlayıcı uygulamaları</span><span class="sxs-lookup"><span data-stu-id="a561f-124">File Provider implementations</span></span>

<span data-ttu-id="a561f-125">Aşağıdaki tabloda `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="a561f-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="a561f-126">Uygulama</span><span class="sxs-lookup"><span data-stu-id="a561f-126">Implementation</span></span> | <span data-ttu-id="a561f-127">Açıklama</span><span class="sxs-lookup"><span data-stu-id="a561f-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="a561f-128">KompozitDosya Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="a561f-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="a561f-129">Bir veya daha fazla sağlayıcıdan gelen dosyalara ve dizinlere birleşik erişim sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="a561f-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a561f-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="a561f-131">Derlemelere katışmış dosyalara erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="a561f-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a561f-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="a561f-133">Sistemin fiziksel dosyalarına erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="a561f-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a561f-134">PhysicalFileProvider</span></span>

<span data-ttu-id="a561f-135">Fiziksel <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> dosya sistemine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="a561f-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="a561f-136">`PhysicalFileProvider`<xref:System.IO.File?displayProperty=fullName> türünü (fiziksel sağlayıcı için) kullanır ve bir dizin ve çocuklarına giden tüm yolları scopelar.</span><span class="sxs-lookup"><span data-stu-id="a561f-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="a561f-137">Bu kapsam, belirtilen dizin ve altlarının dışında dosya sistemine erişimi engeller.</span><span class="sxs-lookup"><span data-stu-id="a561f-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="a561f-138">Oluşturmak ve kullanmak `PhysicalFileProvider` için en yaygın senaryo `IFileProvider` [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla bir yapıcı bir istek.</span><span class="sxs-lookup"><span data-stu-id="a561f-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="a561f-139">Bu sağlayıcıyı doğrudan anında kullanırken, mutlak bir dizin yolu gereklidir ve sağlayıcı kullanılarak yapılan tüm istekler için temel yol olarak hizmet vermektedir.</span><span class="sxs-lookup"><span data-stu-id="a561f-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="a561f-140">Glob desenleri dizin yolunda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="a561f-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="a561f-141">Aşağıdaki kod, dizin `PhysicalFileProvider` içeriği ve dosya bilgilerini elde etmek için nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="a561f-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="a561f-142">Önceki örnekteki türler:</span><span class="sxs-lookup"><span data-stu-id="a561f-142">Types in the preceding example:</span></span>

* <span data-ttu-id="a561f-143">`provider`bir `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="a561f-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="a561f-144">`contents`bir `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="a561f-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="a561f-145">`fileInfo`bir `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="a561f-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="a561f-146">Dosya Sağlayıcı, tarafından `applicationRoot` belirtilen dizini doğrulamak veya dosya `GetFileInfo` bilgilerini almak için aramak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a561f-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="a561f-147">Glob desenleri `GetFileInfo` yönteme geçirilemez.</span><span class="sxs-lookup"><span data-stu-id="a561f-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="a561f-148">Dosya Sağlayıcısı'nın `applicationRoot` dizin dışında erişimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="a561f-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="a561f-149">*FileProviderSample* örnek uygulaması kullanarak `Startup.ConfigureServices` <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>yöntem sağlayıcı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="a561f-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="a561f-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a561f-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="a561f-151">Derlemeler <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> içinde katıştırılmış dosyalara erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="a561f-152">Katıştılı `ManifestEmbeddedFileProvider` dosyaların özgün yollarını yeniden oluşturmak için derlemede derlenmiş bir bildirim kullanır.</span><span class="sxs-lookup"><span data-stu-id="a561f-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="a561f-153">Katıştılmış dosyaların bir bildirimini oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="a561f-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="a561f-154">[Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet paketini projenize ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a561f-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="a561f-155">Özelliği `<GenerateEmbeddedFilesManifest>` ' `true`ye ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="a561f-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="a561f-156">[ \<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)ile katıştırmak için dosyaları belirtin:</span><span class="sxs-lookup"><span data-stu-id="a561f-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="a561f-157">Derlemeye katıştırmak için bir veya daha fazla dosya belirtmek için [glob desenleri](#glob-patterns) kullanın.</span><span class="sxs-lookup"><span data-stu-id="a561f-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="a561f-158">*FileProviderSample* örnek uygulaması bir `ManifestEmbeddedFileProvider` oluşturma zaif ve şu anda yürütülen montaj onun oluşturucu ya da geçer.</span><span class="sxs-lookup"><span data-stu-id="a561f-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="a561f-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a561f-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="a561f-160">Ek aşırı yüklemeler şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="a561f-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="a561f-161">Göreli bir dosya yolu belirtin.</span><span class="sxs-lookup"><span data-stu-id="a561f-161">Specify a relative file path.</span></span>
* <span data-ttu-id="a561f-162">Dosyaları son değiştirilmiş tarihe kadar kapsam.</span><span class="sxs-lookup"><span data-stu-id="a561f-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="a561f-163">Katıştırılmış dosya bildirimini içeren katıştırılmış kaynağı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="a561f-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="a561f-164">Aşırı</span><span class="sxs-lookup"><span data-stu-id="a561f-164">Overload</span></span> | <span data-ttu-id="a561f-165">Açıklama</span><span class="sxs-lookup"><span data-stu-id="a561f-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="a561f-166">İsteğe bağlı `root` göreli yol parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a561f-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="a561f-167">`root` Sağlanan yol altında <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> bu kaynaklara yapılan kapsam çağrılarını belirtin.</span><span class="sxs-lookup"><span data-stu-id="a561f-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="a561f-168">İsteğe bağlı `root` göreli yol `lastModified` parametresi ve tarih (<xref:System.DateTimeOffset>) parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a561f-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="a561f-169">Tarih, `lastModified` <xref:Microsoft.Extensions.FileProviders.IFileInfo> <xref:Microsoft.Extensions.FileProviders.IFileProvider>'nin döndürülen örnekleri için son değişiklik tarihini kapsama</span><span class="sxs-lookup"><span data-stu-id="a561f-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="a561f-170">İsteğe bağlı `root` göreli `lastModified` yolu, `manifestName` tarihi ve parametreleri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a561f-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="a561f-171">Bildirimiçeren `manifestName` katıştırılmış kaynağın adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="a561f-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="a561f-172">KompozitDosya Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="a561f-172">CompositeFileProvider</span></span>

<span data-ttu-id="a561f-173">Örnekleri <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> birleştirerek, `IFileProvider` birden çok sağlayıcıdan gelen dosyalarla çalışmak için tek bir arabirimi açığa çıkarır.</span><span class="sxs-lookup"><span data-stu-id="a561f-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="a561f-174">`CompositeFileProvider`Oluştururken, bir veya `IFileProvider` daha fazla örneği oluşturucuya geçirin.</span><span class="sxs-lookup"><span data-stu-id="a561f-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="a561f-175">*FileProviderSample* örnek uygulamasında, `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` ve a, `CompositeFileProvider` uygulamanın hizmet kapsayıcısında kayıtlı bir dosya sağlar.</span><span class="sxs-lookup"><span data-stu-id="a561f-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="a561f-176">Projenin `Startup.ConfigureServices` yönteminde aşağıdaki kod bulunur:</span><span class="sxs-lookup"><span data-stu-id="a561f-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="a561f-177">Değişikliklere dikkat edin</span><span class="sxs-lookup"><span data-stu-id="a561f-177">Watch for changes</span></span>

<span data-ttu-id="a561f-178">Yöntem, <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> değişiklikler için bir veya daha fazla dosya veya dizin izlemek için bir senaryo sağlar.</span><span class="sxs-lookup"><span data-stu-id="a561f-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="a561f-179">Yöntem: `Watch`</span><span class="sxs-lookup"><span data-stu-id="a561f-179">The `Watch` method:</span></span>

* <span data-ttu-id="a561f-180">Birden çok dosya belirtmek için [glob desenleri](#glob-patterns) kullanabilen bir dosya yolu dizesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a561f-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="a561f-181">Bir <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="a561f-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="a561f-182">Ortaya çıkan değişiklik belirteci şunları ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="a561f-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="a561f-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;Bir değişikliğin meydana geldiğini belirlemek için denetlenebilecek bir özellik.</span><span class="sxs-lookup"><span data-stu-id="a561f-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="a561f-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Belirtilen yol dizesine değişiklikler algılandığında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="a561f-185">Her değişiklik belirteci yalnızca tek bir değişikliğe yanıt olarak ilişkili geri arama çağırır.</span><span class="sxs-lookup"><span data-stu-id="a561f-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="a561f-186">Sürekli izleme sağlamak için, <xref:System.Threading.Tasks.TaskCompletionSource`1> bir (aşağıda gösterilmiştir) kullanın veya değişikliklere yanıt olarak örnekleri yeniden oluşturun. `IChangeToken`</span><span class="sxs-lookup"><span data-stu-id="a561f-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="a561f-187">*WatchConsole* örnek *uygulaması, TextFiles* dizinindeki *bir .txt* dosyası değiştirildiğinde bir ileti yazar:</span><span class="sxs-lookup"><span data-stu-id="a561f-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="a561f-188">Docker kapsayıcıları ve ağ paylaşımları gibi bazı dosya sistemleri, güvenilir bir şekilde değişiklik bildirimleri göndermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="a561f-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="a561f-189">Ortam `DOTNET_USE_POLLING_FILE_WATCHER` değişkenini `1` `true` her dört saniyede bir (yapılandırılamaz) değişiklikler için dosya sistemini yoklamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="a561f-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="a561f-190">Glob desenleri</span><span class="sxs-lookup"><span data-stu-id="a561f-190">Glob patterns</span></span>

<span data-ttu-id="a561f-191">Dosya sistemi yolları *glob (veya globbing) desenleri*olarak adlandırılan joker karakter desenleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="a561f-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="a561f-192">Bu desenlere sahip dosya gruplarını belirtin.</span><span class="sxs-lookup"><span data-stu-id="a561f-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="a561f-193">İki joker karakter ve `*` `**`şunlardır:</span><span class="sxs-lookup"><span data-stu-id="a561f-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="a561f-194">Geçerli klasör düzeyindeki herhangi bir şeyle, dosya adıyla veya dosya uzantısını eşleşir.</span><span class="sxs-lookup"><span data-stu-id="a561f-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="a561f-195">Eşleşmeler tarafından `/` `.` sonlandırılır ve dosya yolundaki karakterler.</span><span class="sxs-lookup"><span data-stu-id="a561f-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="a561f-196">Birden çok dizin düzeyindeki her şeyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="a561f-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="a561f-197">Dizin hiyerarşisi içindeki birçok dosyayı özyinelemeli olarak eşleştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a561f-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="a561f-198">Aşağıdaki tabloglob desenleri ortak örnekler sağlar.</span><span class="sxs-lookup"><span data-stu-id="a561f-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="a561f-199">Desen</span><span class="sxs-lookup"><span data-stu-id="a561f-199">Pattern</span></span>  |<span data-ttu-id="a561f-200">Açıklama</span><span class="sxs-lookup"><span data-stu-id="a561f-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="a561f-201">Belirli bir dizindeki belirli bir dosyayla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="a561f-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="a561f-202">Tüm dosyaları *.txt* uzantısı ile belirli bir dizinde eşler.</span><span class="sxs-lookup"><span data-stu-id="a561f-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="a561f-203">Dizinlerdeki tüm *appsettings.json* dosyalarını *dizin* klasöründen tam olarak bir düzey aşağı eşleştirin.</span><span class="sxs-lookup"><span data-stu-id="a561f-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="a561f-204">Dizin klasörü altında bulunan *bir .txt* uzantısı ile tüm dosyaları eşleşir. *directory*</span><span class="sxs-lookup"><span data-stu-id="a561f-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a561f-205">ASP.NET Core özetleri dosya sistemi erişim Dosya Sağlayıcıları kullanımı yoluyla.</span><span class="sxs-lookup"><span data-stu-id="a561f-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="a561f-206">Dosya Sağlayıcıları ASP.NET Çekirdek çerçevesi boyunca kullanılır:</span><span class="sxs-lookup"><span data-stu-id="a561f-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="a561f-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın [içerik kökünü](xref:fundamentals/index#content-root) ve web `IFileProvider` [kökünü](xref:fundamentals/index#web-root) tür olarak ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="a561f-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="a561f-208">[Statik Dosya Middleware](xref:fundamentals/static-files) statik dosyaları bulmak için Dosya Sağlayıcıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="a561f-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="a561f-209">[Razor,](xref:mvc/views/razor) sayfaları ve görünümleri bulmak için Dosya Sağlayıcıları'nı kullanır.</span><span class="sxs-lookup"><span data-stu-id="a561f-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="a561f-210">.NET Core araçlama, hangi dosyaların yayımlanması gerektiğini belirtmek için Dosya Sağlayıcıları ve glob desenlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="a561f-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="a561f-211">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a561f-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="a561f-212">Dosya Sağlayıcı arabirimleri</span><span class="sxs-lookup"><span data-stu-id="a561f-212">File Provider interfaces</span></span>

<span data-ttu-id="a561f-213">Birincil <xref:Microsoft.Extensions.FileProviders.IFileProvider>arabirim.</span><span class="sxs-lookup"><span data-stu-id="a561f-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="a561f-214">`IFileProvider`yöntemleri ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="a561f-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="a561f-215">Dosya bilgilerini<xref:Microsoft.Extensions.FileProviders.IFileInfo>alma ( ).</span><span class="sxs-lookup"><span data-stu-id="a561f-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="a561f-216">Dizin bilgileri<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>edinin ( ).</span><span class="sxs-lookup"><span data-stu-id="a561f-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="a561f-217">Değişiklik bildirimlerini ayarlama (bir <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="a561f-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="a561f-218">`IFileInfo`dosyalarla çalışmak için yöntem ve özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="a561f-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="a561f-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(bayt olarak)</span><span class="sxs-lookup"><span data-stu-id="a561f-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="a561f-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Tarih</span><span class="sxs-lookup"><span data-stu-id="a561f-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="a561f-221">[Dosyadan IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) yöntemini kullanarak okuyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a561f-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="a561f-222">Örnek uygulama, [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla uygulama `Startup.ConfigureServices` boyunca kullanılmak üzere bir Dosya Sağlayıcısının nasıl yapılandırılabildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="a561f-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="a561f-223">Dosya Sağlayıcı uygulamaları</span><span class="sxs-lookup"><span data-stu-id="a561f-223">File Provider implementations</span></span>

<span data-ttu-id="a561f-224">Üç uygulama `IFileProvider` mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="a561f-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="a561f-225">Uygulama</span><span class="sxs-lookup"><span data-stu-id="a561f-225">Implementation</span></span> | <span data-ttu-id="a561f-226">Açıklama</span><span class="sxs-lookup"><span data-stu-id="a561f-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="a561f-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a561f-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="a561f-228">Fiziksel sağlayıcı, sistemin fiziksel dosyalarına erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="a561f-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a561f-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="a561f-230">Manifest katıştırılmış sağlayıcı derlemeler gömülü dosyalara erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="a561f-231">KompozitDosya Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="a561f-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="a561f-232">Bileşik sağlayıcı, bir veya daha fazla sağlayıcıdan gelen dosyalara ve dizinlere birleşik erişim sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="a561f-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a561f-233">PhysicalFileProvider</span></span>

<span data-ttu-id="a561f-234">Fiziksel <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> dosya sistemine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="a561f-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="a561f-235">`PhysicalFileProvider`<xref:System.IO.File?displayProperty=fullName> türünü (fiziksel sağlayıcı için) kullanır ve bir dizin ve çocuklarına giden tüm yolları scopelar.</span><span class="sxs-lookup"><span data-stu-id="a561f-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="a561f-236">Bu kapsam, belirtilen dizin ve altlarının dışında dosya sistemine erişimi engeller.</span><span class="sxs-lookup"><span data-stu-id="a561f-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="a561f-237">Oluşturmak ve kullanmak `PhysicalFileProvider` için en yaygın senaryo `IFileProvider` [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla bir yapıcı bir istek.</span><span class="sxs-lookup"><span data-stu-id="a561f-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="a561f-238">Bu sağlayıcıyı doğrudan anında kullanırken, bir dizin yolu gereklidir ve sağlayıcı kullanılarak yapılan tüm istekler için temel yol olarak hizmet vermektedir.</span><span class="sxs-lookup"><span data-stu-id="a561f-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="a561f-239">Aşağıdaki kod, dizin `PhysicalFileProvider` içeriği ve dosya bilgilerini elde etmek için nasıl oluşturulup kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="a561f-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="a561f-240">Önceki örnekteki türler:</span><span class="sxs-lookup"><span data-stu-id="a561f-240">Types in the preceding example:</span></span>

* <span data-ttu-id="a561f-241">`provider`bir `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="a561f-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="a561f-242">`contents`bir `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="a561f-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="a561f-243">`fileInfo`bir `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="a561f-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="a561f-244">Dosya Sağlayıcı, tarafından `applicationRoot` belirtilen dizini doğrulamak veya dosya `GetFileInfo` bilgilerini almak için aramak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a561f-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="a561f-245">Dosya Sağlayıcısı'nın `applicationRoot` dizin dışında erişimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="a561f-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="a561f-246">Örnek uygulama `Startup.ConfigureServices` [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)kullanarak uygulamanın sınıfında sağlayıcı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="a561f-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="a561f-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a561f-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="a561f-248">Derlemeler <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> içinde katıştırılmış dosyalara erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="a561f-249">Katıştılı `ManifestEmbeddedFileProvider` dosyaların özgün yollarını yeniden oluşturmak için derlemede derlenmiş bir bildirim kullanır.</span><span class="sxs-lookup"><span data-stu-id="a561f-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="a561f-250">Katıştılmış dosyaların bir bildirimini `<GenerateEmbeddedFilesManifest>` oluşturmak `true`için özelliği ' ye göre ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="a561f-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="a561f-251">[ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)ile katıştırmak için dosyaları belirtin:</span><span class="sxs-lookup"><span data-stu-id="a561f-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="a561f-252">Derlemeye katıştırmak için bir veya daha fazla dosya belirtmek için [glob desenleri](#glob-patterns) kullanın.</span><span class="sxs-lookup"><span data-stu-id="a561f-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="a561f-253">Örnek uygulama bir `ManifestEmbeddedFileProvider` oluşturmaz ve şu anda çalıştırılamakta olan derlemeyi oluşturucuya geçirir.</span><span class="sxs-lookup"><span data-stu-id="a561f-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="a561f-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a561f-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="a561f-255">Ek aşırı yüklemeler şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="a561f-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="a561f-256">Göreli bir dosya yolu belirtin.</span><span class="sxs-lookup"><span data-stu-id="a561f-256">Specify a relative file path.</span></span>
* <span data-ttu-id="a561f-257">Dosyaları son değiştirilmiş tarihe kadar kapsam.</span><span class="sxs-lookup"><span data-stu-id="a561f-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="a561f-258">Katıştırılmış dosya bildirimini içeren katıştırılmış kaynağı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="a561f-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="a561f-259">Aşırı</span><span class="sxs-lookup"><span data-stu-id="a561f-259">Overload</span></span> | <span data-ttu-id="a561f-260">Açıklama</span><span class="sxs-lookup"><span data-stu-id="a561f-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="a561f-261">İsteğe bağlı `root` göreli yol parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a561f-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="a561f-262">`root` Sağlanan yol altında <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> bu kaynaklara yapılan kapsam çağrılarını belirtin.</span><span class="sxs-lookup"><span data-stu-id="a561f-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="a561f-263">İsteğe bağlı `root` göreli yol `lastModified` parametresi ve tarih (<xref:System.DateTimeOffset>) parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a561f-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="a561f-264">Tarih, `lastModified` <xref:Microsoft.Extensions.FileProviders.IFileInfo> <xref:Microsoft.Extensions.FileProviders.IFileProvider>'nin döndürülen örnekleri için son değişiklik tarihini kapsama</span><span class="sxs-lookup"><span data-stu-id="a561f-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="a561f-265">İsteğe bağlı `root` göreli `lastModified` yolu, `manifestName` tarihi ve parametreleri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a561f-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="a561f-266">Bildirimiçeren `manifestName` katıştırılmış kaynağın adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="a561f-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="a561f-267">KompozitDosya Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="a561f-267">CompositeFileProvider</span></span>

<span data-ttu-id="a561f-268">Örnekleri <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> birleştirerek, `IFileProvider` birden çok sağlayıcıdan gelen dosyalarla çalışmak için tek bir arabirimi açığa çıkarır.</span><span class="sxs-lookup"><span data-stu-id="a561f-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="a561f-269">`CompositeFileProvider`Oluştururken, bir veya `IFileProvider` daha fazla örneği oluşturucuya geçirin.</span><span class="sxs-lookup"><span data-stu-id="a561f-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="a561f-270">Örnek uygulamada, uygulamanın `PhysicalFileProvider` `ManifestEmbeddedFileProvider` hizmet kapsayıcısında `CompositeFileProvider` kayıtlı bir dosya sağlar:</span><span class="sxs-lookup"><span data-stu-id="a561f-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="a561f-271">Değişikliklere dikkat edin</span><span class="sxs-lookup"><span data-stu-id="a561f-271">Watch for changes</span></span>

<span data-ttu-id="a561f-272">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) yöntemi, değişiklikler için bir veya daha fazla dosya veya dizin izlemek için bir senaryo sağlar.</span><span class="sxs-lookup"><span data-stu-id="a561f-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="a561f-273">`Watch`birden çok dosya belirtmek için [glob desenleri](#glob-patterns) kullanabilirsiniz bir yol dize, kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a561f-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="a561f-274">`Watch`bir <xref:Microsoft.Extensions.Primitives.IChangeToken>' yi döndürür.</span><span class="sxs-lookup"><span data-stu-id="a561f-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="a561f-275">Değişiklik belirteci şunları ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="a561f-275">The change token exposes:</span></span>

* <span data-ttu-id="a561f-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Bir değişikliğin meydana geldiğini belirlemek için denetlenebilecek bir özellik.</span><span class="sxs-lookup"><span data-stu-id="a561f-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="a561f-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Belirtilen yol dizesine değişiklikler algılandığında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a561f-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="a561f-278">Her değişiklik belirteci yalnızca tek bir değişikliğe yanıt olarak ilişkili geri arama çağırır.</span><span class="sxs-lookup"><span data-stu-id="a561f-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="a561f-279">Sürekli izleme sağlamak için, <xref:System.Threading.Tasks.TaskCompletionSource`1> bir (aşağıda gösterilmiştir) kullanın veya değişikliklere yanıt olarak örnekleri yeniden oluşturun. `IChangeToken`</span><span class="sxs-lookup"><span data-stu-id="a561f-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="a561f-280">Örnek uygulamada, *WatchConsole* konsol uygulaması, bir metin dosyası değiştirildiğinde bir iletiyi görüntülemek üzere yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="a561f-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="a561f-281">Docker kapsayıcıları ve ağ paylaşımları gibi bazı dosya sistemleri, güvenilir bir şekilde değişiklik bildirimleri göndermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="a561f-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="a561f-282">Ortam `DOTNET_USE_POLLING_FILE_WATCHER` değişkenini `1` `true` her dört saniyede bir (yapılandırılamaz) değişiklikler için dosya sistemini yoklamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="a561f-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="a561f-283">Glob desenleri</span><span class="sxs-lookup"><span data-stu-id="a561f-283">Glob patterns</span></span>

<span data-ttu-id="a561f-284">Dosya sistemi yolları *glob (veya globbing) desenleri*olarak adlandırılan joker karakter desenleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="a561f-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="a561f-285">Bu desenlere sahip dosya gruplarını belirtin.</span><span class="sxs-lookup"><span data-stu-id="a561f-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="a561f-286">İki joker karakter ve `*` `**`şunlardır:</span><span class="sxs-lookup"><span data-stu-id="a561f-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="a561f-287">Geçerli klasör düzeyindeki herhangi bir şeyle, dosya adıyla veya dosya uzantısını eşleşir.</span><span class="sxs-lookup"><span data-stu-id="a561f-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="a561f-288">Eşleşmeler tarafından `/` `.` sonlandırılır ve dosya yolundaki karakterler.</span><span class="sxs-lookup"><span data-stu-id="a561f-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="a561f-289">Birden çok dizin düzeyindeki her şeyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="a561f-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="a561f-290">Dizin hiyerarşisi içindeki birçok dosyayı özyinelemeli olarak eşleştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a561f-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="a561f-291">**Glob desen örnekleri**</span><span class="sxs-lookup"><span data-stu-id="a561f-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="a561f-292">Belirli bir dizindeki belirli bir dosyayla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="a561f-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="a561f-293">Tüm dosyaları *.txt* uzantısı ile belirli bir dizinde eşler.</span><span class="sxs-lookup"><span data-stu-id="a561f-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="a561f-294">Dizinlerdeki tüm `appsettings.json` dosyaları *dizin* klasöründen tam olarak bir düzey aşağı eşleştirin.</span><span class="sxs-lookup"><span data-stu-id="a561f-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="a561f-295">*Dizin* klasörü altında bulunan *.txt* uzantısı ile tüm dosyaları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="a561f-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
