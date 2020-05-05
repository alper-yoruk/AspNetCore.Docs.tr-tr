---
title: ASP.NET Core dosya sağlayıcıları
author: rick-anderson
description: Dosya sağlayıcılarının kullanımı aracılığıyla dosya sistemi erişimini ASP.NET Core nasıl soyutleyeceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/file-providers
ms.openlocfilehash: 2f1151d7854aeeb3e315d0de2b0be5267fe2e8f0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776291"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="b62cb-103">ASP.NET Core dosya sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="b62cb-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="b62cb-104">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="b62cb-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b62cb-105">ASP.NET Core dosya sistemi erişimini dosya sağlayıcılarının kullanımı üzerinden soyutlar.</span><span class="sxs-lookup"><span data-stu-id="b62cb-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="b62cb-106">Dosya sağlayıcıları ASP.NET Core Framework boyunca kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="b62cb-107">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b62cb-107">For example:</span></span>

* <span data-ttu-id="b62cb-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>uygulamanın [içerik kökünü](xref:fundamentals/index#content-root) ve [Web kökünü](xref:fundamentals/index#web-root) türler olarak `IFileProvider` gösterir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="b62cb-109">[Statik dosya ara yazılımı](xref:fundamentals/static-files) , statik dosyaları bulmak Için dosya sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="b62cb-110">[Razor](xref:mvc/views/razor) , sayfa ve görünümleri bulmak Için dosya sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="b62cb-111">.NET Core araçları, hangi dosyaların yayımlanacak olduğunu belirlemek için dosya sağlayıcılarını ve glob düzenlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="b62cb-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b62cb-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="b62cb-113">Dosya sağlayıcısı arabirimleri</span><span class="sxs-lookup"><span data-stu-id="b62cb-113">File Provider interfaces</span></span>

