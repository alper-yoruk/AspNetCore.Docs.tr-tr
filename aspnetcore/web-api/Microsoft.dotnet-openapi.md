---
title: Openapı kullanarak ASP.NET Core uygulamaları geliştirme
author: ryanbrandenburg
description: Openapı dosyalarına başvurular eklemek için ' Microsoft. DotNet-openapı ' aracının nasıl kullanılacağını gösterir.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 1924fb8ee5ac1ba8dc31d2175a336c8333c81fb2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775719"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="8d544-103">Openapı araçlarını kullanarak ASP.NET Core uygulamaları geliştirme</span><span class="sxs-lookup"><span data-stu-id="8d544-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="8d544-104">İle Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="8d544-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="8d544-105">[Microsoft. DotNet-openapı](https://www.nuget.org/packages/Microsoft.dotnet-openapi) , bir proje Içindeki [openapı](https://github.com/OAI/OpenAPI-Specification) başvurularını yönetmek için [.NET Core küresel bir araçtır](/dotnet/core/tools/global-tools) .</span><span class="sxs-lookup"><span data-stu-id="8d544-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="8d544-106">Yükleme</span><span class="sxs-lookup"><span data-stu-id="8d544-106">Installation</span></span>

<span data-ttu-id="8d544-107">Yüklemek `Microsoft.dotnet-openapi`için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8d544-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="8d544-108">Ekle</span><span class="sxs-lookup"><span data-stu-id="8d544-108">Add</span></span>

<span data-ttu-id="8d544-109">Bu sayfadaki komutlardan herhangi birini kullanarak bir Openapı başvurusu eklemek, *. csproj* dosyasına `<OpenApiReference />` aşağıdakine benzer bir öğe ekler:</span><span class="sxs-lookup"><span data-stu-id="8d544-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="8d544-110">Uygulamanın oluşturulan istemci kodunu çağırması için önceki başvuru gereklidir.</span><span class="sxs-lookup"><span data-stu-id="8d544-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="8d544-111">Dosya Ekle</span><span class="sxs-lookup"><span data-stu-id="8d544-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="8d544-112">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="8d544-112">Options</span></span>

| <span data-ttu-id="8d544-113">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="8d544-113">Short option</span></span>| <span data-ttu-id="8d544-114">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="8d544-114">Long option</span></span>| <span data-ttu-id="8d544-115">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d544-115">Description</span></span> | <span data-ttu-id="8d544-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="8d544-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="8d544-117">-p</span><span class="sxs-lookup"><span data-stu-id="8d544-117">-p</span></span>|<span data-ttu-id="8d544-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="8d544-118">--updateProject</span></span> | <span data-ttu-id="8d544-119">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="8d544-119">The project to operate on.</span></span> |<span data-ttu-id="8d544-120">DotNet openapı dosya Ekle *--updateproject .\Ref.exe* .\Openapi.exe</span><span class="sxs-lookup"><span data-stu-id="8d544-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="8d544-121">-c</span><span class="sxs-lookup"><span data-stu-id="8d544-121">-c</span></span>|<span data-ttu-id="8d544-122">--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="8d544-122">--code-generator</span></span>| <span data-ttu-id="8d544-123">Başvuruya uygulanacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="8d544-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="8d544-124">Seçenekler ve `NSwagCSharp` ' `NSwagTypeScript`dir.</span><span class="sxs-lookup"><span data-stu-id="8d544-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="8d544-125">Belirtilmemişse `--code-generator` , araçları varsayılan olarak `NSwagCSharp`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8d544-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="8d544-126">DotNet openapı Add dosyası .\Openapi.exe JSON--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="8d544-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="8d544-127">-h</span><span class="sxs-lookup"><span data-stu-id="8d544-127">-h</span></span>|<span data-ttu-id="8d544-128">--yardım</span><span class="sxs-lookup"><span data-stu-id="8d544-128">--help</span></span>|<span data-ttu-id="8d544-129">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="8d544-129">Show help information</span></span>|<span data-ttu-id="8d544-130">DotNet openapı dosya Ekle--yardım</span><span class="sxs-lookup"><span data-stu-id="8d544-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="8d544-131">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="8d544-131">Arguments</span></span>

