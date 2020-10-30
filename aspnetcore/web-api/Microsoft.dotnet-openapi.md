---
title: Openapı kullanarak ASP.NET Core uygulamaları geliştirme
author: ryanbrandenburg
description: Openapı dosyalarına başvurular eklemek için ' Microsoft. DotNet-openapı ' aracının nasıl kullanılacağını gösterir.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 28a71c7040667c7544cc17c1184c09b5b39959b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052558"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="2d1eb-103">Openapı araçlarını kullanarak ASP.NET Core uygulamaları geliştirme</span><span class="sxs-lookup"><span data-stu-id="2d1eb-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="2d1eb-104">İle Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="2d1eb-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="2d1eb-105">[Microsoft. DotNet-openapı](https://www.nuget.org/packages/Microsoft.dotnet-openapi) , bir proje Içindeki [openapı](https://github.com/OAI/OpenAPI-Specification) başvurularını yönetmek için [.NET Core küresel bir araçtır](/dotnet/core/tools/global-tools) .</span><span class="sxs-lookup"><span data-stu-id="2d1eb-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="2d1eb-106">Yükleme</span><span class="sxs-lookup"><span data-stu-id="2d1eb-106">Installation</span></span>

<span data-ttu-id="2d1eb-107">Yüklemek için `Microsoft.dotnet-openapi` Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2d1eb-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="2d1eb-108">Ekle</span><span class="sxs-lookup"><span data-stu-id="2d1eb-108">Add</span></span>

<span data-ttu-id="2d1eb-109">Bu sayfadaki komutlardan herhangi birini kullanarak bir Openapı başvurusu eklemek `<OpenApiReference />` , *. csproj* dosyasına aşağıdakine benzer bir öğe ekler:</span><span class="sxs-lookup"><span data-stu-id="2d1eb-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="2d1eb-110">Uygulamanın oluşturulan istemci kodunu çağırması için önceki başvuru gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="2d1eb-111">Dosya Ekle</span><span class="sxs-lookup"><span data-stu-id="2d1eb-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="2d1eb-112">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="2d1eb-112">Options</span></span>

| <span data-ttu-id="2d1eb-113">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="2d1eb-113">Short option</span></span>| <span data-ttu-id="2d1eb-114">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="2d1eb-114">Long option</span></span>| <span data-ttu-id="2d1eb-115">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2d1eb-115">Description</span></span> | <span data-ttu-id="2d1eb-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="2d1eb-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="2d1eb-117">-p</span><span class="sxs-lookup"><span data-stu-id="2d1eb-117">-p</span></span>|<span data-ttu-id="2d1eb-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="2d1eb-118">--updateProject</span></span> | <span data-ttu-id="2d1eb-119">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-119">The project to operate on.</span></span> |<span data-ttu-id="2d1eb-120">DotNet openapı dosya ekleme *--updateproject .\ref.exe* .\OpenAPI.js</span><span class="sxs-lookup"><span data-stu-id="2d1eb-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="2d1eb-121">-c</span><span class="sxs-lookup"><span data-stu-id="2d1eb-121">-c</span></span>|<span data-ttu-id="2d1eb-122">--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="2d1eb-122">--code-generator</span></span>| <span data-ttu-id="2d1eb-123">Başvuruya uygulanacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="2d1eb-124">Seçenekler `NSwagCSharp` ve ' dir `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="2d1eb-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="2d1eb-125">Belirtilmemişse, `--code-generator` araçları varsayılan olarak ayarlanır `NSwagCSharp` .</span><span class="sxs-lookup"><span data-stu-id="2d1eb-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="2d1eb-126">DotNet openapı dosya ekleme .\OpenApi.js--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="2d1eb-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="2d1eb-127">-h</span><span class="sxs-lookup"><span data-stu-id="2d1eb-127">-h</span></span>|<span data-ttu-id="2d1eb-128">--yardım</span><span class="sxs-lookup"><span data-stu-id="2d1eb-128">--help</span></span>|<span data-ttu-id="2d1eb-129">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="2d1eb-129">Show help information</span></span>|<span data-ttu-id="2d1eb-130">DotNet openapı dosya Ekle--yardım</span><span class="sxs-lookup"><span data-stu-id="2d1eb-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="2d1eb-131">Arguments</span><span class="sxs-lookup"><span data-stu-id="2d1eb-131">Arguments</span></span>

