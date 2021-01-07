---
title: Openapı kullanarak ASP.NET Core uygulamaları geliştirme
author: ryanbrandenburg
description: Openapı dosyalarına başvurular eklemek için ' Microsoft. DotNet-openapı ' aracının nasıl kullanılacağını gösterir.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
- appsettings.json
- ASP.NET Core Identity
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
ms.openlocfilehash: 5d9f1684aa333c38c73673138a703b04d318c6df
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972034"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="237dd-103">Openapı araçlarını kullanarak ASP.NET Core uygulamaları geliştirme</span><span class="sxs-lookup"><span data-stu-id="237dd-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="237dd-104">İle Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="237dd-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="237dd-105">[Microsoft. DotNet-openapı](https://www.nuget.org/packages/Microsoft.dotnet-openapi) , bir proje Içindeki [openapı](https://github.com/OAI/OpenAPI-Specification) başvurularını yönetmek için [.NET Core küresel bir araçtır](/dotnet/core/tools/global-tools) .</span><span class="sxs-lookup"><span data-stu-id="237dd-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="237dd-106">Yükleme</span><span class="sxs-lookup"><span data-stu-id="237dd-106">Installation</span></span>

<span data-ttu-id="237dd-107">Yüklemek için `Microsoft.dotnet-openapi` Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="237dd-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="237dd-108">Ekle</span><span class="sxs-lookup"><span data-stu-id="237dd-108">Add</span></span>

<span data-ttu-id="237dd-109">Bu sayfadaki komutlardan herhangi birini kullanarak bir Openapı başvurusu eklemek `<OpenApiReference />` , *. csproj* dosyasına aşağıdakine benzer bir öğe ekler:</span><span class="sxs-lookup"><span data-stu-id="237dd-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="237dd-110">Uygulamanın oluşturulan istemci kodunu çağırması için önceki başvuru gereklidir.</span><span class="sxs-lookup"><span data-stu-id="237dd-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="237dd-111">Dosya Ekle</span><span class="sxs-lookup"><span data-stu-id="237dd-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="237dd-112">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="237dd-112">Options</span></span>

| <span data-ttu-id="237dd-113">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="237dd-113">Short option</span></span>| <span data-ttu-id="237dd-114">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="237dd-114">Long option</span></span>| <span data-ttu-id="237dd-115">Açıklama</span><span class="sxs-lookup"><span data-stu-id="237dd-115">Description</span></span> | <span data-ttu-id="237dd-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="237dd-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="237dd-117">-p</span><span class="sxs-lookup"><span data-stu-id="237dd-117">-p</span></span>|<span data-ttu-id="237dd-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="237dd-118">--updateProject</span></span> | <span data-ttu-id="237dd-119">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="237dd-119">The project to operate on.</span></span> |<span data-ttu-id="237dd-120">DotNet openapı dosya ekleme *--updateproject .\ref.exe* .\OpenAPI.js</span><span class="sxs-lookup"><span data-stu-id="237dd-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="237dd-121">-c</span><span class="sxs-lookup"><span data-stu-id="237dd-121">-c</span></span>|<span data-ttu-id="237dd-122">--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="237dd-122">--code-generator</span></span>| <span data-ttu-id="237dd-123">Başvuruya uygulanacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="237dd-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="237dd-124">Seçenekler `NSwagCSharp` ve ' dir `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="237dd-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="237dd-125">Belirtilmemişse, `--code-generator` araçları varsayılan olarak ayarlanır `NSwagCSharp` .</span><span class="sxs-lookup"><span data-stu-id="237dd-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="237dd-126">DotNet openapı dosya ekleme .\OpenApi.js--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="237dd-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="237dd-127">-h</span><span class="sxs-lookup"><span data-stu-id="237dd-127">-h</span></span>|<span data-ttu-id="237dd-128">--yardım</span><span class="sxs-lookup"><span data-stu-id="237dd-128">--help</span></span>|<span data-ttu-id="237dd-129">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="237dd-129">Show help information</span></span>|<span data-ttu-id="237dd-130">DotNet openapı dosya Ekle--yardım</span><span class="sxs-lookup"><span data-stu-id="237dd-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="237dd-131">Arguments</span><span class="sxs-lookup"><span data-stu-id="237dd-131">Arguments</span></span>

|  <span data-ttu-id="237dd-132">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="237dd-132">Argument</span></span>  | <span data-ttu-id="237dd-133">Açıklama</span><span class="sxs-lookup"><span data-stu-id="237dd-133">Description</span></span> | <span data-ttu-id="237dd-134">Örnek</span><span class="sxs-lookup"><span data-stu-id="237dd-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="237dd-135">Kaynak dosya</span><span class="sxs-lookup"><span data-stu-id="237dd-135">source-file</span></span> | <span data-ttu-id="237dd-136">Başvuru oluşturulacak kaynak.</span><span class="sxs-lookup"><span data-stu-id="237dd-136">The source to create a reference from.</span></span> <span data-ttu-id="237dd-137">Bir Openapı dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="237dd-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="237dd-138">DotNet openapı dosya ekleme *.\OpenAPI.js*</span><span class="sxs-lookup"><span data-stu-id="237dd-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="237dd-139">URL Ekle</span><span class="sxs-lookup"><span data-stu-id="237dd-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="237dd-140">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="237dd-140">Options</span></span>