<span data-ttu-id="b62cb-114">Birincil arabirim <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="b62cb-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="b62cb-115">`IFileProvider`şunları yapmak için yöntemler sunar:</span><span class="sxs-lookup"><span data-stu-id="b62cb-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="b62cb-116">Dosya bilgilerini edinin (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="b62cb-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="b62cb-117">Dizin bilgilerini (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>) alın.</span><span class="sxs-lookup"><span data-stu-id="b62cb-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="b62cb-118">Değişiklik bildirimlerini ayarlayın (bir <xref:Microsoft.Extensions.Primitives.IChangeToken>kullanarak).</span><span class="sxs-lookup"><span data-stu-id="b62cb-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="b62cb-119">`IFileInfo`dosyalarla çalışma için yöntemler ve özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="b62cb-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="b62cb-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(bayt cinsinden)</span><span class="sxs-lookup"><span data-stu-id="b62cb-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="b62cb-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>güncel</span><span class="sxs-lookup"><span data-stu-id="b62cb-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="b62cb-122"><xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> Yöntemini kullanarak dosyasından okuma yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b62cb-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="b62cb-123">*Fileprovidersample* örnek uygulaması, uygulamasında `Startup.ConfigureServices` [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama genelinde kullanılmak üzere bir dosya sağlayıcısının nasıl yapılandırılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="b62cb-124">Dosya sağlayıcısı uygulamaları</span><span class="sxs-lookup"><span data-stu-id="b62cb-124">File Provider implementations</span></span>

<span data-ttu-id="b62cb-125">Aşağıdaki tablo uygulamasının `IFileProvider`uygulamalarını listelemektedir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="b62cb-126">Uygulama</span><span class="sxs-lookup"><span data-stu-id="b62cb-126">Implementation</span></span> | <span data-ttu-id="b62cb-127">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b62cb-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="b62cb-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="b62cb-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="b62cb-129">Bir veya daha fazla sağlayıcıdan dosya ve dizinlere Birleşik erişim sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="b62cb-130">Bildirimli Estembeddedfileprovider</span><span class="sxs-lookup"><span data-stu-id="b62cb-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="b62cb-131">Derlemelerde yerleşik dosyalara erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="b62cb-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="b62cb-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="b62cb-133">Sistemin fiziksel dosyalarına erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="b62cb-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="b62cb-134">PhysicalFileProvider</span></span>

<span data-ttu-id="b62cb-135">Fiziksel <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> dosya sistemine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="b62cb-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="b62cb-136">`PhysicalFileProvider`, <xref:System.IO.File?displayProperty=fullName> türü (fiziksel sağlayıcı için) ve tüm yolları bir dizine ve alt öğelerine kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="b62cb-137">Bu kapsam, belirtilen dizin ve alt öğeleri dışındaki dosya sistemine erişimi engeller.</span><span class="sxs-lookup"><span data-stu-id="b62cb-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="b62cb-138">Oluşturma ve kullanma için en yaygın senaryo, `PhysicalFileProvider` [bağımlılık ekleme](xref:fundamentals/dependency-injection)aracılığıyla bir Oluşturucu `IFileProvider` içinde istekte bulunur.</span><span class="sxs-lookup"><span data-stu-id="b62cb-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="b62cb-139">Bu sağlayıcıyı doğrudan örnekledikten sonra, mutlak bir dizin yolu gereklidir ve sağlayıcı kullanılarak yapılan tüm isteklerin temel yolu olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="b62cb-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="b62cb-140">Glob desenleri dizin yolunda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b62cb-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="b62cb-141">Aşağıdaki kod, dizin içeriğini ve dosya `PhysicalFileProvider` bilgilerini elde etmek için nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="b62cb-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="b62cb-142">Önceki örnekteki türler:</span><span class="sxs-lookup"><span data-stu-id="b62cb-142">Types in the preceding example:</span></span>

* <span data-ttu-id="b62cb-143">`provider`bir `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="b62cb-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="b62cb-144">`contents`bir `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="b62cb-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="b62cb-145">`fileInfo`bir `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="b62cb-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="b62cb-146">Dosya sağlayıcısı, tarafından `applicationRoot` belirtilen dizin üzerinden yinelemek veya bir dosyanın bilgilerini almak için çağırmak `GetFileInfo` üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="b62cb-147">Glob desenleri `GetFileInfo` yönteme geçirilememelidir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="b62cb-148">Dosya sağlayıcısı, `applicationRoot` dizin dışında bir erişime sahip değil.</span><span class="sxs-lookup"><span data-stu-id="b62cb-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="b62cb-149">*Fileprovidersample* örnek uygulaması, şunu kullanarak `Startup.ConfigureServices` <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>sağlayıcıyı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b62cb-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="b62cb-150">Bildirimli Estembeddedfileprovider</span><span class="sxs-lookup"><span data-stu-id="b62cb-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="b62cb-151">, <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Derlemeler içine katıştırılmış dosyalara erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="b62cb-152">, `ManifestEmbeddedFileProvider` Gömülü dosyaların özgün yollarını yeniden oluşturmak için derlemeye derlenen bir bildirim kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="b62cb-153">Katıştırılmış dosyaların bir bildirimini oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="b62cb-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="b62cb-154">Projenize [Microsoft. Extensions. FileProviders. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b62cb-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="b62cb-155">`<GenerateEmbeddedFilesManifest>` Özelliğini olarak `true`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b62cb-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="b62cb-156">EmbeddedResource>eklenecek dosyaları belirtin: [ \< ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)</span><span class="sxs-lookup"><span data-stu-id="b62cb-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="b62cb-157">Derlemeye eklemek üzere bir veya daha fazla dosya belirtmek için [Glob desenlerini](#glob-patterns) kullanın.</span><span class="sxs-lookup"><span data-stu-id="b62cb-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="b62cb-158">*Fileprovidersample* örnek uygulaması bir `ManifestEmbeddedFileProvider` oluşturur ve şu anda yürütülmekte olan derlemeyi oluşturucusuna geçirir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="b62cb-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b62cb-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="b62cb-160">Ek aşırı yüklemeler şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="b62cb-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="b62cb-161">Göreli bir dosya yolu belirtin.</span><span class="sxs-lookup"><span data-stu-id="b62cb-161">Specify a relative file path.</span></span>
* <span data-ttu-id="b62cb-162">Dosya kapsamını son değiştirilme tarihine kadar.</span><span class="sxs-lookup"><span data-stu-id="b62cb-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="b62cb-163">Katıştırılmış dosya bildirimini içeren gömülü kaynağı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="b62cb-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="b62cb-164">Yüklemek</span><span class="sxs-lookup"><span data-stu-id="b62cb-164">Overload</span></span> | <span data-ttu-id="b62cb-165">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b62cb-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="b62cb-166">İsteğe bağlı `root` bir göreli yol parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b62cb-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="b62cb-167">`root` Belirtilen yol altında bu kaynaklara yönelik <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> olarak yapılan çağrıları belirtin.</span><span class="sxs-lookup"><span data-stu-id="b62cb-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="b62cb-168">İsteğe bağlı `root` göreli yol parametresini ve bir `lastModified` Date (<xref:System.DateTimeOffset>) parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b62cb-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="b62cb-169">`lastModified` Tarih kapsamları tarafından döndürülen <xref:Microsoft.Extensions.FileProviders.IFileInfo> örneklerin son değiştirilme tarihidir <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="b62cb-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="b62cb-170">İsteğe bağlı `root` bir göreli yolu, `lastModified` tarihi ve `manifestName` parametreleri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b62cb-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="b62cb-171">, `manifestName` Bildirimi içeren gömülü kaynağın adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="b62cb-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="b62cb-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="b62cb-172">CompositeFileProvider</span></span>

<span data-ttu-id="b62cb-173">, <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> Birden `IFileProvider` çok sağlayıcıdan dosyalarla çalışmak için tek bir arabirim ortaya çıkaran örnekleri birleştirir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="b62cb-174">Oluştururken `CompositeFileProvider`bir veya daha fazla `IFileProvider` örneği oluşturucuya geçirin.</span><span class="sxs-lookup"><span data-stu-id="b62cb-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="b62cb-175">*Fileprovidersample* örnek uygulamasında bir `PhysicalFileProvider` ve, uygulamanın hizmet kapsayıcısında `ManifestEmbeddedFileProvider` `CompositeFileProvider` kayıtlı bir dosya sağlayın.</span><span class="sxs-lookup"><span data-stu-id="b62cb-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="b62cb-176">Aşağıdaki kod proje `Startup.ConfigureServices` yönteminde bulunur:</span><span class="sxs-lookup"><span data-stu-id="b62cb-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="b62cb-177">Değişiklikleri izle</span><span class="sxs-lookup"><span data-stu-id="b62cb-177">Watch for changes</span></span>

<span data-ttu-id="b62cb-178">Yöntemi <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> , değişiklikler için bir veya daha fazla dosya veya dizini izlemek üzere bir senaryo sağlar.</span><span class="sxs-lookup"><span data-stu-id="b62cb-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="b62cb-179">`Watch` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b62cb-179">The `Watch` method:</span></span>

* <span data-ttu-id="b62cb-180">Birden çok dosya belirtmek için [Glob desenlerini](#glob-patterns) içerebilen bir dosya yolu dizesi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b62cb-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="b62cb-181">Döndürür <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="b62cb-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="b62cb-182">Ortaya çıkan değişiklik belirteci şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="b62cb-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="b62cb-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;Bir değişikliğin oluşup oluşmadığını tespit etmek için incelenebilir bir özellik.</span><span class="sxs-lookup"><span data-stu-id="b62cb-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="b62cb-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Belirtilen yol dizesinde değişiklikler algılandığında çağırılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="b62cb-185">Her değişiklik belirteci yalnızca, ilişkili geri çağırma işlemini tek bir değişikliğe yanıt olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="b62cb-186">Sabit izlemeyi etkinleştirmek için bir <xref:System.Threading.Tasks.TaskCompletionSource`1> (aşağıda gösterilmiştir) kullanın veya değişiklikleri yanıt `IChangeToken` olarak yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b62cb-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="b62cb-187">*WatchConsole* örnek uygulaması, *TextFiles* dizinindeki bir *. txt* dosyası her değiştirildiğinde bir ileti yazar:</span><span class="sxs-lookup"><span data-stu-id="b62cb-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="b62cb-188">Docker kapsayıcıları ve ağ paylaşımları gibi bazı dosya sistemleri, değişiklik bildirimlerini güvenilir bir şekilde gönderemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="b62cb-189">Ortam değişkenini, her dört `1` saniyede `true` bir değişiklikler için dosya sistemini yoklamak üzere veya olarak ayarlayın (yapılandırılamaz). `DOTNET_USE_POLLING_FILE_WATCHER`</span><span class="sxs-lookup"><span data-stu-id="b62cb-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="b62cb-190">Glob desenleri</span><span class="sxs-lookup"><span data-stu-id="b62cb-190">Glob patterns</span></span>

<span data-ttu-id="b62cb-191">Dosya sistemi yolları, *Glob (veya glob) desenleri*adlı joker karakter desenleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="b62cb-192">Bu desenlerle dosya gruplarını belirtin.</span><span class="sxs-lookup"><span data-stu-id="b62cb-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="b62cb-193">İki joker karakter şunlardır `*` `**`:</span><span class="sxs-lookup"><span data-stu-id="b62cb-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="b62cb-194">Geçerli klasör düzeyindeki her şeyi, dosya adını veya herhangi bir dosya uzantısını eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="b62cb-195">Eşleşmeler, dosya yolundaki `/` `.` karakterler ile sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="b62cb-196">Birden çok dizin düzeyindeki tüm öğeleri eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="b62cb-197">, Bir Dizin hiyerarşisinde birçok dosya yinelemeli olarak eşleşmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="b62cb-198">Aşağıdaki tabloda, glob desenlerinin yaygın örnekleri verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="b62cb-199">Desen</span><span class="sxs-lookup"><span data-stu-id="b62cb-199">Pattern</span></span>  |<span data-ttu-id="b62cb-200">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b62cb-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="b62cb-201">Belirli bir dizindeki belirli bir dosyayla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="b62cb-202">Belirli bir dizinde *. txt* uzantılı tüm dosyaları eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="b62cb-203">Dizinler içindeki tüm *appSettings. JSON* dosyalarıyla *Dizin* klasörünün altında tam olarak bir düzey eşleşir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="b62cb-204">*. Txt* uzantılı tüm dosyaları, *Dizin* klasörünün altında herhangi bir yerde buldu.</span><span class="sxs-lookup"><span data-stu-id="b62cb-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b62cb-205">ASP.NET Core dosya sistemi erişimini dosya sağlayıcılarının kullanımı üzerinden soyutlar.</span><span class="sxs-lookup"><span data-stu-id="b62cb-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="b62cb-206">Dosya sağlayıcıları ASP.NET Core Framework boyunca kullanılır:</span><span class="sxs-lookup"><span data-stu-id="b62cb-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="b62cb-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın [içerik kökünü](xref:fundamentals/index#content-root) ve [Web kökünü](xref:fundamentals/index#web-root) türler olarak `IFileProvider` gösterir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="b62cb-208">[Statik dosya ara yazılımı](xref:fundamentals/static-files) , statik dosyaları bulmak Için dosya sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="b62cb-209">[Razor](xref:mvc/views/razor) , sayfa ve görünümleri bulmak Için dosya sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="b62cb-210">.NET Core araçları, hangi dosyaların yayımlanacak olduğunu belirlemek için dosya sağlayıcılarını ve glob düzenlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="b62cb-211">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b62cb-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="b62cb-212">Dosya sağlayıcısı arabirimleri</span><span class="sxs-lookup"><span data-stu-id="b62cb-212">File Provider interfaces</span></span>

<span data-ttu-id="b62cb-213">Birincil arabirim <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="b62cb-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="b62cb-214">`IFileProvider`şunları yapmak için yöntemler sunar:</span><span class="sxs-lookup"><span data-stu-id="b62cb-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="b62cb-215">Dosya bilgilerini edinin (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="b62cb-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="b62cb-216">Dizin bilgilerini (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>) alın.</span><span class="sxs-lookup"><span data-stu-id="b62cb-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="b62cb-217">Değişiklik bildirimlerini ayarlayın (bir <xref:Microsoft.Extensions.Primitives.IChangeToken>kullanarak).</span><span class="sxs-lookup"><span data-stu-id="b62cb-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="b62cb-218">`IFileInfo`dosyalarla çalışma için yöntemler ve özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="b62cb-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="b62cb-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(bayt cinsinden)</span><span class="sxs-lookup"><span data-stu-id="b62cb-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="b62cb-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>güncel</span><span class="sxs-lookup"><span data-stu-id="b62cb-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="b62cb-221">[Ifileınfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) yöntemini kullanarak dosyadan okuma yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b62cb-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="b62cb-222">Örnek uygulama, uygulamasında `Startup.ConfigureServices` [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama genelinde kullanılmak üzere bir dosya sağlayıcısının nasıl yapılandırılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="b62cb-223">Dosya sağlayıcısı uygulamaları</span><span class="sxs-lookup"><span data-stu-id="b62cb-223">File Provider implementations</span></span>

<span data-ttu-id="b62cb-224">Üç uygulaması `IFileProvider` mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="b62cb-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="b62cb-225">Uygulama</span><span class="sxs-lookup"><span data-stu-id="b62cb-225">Implementation</span></span> | <span data-ttu-id="b62cb-226">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b62cb-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="b62cb-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="b62cb-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="b62cb-228">Fiziksel sağlayıcı, sistemin fiziksel dosyalarına erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="b62cb-229">Bildirimli Estembeddedfileprovider</span><span class="sxs-lookup"><span data-stu-id="b62cb-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="b62cb-230">Bildirimde yerleşik olarak bulunan dosyalara erişmek için bildirim eklenmiş sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="b62cb-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="b62cb-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="b62cb-232">Bileşik sağlayıcı, bir veya daha fazla sağlayıcıdan gelen dosyalara ve dizinlere Birleşik erişim sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="b62cb-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="b62cb-233">PhysicalFileProvider</span></span>

<span data-ttu-id="b62cb-234">Fiziksel <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> dosya sistemine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="b62cb-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="b62cb-235">`PhysicalFileProvider`, <xref:System.IO.File?displayProperty=fullName> türü (fiziksel sağlayıcı için) ve tüm yolları bir dizine ve alt öğelerine kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="b62cb-236">Bu kapsam, belirtilen dizin ve alt öğeleri dışındaki dosya sistemine erişimi engeller.</span><span class="sxs-lookup"><span data-stu-id="b62cb-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="b62cb-237">Oluşturma ve kullanma için en yaygın senaryo, `PhysicalFileProvider` [bağımlılık ekleme](xref:fundamentals/dependency-injection)aracılığıyla bir Oluşturucu `IFileProvider` içinde istekte bulunur.</span><span class="sxs-lookup"><span data-stu-id="b62cb-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="b62cb-238">Bu sağlayıcıyı doğrudan örnekleyen bir dizin yolu gereklidir ve sağlayıcı kullanılarak yapılan tüm isteklerin temel yolu olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="b62cb-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="b62cb-239">Aşağıdaki kod, nasıl oluşturulacağını `PhysicalFileProvider` ve dizin içeriğini ve dosya bilgilerini almak için nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="b62cb-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="b62cb-240">Önceki örnekteki türler:</span><span class="sxs-lookup"><span data-stu-id="b62cb-240">Types in the preceding example:</span></span>

* <span data-ttu-id="b62cb-241">`provider`bir `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="b62cb-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="b62cb-242">`contents`bir `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="b62cb-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="b62cb-243">`fileInfo`bir `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="b62cb-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="b62cb-244">Dosya sağlayıcısı, tarafından `applicationRoot` belirtilen dizin üzerinden yinelemek veya bir dosyanın bilgilerini almak için çağırmak `GetFileInfo` üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="b62cb-245">Dosya sağlayıcısı, `applicationRoot` dizin dışında bir erişime sahip değil.</span><span class="sxs-lookup"><span data-stu-id="b62cb-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="b62cb-246">Örnek uygulama, sağlayıcıyı `Startup.ConfigureServices` [ıhostingenvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)kullanarak uygulamanın sınıfında oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b62cb-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="b62cb-247">Bildirimli Estembeddedfileprovider</span><span class="sxs-lookup"><span data-stu-id="b62cb-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="b62cb-248">, <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Derlemeler içine katıştırılmış dosyalara erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="b62cb-249">, `ManifestEmbeddedFileProvider` Gömülü dosyaların özgün yollarını yeniden oluşturmak için derlemeye derlenen bir bildirim kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="b62cb-250">Gömülü dosyaların bir bildirimini oluşturmak için `<GenerateEmbeddedFilesManifest>` özelliğini olarak `true`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b62cb-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="b62cb-251">[ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)ile eklenecek dosyaları belirtin:</span><span class="sxs-lookup"><span data-stu-id="b62cb-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="b62cb-252">Derlemeye eklemek üzere bir veya daha fazla dosya belirtmek için [Glob desenlerini](#glob-patterns) kullanın.</span><span class="sxs-lookup"><span data-stu-id="b62cb-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="b62cb-253">Örnek uygulama bir `ManifestEmbeddedFileProvider` oluşturur ve şu anda yürütülmekte olan derlemeyi oluşturucusuna geçirir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="b62cb-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b62cb-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="b62cb-255">Ek aşırı yüklemeler şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="b62cb-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="b62cb-256">Göreli bir dosya yolu belirtin.</span><span class="sxs-lookup"><span data-stu-id="b62cb-256">Specify a relative file path.</span></span>
* <span data-ttu-id="b62cb-257">Dosya kapsamını son değiştirilme tarihine kadar.</span><span class="sxs-lookup"><span data-stu-id="b62cb-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="b62cb-258">Katıştırılmış dosya bildirimini içeren gömülü kaynağı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="b62cb-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="b62cb-259">Yüklemek</span><span class="sxs-lookup"><span data-stu-id="b62cb-259">Overload</span></span> | <span data-ttu-id="b62cb-260">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b62cb-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="b62cb-261">İsteğe bağlı `root` bir göreli yol parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b62cb-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="b62cb-262">`root` Belirtilen yol altında bu kaynaklara yönelik <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> olarak yapılan çağrıları belirtin.</span><span class="sxs-lookup"><span data-stu-id="b62cb-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="b62cb-263">İsteğe bağlı `root` göreli yol parametresini ve bir `lastModified` Date (<xref:System.DateTimeOffset>) parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b62cb-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="b62cb-264">`lastModified` Tarih kapsamları tarafından döndürülen <xref:Microsoft.Extensions.FileProviders.IFileInfo> örneklerin son değiştirilme tarihidir <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="b62cb-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="b62cb-265">İsteğe bağlı `root` bir göreli yolu, `lastModified` tarihi ve `manifestName` parametreleri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b62cb-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="b62cb-266">, `manifestName` Bildirimi içeren gömülü kaynağın adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="b62cb-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="b62cb-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="b62cb-267">CompositeFileProvider</span></span>

<span data-ttu-id="b62cb-268">, <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> Birden `IFileProvider` çok sağlayıcıdan dosyalarla çalışmak için tek bir arabirim ortaya çıkaran örnekleri birleştirir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="b62cb-269">Oluştururken `CompositeFileProvider`bir veya daha fazla `IFileProvider` örneği oluşturucuya geçirin.</span><span class="sxs-lookup"><span data-stu-id="b62cb-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="b62cb-270">Örnek uygulamada, `PhysicalFileProvider` ve, uygulamanın hizmet kapsayıcısında `ManifestEmbeddedFileProvider` `CompositeFileProvider` kayıtlı bir dosya sağlar:</span><span class="sxs-lookup"><span data-stu-id="b62cb-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="b62cb-271">Değişiklikleri izle</span><span class="sxs-lookup"><span data-stu-id="b62cb-271">Watch for changes</span></span>

<span data-ttu-id="b62cb-272">[IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) yöntemi, değişiklikler için bir veya daha fazla dosya ya da dizin izlemek üzere bir senaryo sağlar.</span><span class="sxs-lookup"><span data-stu-id="b62cb-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="b62cb-273">`Watch`birden çok dosya belirtmek için [Glob desenlerini](#glob-patterns) içerebilen bir yol dizesi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b62cb-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="b62cb-274">`Watch`döndürür <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="b62cb-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="b62cb-275">Değişiklik belirteci şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="b62cb-275">The change token exposes:</span></span>

* <span data-ttu-id="b62cb-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Bir değişikliğin oluşup oluşmadığını tespit etmek için incelenebilir bir özellik.</span><span class="sxs-lookup"><span data-stu-id="b62cb-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="b62cb-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Belirtilen yol dizesinde değişiklikler algılandığında çağırılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="b62cb-278">Her değişiklik belirteci yalnızca, ilişkili geri çağırma işlemini tek bir değişikliğe yanıt olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="b62cb-279">Sabit izlemeyi etkinleştirmek için bir <xref:System.Threading.Tasks.TaskCompletionSource`1> (aşağıda gösterilmiştir) kullanın veya değişiklikleri yanıt `IChangeToken` olarak yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b62cb-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="b62cb-280">Örnek uygulamada, *WatchConsole* konsol uygulaması bir metin dosyası her değiştirildiğinde bir ileti görüntüleyecek şekilde yapılandırılmıştır:</span><span class="sxs-lookup"><span data-stu-id="b62cb-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="b62cb-281">Docker kapsayıcıları ve ağ paylaşımları gibi bazı dosya sistemleri, değişiklik bildirimlerini güvenilir bir şekilde gönderemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="b62cb-282">Ortam değişkenini, her dört `1` saniyede `true` bir değişiklikler için dosya sistemini yoklamak üzere veya olarak ayarlayın (yapılandırılamaz). `DOTNET_USE_POLLING_FILE_WATCHER`</span><span class="sxs-lookup"><span data-stu-id="b62cb-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="b62cb-283">Glob desenleri</span><span class="sxs-lookup"><span data-stu-id="b62cb-283">Glob patterns</span></span>

<span data-ttu-id="b62cb-284">Dosya sistemi yolları, *Glob (veya glob) desenleri*adlı joker karakter desenleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="b62cb-285">Bu desenlerle dosya gruplarını belirtin.</span><span class="sxs-lookup"><span data-stu-id="b62cb-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="b62cb-286">İki joker karakter şunlardır `*` `**`:</span><span class="sxs-lookup"><span data-stu-id="b62cb-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="b62cb-287">Geçerli klasör düzeyindeki her şeyi, dosya adını veya herhangi bir dosya uzantısını eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="b62cb-288">Eşleşmeler, dosya yolundaki `/` `.` karakterler ile sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="b62cb-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="b62cb-289">Birden çok dizin düzeyindeki tüm öğeleri eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="b62cb-290">, Bir Dizin hiyerarşisinde birçok dosya yinelemeli olarak eşleşmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="b62cb-291">**Glob deseninin örnekleri**</span><span class="sxs-lookup"><span data-stu-id="b62cb-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="b62cb-292">Belirli bir dizindeki belirli bir dosyayla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="b62cb-293">Belirli bir dizinde *. txt* uzantılı tüm dosyaları eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="b62cb-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="b62cb-294">Dizinteki `appsettings.json` tüm dosyaları, *Dizin* klasörünün altında tam olarak bir düzey eşler.</span><span class="sxs-lookup"><span data-stu-id="b62cb-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="b62cb-295">*. Txt* uzantılı tüm dosyaları, *Dizin* klasörünün altında herhangi bir yerde buldu.</span><span class="sxs-lookup"><span data-stu-id="b62cb-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
