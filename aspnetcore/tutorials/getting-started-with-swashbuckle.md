---
title: Swashbuckle ve ASP.NET Core kullanmaya başlayın
author: zuckerthoben
description: Swagger Kullanıcı arabirimini bütünleştirmek için ASP.NET Core Web API Projenize swashbuckle ekleme hakkında bilgi edinin.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 0a47ed3338ebfbc5361a6082978d407543fb95c5
ms.sourcegitcommit: b06511252f165dd4590ba9b5beca4153fa220779
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/27/2020
ms.locfileid: "85459785"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="55ea9-103">Swashbuckle ve ASP.NET Core kullanmaya başlayın</span><span class="sxs-lookup"><span data-stu-id="55ea9-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="55ea9-104">, [Shayne Boyer](https://twitter.com/spboyer) ve [Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="55ea9-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="55ea9-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="55ea9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="55ea9-106">Swashbuckle'ın üç temel bileşeni vardır:</span><span class="sxs-lookup"><span data-stu-id="55ea9-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="55ea9-107">[Swashbuckle. AspNetCore. Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): `SwaggerDocument` nesneleri JSON uç noktaları olarak göstermek için Swagger nesne modeli ve ara yazılımı.</span><span class="sxs-lookup"><span data-stu-id="55ea9-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="55ea9-108">[Swashbuckle. AspNetCore. SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): `SwaggerDocument` nesneleri doğrudan rotalarınız, Denetleyicilerinizden ve modellerden oluşturan bir Swagger Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="55ea9-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="55ea9-109">Bu genellikle Swagger JSON uç noktasını otomatik olarak kullanıma sunmak için Swagger uç noktası ara katmanıyla birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="55ea9-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="55ea9-110">[Swashbuckle. AspNetCore. SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): Swagger Kullanıcı arabirimi aracının gömülü bir sürümü.</span><span class="sxs-lookup"><span data-stu-id="55ea9-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="55ea9-111">Swagger JSON uç noktasını yorumlayarak web API'sinin işlevlerini tanımlayan zengin ve özelleştirilebilir bir deneyim oluşturur.</span><span class="sxs-lookup"><span data-stu-id="55ea9-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="55ea9-112">Genel yöntemler için yerleşik test kuluçkaları içerir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="55ea9-113">Paket yüklemesi</span><span class="sxs-lookup"><span data-stu-id="55ea9-113">Package installation</span></span>