| <span data-ttu-id="237dd-141">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="237dd-141">Short option</span></span>| <span data-ttu-id="237dd-142">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="237dd-142">Long option</span></span>| <span data-ttu-id="237dd-143">Açıklama</span><span class="sxs-lookup"><span data-stu-id="237dd-143">Description</span></span> | <span data-ttu-id="237dd-144">Örnek</span><span class="sxs-lookup"><span data-stu-id="237dd-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="237dd-145">-p</span><span class="sxs-lookup"><span data-stu-id="237dd-145">-p</span></span>|<span data-ttu-id="237dd-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="237dd-146">--updateProject</span></span> | <span data-ttu-id="237dd-147">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="237dd-147">The project to operate on.</span></span> |<span data-ttu-id="237dd-148">DotNet openapı Add URL *--updateproject .\Ref.exe*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="237dd-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="237dd-149">-o</span><span class="sxs-lookup"><span data-stu-id="237dd-149">-o</span></span>|<span data-ttu-id="237dd-150">--Çıkış-dosya</span><span class="sxs-lookup"><span data-stu-id="237dd-150">--output-file</span></span> | <span data-ttu-id="237dd-151">Openapı dosyasının yerel kopyasının yerleştirileceği yer.</span><span class="sxs-lookup"><span data-stu-id="237dd-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="237dd-152">DotNet openapı Add URL `https://contoso.com/openapi.json` *--Çıkış-dosya myclient.js*</span><span class="sxs-lookup"><span data-stu-id="237dd-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="237dd-153">-c</span><span class="sxs-lookup"><span data-stu-id="237dd-153">-c</span></span>|<span data-ttu-id="237dd-154">--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="237dd-154">--code-generator</span></span>| <span data-ttu-id="237dd-155">Başvuruya uygulanacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="237dd-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="237dd-156">Seçenekler `NSwagCSharp` ve ' dir `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="237dd-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="237dd-157">DotNet openapı Add URL `https://contoso.com/openapi.json` --Code-Generator</span><span class="sxs-lookup"><span data-stu-id="237dd-157">dotnet openapi add url `https://contoso.com/openapi.json` --code-generator</span></span>
| <span data-ttu-id="237dd-158">-h</span><span class="sxs-lookup"><span data-stu-id="237dd-158">-h</span></span>|<span data-ttu-id="237dd-159">--yardım</span><span class="sxs-lookup"><span data-stu-id="237dd-159">--help</span></span>|<span data-ttu-id="237dd-160">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="237dd-160">Show help information</span></span>|<span data-ttu-id="237dd-161">DotNet openapı URL ekleme--Yardım</span><span class="sxs-lookup"><span data-stu-id="237dd-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="237dd-162">Arguments</span><span class="sxs-lookup"><span data-stu-id="237dd-162">Arguments</span></span>

|  <span data-ttu-id="237dd-163">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="237dd-163">Argument</span></span>  | <span data-ttu-id="237dd-164">Açıklama</span><span class="sxs-lookup"><span data-stu-id="237dd-164">Description</span></span> | <span data-ttu-id="237dd-165">Örnek</span><span class="sxs-lookup"><span data-stu-id="237dd-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="237dd-166">Kaynak-URL</span><span class="sxs-lookup"><span data-stu-id="237dd-166">source-URL</span></span> | <span data-ttu-id="237dd-167">Başvuru oluşturulacak kaynak.</span><span class="sxs-lookup"><span data-stu-id="237dd-167">The source to create a reference from.</span></span> <span data-ttu-id="237dd-168">Bir URL olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="237dd-168">Must be a URL.</span></span> |<span data-ttu-id="237dd-169">DotNet openapı URL 'si Ekle `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="237dd-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="237dd-170">Kaldır</span><span class="sxs-lookup"><span data-stu-id="237dd-170">Remove</span></span>

<span data-ttu-id="237dd-171">Verilen dosya adıyla eşleşen Openapı başvurusunu *. csproj* dosyasından kaldırır.</span><span class="sxs-lookup"><span data-stu-id="237dd-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="237dd-172">Openapı başvurusu kaldırıldığında, istemciler oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="237dd-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="237dd-173">Local *. JSON* ve *. YAML* dosyaları silinir.</span><span class="sxs-lookup"><span data-stu-id="237dd-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="237dd-174">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="237dd-174">Options</span></span>