|  <span data-ttu-id="8d544-132">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="8d544-132">Argument</span></span>  | <span data-ttu-id="8d544-133">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d544-133">Description</span></span> | <span data-ttu-id="8d544-134">Örnek</span><span class="sxs-lookup"><span data-stu-id="8d544-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="8d544-135">Kaynak dosya</span><span class="sxs-lookup"><span data-stu-id="8d544-135">source-file</span></span> | <span data-ttu-id="8d544-136">Başvuru oluşturulacak kaynak.</span><span class="sxs-lookup"><span data-stu-id="8d544-136">The source to create a reference from.</span></span> <span data-ttu-id="8d544-137">Bir Openapı dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d544-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="8d544-138">DotNet openapı dosya ekleme *.\Openapi.exe*</span><span class="sxs-lookup"><span data-stu-id="8d544-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="8d544-139">URL Ekle</span><span class="sxs-lookup"><span data-stu-id="8d544-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="8d544-140">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="8d544-140">Options</span></span>

| <span data-ttu-id="8d544-141">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="8d544-141">Short option</span></span>| <span data-ttu-id="8d544-142">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="8d544-142">Long option</span></span>| <span data-ttu-id="8d544-143">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d544-143">Description</span></span> | <span data-ttu-id="8d544-144">Örnek</span><span class="sxs-lookup"><span data-stu-id="8d544-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="8d544-145">-p</span><span class="sxs-lookup"><span data-stu-id="8d544-145">-p</span></span>|<span data-ttu-id="8d544-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="8d544-146">--updateProject</span></span> | <span data-ttu-id="8d544-147">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="8d544-147">The project to operate on.</span></span> |<span data-ttu-id="8d544-148">DotNet openapı Add URL *--updateproject .\Ref.exe*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="8d544-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="8d544-149">-o</span><span class="sxs-lookup"><span data-stu-id="8d544-149">-o</span></span>|<span data-ttu-id="8d544-150">--Çıkış-dosya</span><span class="sxs-lookup"><span data-stu-id="8d544-150">--output-file</span></span> | <span data-ttu-id="8d544-151">Openapı dosyasının yerel kopyasının yerleştirileceği yer.</span><span class="sxs-lookup"><span data-stu-id="8d544-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="8d544-152">DotNet openapı Add URL `https://contoso.com/openapi.json` *--çıktı-File MyClient. JSON*</span><span class="sxs-lookup"><span data-stu-id="8d544-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="8d544-153">-c</span><span class="sxs-lookup"><span data-stu-id="8d544-153">-c</span></span>|<span data-ttu-id="8d544-154">--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="8d544-154">--code-generator</span></span>| <span data-ttu-id="8d544-155">Başvuruya uygulanacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="8d544-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="8d544-156">Seçenekler ve `NSwagCSharp` ' `NSwagTypeScript`dir.</span><span class="sxs-lookup"><span data-stu-id="8d544-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="8d544-157">DotNet openapı Add dosyası .\Openapi.exe JSON--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="8d544-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="8d544-158">-h</span><span class="sxs-lookup"><span data-stu-id="8d544-158">-h</span></span>|<span data-ttu-id="8d544-159">--yardım</span><span class="sxs-lookup"><span data-stu-id="8d544-159">--help</span></span>|<span data-ttu-id="8d544-160">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="8d544-160">Show help information</span></span>|<span data-ttu-id="8d544-161">DotNet openapı URL ekleme--Yardım</span><span class="sxs-lookup"><span data-stu-id="8d544-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="8d544-162">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="8d544-162">Arguments</span></span>

|  <span data-ttu-id="8d544-163">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="8d544-163">Argument</span></span>  | <span data-ttu-id="8d544-164">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d544-164">Description</span></span> | <span data-ttu-id="8d544-165">Örnek</span><span class="sxs-lookup"><span data-stu-id="8d544-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="8d544-166">Kaynak-URL</span><span class="sxs-lookup"><span data-stu-id="8d544-166">source-URL</span></span> | <span data-ttu-id="8d544-167">Başvuru oluşturulacak kaynak.</span><span class="sxs-lookup"><span data-stu-id="8d544-167">The source to create a reference from.</span></span> <span data-ttu-id="8d544-168">Bir URL olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d544-168">Must be a URL.</span></span> |<span data-ttu-id="8d544-169">DotNet openapı URL 'si Ekle`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="8d544-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="8d544-170">Kaldır</span><span class="sxs-lookup"><span data-stu-id="8d544-170">Remove</span></span>

<span data-ttu-id="8d544-171">Verilen dosya adıyla eşleşen Openapı başvurusunu *. csproj* dosyasından kaldırır.</span><span class="sxs-lookup"><span data-stu-id="8d544-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="8d544-172">Openapı başvurusu kaldırıldığında, istemciler oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="8d544-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="8d544-173">Local *. JSON* ve *. YAML* dosyaları silinir.</span><span class="sxs-lookup"><span data-stu-id="8d544-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="8d544-174">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="8d544-174">Options</span></span>