<span data-ttu-id="55ea9-114">Aşağıdaki yaklaşımlar ile swashbuckle eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="55ea9-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="55ea9-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55ea9-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="55ea9-116">**Paket Yöneticisi konsol** penceresinde:</span><span class="sxs-lookup"><span data-stu-id="55ea9-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="55ea9-117">**View**  >  **Diğer Windows**  >  **Paket Yöneticisi konsolunu** görüntüle ' ye git</span><span class="sxs-lookup"><span data-stu-id="55ea9-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="55ea9-118">*TodoApi. csproj* dosyasının bulunduğu dizine gidin</span><span class="sxs-lookup"><span data-stu-id="55ea9-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="55ea9-119">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="55ea9-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.5.0
    ```

* <span data-ttu-id="55ea9-120">**NuGet Paketlerini Yönet** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="55ea9-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="55ea9-121">**Çözüm Gezgini**  >  **NuGet Paketlerini Yönet** ' de projeye sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="55ea9-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="55ea9-122">**Paket kaynağını** "NuGet.org" olarak ayarlayın</span><span class="sxs-lookup"><span data-stu-id="55ea9-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="55ea9-123">"Ön sürümü dahil et" seçeneğinin etkinleştirildiğinden emin olun</span><span class="sxs-lookup"><span data-stu-id="55ea9-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="55ea9-124">Arama kutusuna "swashbuckle. AspNetCore" yazın</span><span class="sxs-lookup"><span data-stu-id="55ea9-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="55ea9-125">**Araştır** sekmesinden en son "swashbuckle. aspnetcore" paketini seçin ve sonra da **yüklensin** ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="55ea9-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="55ea9-126">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55ea9-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="55ea9-127">*Packages* **Çözüm bölmesi**  >  **paket Ekle...** ' da paketler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="55ea9-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="55ea9-128">**Paket Ekle** penceresinin **kaynak** açılan penceresini "NuGet.org" olarak ayarlayın</span><span class="sxs-lookup"><span data-stu-id="55ea9-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="55ea9-129">"Yayın öncesi paketleri göster" seçeneğinin etkin olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="55ea9-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="55ea9-130">Arama kutusuna "swashbuckle. AspNetCore" yazın</span><span class="sxs-lookup"><span data-stu-id="55ea9-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="55ea9-131">Sonuçlar bölmesinden en son "swashbuckle. AspNetCore" paketini seçin ve **paket Ekle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="55ea9-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="55ea9-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="55ea9-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="55ea9-133">**Tümleşik terminalden**aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="55ea9-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="55ea9-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="55ea9-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="55ea9-135">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="55ea9-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="55ea9-136">Swagger ara yazılım ekleme ve yapılandırma</span><span class="sxs-lookup"><span data-stu-id="55ea9-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="55ea9-137">Yöntemdeki Services koleksiyonuna Swagger oluşturucuyu ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="55ea9-137">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

<span data-ttu-id="55ea9-138">`Startup.Configure`Yönteminde, oluşturulan JSON belgesine ve Swagger Kullanıcı arabirimine hizmet veren ara yazılımı etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="55ea9-138">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="55ea9-139">Swashbuckle <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> , yolları ve uç noktalarını öğrenmek IÇIN MVC 'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="55ea9-139">Swashbuckle relies on MVC's <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> to discover the routes and endpoints.</span></span> <span data-ttu-id="55ea9-140">Proje çağrıları <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A> , rotalar ve uç noktaları otomatik olarak keşfedilir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-140">If the project calls <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>, routes and endpoints are discovered automatically.</span></span> <span data-ttu-id="55ea9-141">Çağrıldığında <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A> , <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> yönteminin açıkça çağrılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-141">When calling <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>, the <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> method must be explicitly called.</span></span> <span data-ttu-id="55ea9-142">Daha fazla bilgi için bkz. [swashbuckle, ApiExplorer ve yönlendirme](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span><span class="sxs-lookup"><span data-stu-id="55ea9-142">For more information, see [Swashbuckle, ApiExplorer, and Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span></span>

<span data-ttu-id="55ea9-143">Önceki `UseSwaggerUI` Yöntem çağrısı [statik dosya ara yazılımını](xref:fundamentals/static-files)sunar.</span><span class="sxs-lookup"><span data-stu-id="55ea9-143">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="55ea9-144">.NET Framework veya .NET Core 1. x 'i hedefliyorsanız, projeye [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="55ea9-144">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="55ea9-145">Uygulamayı başlatın ve adresine gidin `http://localhost:<port>/swagger/v1/swagger.json` .</span><span class="sxs-lookup"><span data-stu-id="55ea9-145">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="55ea9-146">Uç noktaları tanımlayan oluşturulan belge, Swagger belirtiminde gösterildiği gibi görünür [(üzerinde swagger.js)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="55ea9-146">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="55ea9-147">Swagger Kullanıcı arabirimi adresinde bulunabilir `http://localhost:<port>/swagger` .</span><span class="sxs-lookup"><span data-stu-id="55ea9-147">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="55ea9-148">Swagger Kullanıcı arabirimi aracılığıyla API 'YI keşfet ve diğer programlarda birleştirme.</span><span class="sxs-lookup"><span data-stu-id="55ea9-148">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="55ea9-149">Swagger Kullanıcı arabirimine uygulamanın kökünde () hizmeti sağlamak için `http://localhost:<port>/` , `RoutePrefix` özelliğini boş bir dizeye ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="55ea9-149">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="55ea9-150">IIS veya ters proxy ile dizin kullanıyorsanız, Swagger uç noktasını, öneki kullanılarak göreli bir yol olarak ayarlayın `./` .</span><span class="sxs-lookup"><span data-stu-id="55ea9-150">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="55ea9-151">Örneğin, `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="55ea9-151">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="55ea9-152">Kullanarak `/swagger/v1/swagger.json` , UYGULAMANıN URL 'nin gerçek KÖKÜNDE json dosyasını aramasını söyler (Ayrıca kullanılıyorsa rota öneki).</span><span class="sxs-lookup"><span data-stu-id="55ea9-152">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="55ea9-153">Örneğin `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` yerine `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` kullanın.</span><span class="sxs-lookup"><span data-stu-id="55ea9-153">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