| <span data-ttu-id="237dd-175">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="237dd-175">Short option</span></span>| <span data-ttu-id="237dd-176">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="237dd-176">Long option</span></span>| <span data-ttu-id="237dd-177">Açıklama</span><span class="sxs-lookup"><span data-stu-id="237dd-177">Description</span></span>| <span data-ttu-id="237dd-178">Örnek</span><span class="sxs-lookup"><span data-stu-id="237dd-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="237dd-179">-p</span><span class="sxs-lookup"><span data-stu-id="237dd-179">-p</span></span>|<span data-ttu-id="237dd-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="237dd-180">--updateProject</span></span> | <span data-ttu-id="237dd-181">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="237dd-181">The project to operate on.</span></span> |<span data-ttu-id="237dd-182">DotNet openapı Remove *--updateproject .\ref.exe* on .\OpenAPI.js</span><span class="sxs-lookup"><span data-stu-id="237dd-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="237dd-183">-h</span><span class="sxs-lookup"><span data-stu-id="237dd-183">-h</span></span>|<span data-ttu-id="237dd-184">--yardım</span><span class="sxs-lookup"><span data-stu-id="237dd-184">--help</span></span>|<span data-ttu-id="237dd-185">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="237dd-185">Show help information</span></span>|<span data-ttu-id="237dd-186">DotNet openapı Remove--Help</span><span class="sxs-lookup"><span data-stu-id="237dd-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="237dd-187">Arguments</span><span class="sxs-lookup"><span data-stu-id="237dd-187">Arguments</span></span>

|  <span data-ttu-id="237dd-188">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="237dd-188">Argument</span></span>  | <span data-ttu-id="237dd-189">Açıklama</span><span class="sxs-lookup"><span data-stu-id="237dd-189">Description</span></span>| <span data-ttu-id="237dd-190">Örnek</span><span class="sxs-lookup"><span data-stu-id="237dd-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="237dd-191">Kaynak dosya</span><span class="sxs-lookup"><span data-stu-id="237dd-191">source-file</span></span> | <span data-ttu-id="237dd-192">Başvurunun kaldırılacağı kaynak.</span><span class="sxs-lookup"><span data-stu-id="237dd-192">The source to remove the reference to.</span></span> |<span data-ttu-id="237dd-193">DotNet openapı kaldırma *.\OpenAPI.js*</span><span class="sxs-lookup"><span data-stu-id="237dd-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="237dd-194">Yenile</span><span class="sxs-lookup"><span data-stu-id="237dd-194">Refresh</span></span>

<span data-ttu-id="237dd-195">İndirme URL 'sindeki en son içerik kullanılarak indirilen bir dosyanın yerel sürümünü yeniler.</span><span class="sxs-lookup"><span data-stu-id="237dd-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="237dd-196">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="237dd-196">Options</span></span>

| <span data-ttu-id="237dd-197">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="237dd-197">Short option</span></span>| <span data-ttu-id="237dd-198">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="237dd-198">Long option</span></span>| <span data-ttu-id="237dd-199">Açıklama</span><span class="sxs-lookup"><span data-stu-id="237dd-199">Description</span></span> | <span data-ttu-id="237dd-200">Örnek</span><span class="sxs-lookup"><span data-stu-id="237dd-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="237dd-201">-p</span><span class="sxs-lookup"><span data-stu-id="237dd-201">-p</span></span>|<span data-ttu-id="237dd-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="237dd-202">--updateProject</span></span> | <span data-ttu-id="237dd-203">Üzerinde çalışılacak proje.</span><span class="sxs-lookup"><span data-stu-id="237dd-203">The project to operate on.</span></span> | <span data-ttu-id="237dd-204">DotNet openapı yenileme *--updateproject .\ref.exe*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="237dd-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="237dd-205">-h</span><span class="sxs-lookup"><span data-stu-id="237dd-205">-h</span></span>|<span data-ttu-id="237dd-206">--yardım</span><span class="sxs-lookup"><span data-stu-id="237dd-206">--help</span></span>|<span data-ttu-id="237dd-207">Yardım bilgilerini göster</span><span class="sxs-lookup"><span data-stu-id="237dd-207">Show help information</span></span>|<span data-ttu-id="237dd-208">DotNet openapı yenilemesi--yardım</span><span class="sxs-lookup"><span data-stu-id="237dd-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="237dd-209">Arguments</span><span class="sxs-lookup"><span data-stu-id="237dd-209">Arguments</span></span>

|  <span data-ttu-id="237dd-210">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="237dd-210">Argument</span></span>  | <span data-ttu-id="237dd-211">Açıklama</span><span class="sxs-lookup"><span data-stu-id="237dd-211">Description</span></span> | <span data-ttu-id="237dd-212">Örnek</span><span class="sxs-lookup"><span data-stu-id="237dd-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="237dd-213">Kaynak-URL</span><span class="sxs-lookup"><span data-stu-id="237dd-213">source-URL</span></span> | <span data-ttu-id="237dd-214">Başvurunun yenileneceği URL.</span><span class="sxs-lookup"><span data-stu-id="237dd-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="237dd-215">DotNet openapı yenilemesi `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="237dd-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
