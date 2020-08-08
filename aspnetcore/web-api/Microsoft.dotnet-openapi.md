---
title: Openapı kullanarak ASP.NET Core uygulamaları geliştirme
author: ryanbrandenburg
description: Openapı dosyalarına başvurular eklemek için ' Microsoft. DotNet-openapı ' aracının nasıl kullanılacağını gösterir.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 6a9b80e868a54bd76503a6421c34ae159421699b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022244"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="54af8-103">Openapı araçlarını kullanarak ASP.NET Core uygulamaları geliştirme</span><span class="sxs-lookup"><span data-stu-id="54af8-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="54af8-104">İle Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="54af8-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="54af8-105">[Microsoft. DotNet-openapı](https://www.nuget.org/packages/Microsoft.dotnet-openapi) , bir proje Içindeki [openapı](https://github.com/OAI/OpenAPI-Specification) başvurularını yönetmek için [.NET Core küresel bir araçtır](/dotnet/core/tools/global-tools) .</span><span class="sxs-lookup"><span data-stu-id="54af8-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="54af8-106">Yükleme</span><span class="sxs-lookup"><span data-stu-id="54af8-106">Installation</span></span>

<span data-ttu-id="54af8-107">Yüklemek için `Microsoft.dotnet-openapi` Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="54af8-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="54af8-108">Ekle</span><span class="sxs-lookup"><span data-stu-id="54af8-108">Add</span></span>

<span data-ttu-id="54af8-109">Bu sayfadaki komutlardan herhangi birini kullanarak bir Openapı başvurusu eklemek `<OpenApiReference />` , *. csproj* dosyasına aşağıdakine benzer bir öğe ekler:</span><span class="sxs-lookup"><span data-stu-id="54af8-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="54af8-110">Uygulamanın oluşturulan istemci kodunu çağırması için önceki başvuru gereklidir.</span><span class="sxs-lookup"><span data-stu-id="54af8-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="54af8-111">Dosya Ekle</span><span class="sxs-lookup"><span data-stu-id="54af8-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="54af8-112">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="54af8-112">Options</span></span>

| <span data-ttu-id="54af8-113">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="54af8-113">Short option</span></span>| <span data-ttu-id="54af8-114">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="54af8-114">Long option</span></span>| <span data-ttu-id="54af8-115">Açıklama</span><span class="sxs-lookup"><span data-stu-id="54af8-115">Description</span></span> | <span data-ttu-id="54af8-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="54af8-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="54af8-117">-p</span><span class="sxs-lookup"><span data-stu-id="54af8-117">-p</span></span>|<span data-ttu-id="54af8-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="54af8-118">--updateProject</span></span> | <span data-ttu-id="54af8-119">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="54af8-119">The project to operate on.</span></span> |<span data-ttu-id="54af8-120">DotNet openapı dosya ekleme *--updateproject .\ref.exe* .\OpenAPI.js</span><span class="sxs-lookup"><span data-stu-id="54af8-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="54af8-121">-c</span><span class="sxs-lookup"><span data-stu-id="54af8-121">-c</span></span>|<span data-ttu-id="54af8-122">--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="54af8-122">--code-generator</span></span>| <span data-ttu-id="54af8-123">Başvuruya uygulanacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="54af8-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="54af8-124">Seçenekler `NSwagCSharp` ve ' dir `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="54af8-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="54af8-125">Belirtilmemişse, `--code-generator` araçları varsayılan olarak ayarlanır `NSwagCSharp` .</span><span class="sxs-lookup"><span data-stu-id="54af8-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="54af8-126">DotNet openapı dosya ekleme .\OpenApi.js--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="54af8-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="54af8-127">-h</span><span class="sxs-lookup"><span data-stu-id="54af8-127">-h</span></span>|<span data-ttu-id="54af8-128">--yardım</span><span class="sxs-lookup"><span data-stu-id="54af8-128">--help</span></span>|<span data-ttu-id="54af8-129">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="54af8-129">Show help information</span></span>|<span data-ttu-id="54af8-130">DotNet openapı dosya Ekle--yardım</span><span class="sxs-lookup"><span data-stu-id="54af8-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="54af8-131">Arguments</span><span class="sxs-lookup"><span data-stu-id="54af8-131">Arguments</span></span>

|  <span data-ttu-id="54af8-132">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="54af8-132">Argument</span></span>  | <span data-ttu-id="54af8-133">Açıklama</span><span class="sxs-lookup"><span data-stu-id="54af8-133">Description</span></span> | <span data-ttu-id="54af8-134">Örnek</span><span class="sxs-lookup"><span data-stu-id="54af8-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="54af8-135">Kaynak dosya</span><span class="sxs-lookup"><span data-stu-id="54af8-135">source-file</span></span> | <span data-ttu-id="54af8-136">Başvuru oluşturulacak kaynak.</span><span class="sxs-lookup"><span data-stu-id="54af8-136">The source to create a reference from.</span></span> <span data-ttu-id="54af8-137">Bir Openapı dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="54af8-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="54af8-138">DotNet openapı dosya ekleme *.\OpenAPI.js*</span><span class="sxs-lookup"><span data-stu-id="54af8-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="54af8-139">URL Ekle</span><span class="sxs-lookup"><span data-stu-id="54af8-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="54af8-140">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="54af8-140">Options</span></span>