> [!NOTE]
> <span data-ttu-id="55ea9-154">Varsayılan olarak, swashbuckle, tam olarak &mdash; Openapı belirtimi olarak adlandırılan belirtiminin 3,0 sürümünde Swagger JSON oluşturur ve kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="55ea9-154">By default, Swashbuckle generates and exposes Swagger JSON in version 3.0 of the specification&mdash;officially called the OpenAPI Specification.</span></span> <span data-ttu-id="55ea9-155">Geriye dönük uyumluluğu desteklemek için, bunun yerine 2,0 biçiminde JSON 'u açığa çıkarmayı tercih edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="55ea9-155">To support backwards compatibility, you can opt into exposing JSON in the 2.0 format instead.</span></span> <span data-ttu-id="55ea9-156">Bu 2,0 biçimi, Microsoft Power Apps ve şu anda Openapı sürüm 2,0 ' i destekleyen Microsoft Flow gibi Tümleştirmeler için önemlidir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-156">This 2.0 format is important for integrations such as Microsoft Power Apps and Microsoft Flow that currently support OpenAPI version 2.0.</span></span> <span data-ttu-id="55ea9-157">2,0 biçimini kabul etmek için özelliği ' de ayarlayın `SerializeAsV2` `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="55ea9-157">To opt into the 2.0 format, set the `SerializeAsV2` property in `Startup.Configure`:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a><span data-ttu-id="55ea9-158">Özelleştirme ve genişletme</span><span class="sxs-lookup"><span data-stu-id="55ea9-158">Customize and extend</span></span>

<span data-ttu-id="55ea9-159">Swagger, nesne modelini belgeleme ve Kullanıcı arabirimini temanızla eşleşecek şekilde özelleştirme seçenekleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="55ea9-159">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="55ea9-160">`Startup`Sınıfında, aşağıdaki ad alanlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="55ea9-160">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="55ea9-161">API bilgisi ve açıklaması</span><span class="sxs-lookup"><span data-stu-id="55ea9-161">API info and description</span></span>

<span data-ttu-id="55ea9-162">Yöntemine geçirilen yapılandırma eylemi `AddSwaggerGen` Yazar, lisans ve açıklama gibi bilgileri ekler:</span><span class="sxs-lookup"><span data-stu-id="55ea9-162">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

<span data-ttu-id="55ea9-163">`Startup`Sınıfında, sınıfını kullanmak için aşağıdaki ad alanını içeri aktarın `OpenApiInfo` :</span><span class="sxs-lookup"><span data-stu-id="55ea9-163">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="55ea9-164">Sınıfını kullanarak `OpenApiInfo` , Kullanıcı arabiriminde görünen bilgileri değiştirin:</span><span class="sxs-lookup"><span data-stu-id="55ea9-164">Using the `OpenApiInfo` class, modify the information displayed in the UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="55ea9-165">Swagger Kullanıcı arabirimi, sürümün bilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="55ea9-165">The Swagger UI displays the version's information:</span></span>

