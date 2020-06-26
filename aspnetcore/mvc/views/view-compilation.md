---
title: RazorASP.NET Core 'de dosya derleme
author: rick-anderson
description: ASP.NET Core uygulamasında dosya derlemesinin nasıl Razor oluştuğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-compilation
ms.openlocfilehash: 71487ff2d5d7d7cf96835778f386e5f30fa32254
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405451"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Razor<span data-ttu-id="2422c-103">ASP.NET Core 'de dosya derleme</span><span class="sxs-lookup"><span data-stu-id="2422c-103"> file compilation in ASP.NET Core</span></span>

<span data-ttu-id="2422c-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2422c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

Razor<span data-ttu-id="2422c-105">*. cshtml* uzantılı dosyalar, [ Razor SDK](xref:razor-pages/sdk)kullanılarak hem derleme hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="2422c-105"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="2422c-106">Çalışma zamanı derlemesi, projeniz yapılandırılarak isteğe bağlı olarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2422c-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="2422c-107">derleme</span><span class="sxs-lookup"><span data-stu-id="2422c-107"> compilation</span></span>

<span data-ttu-id="2422c-108">Dosyaların derleme zamanı ve yayımlama zamanı derlemesi, Razor SDK tarafından varsayılan olarak etkindir Razor .</span><span class="sxs-lookup"><span data-stu-id="2422c-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="2422c-109">Etkinleştirildiğinde, çalışma zamanı derlemesi derleme zamanı derlemesini tamamlar ve bu Razor dosyalar düzenlendiklerinde güncelleştirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2422c-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="2422c-110">Proje oluşturulurken çalışma zamanı derlemesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="2422c-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="2422c-111">RazorSayfalar ve MVC proje şablonları, proje oluşturulduğunda çalışma zamanı derlemesini etkinleştirme seçeneğini içerir.</span><span class="sxs-lookup"><span data-stu-id="2422c-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="2422c-112">Bu seçenek ASP.NET Core 3,1 ve üzeri sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2422c-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2422c-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2422c-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2422c-114">**Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="2422c-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="2422c-115">**Web uygulaması** veya **Web uygulaması (Model-View-Controller)** proje şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="2422c-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="2422c-116">\*\* Razor Çalışma zamanı derlemesini etkinleştir\*\* onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="2422c-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2422c-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2422c-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2422c-118">`-rrc`Veya `--razor-runtime-compilation` şablon seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="2422c-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="2422c-119">Örneğin, aşağıdaki komut Razor çalışma zamanı derlemesi etkin olan yeni bir sayfa projesi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2422c-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="2422c-120">Mevcut bir projede çalışma zamanı derlemesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="2422c-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="2422c-121">Mevcut bir projedeki tüm ortamlarda çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="2422c-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="2422c-122">[Microsoft. AspNetCore. Mvc 'yi yükler. Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="2422c-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="2422c-123">`Startup.ConfigureServices`İçin bir çağrı içerecek şekilde projenin metodunu güncelleştirin <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="2422c-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="2422c-124">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2422c-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="2422c-125">Mevcut bir projede çalışma zamanı derlemesini koşullu olarak etkinleştir</span><span class="sxs-lookup"><span data-stu-id="2422c-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="2422c-126">Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilir olacak şekilde etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2422c-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="2422c-127">Bu şekilde koşullu etkinleştirme, yayımlanan çıktının olmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="2422c-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="2422c-128">Derlenmiş görünümleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="2422c-128">Uses compiled views.</span></span>
* <span data-ttu-id="2422c-129">, İzleyicileri dosyasını üretimde etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="2422c-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="2422c-130">Çalışma zamanı derlemesini yalnızca geliştirme ortamında etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="2422c-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="2422c-131">[Microsoft. AspNetCore. Mvc 'yi yükler. Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="2422c-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="2422c-132">`environmentVariables` *ÜzerindelaunchSettings.js*başlatma profili bölümünü değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2422c-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="2422c-133">Verify `ASPNETCORE_ENVIRONMENT` olarak ayarlanır `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="2422c-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="2422c-134">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`Olarak ayarlayın `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` .</span><span class="sxs-lookup"><span data-stu-id="2422c-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="2422c-135">Aşağıdaki örnekte, `IIS Express` ve başlatma profillerinin geliştirme ortamında çalışma zamanı derlemesi etkinleştirilmiştir `RazorPagesApp` :</span><span class="sxs-lookup"><span data-stu-id="2422c-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="2422c-136">Projenin sınıfında kod değişikliği yapılması gerekmez `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2422c-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="2422c-137">Çalışma zamanında, içinde [derleme düzeyi HostingStartup özniteliğini](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) arar ASP.NET Core `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="2422c-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="2422c-138">`HostingStartup`Öznitelik, çalıştırılacak uygulama başlangıç kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="2422c-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="2422c-139">Bu başlangıç kodu, çalışma zamanı derlemesini sunar.</span><span class="sxs-lookup"><span data-stu-id="2422c-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="2422c-140">Sınıf kitaplığı için çalışma zamanı derlemesini Etkinleştir Razor</span><span class="sxs-lookup"><span data-stu-id="2422c-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="2422c-141">Bir Razor sayfa projesinin *myclasslib*adlı bir [ Razor sınıf kitaplığına (RCL)](xref:razor-pages/ui-class) başvurduğu bir senaryo düşünün.</span><span class="sxs-lookup"><span data-stu-id="2422c-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="2422c-142">RCL, tüm takımınızın MVC ve sayfa projelerinin tükettiği bir *_Layout. cshtml* dosyası içerir Razor .</span><span class="sxs-lookup"><span data-stu-id="2422c-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="2422c-143">Bu RCL 'de *_Layout. cshtml* dosyası için çalışma zamanı derlemesini etkinleştirmek istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="2422c-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="2422c-144">Sayfalar projesinde aşağıdaki değişiklikleri yapın Razor :</span><span class="sxs-lookup"><span data-stu-id="2422c-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="2422c-145">[Mevcut bir projede çalışma zamanı derlemesini koşullu olarak etkinleştirme](#conditionally-enable-runtime-compilation-in-an-existing-project)yönergelerini kullanarak çalışma zamanı derlemesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="2422c-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="2422c-146">Çalışma zamanı derleme seçeneklerini şu şekilde yapılandırın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2422c-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="2422c-147">Yukarıdaki kodda, *Myclasslib* RCL için mutlak bir yol oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2422c-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="2422c-148">Bu mutlak yoldaki dizinleri ve dosyaları bulmak için [Physicalfileprovider API 'si](xref:fundamentals/file-providers#physicalfileprovider) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2422c-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="2422c-149">Son olarak, `PhysicalFileProvider` örnek, RCL 'nin *. cshtml* dosyalarına erişime izin veren bir dosya sağlayıcıları koleksiyonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="2422c-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2422c-150">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2422c-150">Additional resources</span></span>

* <span data-ttu-id="2422c-151">[RazorCompileOnBuild ve RazorCompileOnPublish](xref:razor-pages/sdk#properties) özellikleri.</span><span class="sxs-lookup"><span data-stu-id="2422c-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor<span data-ttu-id="2422c-152">*. cshtml* uzantılı dosyalar, [ Razor SDK](xref:razor-pages/sdk)kullanılarak hem derleme hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="2422c-152"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="2422c-153">Çalışma zamanı derlemesi, uygulamanız yapılandırılarak isteğe bağlı olarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2422c-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="2422c-154">derleme</span><span class="sxs-lookup"><span data-stu-id="2422c-154"> compilation</span></span>

<span data-ttu-id="2422c-155">Dosyaların derleme zamanı ve yayımlama zamanı derlemesi, Razor SDK tarafından varsayılan olarak etkindir Razor .</span><span class="sxs-lookup"><span data-stu-id="2422c-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="2422c-156">Etkinleştirildiğinde, çalışma zamanı derlemesi derleme zamanı derlemesini tamamlar ve bu Razor dosyalar düzenlendiklerinde güncelleştirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2422c-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="2422c-157">Çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="2422c-157">Runtime compilation</span></span>

<span data-ttu-id="2422c-158">Tüm ortamlar ve yapılandırma modları için çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="2422c-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="2422c-159">[Microsoft. AspNetCore. Mvc 'yi yükler. Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="2422c-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="2422c-160">`Startup.ConfigureServices`İçin bir çağrı içerecek şekilde projenin metodunu güncelleştirin <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="2422c-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="2422c-161">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2422c-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="2422c-162">Çalışma zamanı derlemesini koşullu olarak etkinleştir</span><span class="sxs-lookup"><span data-stu-id="2422c-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="2422c-163">Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilir olacak şekilde etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2422c-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="2422c-164">Bu şekilde koşullu etkinleştirme, yayımlanan çıktının olmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="2422c-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="2422c-165">Derlenmiş görünümleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="2422c-165">Uses compiled views.</span></span>
* <span data-ttu-id="2422c-166">Boyutu küçüktür.</span><span class="sxs-lookup"><span data-stu-id="2422c-166">Is smaller in size.</span></span>
* <span data-ttu-id="2422c-167">, İzleyicileri dosyasını üretimde etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="2422c-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="2422c-168">Çalışma zamanı derlemesini ortam ve yapılandırma moduna göre etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="2422c-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="2422c-169">Koşullu olarak [Microsoft. AspNetCore. Mvc öğesine başvurun. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Etkin değere göre RuntimeCompilation paketi `Configuration` :</span><span class="sxs-lookup"><span data-stu-id="2422c-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="2422c-170">`Startup.ConfigureServices`İçin bir çağrı içerecek şekilde projenin metodunu güncelleştirin `AddRazorRuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="2422c-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="2422c-171">Koşullu olarak Execute `AddRazorRuntimeCompilation` yalnızca `ASPNETCORE_ENVIRONMENT` değişken olarak ayarlandığında hata ayıklama modunda çalışır `Development` :</span><span class="sxs-lookup"><span data-stu-id="2422c-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="2422c-172">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2422c-172">Additional resources</span></span>

* <span data-ttu-id="2422c-173">[RazorCompileOnBuild ve RazorCompileOnPublish](xref:razor-pages/sdk#properties) özellikleri.</span><span class="sxs-lookup"><span data-stu-id="2422c-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="2422c-174">Çalışma zamanı derleme işini projeler arasında yapmayı gösteren bir örnek için bkz. [GitHub üzerinde çalışma zamanı derleme örneği](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) .</span><span class="sxs-lookup"><span data-stu-id="2422c-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2422c-175">RazorÇalışma zamanında, ilişkili Razor sayfa veya MVC görünümü çağrıldığında bir dosya derlenir.</span><span class="sxs-lookup"><span data-stu-id="2422c-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> Razor<span data-ttu-id="2422c-176">dosyalar, [ Razor SDK](xref:razor-pages/sdk)kullanılarak hem derlemede hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="2422c-176"> files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="2422c-177">derleme</span><span class="sxs-lookup"><span data-stu-id="2422c-177"> compilation</span></span>

<span data-ttu-id="2422c-178">Dosyaların derleme ve yayımlama zamanı derlemesi, Razor SDK tarafından varsayılan olarak etkindir Razor .</span><span class="sxs-lookup"><span data-stu-id="2422c-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="2422c-179">RazorDosyalar güncelleştirildikten sonra düzenlendikten sonra derleme sırasında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2422c-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="2422c-180">Varsayılan olarak, yalnızca derlenmiş *Views.dll* ve No *. cshtml* dosyası ya da dosyaları derlemek için gerekli derlemeler Razor uygulamanız ile dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="2422c-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2422c-181">Ön derleme aracı kullanım dışı bırakılmıştır ve ASP.NET Core 3,0 ' de kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="2422c-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="2422c-182">[ Razor SDK](xref:razor-pages/sdk)'ya geçiş yapmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="2422c-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="2422c-183">RazorSDK yalnızca proje dosyasında ön derleme özgü hiçbir özellik ayarlanmamışsa etkilidir.</span><span class="sxs-lookup"><span data-stu-id="2422c-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="2422c-184">Örneğin, *. csproj* dosyasının `MvcRazorCompileOnPublish` özelliğini, `true` SDK 'yı devre dışı bırakmak için ayarlama Razor .</span><span class="sxs-lookup"><span data-stu-id="2422c-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="2422c-185">Çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="2422c-185">Runtime compilation</span></span>

<span data-ttu-id="2422c-186">Derleme zamanı derlemesi, dosyaların çalışma zamanı derlemesi tarafından takıma alınmıştır Razor .</span><span class="sxs-lookup"><span data-stu-id="2422c-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="2422c-187">ASP.NET Core MVC Razor , bir *. cshtml* dosyasının içeriği değiştiğinde dosyaları yeniden derleyerek.</span><span class="sxs-lookup"><span data-stu-id="2422c-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2422c-188">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2422c-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