| <span data-ttu-id="54af8-141">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="54af8-141">Short option</span></span>| <span data-ttu-id="54af8-142">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="54af8-142">Long option</span></span>| <span data-ttu-id="54af8-143">Açıklama</span><span class="sxs-lookup"><span data-stu-id="54af8-143">Description</span></span> | <span data-ttu-id="54af8-144">Örnek</span><span class="sxs-lookup"><span data-stu-id="54af8-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="54af8-145">-p</span><span class="sxs-lookup"><span data-stu-id="54af8-145">-p</span></span>|<span data-ttu-id="54af8-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="54af8-146">--updateProject</span></span> | <span data-ttu-id="54af8-147">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="54af8-147">The project to operate on.</span></span> |<span data-ttu-id="54af8-148">DotNet openapı Add URL *--updateproject .\Ref.exe*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="54af8-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="54af8-149">-o</span><span class="sxs-lookup"><span data-stu-id="54af8-149">-o</span></span>|<span data-ttu-id="54af8-150">--Çıkış-dosya</span><span class="sxs-lookup"><span data-stu-id="54af8-150">--output-file</span></span> | <span data-ttu-id="54af8-151">Openapı dosyasının yerel kopyasının yerleştirileceği yer.</span><span class="sxs-lookup"><span data-stu-id="54af8-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="54af8-152">DotNet openapı Add URL `https://contoso.com/openapi.json` *--Çıkış-dosya myclient.js*</span><span class="sxs-lookup"><span data-stu-id="54af8-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="54af8-153">-c</span><span class="sxs-lookup"><span data-stu-id="54af8-153">-c</span></span>|<span data-ttu-id="54af8-154">--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="54af8-154">--code-generator</span></span>| <span data-ttu-id="54af8-155">Başvuruya uygulanacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="54af8-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="54af8-156">Seçenekler `NSwagCSharp` ve ' dir `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="54af8-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="54af8-157">DotNet openapı dosya ekleme .\OpenApi.js--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="54af8-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="54af8-158">-h</span><span class="sxs-lookup"><span data-stu-id="54af8-158">-h</span></span>|<span data-ttu-id="54af8-159">--yardım</span><span class="sxs-lookup"><span data-stu-id="54af8-159">--help</span></span>|<span data-ttu-id="54af8-160">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="54af8-160">Show help information</span></span>|<span data-ttu-id="54af8-161">DotNet openapı URL ekleme--Yardım</span><span class="sxs-lookup"><span data-stu-id="54af8-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="54af8-162">Arguments</span><span class="sxs-lookup"><span data-stu-id="54af8-162">Arguments</span></span>

|  <span data-ttu-id="54af8-163">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="54af8-163">Argument</span></span>  | <span data-ttu-id="54af8-164">Açıklama</span><span class="sxs-lookup"><span data-stu-id="54af8-164">Description</span></span> | <span data-ttu-id="54af8-165">Örnek</span><span class="sxs-lookup"><span data-stu-id="54af8-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="54af8-166">Kaynak-URL</span><span class="sxs-lookup"><span data-stu-id="54af8-166">source-URL</span></span> | <span data-ttu-id="54af8-167">Başvuru oluşturulacak kaynak.</span><span class="sxs-lookup"><span data-stu-id="54af8-167">The source to create a reference from.</span></span> <span data-ttu-id="54af8-168">Bir URL olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="54af8-168">Must be a URL.</span></span> |<span data-ttu-id="54af8-169">DotNet openapı URL 'si Ekle`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="54af8-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="54af8-170">Kaldır</span><span class="sxs-lookup"><span data-stu-id="54af8-170">Remove</span></span>

<span data-ttu-id="54af8-171">Verilen dosya adıyla eşleşen Openapı başvurusunu *. csproj* dosyasından kaldırır.</span><span class="sxs-lookup"><span data-stu-id="54af8-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="54af8-172">Openapı başvurusu kaldırıldığında, istemciler oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="54af8-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="54af8-173">Local *. JSON* ve *. YAML* dosyaları silinir.</span><span class="sxs-lookup"><span data-stu-id="54af8-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="54af8-174">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="54af8-174">Options</span></span>