|  <span data-ttu-id="2d1eb-132">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="2d1eb-132">Argument</span></span>  | <span data-ttu-id="2d1eb-133">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2d1eb-133">Description</span></span> | <span data-ttu-id="2d1eb-134">Örnek</span><span class="sxs-lookup"><span data-stu-id="2d1eb-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="2d1eb-135">Kaynak dosya</span><span class="sxs-lookup"><span data-stu-id="2d1eb-135">source-file</span></span> | <span data-ttu-id="2d1eb-136">Başvuru oluşturulacak kaynak.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-136">The source to create a reference from.</span></span> <span data-ttu-id="2d1eb-137">Bir Openapı dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="2d1eb-138">DotNet openapı dosya ekleme *.\OpenAPI.js*</span><span class="sxs-lookup"><span data-stu-id="2d1eb-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="2d1eb-139">URL Ekle</span><span class="sxs-lookup"><span data-stu-id="2d1eb-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="2d1eb-140">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="2d1eb-140">Options</span></span>

| <span data-ttu-id="2d1eb-141">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="2d1eb-141">Short option</span></span>| <span data-ttu-id="2d1eb-142">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="2d1eb-142">Long option</span></span>| <span data-ttu-id="2d1eb-143">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2d1eb-143">Description</span></span> | <span data-ttu-id="2d1eb-144">Örnek</span><span class="sxs-lookup"><span data-stu-id="2d1eb-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="2d1eb-145">-p</span><span class="sxs-lookup"><span data-stu-id="2d1eb-145">-p</span></span>|<span data-ttu-id="2d1eb-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="2d1eb-146">--updateProject</span></span> | <span data-ttu-id="2d1eb-147">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-147">The project to operate on.</span></span> |<span data-ttu-id="2d1eb-148">DotNet openapı Add URL *--updateproject .\Ref.exe*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="2d1eb-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="2d1eb-149">-o</span><span class="sxs-lookup"><span data-stu-id="2d1eb-149">-o</span></span>|<span data-ttu-id="2d1eb-150">--Çıkış-dosya</span><span class="sxs-lookup"><span data-stu-id="2d1eb-150">--output-file</span></span> | <span data-ttu-id="2d1eb-151">Openapı dosyasının yerel kopyasının yerleştirileceği yer.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="2d1eb-152">DotNet openapı Add URL `https://contoso.com/openapi.json` *--Çıkış-dosya myclient.js*</span><span class="sxs-lookup"><span data-stu-id="2d1eb-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="2d1eb-153">-c</span><span class="sxs-lookup"><span data-stu-id="2d1eb-153">-c</span></span>|<span data-ttu-id="2d1eb-154">--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="2d1eb-154">--code-generator</span></span>| <span data-ttu-id="2d1eb-155">Başvuruya uygulanacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="2d1eb-156">Seçenekler `NSwagCSharp` ve ' dir `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="2d1eb-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="2d1eb-157">DotNet openapı dosya ekleme .\OpenApi.js--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="2d1eb-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="2d1eb-158">-h</span><span class="sxs-lookup"><span data-stu-id="2d1eb-158">-h</span></span>|<span data-ttu-id="2d1eb-159">--yardım</span><span class="sxs-lookup"><span data-stu-id="2d1eb-159">--help</span></span>|<span data-ttu-id="2d1eb-160">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="2d1eb-160">Show help information</span></span>|<span data-ttu-id="2d1eb-161">DotNet openapı URL ekleme--Yardım</span><span class="sxs-lookup"><span data-stu-id="2d1eb-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="2d1eb-162">Arguments</span><span class="sxs-lookup"><span data-stu-id="2d1eb-162">Arguments</span></span>

|  <span data-ttu-id="2d1eb-163">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="2d1eb-163">Argument</span></span>  | <span data-ttu-id="2d1eb-164">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2d1eb-164">Description</span></span> | <span data-ttu-id="2d1eb-165">Örnek</span><span class="sxs-lookup"><span data-stu-id="2d1eb-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="2d1eb-166">Kaynak-URL</span><span class="sxs-lookup"><span data-stu-id="2d1eb-166">source-URL</span></span> | <span data-ttu-id="2d1eb-167">Başvuru oluşturulacak kaynak.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-167">The source to create a reference from.</span></span> <span data-ttu-id="2d1eb-168">Bir URL olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-168">Must be a URL.</span></span> |<span data-ttu-id="2d1eb-169">DotNet openapı URL 'si Ekle `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="2d1eb-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="2d1eb-170">Kaldır</span><span class="sxs-lookup"><span data-stu-id="2d1eb-170">Remove</span></span>

<span data-ttu-id="2d1eb-171">Verilen dosya adıyla eşleşen Openapı başvurusunu *. csproj* dosyasından kaldırır.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="2d1eb-172">Openapı başvurusu kaldırıldığında, istemciler oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="2d1eb-173">Local *. JSON* ve *. YAML* dosyaları silinir.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="2d1eb-174">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="2d1eb-174">Options</span></span>

