---
title: RazorASP.NET Core 'de dosya derleme
author: rick-anderson
description: ASP.NET Core uygulamasında dosya derlemesinin nasıl Razor oluştuğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: fc7924f8f8b321ae017b7acd729fe11c4e0e3c7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021087"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a><span data-ttu-id="f2488-103">RazorASP.NET Core 'de dosya derleme</span><span class="sxs-lookup"><span data-stu-id="f2488-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="f2488-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f2488-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="f2488-105">Razor*. cshtml* uzantılı dosyalar, [ Razor SDK](xref:razor-pages/sdk)kullanılarak hem derleme hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="f2488-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="f2488-106">Çalışma zamanı derlemesi, projeniz yapılandırılarak isteğe bağlı olarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f2488-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="f2488-107">Razorderleme</span><span class="sxs-lookup"><span data-stu-id="f2488-107">Razor compilation</span></span>

<span data-ttu-id="f2488-108">Dosyaların derleme zamanı ve yayımlama zamanı derlemesi, Razor SDK tarafından varsayılan olarak etkindir Razor .</span><span class="sxs-lookup"><span data-stu-id="f2488-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="f2488-109">Etkinleştirildiğinde, çalışma zamanı derlemesi derleme zamanı derlemesini tamamlar ve bu Razor dosyalar düzenlendiklerinde güncelleştirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f2488-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="f2488-110">Proje oluşturulurken çalışma zamanı derlemesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="f2488-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="f2488-111">RazorSayfalar ve MVC proje şablonları, proje oluşturulduğunda çalışma zamanı derlemesini etkinleştirme seçeneğini içerir.</span><span class="sxs-lookup"><span data-stu-id="f2488-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="f2488-112">Bu seçenek ASP.NET Core 3,1 ve üzeri sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f2488-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2488-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2488-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2488-114">**Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="f2488-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="f2488-115">**Web uygulaması** veya **Web uygulaması (Model-View-Controller)** proje şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f2488-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="f2488-116">\*\* Razor Çalışma zamanı derlemesini etkinleştir\*\* onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f2488-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2488-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f2488-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f2488-118">`-rrc`Veya `--razor-runtime-compilation` şablon seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f2488-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="f2488-119">Örneğin, aşağıdaki komut Razor çalışma zamanı derlemesi etkin olan yeni bir sayfa projesi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f2488-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="f2488-120">Mevcut bir projede çalışma zamanı derlemesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="f2488-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="f2488-121">Mevcut bir projedeki tüm ortamlarda çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="f2488-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="f2488-122">[Microsoft. AspNetCore. Mvc 'yi yükler. Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="f2488-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="f2488-123">`Startup.ConfigureServices`İçin bir çağrı içerecek şekilde projenin metodunu güncelleştirin <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="f2488-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="f2488-124">Örnek:</span><span class="sxs-lookup"><span data-stu-id="f2488-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="f2488-125">Mevcut bir projede çalışma zamanı derlemesini koşullu olarak etkinleştir</span><span class="sxs-lookup"><span data-stu-id="f2488-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="f2488-126">Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilir olacak şekilde etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f2488-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="f2488-127">Bu şekilde koşullu etkinleştirme, yayımlanan çıktının olmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="f2488-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="f2488-128">Derlenmiş görünümleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="f2488-128">Uses compiled views.</span></span>
* <span data-ttu-id="f2488-129">, İzleyicileri dosyasını üretimde etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="f2488-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="f2488-130">Çalışma zamanı derlemesini yalnızca geliştirme ortamında etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="f2488-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="f2488-131">[Microsoft. AspNetCore. Mvc 'yi yükler. Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="f2488-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="f2488-132">`environmentVariables` *ÜzerindelaunchSettings.js*başlatma profili bölümünü değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f2488-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="f2488-133">Verify `ASPNETCORE_ENVIRONMENT` olarak ayarlanır `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="f2488-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="f2488-134">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`Olarak ayarlayın `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` .</span><span class="sxs-lookup"><span data-stu-id="f2488-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="f2488-135">Aşağıdaki örnekte, `IIS Express` ve başlatma profillerinin geliştirme ortamında çalışma zamanı derlemesi etkinleştirilmiştir `RazorPagesApp` :</span><span class="sxs-lookup"><span data-stu-id="f2488-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="f2488-136">Projenin sınıfında kod değişikliği yapılması gerekmez `Startup` .</span><span class="sxs-lookup"><span data-stu-id="f2488-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="f2488-137">Çalışma zamanında, içinde [derleme düzeyi HostingStartup özniteliğini](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) arar ASP.NET Core `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="f2488-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="f2488-138">`HostingStartup`Öznitelik, çalıştırılacak uygulama başlangıç kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="f2488-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="f2488-139">Bu başlangıç kodu, çalışma zamanı derlemesini sunar.</span><span class="sxs-lookup"><span data-stu-id="f2488-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a><span data-ttu-id="f2488-140">Sınıf kitaplığı için çalışma zamanı derlemesini Etkinleştir Razor</span><span class="sxs-lookup"><span data-stu-id="f2488-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="f2488-141">Bir Razor sayfa projesinin *myclasslib*adlı bir [ Razor sınıf kitaplığına (RCL)](xref:razor-pages/ui-class) başvurduğu bir senaryo düşünün.</span><span class="sxs-lookup"><span data-stu-id="f2488-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="f2488-142">RCL, tüm takımınızın MVC ve sayfa projelerinin tükettiği bir *_Layout. cshtml* dosyası içerir Razor .</span><span class="sxs-lookup"><span data-stu-id="f2488-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="f2488-143">Bu RCL 'de *_Layout. cshtml* dosyası için çalışma zamanı derlemesini etkinleştirmek istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="f2488-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="f2488-144">Sayfalar projesinde aşağıdaki değişiklikleri yapın Razor :</span><span class="sxs-lookup"><span data-stu-id="f2488-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="f2488-145">[Mevcut bir projede çalışma zamanı derlemesini koşullu olarak etkinleştirme](#conditionally-enable-runtime-compilation-in-an-existing-project)yönergelerini kullanarak çalışma zamanı derlemesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f2488-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="f2488-146">Çalışma zamanı derleme seçeneklerini şu şekilde yapılandırın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f2488-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="f2488-147">Yukarıdaki kodda, *Myclasslib* RCL için mutlak bir yol oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f2488-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="f2488-148">Bu mutlak yoldaki dizinleri ve dosyaları bulmak için [Physicalfileprovider API 'si](xref:fundamentals/file-providers#physicalfileprovider) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2488-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="f2488-149">Son olarak, `PhysicalFileProvider` örnek, RCL 'nin *. cshtml* dosyalarına erişime izin veren bir dosya sağlayıcıları koleksiyonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="f2488-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2488-150">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f2488-150">Additional resources</span></span>

* <span data-ttu-id="f2488-151">[ Razor Compileonbuild ve Razor compileonpublish](xref:razor-pages/sdk#properties) özellikleri.</span><span class="sxs-lookup"><span data-stu-id="f2488-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="f2488-152">Razor*. cshtml* uzantılı dosyalar, [ Razor SDK](xref:razor-pages/sdk)kullanılarak hem derleme hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="f2488-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="f2488-153">Çalışma zamanı derlemesi, uygulamanız yapılandırılarak isteğe bağlı olarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f2488-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="f2488-154">Razorderleme</span><span class="sxs-lookup"><span data-stu-id="f2488-154">Razor compilation</span></span>

<span data-ttu-id="f2488-155">Dosyaların derleme zamanı ve yayımlama zamanı derlemesi, Razor SDK tarafından varsayılan olarak etkindir Razor .</span><span class="sxs-lookup"><span data-stu-id="f2488-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="f2488-156">Etkinleştirildiğinde, çalışma zamanı derlemesi derleme zamanı derlemesini tamamlar ve bu Razor dosyalar düzenlendiklerinde güncelleştirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f2488-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="f2488-157">Çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="f2488-157">Runtime compilation</span></span>

<span data-ttu-id="f2488-158">Tüm ortamlar ve yapılandırma modları için çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="f2488-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="f2488-159">[Microsoft. AspNetCore. Mvc 'yi yükler. Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="f2488-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="f2488-160">`Startup.ConfigureServices`İçin bir çağrı içerecek şekilde projenin metodunu güncelleştirin <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="f2488-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="f2488-161">Örnek:</span><span class="sxs-lookup"><span data-stu-id="f2488-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="f2488-162">Çalışma zamanı derlemesini koşullu olarak etkinleştir</span><span class="sxs-lookup"><span data-stu-id="f2488-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="f2488-163">Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilir olacak şekilde etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f2488-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="f2488-164">Bu şekilde koşullu etkinleştirme, yayımlanan çıktının olmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="f2488-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="f2488-165">Derlenmiş görünümleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="f2488-165">Uses compiled views.</span></span>
* <span data-ttu-id="f2488-166">Boyutu küçüktür.</span><span class="sxs-lookup"><span data-stu-id="f2488-166">Is smaller in size.</span></span>
* <span data-ttu-id="f2488-167">, İzleyicileri dosyasını üretimde etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="f2488-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="f2488-168">Çalışma zamanı derlemesini ortam ve yapılandırma moduna göre etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="f2488-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="f2488-169">Koşullu olarak [Microsoft. AspNetCore. Mvc öğesine başvurun. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Etkin değere göre RuntimeCompilation paketi `Configuration` :</span><span class="sxs-lookup"><span data-stu-id="f2488-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="f2488-170">`Startup.ConfigureServices`İçin bir çağrı içerecek şekilde projenin metodunu güncelleştirin `AddRazorRuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="f2488-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="f2488-171">Koşullu olarak Execute `AddRazorRuntimeCompilation` yalnızca `ASPNETCORE_ENVIRONMENT` değişken olarak ayarlandığında hata ayıklama modunda çalışır `Development` :</span><span class="sxs-lookup"><span data-stu-id="f2488-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="f2488-172">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f2488-172">Additional resources</span></span>

* <span data-ttu-id="f2488-173">[ Razor Compileonbuild ve Razor compileonpublish](xref:razor-pages/sdk#properties) özellikleri.</span><span class="sxs-lookup"><span data-stu-id="f2488-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="f2488-174">Çalışma zamanı derleme işini projeler arasında yapmayı gösteren bir örnek için bkz. [GitHub üzerinde çalışma zamanı derleme örneği](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) .</span><span class="sxs-lookup"><span data-stu-id="f2488-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f2488-175">RazorÇalışma zamanında, ilişkili Razor sayfa veya MVC görünümü çağrıldığında bir dosya derlenir.</span><span class="sxs-lookup"><span data-stu-id="f2488-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="f2488-176">Razordosyalar, [ Razor SDK](xref:razor-pages/sdk)kullanılarak hem derlemede hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="f2488-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="f2488-177">Razorderleme</span><span class="sxs-lookup"><span data-stu-id="f2488-177">Razor compilation</span></span>

<span data-ttu-id="f2488-178">Dosyaların derleme ve yayımlama zamanı derlemesi, Razor SDK tarafından varsayılan olarak etkindir Razor .</span><span class="sxs-lookup"><span data-stu-id="f2488-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="f2488-179">RazorDosyalar güncelleştirildikten sonra düzenlendikten sonra derleme sırasında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f2488-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="f2488-180">Varsayılan olarak, yalnızca derlenmiş *Views.dll* ve No *. cshtml* dosyası ya da dosyaları derlemek için gerekli derlemeler Razor uygulamanız ile dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="f2488-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f2488-181">Ön derleme aracı kullanım dışı bırakılmıştır ve ASP.NET Core 3,0 ' de kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="f2488-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="f2488-182">[ Razor SDK](xref:razor-pages/sdk)'ya geçiş yapmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="f2488-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="f2488-183">RazorSDK yalnızca proje dosyasında ön derleme özgü hiçbir özellik ayarlanmamışsa etkilidir.</span><span class="sxs-lookup"><span data-stu-id="f2488-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="f2488-184">Örneğin, *. csproj* dosyasının `MvcRazorCompileOnPublish` özelliğini, `true` SDK 'yı devre dışı bırakmak için ayarlama Razor .</span><span class="sxs-lookup"><span data-stu-id="f2488-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="f2488-185">Çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="f2488-185">Runtime compilation</span></span>

<span data-ttu-id="f2488-186">Derleme zamanı derlemesi, dosyaların çalışma zamanı derlemesi tarafından takıma alınmıştır Razor .</span><span class="sxs-lookup"><span data-stu-id="f2488-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="f2488-187">ASP.NET Core MVC Razor , bir *. cshtml* dosyasının içeriği değiştiğinde dosyaları yeniden derleyerek.</span><span class="sxs-lookup"><span data-stu-id="f2488-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2488-188">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f2488-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