| <span data-ttu-id="54af8-175">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="54af8-175">Short option</span></span>| <span data-ttu-id="54af8-176">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="54af8-176">Long option</span></span>| <span data-ttu-id="54af8-177">Açıklama</span><span class="sxs-lookup"><span data-stu-id="54af8-177">Description</span></span>| <span data-ttu-id="54af8-178">Örnek</span><span class="sxs-lookup"><span data-stu-id="54af8-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="54af8-179">-p</span><span class="sxs-lookup"><span data-stu-id="54af8-179">-p</span></span>|<span data-ttu-id="54af8-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="54af8-180">--updateProject</span></span> | <span data-ttu-id="54af8-181">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="54af8-181">The project to operate on.</span></span> |<span data-ttu-id="54af8-182">DotNet openapı Remove *--updateproject .\ref.exe* on .\OpenAPI.js</span><span class="sxs-lookup"><span data-stu-id="54af8-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="54af8-183">-h</span><span class="sxs-lookup"><span data-stu-id="54af8-183">-h</span></span>|<span data-ttu-id="54af8-184">--yardım</span><span class="sxs-lookup"><span data-stu-id="54af8-184">--help</span></span>|<span data-ttu-id="54af8-185">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="54af8-185">Show help information</span></span>|<span data-ttu-id="54af8-186">DotNet openapı Remove--Help</span><span class="sxs-lookup"><span data-stu-id="54af8-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="54af8-187">Arguments</span><span class="sxs-lookup"><span data-stu-id="54af8-187">Arguments</span></span>

|  <span data-ttu-id="54af8-188">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="54af8-188">Argument</span></span>  | <span data-ttu-id="54af8-189">Açıklama</span><span class="sxs-lookup"><span data-stu-id="54af8-189">Description</span></span>| <span data-ttu-id="54af8-190">Örnek</span><span class="sxs-lookup"><span data-stu-id="54af8-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="54af8-191">Kaynak dosya</span><span class="sxs-lookup"><span data-stu-id="54af8-191">source-file</span></span> | <span data-ttu-id="54af8-192">Başvurunun kaldırılacağı kaynak.</span><span class="sxs-lookup"><span data-stu-id="54af8-192">The source to remove the reference to.</span></span> |<span data-ttu-id="54af8-193">DotNet openapı kaldırma *.\OpenAPI.js*</span><span class="sxs-lookup"><span data-stu-id="54af8-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="54af8-194">Yenile</span><span class="sxs-lookup"><span data-stu-id="54af8-194">Refresh</span></span>

<span data-ttu-id="54af8-195">İndirme URL 'sindeki en son içerik kullanılarak indirilen bir dosyanın yerel sürümünü yeniler.</span><span class="sxs-lookup"><span data-stu-id="54af8-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="54af8-196">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="54af8-196">Options</span></span>

| <span data-ttu-id="54af8-197">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="54af8-197">Short option</span></span>| <span data-ttu-id="54af8-198">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="54af8-198">Long option</span></span>| <span data-ttu-id="54af8-199">Açıklama</span><span class="sxs-lookup"><span data-stu-id="54af8-199">Description</span></span> | <span data-ttu-id="54af8-200">Örnek</span><span class="sxs-lookup"><span data-stu-id="54af8-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="54af8-201">-p</span><span class="sxs-lookup"><span data-stu-id="54af8-201">-p</span></span>|<span data-ttu-id="54af8-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="54af8-202">--updateProject</span></span> | <span data-ttu-id="54af8-203">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="54af8-203">The project to operate on.</span></span> | <span data-ttu-id="54af8-204">DotNet openapı yenileme *--updateproject .\ref.exe*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="54af8-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="54af8-205">-h</span><span class="sxs-lookup"><span data-stu-id="54af8-205">-h</span></span>|<span data-ttu-id="54af8-206">--yardım</span><span class="sxs-lookup"><span data-stu-id="54af8-206">--help</span></span>|<span data-ttu-id="54af8-207">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="54af8-207">Show help information</span></span>|<span data-ttu-id="54af8-208">DotNet openapı yenilemesi--yardım</span><span class="sxs-lookup"><span data-stu-id="54af8-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="54af8-209">Arguments</span><span class="sxs-lookup"><span data-stu-id="54af8-209">Arguments</span></span>

|  <span data-ttu-id="54af8-210">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="54af8-210">Argument</span></span>  | <span data-ttu-id="54af8-211">Açıklama</span><span class="sxs-lookup"><span data-stu-id="54af8-211">Description</span></span> | <span data-ttu-id="54af8-212">Örnek</span><span class="sxs-lookup"><span data-stu-id="54af8-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="54af8-213">Kaynak-URL</span><span class="sxs-lookup"><span data-stu-id="54af8-213">source-URL</span></span> | <span data-ttu-id="54af8-214">Başvurunun yenileneceği URL.</span><span class="sxs-lookup"><span data-stu-id="54af8-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="54af8-215">DotNet openapı yenilemesi`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="54af8-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