| <span data-ttu-id="8d544-175">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="8d544-175">Short option</span></span>| <span data-ttu-id="8d544-176">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="8d544-176">Long option</span></span>| <span data-ttu-id="8d544-177">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d544-177">Description</span></span>| <span data-ttu-id="8d544-178">Örnek</span><span class="sxs-lookup"><span data-stu-id="8d544-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="8d544-179">-p</span><span class="sxs-lookup"><span data-stu-id="8d544-179">-p</span></span>|<span data-ttu-id="8d544-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="8d544-180">--updateProject</span></span> | <span data-ttu-id="8d544-181">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="8d544-181">The project to operate on.</span></span> |<span data-ttu-id="8d544-182">DotNet openapı Remove *--updateproject .\ref. csproj* .\openapi.exe</span><span class="sxs-lookup"><span data-stu-id="8d544-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="8d544-183">-h</span><span class="sxs-lookup"><span data-stu-id="8d544-183">-h</span></span>|<span data-ttu-id="8d544-184">--yardım</span><span class="sxs-lookup"><span data-stu-id="8d544-184">--help</span></span>|<span data-ttu-id="8d544-185">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="8d544-185">Show help information</span></span>|<span data-ttu-id="8d544-186">DotNet openapı Remove--Help</span><span class="sxs-lookup"><span data-stu-id="8d544-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="8d544-187">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="8d544-187">Arguments</span></span>

|  <span data-ttu-id="8d544-188">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="8d544-188">Argument</span></span>  | <span data-ttu-id="8d544-189">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d544-189">Description</span></span>| <span data-ttu-id="8d544-190">Örnek</span><span class="sxs-lookup"><span data-stu-id="8d544-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="8d544-191">Kaynak dosya</span><span class="sxs-lookup"><span data-stu-id="8d544-191">source-file</span></span> | <span data-ttu-id="8d544-192">Başvurunun kaldırılacağı kaynak.</span><span class="sxs-lookup"><span data-stu-id="8d544-192">The source to remove the reference to.</span></span> |<span data-ttu-id="8d544-193">DotNet openapı kaldırma *.\Openapi.exe*</span><span class="sxs-lookup"><span data-stu-id="8d544-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="8d544-194">Yenile</span><span class="sxs-lookup"><span data-stu-id="8d544-194">Refresh</span></span>

<span data-ttu-id="8d544-195">İndirme URL 'sindeki en son içerik kullanılarak indirilen bir dosyanın yerel sürümünü yeniler.</span><span class="sxs-lookup"><span data-stu-id="8d544-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="8d544-196">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="8d544-196">Options</span></span>

| <span data-ttu-id="8d544-197">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="8d544-197">Short option</span></span>| <span data-ttu-id="8d544-198">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="8d544-198">Long option</span></span>| <span data-ttu-id="8d544-199">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d544-199">Description</span></span> | <span data-ttu-id="8d544-200">Örnek</span><span class="sxs-lookup"><span data-stu-id="8d544-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="8d544-201">-p</span><span class="sxs-lookup"><span data-stu-id="8d544-201">-p</span></span>|<span data-ttu-id="8d544-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="8d544-202">--updateProject</span></span> | <span data-ttu-id="8d544-203">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="8d544-203">The project to operate on.</span></span> | <span data-ttu-id="8d544-204">DotNet openapı yenileme *--updateproject .\ref.exe*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="8d544-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="8d544-205">-h</span><span class="sxs-lookup"><span data-stu-id="8d544-205">-h</span></span>|<span data-ttu-id="8d544-206">--yardım</span><span class="sxs-lookup"><span data-stu-id="8d544-206">--help</span></span>|<span data-ttu-id="8d544-207">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="8d544-207">Show help information</span></span>|<span data-ttu-id="8d544-208">DotNet openapı yenilemesi--yardım</span><span class="sxs-lookup"><span data-stu-id="8d544-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="8d544-209">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="8d544-209">Arguments</span></span>

|  <span data-ttu-id="8d544-210">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="8d544-210">Argument</span></span>  | <span data-ttu-id="8d544-211">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d544-211">Description</span></span> | <span data-ttu-id="8d544-212">Örnek</span><span class="sxs-lookup"><span data-stu-id="8d544-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="8d544-213">Kaynak-URL</span><span class="sxs-lookup"><span data-stu-id="8d544-213">source-URL</span></span> | <span data-ttu-id="8d544-214">Başvurunun yenileneceği URL.</span><span class="sxs-lookup"><span data-stu-id="8d544-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="8d544-215">DotNet openapı yenilemesi`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="8d544-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