| <span data-ttu-id="2d1eb-175">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="2d1eb-175">Short option</span></span>| <span data-ttu-id="2d1eb-176">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="2d1eb-176">Long option</span></span>| <span data-ttu-id="2d1eb-177">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2d1eb-177">Description</span></span>| <span data-ttu-id="2d1eb-178">Örnek</span><span class="sxs-lookup"><span data-stu-id="2d1eb-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="2d1eb-179">-p</span><span class="sxs-lookup"><span data-stu-id="2d1eb-179">-p</span></span>|<span data-ttu-id="2d1eb-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="2d1eb-180">--updateProject</span></span> | <span data-ttu-id="2d1eb-181">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-181">The project to operate on.</span></span> |<span data-ttu-id="2d1eb-182">DotNet openapı Remove *--updateproject .\ref.exe* on .\OpenAPI.js</span><span class="sxs-lookup"><span data-stu-id="2d1eb-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="2d1eb-183">-h</span><span class="sxs-lookup"><span data-stu-id="2d1eb-183">-h</span></span>|<span data-ttu-id="2d1eb-184">--yardım</span><span class="sxs-lookup"><span data-stu-id="2d1eb-184">--help</span></span>|<span data-ttu-id="2d1eb-185">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="2d1eb-185">Show help information</span></span>|<span data-ttu-id="2d1eb-186">DotNet openapı Remove--Help</span><span class="sxs-lookup"><span data-stu-id="2d1eb-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="2d1eb-187">Arguments</span><span class="sxs-lookup"><span data-stu-id="2d1eb-187">Arguments</span></span>

|  <span data-ttu-id="2d1eb-188">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="2d1eb-188">Argument</span></span>  | <span data-ttu-id="2d1eb-189">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2d1eb-189">Description</span></span>| <span data-ttu-id="2d1eb-190">Örnek</span><span class="sxs-lookup"><span data-stu-id="2d1eb-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="2d1eb-191">Kaynak dosya</span><span class="sxs-lookup"><span data-stu-id="2d1eb-191">source-file</span></span> | <span data-ttu-id="2d1eb-192">Başvurunun kaldırılacağı kaynak.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-192">The source to remove the reference to.</span></span> |<span data-ttu-id="2d1eb-193">DotNet openapı kaldırma *.\OpenAPI.js*</span><span class="sxs-lookup"><span data-stu-id="2d1eb-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="2d1eb-194">Yenile</span><span class="sxs-lookup"><span data-stu-id="2d1eb-194">Refresh</span></span>

<span data-ttu-id="2d1eb-195">İndirme URL 'sindeki en son içerik kullanılarak indirilen bir dosyanın yerel sürümünü yeniler.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="2d1eb-196">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="2d1eb-196">Options</span></span>

| <span data-ttu-id="2d1eb-197">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="2d1eb-197">Short option</span></span>| <span data-ttu-id="2d1eb-198">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="2d1eb-198">Long option</span></span>| <span data-ttu-id="2d1eb-199">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2d1eb-199">Description</span></span> | <span data-ttu-id="2d1eb-200">Örnek</span><span class="sxs-lookup"><span data-stu-id="2d1eb-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="2d1eb-201">-p</span><span class="sxs-lookup"><span data-stu-id="2d1eb-201">-p</span></span>|<span data-ttu-id="2d1eb-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="2d1eb-202">--updateProject</span></span> | <span data-ttu-id="2d1eb-203">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-203">The project to operate on.</span></span> | <span data-ttu-id="2d1eb-204">DotNet openapı yenileme *--updateproject .\ref.exe*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="2d1eb-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="2d1eb-205">-h</span><span class="sxs-lookup"><span data-stu-id="2d1eb-205">-h</span></span>|<span data-ttu-id="2d1eb-206">--yardım</span><span class="sxs-lookup"><span data-stu-id="2d1eb-206">--help</span></span>|<span data-ttu-id="2d1eb-207">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="2d1eb-207">Show help information</span></span>|<span data-ttu-id="2d1eb-208">DotNet openapı yenilemesi--yardım</span><span class="sxs-lookup"><span data-stu-id="2d1eb-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="2d1eb-209">Arguments</span><span class="sxs-lookup"><span data-stu-id="2d1eb-209">Arguments</span></span>

|  <span data-ttu-id="2d1eb-210">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="2d1eb-210">Argument</span></span>  | <span data-ttu-id="2d1eb-211">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2d1eb-211">Description</span></span> | <span data-ttu-id="2d1eb-212">Örnek</span><span class="sxs-lookup"><span data-stu-id="2d1eb-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="2d1eb-213">Kaynak-URL</span><span class="sxs-lookup"><span data-stu-id="2d1eb-213">source-URL</span></span> | <span data-ttu-id="2d1eb-214">Başvurunun yenileneceği URL.</span><span class="sxs-lookup"><span data-stu-id="2d1eb-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="2d1eb-215">DotNet openapı yenilemesi `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="2d1eb-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