![Sürüm bilgileriyle Swagger Kullanıcı arabirimi: Açıklama, yazar ve daha fazla bağlantı görüntüle](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="55ea9-167">XML açıklamaları</span><span class="sxs-lookup"><span data-stu-id="55ea9-167">XML comments</span></span>

<span data-ttu-id="55ea9-168">XML açıklamaları aşağıdaki yaklaşımlar ile etkinleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="55ea9-168">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="55ea9-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55ea9-169">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="55ea9-170">**Çözüm Gezgini** projeye sağ tıklayın ve **>. csproj Project_Name <Düzenle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="55ea9-170">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="55ea9-171">Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="55ea9-171">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="55ea9-172">**Çözüm Gezgini** ' de projeye sağ tıklayın ve **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="55ea9-172">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="55ea9-173">**Build** sekmesinin **output** bölümünün altındaki **XML belge dosyası** kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="55ea9-173">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="55ea9-174">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55ea9-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="55ea9-175">*Çözüm bölmesi*, **Denetim** ' e basın ve proje adına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="55ea9-175">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="55ea9-176">**Araçlar**  >  **dosya düzenleme**sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="55ea9-176">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="55ea9-177">Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="55ea9-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="55ea9-178">**Derleme** derleyicisi > **Proje seçenekleri** iletişim kutusunu açın > **Compiler**</span><span class="sxs-lookup"><span data-stu-id="55ea9-178">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="55ea9-179">**Genel Seçenekler** bölümünün altındaki **XML oluştur belge** kutusunu işaretleyin</span><span class="sxs-lookup"><span data-stu-id="55ea9-179">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="55ea9-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="55ea9-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="55ea9-181">Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="55ea9-181">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="55ea9-182">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="55ea9-182">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="55ea9-183">Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="55ea9-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="55ea9-184">XML açıklamalarını etkinleştirmek, belgelenmemiş ortak türler ve Üyeler için hata ayıklama bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="55ea9-184">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="55ea9-185">Belgelenmemiş türler ve Üyeler uyarı iletisiyle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-185">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="55ea9-186">Örneğin, aşağıdaki ileti 1591 uyarı kodunu ihlal eder:</span><span class="sxs-lookup"><span data-stu-id="55ea9-186">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="55ea9-187">Uyarıları proje genelinde gizlemek için, proje dosyasında yoksayılacak uyarı kodlarının noktalı virgülle ayrılmış bir listesini tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="55ea9-187">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="55ea9-188">Yalnızca `$(NoWarn);` [C# varsayılan değerlerini](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) uygulamak için uyarı kodlarını eklemek.</span><span class="sxs-lookup"><span data-stu-id="55ea9-188">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="55ea9-189">Yalnızca belirli Üyeler için uyarıları gizlemek için, kodu [#pragma uyarı](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) Önişlemci yönergeleri arasına alın.</span><span class="sxs-lookup"><span data-stu-id="55ea9-189">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="55ea9-190">Bu yaklaşım, API belgeleri aracılığıyla sunulmaması gereken kod için yararlıdır. Aşağıdaki örnekte, tüm sınıf için uyarı kodu CS1591 yok sayılır `Program` .</span><span class="sxs-lookup"><span data-stu-id="55ea9-190">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="55ea9-191">Uyarı kodu zorlaması sınıf tanımının kapandığına geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-191">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="55ea9-192">Virgülle ayrılmış bir liste ile birden çok uyarı kodu belirtin.</span><span class="sxs-lookup"><span data-stu-id="55ea9-192">Specify multiple warning codes with a comma-delimited list.</span></span>

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

<span data-ttu-id="55ea9-193">Swagger 'yi yukarıdaki yönergelerle oluşturulan XML dosyasını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="55ea9-193">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="55ea9-194">Linux veya Windows dışı işletim sistemleri için dosya adları ve yolları büyük/küçük harfe duyarlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-194">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="55ea9-195">Örneğin, bir *TodoApi.XML* dosyası Windows üzerinde geçerlidir ancak CentOS değildir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-195">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

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

<span data-ttu-id="55ea9-196">Yukarıdaki kodda, [yansıma](/dotnet/csharp/programming-guide/concepts/reflection) , Web API projesi ile eşleşen bir XML dosya adı oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="55ea9-196">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="55ea9-197">[AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory*) ÖZELLIĞI, XML dosyasının yolunu oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="55ea9-197">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="55ea9-198">Bazı Swagger özellikleri (örneğin, bir XML belge dosyası kullanılmadan, giriş parametrelerinin veya HTTP yöntemlerinin ve yanıt kodlarının) bir bölümü çalışır.</span><span class="sxs-lookup"><span data-stu-id="55ea9-198">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="55ea9-199">Çoğu özellik için, yöntem özetleri ve parametrelerin ve yanıt kodlarının açıklamaları, bir XML dosyası kullanımı zorunludur.</span><span class="sxs-lookup"><span data-stu-id="55ea9-199">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="55ea9-200">Bir eyleme üç eğik çizgiyle açıklama eklediğinizde bölüm üst bilgisine açıklama eklenir ve Swagger UI geliştirilir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-200">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="55ea9-201">[\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary)Eylemin üstüne bir öğe ekleyin `Delete` :</span><span class="sxs-lookup"><span data-stu-id="55ea9-201">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="55ea9-202">Swagger Kullanıcı arabirimi, önceki kodun öğesinin iç metnini görüntüler `<summary>` :</span><span class="sxs-lookup"><span data-stu-id="55ea9-202">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![XML açıklamasını gösteren Swagger Kullanıcı arabirimi, belirli bir TodoItem siler.](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="55ea9-205">Kullanıcı arabirimi, oluşturulan JSON şeması tarafından çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="55ea9-205">The UI is driven by the generated JSON schema:</span></span>

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

<span data-ttu-id="55ea9-206">[\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) `Create` Eylem yöntemi belgelerine bir öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="55ea9-206">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="55ea9-207">Öğesinde belirtilen bilgileri tamamlar `<summary>` ve daha sağlam bir Swagger Kullanıcı arabirimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="55ea9-207">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="55ea9-208">`<remarks>`Öğe içeriği metin, JSON veya XML içerebilir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-208">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="55ea9-209">Bu ek açıklamalarla UI geliştirmelerini göz unutmayın:</span><span class="sxs-lookup"><span data-stu-id="55ea9-209">Notice the UI enhancements with these additional comments:</span></span>

![Ek açıklamaların gösterildiği Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="55ea9-211">Veri açıklamaları</span><span class="sxs-lookup"><span data-stu-id="55ea9-211">Data annotations</span></span>

<span data-ttu-id="55ea9-212">Swagger Kullanıcı Arabirimi bileşenlerini sağlamaya yardımcı olmak için [System. ComponentModel. Dataaçıklamalarda](/dotnet/api/system.componentmodel.dataannotations) ad alanında bulunan öznitelikleri olan modeli işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="55ea9-212">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="55ea9-213">`[Required]`Özniteliğini `Name` sınıfının özelliğine ekleyin `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="55ea9-213">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="55ea9-214">Bu özniteliğin varlığı, Kullanıcı arabirimi davranışını değiştirir ve temel alınan JSON şemasını değiştirir:</span><span class="sxs-lookup"><span data-stu-id="55ea9-214">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

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

<span data-ttu-id="55ea9-215">`[Produces("application/json")]`ÖZNITELIĞI API denetleyicisine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="55ea9-215">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="55ea9-216">Amaç, denetleyicinin eylemlerinin bir *uygulama/JSON*yanıt içerik türünü desteklediğini bildirsağlamaktır:</span><span class="sxs-lookup"><span data-stu-id="55ea9-216">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="55ea9-217">**Yanıt Içerik türü** açılan liste, denetleyicinin al eylemleri için varsayılan olarak bu içerik türünü seçer:</span><span class="sxs-lookup"><span data-stu-id="55ea9-217">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Varsayılan yanıt içerik türüyle Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="55ea9-219">Web API 'sindeki veri ek açıklamaların kullanımı arttıkça, UI ve API Yardım sayfaları daha açıklayıcı ve yararlı hale gelir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-219">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="55ea9-220">Yanıt türlerini açıkla</span><span class="sxs-lookup"><span data-stu-id="55ea9-220">Describe response types</span></span>

<span data-ttu-id="55ea9-221">Bir Web API 'SI kullanan geliştiriciler, &mdash; özellikle yanıt türleri ve hata kodları (Standart değilse) döndürülmesiyle ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-221">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="55ea9-222">Yanıt türleri ve hata kodları XML açıklamaları ve veri ek açıklamalarında gösterilir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-222">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="55ea9-223">`Create`Eylem başarılı olduğunda BIR HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="55ea9-223">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="55ea9-224">Postalanan istek gövdesi null olduğunda bir HTTP 400 durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="55ea9-224">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="55ea9-225">Swagger Kullanıcı arabiriminde doğru belgeler olmadan, tüketici beklenen bu sonuçlar hakkında bilgi sahibi yoktur.</span><span class="sxs-lookup"><span data-stu-id="55ea9-225">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="55ea9-226">Aşağıdaki örneğe vurgulanan satırları ekleyerek bu sorunu giderebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="55ea9-226">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="55ea9-227">Swagger Kullanıcı arabirimi artık beklenen HTTP yanıt kodlarını açıkça belgelemektedir:</span><span class="sxs-lookup"><span data-stu-id="55ea9-227">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Yanıt Iletileri ' ndeki durum kodu ve nedeni için, POST Response sınıfının Description ', yeni oluşturulan Todo öğesini döndürür ve ' 400-öğesi null ise '](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="55ea9-229">ASP.NET Core 2,2 veya üzeri sürümlerde kurallar, ile tek tek eylemleri açıkça dekorasyon alternatifi olarak kullanılabilir `[ProducesResponseType]` .</span><span class="sxs-lookup"><span data-stu-id="55ea9-229">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="55ea9-230">Daha fazla bilgi için bkz. <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="55ea9-230">For more information, see <xref:web-api/advanced/conventions>.</span></span>

<span data-ttu-id="55ea9-231">Deseni desteklemek için `[ProducesResponseType]` , [swashbuckle. Aspnetcore. açıklamalarda](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) paketi, yanıt, şema ve parametre meta verilerini etkinleştirmek ve zenginleştirmek için uzantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="55ea9-231">To support the `[ProducesResponseType]` decoration, the [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) package offers extensions to enable and enrich the response, schema, and parameter metadata.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="55ea9-232">Kullanıcı arabirimini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="55ea9-232">Customize the UI</span></span>

<span data-ttu-id="55ea9-233">Varsayılan Kullanıcı arabirimi hem işlevsel hem de edileni.</span><span class="sxs-lookup"><span data-stu-id="55ea9-233">The default UI is both functional and presentable.</span></span> <span data-ttu-id="55ea9-234">Ancak, API belge sayfaları markanızı veya temanızı temsil etmelidir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-234">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="55ea9-235">Marka, swashbuckle bileşenleri, statik dosyalara ve bu dosyaları barındırmak için klasör yapısını oluşturmaya yönelik kaynakların eklenmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="55ea9-235">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="55ea9-236">.NET Framework veya .NET Core 1. x 'i hedefliyorsanız, [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet paketini projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="55ea9-236">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="55ea9-237">Önceki NuGet paketi, .NET Core 2. x hedefleniyorsa ve [metapackage](xref:fundamentals/metapackage)kullanılarak zaten yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="55ea9-237">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="55ea9-238">Statik dosya ara yazılımını etkinleştir:</span><span class="sxs-lookup"><span data-stu-id="55ea9-238">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="55ea9-239">Ek CSS stil sayfaları eklemek için, bunları projenin *Wwwroot* klasörüne ekleyin ve ara yazılım seçeneklerinde göreli yolu belirtin:</span><span class="sxs-lookup"><span data-stu-id="55ea9-239">To inject additional CSS stylesheets, add them to the project's *wwwroot* folder and specify the relative path in the middleware options:</span></span>

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```
