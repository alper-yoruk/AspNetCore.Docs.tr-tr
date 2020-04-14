---
title: ASP.NET Core'da jilet dosya derlemesi
author: rick-anderson
description: Razor dosyalarının derlemesinin ASP.NET Core uygulamasında nasıl oluştuğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277281"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="7f928-103">ASP.NET Core'da jilet dosya derlemesi</span><span class="sxs-lookup"><span data-stu-id="7f928-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="7f928-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7f928-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="7f928-105">*.cshtml* uzantılı razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="7f928-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="7f928-106">Runtime derlemesi, projenizi yapılandırarak isteğe bağlı olarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="7f928-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="7f928-107">Jilet derlemesi</span><span class="sxs-lookup"><span data-stu-id="7f928-107">Razor compilation</span></span>

<span data-ttu-id="7f928-108">Razor dosyalarının oluşturma zamanı ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7f928-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="7f928-109">Etkinleştirildiğinde, runtime derlemesi yapı zamanı derlemesini tamamlayarak Razor dosyalarının düzenlenirse güncelleştirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f928-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="7f928-110">Proje oluşturmada çalışma zamanı derlemesini etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="7f928-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="7f928-111">Razor Pages ve MVC proje şablonları, proje oluşturulduğunda çalışma zamanı derlemesini etkinleştirme seçeneğini içerir.</span><span class="sxs-lookup"><span data-stu-id="7f928-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="7f928-112">Bu seçenek Core 3.1 ve sonraki ASP.NET desteklenir.</span><span class="sxs-lookup"><span data-stu-id="7f928-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f928-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f928-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f928-114">Yeni **bir ASP.NET Core web uygulaması** oluştur iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="7f928-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="7f928-115">**Web Uygulaması** veya Web **Uygulaması (Model-View-Controller)** proje şablonu seçin.</span><span class="sxs-lookup"><span data-stu-id="7f928-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="7f928-116">Etkinleştir **Jilet çalışma zamanı derleme** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="7f928-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7f928-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7f928-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7f928-118">`-rrc` Veya `--razor-runtime-compilation` şablon seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7f928-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="7f928-119">Örneğin, aşağıdaki komut, çalışma zamanı derlemesi etkin leştirilmiş yeni bir Razor Pages projesi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7f928-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="7f928-120">Varolan bir projede çalışma zamanı derlemesini etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="7f928-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="7f928-121">Varolan bir projedeki tüm ortamlar için çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="7f928-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="7f928-122">[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="7f928-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="7f928-123">Bir çağrı içerecek `Startup.ConfigureServices` şekilde projenin yöntemini <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="7f928-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="7f928-124">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="7f928-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="7f928-125">Varolan bir projede koşullu olarak çalışma zamanı derlemesini etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="7f928-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="7f928-126">Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilecek şekilde etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="7f928-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="7f928-127">Bu şekilde koşullu olarak etkinleştirmek, yayımlanmış çıktının aşağıdakileri sağlamasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="7f928-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="7f928-128">Derlenmiş görünümleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="7f928-128">Uses compiled views.</span></span>
* <span data-ttu-id="7f928-129">Üretimde dosya izleyicilerine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="7f928-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="7f928-130">Yalnızca Geliştirme ortamında çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="7f928-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="7f928-131">[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="7f928-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="7f928-132">`environmentVariables` *launchSettings.json'daki başlatma*profili bölümünü değiştirin:</span><span class="sxs-lookup"><span data-stu-id="7f928-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="7f928-133">Doğrula' `ASPNETCORE_ENVIRONMENT` `"Development"`ya ' olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7f928-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="7f928-134">`"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`Ayarlayın. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="7f928-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="7f928-135">Aşağıdaki örnekte, çalışma zamanı derlemesi geliştirme ortamında `IIS Express` `RazorPagesApp` ve başlatma profilleri için etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="7f928-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="7f928-136">Projenin `Startup` sınıfında kod değişikliği gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7f928-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="7f928-137">Çalışma zamanında, ASP.NET Core' da derleme düzeyinde bir `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` [HostingStartup özniteliği](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) arar.</span><span class="sxs-lookup"><span data-stu-id="7f928-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="7f928-138">Öznitelik, `HostingStartup` yürütülecek uygulama başlangıç kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="7f928-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="7f928-139">Bu başlangıç kodu çalışma zamanı derlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f928-139">That startup code enables runtime compilation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f928-140">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7f928-140">Additional resources</span></span>

* <span data-ttu-id="7f928-141">[RazorCompileOnBuild ve RazorCompileOnPublish](xref:razor-pages/sdk#properties) özellikleri.</span><span class="sxs-lookup"><span data-stu-id="7f928-141">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="7f928-142">Projeler arasında çalışma zamanı derleme çalışması yaptığını gösteren bir örnek için [GitHub'daki çalışma zamanı derleme örneğine](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) bakın.</span><span class="sxs-lookup"><span data-stu-id="7f928-142">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="7f928-143">*.cshtml* uzantılı razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="7f928-143">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="7f928-144">Çalışma süresi derlemesi, uygulamanızı yapılandırarak isteğe bağlı olarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="7f928-144">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="7f928-145">Jilet derlemesi</span><span class="sxs-lookup"><span data-stu-id="7f928-145">Razor compilation</span></span>

<span data-ttu-id="7f928-146">Razor dosyalarının oluşturma zamanı ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7f928-146">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="7f928-147">Etkinleştirildiğinde, runtime derlemesi yapı zamanı derlemesini tamamlayarak Razor dosyalarının düzenlenirse güncelleştirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f928-147">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="7f928-148">Çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="7f928-148">Runtime compilation</span></span>

<span data-ttu-id="7f928-149">Tüm ortamlar ve yapılandırma modları için çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="7f928-149">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="7f928-150">[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="7f928-150">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="7f928-151">Bir çağrı içerecek `Startup.ConfigureServices` şekilde projenin yöntemini <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="7f928-151">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="7f928-152">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="7f928-152">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="7f928-153">Koşullu etkinleştirme çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="7f928-153">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="7f928-154">Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilecek şekilde etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="7f928-154">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="7f928-155">Bu şekilde koşullu olarak etkinleştirmek, yayımlanmış çıktının aşağıdakileri sağlamasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="7f928-155">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="7f928-156">Derlenmiş görünümleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="7f928-156">Uses compiled views.</span></span>
* <span data-ttu-id="7f928-157">Boyutu daha küçüktür.</span><span class="sxs-lookup"><span data-stu-id="7f928-157">Is smaller in size.</span></span>
* <span data-ttu-id="7f928-158">Üretimde dosya izleyicilerine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="7f928-158">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="7f928-159">Ortam ve yapılandırma moduna göre çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="7f928-159">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="7f928-160">Etkin `Configuration` değere dayalı [olarak Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) paketine koşullu olarak başvurun:</span><span class="sxs-lookup"><span data-stu-id="7f928-160">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="7f928-161">Bir çağrı içerecek `Startup.ConfigureServices` şekilde projenin yöntemini `AddRazorRuntimeCompilation`güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="7f928-161">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="7f928-162">Koşullu `AddRazorRuntimeCompilation` olarak, `ASPNETCORE_ENVIRONMENT` yalnızca değişken ayarlandığında Hata Ayıklama `Development`modunda çalışır:</span><span class="sxs-lookup"><span data-stu-id="7f928-162">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="7f928-163">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7f928-163">Additional resources</span></span>

* <span data-ttu-id="7f928-164">[RazorCompileOnBuild ve RazorCompileOnPublish](xref:razor-pages/sdk#properties) özellikleri.</span><span class="sxs-lookup"><span data-stu-id="7f928-164">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="7f928-165">Projeler arasında çalışma zamanı derleme çalışması yaptığını gösteren bir örnek için [GitHub'daki çalışma zamanı derleme örneğine](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) bakın.</span><span class="sxs-lookup"><span data-stu-id="7f928-165">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7f928-166">İlgili Razor Page veya MVC görünümü çağrıldığı çalışma zamanında bir Razor dosyası derlenir.</span><span class="sxs-lookup"><span data-stu-id="7f928-166">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="7f928-167">Razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="7f928-167">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="7f928-168">Jilet derlemesi</span><span class="sxs-lookup"><span data-stu-id="7f928-168">Razor compilation</span></span>

<span data-ttu-id="7f928-169">Razor dosyalarının oluşturma ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7f928-169">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="7f928-170">Razor dosyalarının güncelleştirildikten sonra düzenlenmesi yapı zamanında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="7f928-170">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="7f928-171">Varsayılan olarak, yalnızca derlenmiş *Views.dll* ve razor dosyalarını derlemek için gereken *.cshtml* dosyaları veya referans derlemeleri uygulamanızla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="7f928-171">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7f928-172">Precompilation aracı amortismana kaldırılmıştır ve Core 3.0 ASP.NET kaldırılacaktır.</span><span class="sxs-lookup"><span data-stu-id="7f928-172">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="7f928-173">[Razor Sdk'ya](xref:razor-pages/sdk)göç etmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="7f928-173">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="7f928-174">Razor SDK, yalnızca proje dosyasında precompilation'a özgü özellikler ayarlanmadığında etkilidir.</span><span class="sxs-lookup"><span data-stu-id="7f928-174">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="7f928-175">Örneğin, *.csproj* dosyasının `MvcRazorCompileOnPublish` özelliğini `true` Razor SDK'yı devre dışı kılabilir şekilde ayarlamak.</span><span class="sxs-lookup"><span data-stu-id="7f928-175">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="7f928-176">Çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="7f928-176">Runtime compilation</span></span>

<span data-ttu-id="7f928-177">Oluşturma zamanı derlemesi, Razor dosyalarının çalışma zamanı derlemesi ile desteklenir.</span><span class="sxs-lookup"><span data-stu-id="7f928-177">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="7f928-178">ASP.NET Core MVC, *.cshtml* dosyasının içeriği değiştiğinde Razor dosyalarını yeniden derler.</span><span class="sxs-lookup"><span data-stu-id="7f928-178">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f928-179">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7f928-179">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
