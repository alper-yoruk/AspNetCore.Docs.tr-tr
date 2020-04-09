---
title: Swashbuckle ve ASP.NET Core ile başlayın
author: zuckerthoben
description: Swagger UI'yi entegre etmek için ASP.NET Core web API projenize Swashbuckle'ı nasıl ekleyeceğinizi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: da848ef9c5fa85f5186d1b6f0a6111d8c8d069c4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661303"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="ebbde-103">Swashbuckle ve ASP.NET Core ile başlayın</span><span class="sxs-lookup"><span data-stu-id="ebbde-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="ebbde-104">Yazar: [Shayne Boyer](https://twitter.com/spboyer) ve [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="ebbde-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="ebbde-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ebbde-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ebbde-106">Swashbuckle'ın üç temel bileşeni vardır:</span><span class="sxs-lookup"><span data-stu-id="ebbde-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="ebbde-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): JSON uç noktaları olarak `SwaggerDocument` nesneleri ortaya çıkarmak için bir Swagger nesne modeli ve middleware.</span><span class="sxs-lookup"><span data-stu-id="ebbde-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="ebbde-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): `SwaggerDocument` doğrudan rotaları, denetleyicileri ve modelleri nesneleri inşa eden bir Swagger jeneratör.</span><span class="sxs-lookup"><span data-stu-id="ebbde-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="ebbde-109">Bu genellikle Swagger JSON uç noktasını otomatik olarak kullanıma sunmak için Swagger uç noktası ara katmanıyla birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebbde-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="ebbde-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): Swagger UI aracının gömülü bir versiyonu.</span><span class="sxs-lookup"><span data-stu-id="ebbde-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="ebbde-111">Swagger JSON uç noktasını yorumlayarak web API'sinin işlevlerini tanımlayan zengin ve özelleştirilebilir bir deneyim oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ebbde-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="ebbde-112">Genel yöntemler için yerleşik test kuluçkaları içerir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="ebbde-113">Paket kurulumu</span><span class="sxs-lookup"><span data-stu-id="ebbde-113">Package installation</span></span>

<span data-ttu-id="ebbde-114">Swashbuckle aşağıdaki yaklaşımlarla eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="ebbde-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="ebbde-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebbde-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ebbde-116">Paket **Yöneticisi Konsolu** penceresinden:</span><span class="sxs-lookup"><span data-stu-id="ebbde-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="ebbde-117">**Diğer Windows** > **Paket Yöneticisi Konsolu'na** **Git** > </span><span class="sxs-lookup"><span data-stu-id="ebbde-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="ebbde-118">*TodoApi.csproj* dosyasının bulunduğu dizine gidin</span><span class="sxs-lookup"><span data-stu-id="ebbde-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="ebbde-119">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ebbde-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* <span data-ttu-id="ebbde-120">**NuGet Paketlerini Yönet** iletişim kutusundan:</span><span class="sxs-lookup"><span data-stu-id="ebbde-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="ebbde-121">**Solution Explorer** > **Manage NuGet Paketleri'nde** projeye sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="ebbde-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="ebbde-122">Paket **kaynağını** "nuget.org" olarak ayarlama</span><span class="sxs-lookup"><span data-stu-id="ebbde-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="ebbde-123">"Ön sürüm ekle" seçeneğinin etkin olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="ebbde-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="ebbde-124">Arama kutusuna "Swashbuckle.AspNetCore" girin</span><span class="sxs-lookup"><span data-stu-id="ebbde-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="ebbde-125">**Gözat** sekmesinden en son "Swashbuckle.AspNetCore" paketini seçin ve **Yükle'yi** tıklatın</span><span class="sxs-lookup"><span data-stu-id="ebbde-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ebbde-126">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebbde-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ebbde-127">**Çözüm Pad** > Ekle Paketleri'ndeki *Paketler* klasörüne sağ**tıklayın...**</span><span class="sxs-lookup"><span data-stu-id="ebbde-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="ebbde-128">Paketleri **Ekle** penceresinin **Kaynak** açılır penceresini "nuget.org" olarak ayarlama</span><span class="sxs-lookup"><span data-stu-id="ebbde-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="ebbde-129">"Ön sürüm paketlerini göster" seçeneğinin etkin olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="ebbde-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="ebbde-130">Arama kutusuna "Swashbuckle.AspNetCore" girin</span><span class="sxs-lookup"><span data-stu-id="ebbde-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="ebbde-131">Sonuç bölmesinden en son "Swashbuckle.AspNetCore" paketini seçin ve **Paket Ekle'yi** tıklatın</span><span class="sxs-lookup"><span data-stu-id="ebbde-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="ebbde-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebbde-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ebbde-133">**Entegre Terminal'den**aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebbde-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="ebbde-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ebbde-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ebbde-135">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ebbde-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="ebbde-136">Swagger ara yazılım ekleme ve yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ebbde-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="ebbde-137">`Startup` Sınıfta, sınıfı kullanmak için aşağıdaki ad `OpenApiInfo` alanını içeri aktarın:</span><span class="sxs-lookup"><span data-stu-id="ebbde-137">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="ebbde-138">Yöntemde hizmet koleksiyonuna Swagger `Startup.ConfigureServices` jeneratörü ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-138">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

<span data-ttu-id="ebbde-139">`Startup.Configure` Yöntemde, oluşturulan JSON belgesive Swagger UI hizmet için ara yazılım etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-139">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

<span data-ttu-id="ebbde-140">Önceki `UseSwaggerUI` yöntem arama statik [dosya middleware](xref:fundamentals/static-files)sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebbde-140">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="ebbde-141">.NET Framework veya .NET Core 1.x hedeflemesi durumunda, [projeye Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-141">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="ebbde-142">Uygulamayı başlatın ve `http://localhost:<port>/swagger/v1/swagger.json`'ye gidin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-142">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="ebbde-143">Uç noktaları açıklayan oluşturulan belge [Swagger belirtiminde (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)gösterildiği gibi görünür.</span><span class="sxs-lookup"><span data-stu-id="ebbde-143">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="ebbde-144">Swagger UI bulunabilir. `http://localhost:<port>/swagger`</span><span class="sxs-lookup"><span data-stu-id="ebbde-144">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="ebbde-145">Swagger UI ile API'yi keşfedin ve diğer programlara dahil edin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-145">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="ebbde-146">Swagger UI'ye uygulamanın kökünden`http://localhost:<port>/`hizmet etmek `RoutePrefix` için ( ), özelliği boş bir dize olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="ebbde-146">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="ebbde-147">IIS veya ters proxy ile dizinler kullanıyorsanız, `./` önek kullanarak Swagger bitiş noktasını göreli bir yola ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ebbde-147">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="ebbde-148">Örneğin, `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="ebbde-148">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="ebbde-149">Kullanmak, `/swagger/v1/swagger.json` uygulamanın URL'nin gerçek kökündeki JSON dosyasını aramasını (artı kullanılırsa rota öneki) kullanmatalimatı verir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-149">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="ebbde-150">Örneğin `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` yerine `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` kullanın.</span><span class="sxs-lookup"><span data-stu-id="ebbde-150">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="ebbde-151">Özelleştirin ve genişletin</span><span class="sxs-lookup"><span data-stu-id="ebbde-151">Customize and extend</span></span>

<span data-ttu-id="ebbde-152">Swagger, nesne modelini belgelemek ve kullanıcı arasını temanıza uyacak şekilde özelleştirmek için seçenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="ebbde-152">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="ebbde-153">`Startup` Sınıfta, aşağıdaki ad alanlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-153">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="ebbde-154">API bilgileri ve açıklaması</span><span class="sxs-lookup"><span data-stu-id="ebbde-154">API info and description</span></span>

<span data-ttu-id="ebbde-155">`AddSwaggerGen` Yönteme geçirilen yapılandırma eylemi, yazar, lisans ve açıklama gibi bilgiler ekler:</span><span class="sxs-lookup"><span data-stu-id="ebbde-155">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="ebbde-156">Swagger Kullanıcı İyiuŞu sürümün bilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ebbde-156">The Swagger UI displays the version's information:</span></span>

![Sürüm bilgileriyle Swagger Kullanıcı Özel Birimi: açıklama, yazar ve daha fazla bağlantı görün](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="ebbde-158">XML açıklamaları</span><span class="sxs-lookup"><span data-stu-id="ebbde-158">XML comments</span></span>

<span data-ttu-id="ebbde-159">XML yorumları aşağıdaki yaklaşımlarla etkinleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="ebbde-159">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="ebbde-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebbde-160">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="ebbde-161">**Solution Explorer'da** projeyi sağ tıklatın ve project_name **<>.csproj'u**<edit'i seçin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-161">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="ebbde-162">Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-162">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="ebbde-163">**Solution Explorer'da** projeyi sağ tıklatın ve **Özellikler'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-163">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="ebbde-164">**Yap** sekmesinin **Çıktı** bölümü altındaki **XML dokümantasyon dosya** kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-164">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ebbde-165">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ebbde-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="ebbde-166">Çözüm *Defteri'nden* **denetime** basın ve proje adını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="ebbde-166">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="ebbde-167">**Araçlar** > **Dosyayı Edit'e**gidin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-167">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="ebbde-168">Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-168">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="ebbde-169">> **Yapı** **Derleyicisi** > **Proje Seçenekleri** iletişim kutusunu açın</span><span class="sxs-lookup"><span data-stu-id="ebbde-169">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="ebbde-170">**Genel Seçenekler** bölümünün altındaki **XML dokümantasyon** kutusunu oluştur'u işaretleyin</span><span class="sxs-lookup"><span data-stu-id="ebbde-170">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="ebbde-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebbde-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ebbde-172">Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-172">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="ebbde-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ebbde-173">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ebbde-174">Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-174">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="ebbde-175">XML yorumlarını etkinleştirmek, belgesiz genel türler ve üyeler için hata ayıklama bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebbde-175">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="ebbde-176">Belgelenmemiş türleri ve üyeleri uyarı iletisi ile gösterilir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-176">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="ebbde-177">Örneğin, aşağıdaki ileti uyarı kodu 1591 ihlalini gösterir:</span><span class="sxs-lookup"><span data-stu-id="ebbde-177">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="ebbde-178">Proje genelinde ki uyarıları bastırmak için, proje dosyasında yoksayılması gereken yarı sütunlu sınırlı bir uyarı kodları listesi tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="ebbde-178">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="ebbde-179">[C# varsayılan](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) değerlerini `$(NoWarn);` de uygular için uyarı kodlarını ekler.</span><span class="sxs-lookup"><span data-stu-id="ebbde-179">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="ebbde-180">Yalnızca belirli üyeler için uyarıları bastırmak için, kodu uyarı önişlemci [yönergelerine #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) girin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-180">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="ebbde-181">Bu yaklaşım, API dokümanları aracılığıyla maruz kalmaması gereken kodlar için yararlıdır. Aşağıdaki örnekte, cs1591 uyarı kodu tüm `Program` sınıf için yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="ebbde-181">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="ebbde-182">Uyarı kodunun uygulanması sınıf tanımının sonunda geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-182">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="ebbde-183">Virgülle sınırlandırılmış listeyle birden çok uyarı kodu belirtin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-183">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="ebbde-184">Swagger'ı, önceki yönergelerle oluşturulan XML dosyasını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ebbde-184">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="ebbde-185">Linux veya Windows olmayan işletim sistemleri için dosya adları ve yollar büyük/küçük harf duyarlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-185">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="ebbde-186">Örneğin, *Bir TodoApi.XML* dosyası Windows'da geçerlidir, ancak CentOS'ta geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-186">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="ebbde-187">Önceki kodda, [Yansıma](/dotnet/csharp/programming-guide/concepts/reflection) web API projesiyle eşleşen bir XML dosya adı oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebbde-187">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="ebbde-188">[AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) özelliği XML dosyasına bir yol oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ebbde-188">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="ebbde-189">Bazı Swagger özellikleri (örneğin, giriş parametrelerinin şema veya ilgili özniteliklerden HTTP yöntemleri ve yanıt kodları) bir XML dokümantasyon dosyası kullanmadan çalışır.</span><span class="sxs-lookup"><span data-stu-id="ebbde-189">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="ebbde-190">Çoğu özellik, yani yöntem özetleri ve parametrelerin ve yanıt kodlarının açıklamaları için, bir XML dosyasının kullanılması zorunludur.</span><span class="sxs-lookup"><span data-stu-id="ebbde-190">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="ebbde-191">Bir eyleme üç eğik çizgiyle açıklama eklediğinizde bölüm üst bilgisine açıklama eklenir ve Swagger UI geliştirilir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-191">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="ebbde-192">Eylemin üzerine [ \<bir özet>](/dotnet/csharp/programming-guide/xmldoc/summary) öğesi ekleyin: `Delete`</span><span class="sxs-lookup"><span data-stu-id="ebbde-192">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="ebbde-193">Swagger Kullanıcı İyiucu Su Birimi, önceki kodun `<summary>` öğesinin iç metnini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ebbde-193">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![XML yorumunu gösteren Swagger UI 'Belirli bir TodoItem'i siler.'](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="ebbde-196">UI oluşturulan JSON şema tarafından tahrik edilir:</span><span class="sxs-lookup"><span data-stu-id="ebbde-196">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="ebbde-197">Eylem yöntemi belgelerine [ \<bir açıklama>](/dotnet/csharp/programming-guide/xmldoc/remarks) öğesi ekleyin. `Create`</span><span class="sxs-lookup"><span data-stu-id="ebbde-197">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="ebbde-198">Bu `<summary>` öğede belirtilen bilgileri tamamlar ve daha sağlam bir Swagger UI sağlar.</span><span class="sxs-lookup"><span data-stu-id="ebbde-198">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="ebbde-199">`<remarks>` Öğe içeriği metin, JSON veya XML'den oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-199">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="ebbde-200">Bu ek açıklamalar ile UI geliştirmeleri dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-200">Notice the UI enhancements with these additional comments:</span></span>

![Gösterilen ek yorumlar ile Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="ebbde-202">Veri açıklamaları</span><span class="sxs-lookup"><span data-stu-id="ebbde-202">Data annotations</span></span>

<span data-ttu-id="ebbde-203">Swagger UI bileşenlerini sürücüye yardımcı olmak için [modeli System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) ad alanında bulunan özniteliklerle işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-203">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="ebbde-204">`[Required]` `Name` Sınıfın özelliğine öznitelik `TodoItem` ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-204">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="ebbde-205">Bu öznitelik varlığı UI davranışını değiştirir ve altta yatan JSON şema sını değiştirir:</span><span class="sxs-lookup"><span data-stu-id="ebbde-205">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="ebbde-206">`[Produces("application/json")]` ÖZniteliği API denetleyicisine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-206">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="ebbde-207">Amacı, denetleyicinin eylemlerinin bir yanıt içeriği *uygulama/json*türünü desteklediğini beyan etmektir:</span><span class="sxs-lookup"><span data-stu-id="ebbde-207">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="ebbde-208">**Yanıt İçerik Türü** açılır bu içerik türünü denetleyicinin GET eylemleri için varsayılan olarak seçer:</span><span class="sxs-lookup"><span data-stu-id="ebbde-208">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Varsayılan yanıt içeriği türüne sahip Swagger Kullanıcı BiraSı](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="ebbde-210">Web API'deki veri ek açıklamalarının kullanımı arttıkça, UI ve API yardım sayfaları daha açıklayıcı ve yararlı hale gelir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-210">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="ebbde-211">Yanıt türlerini açıklayın</span><span class="sxs-lookup"><span data-stu-id="ebbde-211">Describe response types</span></span>

<span data-ttu-id="ebbde-212">Web API'si tüketen geliştiriciler en&mdash;çok yanıt türleri ve hata kodları (standart değilse) döndürülenlerle ilgilenirler.</span><span class="sxs-lookup"><span data-stu-id="ebbde-212">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="ebbde-213">Yanıt türleri ve hata kodları XML yorumlarında ve veri ek açıklamalarında gösterilir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-213">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="ebbde-214">Eylem `Create` başarı bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="ebbde-214">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="ebbde-215">Deftere nakledilen istek gövdesi null olduğunda bir HTTP 400 durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ebbde-215">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="ebbde-216">Swagger UI uygun belgeler olmadan, tüketici bu beklenen sonuçlar hakkında bilgi yoksundur.</span><span class="sxs-lookup"><span data-stu-id="ebbde-216">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="ebbde-217">Aşağıdaki örnekte vurgulanan satırları ekleyerek bu sorunu giderin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-217">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="ebbde-218">Swagger UI şimdi açıkça beklenen HTTP yanıt kodları belgeler:</span><span class="sxs-lookup"><span data-stu-id="ebbde-218">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![POSTA Yanıt Sınıfı açıklamasını gösteren Swagger UI 'Yeni oluşturulan Todo öğesini döndürür' ve '400 - Öğe null ise' durum kodu ve Yanıt İletileri altında neden için](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ebbde-220">Core 2.2 veya daha sonra ASP.NET, sözleşmeler açıkça ile bireysel eylemleri `[ProducesResponseType]`dekorasyon alternatif olarak kullanılabilir .</span><span class="sxs-lookup"><span data-stu-id="ebbde-220">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="ebbde-221">Daha fazla bilgi için bkz. <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="ebbde-221">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="ebbde-222">Kullanıcı arabirimini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="ebbde-222">Customize the UI</span></span>

<span data-ttu-id="ebbde-223">Stok UI hem işlevsel hem de sunulabilir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-223">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="ebbde-224">Ancak, API dokümantasyon sayfaları markanızı veya temanızı temsil etmelidir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-224">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="ebbde-225">Swashbuckle bileşenlerinin markalandırılması, statik dosyalara hizmet verecek kaynakların eklenmesini ve bu dosyaları barındıracak klasör yapısını oluşturmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-225">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="ebbde-226">.NET Framework veya .NET Core 1.x hedeflemesi durumunda, [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet paketini projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-226">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="ebbde-227">.NET Core 2.x'i hedefleyen ve [meta paketi](xref:fundamentals/metapackage)kullanılarak önceki NuGet paketi zaten yüklenmiş.</span><span class="sxs-lookup"><span data-stu-id="ebbde-227">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="ebbde-228">Statik Dosya Middleware etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="ebbde-228">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="ebbde-229">[Swagger UI GitHub deposundan](https://github.com/swagger-api/swagger-ui/tree/master/dist) *dist* klasörünün içeriğini edinin.</span><span class="sxs-lookup"><span data-stu-id="ebbde-229">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="ebbde-230">Bu klasör, Swagger UI sayfası için gerekli varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="ebbde-230">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="ebbde-231">Bir *wwwroot/swagger/ui* klasörü oluşturun ve *dist* klasörünün içeriğini kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="ebbde-231">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="ebbde-232">Sayfa üstbilgisini özelleştirmek için aşağıdaki CSS ile *wwwroot/swagger/ui'de* *bir custom.css* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ebbde-232">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="ebbde-233">Diğer CSS dosyalarından sonra, ui klasörü içindeki *index.html* dosyasında *custom.css'ye* başvurun:</span><span class="sxs-lookup"><span data-stu-id="ebbde-233">Reference *custom.css* in the *index.html* file inside ui folder, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="ebbde-234">*index.html* sayfasına göz `http://localhost:<port>/swagger/ui/index.html`atın.</span><span class="sxs-lookup"><span data-stu-id="ebbde-234">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="ebbde-235">Üstbilginin metin kutusuna girin `https://localhost:<port>/swagger/v1/swagger.json` ve **Keşfet** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="ebbde-235">Enter `https://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="ebbde-236">Ortaya çıkan sayfa aşağıdaki gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="ebbde-236">The resulting page looks as follows:</span></span>

![Özel üstbilgi başlığı ile Swagger UI](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="ebbde-238">Sayfayla yapabileceğiniz çok şey var.</span><span class="sxs-lookup"><span data-stu-id="ebbde-238">There's much more you can do with the page.</span></span> <span data-ttu-id="ebbde-239">[Swagger UI GitHub deposundaki](https://github.com/swagger-api/swagger-ui)UI kaynaklarıiçin tam yetenekleri görün.</span><span class="sxs-lookup"><span data-stu-id="ebbde-239">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
