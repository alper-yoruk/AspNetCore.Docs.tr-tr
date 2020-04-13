---
title: ASP.NET Core'da jilet dosya derlemesi
author: rick-anderson
description: Razor dosyalarının derlemesinin ASP.NET Core uygulamasında nasıl oluştuğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223965"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="9ee30-103">ASP.NET Core'da jilet dosya derlemesi</span><span class="sxs-lookup"><span data-stu-id="9ee30-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="9ee30-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9ee30-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9ee30-105">*.cshtml* uzantılı razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="9ee30-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="9ee30-106">Çalışma süresi derlemesi, uygulamanızı yapılandırarak isteğe bağlı olarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="9ee30-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="9ee30-107">Jilet derlemesi</span><span class="sxs-lookup"><span data-stu-id="9ee30-107">Razor compilation</span></span>

<span data-ttu-id="9ee30-108">Razor dosyalarının oluşturma ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9ee30-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="9ee30-109">Etkinleştirildiğinde, runtime derlemesi yapı zamanı derlemesini tamamlayarak Razor dosyalarının düzenlenirse güncelleştirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9ee30-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="9ee30-110">Çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="9ee30-110">Runtime compilation</span></span>

<span data-ttu-id="9ee30-111">Tüm ortamlar ve yapılandırma modları için çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="9ee30-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="9ee30-112">[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="9ee30-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="9ee30-113">Bir çağrı içerecek `Startup.ConfigureServices` şekilde projenin yöntemini <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="9ee30-113">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="9ee30-114">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9ee30-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="9ee30-115">Koşullu etkinleştirme çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="9ee30-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="9ee30-116">Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilecek şekilde etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="9ee30-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="9ee30-117">Bu şekilde koşullu olarak etkinleştirmek, yayımlanmış çıktının aşağıdakileri sağlamasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="9ee30-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="9ee30-118">Derlenmiş görünümleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="9ee30-118">Uses compiled views.</span></span>
* <span data-ttu-id="9ee30-119">Boyutu daha küçüktür.</span><span class="sxs-lookup"><span data-stu-id="9ee30-119">Is smaller in size.</span></span>
* <span data-ttu-id="9ee30-120">Üretimde dosya izleyicilerine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="9ee30-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="9ee30-121">Ortam ve yapılandırma moduna göre çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="9ee30-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="9ee30-122">Etkin `Configuration` değere dayalı [olarak Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) paketine koşullu olarak başvurun:</span><span class="sxs-lookup"><span data-stu-id="9ee30-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="9ee30-123">Bir çağrı içerecek `Startup.ConfigureServices` şekilde projenin yöntemini `AddRazorRuntimeCompilation`güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="9ee30-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="9ee30-124">Koşullu `AddRazorRuntimeCompilation` olarak, `ASPNETCORE_ENVIRONMENT` yalnızca değişken ayarlandığında Hata Ayıklama `Development`modunda çalışır:</span><span class="sxs-lookup"><span data-stu-id="9ee30-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="9ee30-125">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9ee30-125">Additional resources</span></span>

* <span data-ttu-id="9ee30-126">[RazorCompileOnBuild ve RazorCompileOnPublish](xref:razor-pages/sdk#properties) özellikleri.</span><span class="sxs-lookup"><span data-stu-id="9ee30-126">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="9ee30-127">Projeler arasında çalışma zamanı derleme çalışması yaptığını gösteren bir örnek için [GitHub'daki çalışma zamanı derleme örneğine](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) bakın.</span><span class="sxs-lookup"><span data-stu-id="9ee30-127">See the [runtimecompilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9ee30-128">İlgili Razor Page veya MVC görünümü çağrıldığı çalışma zamanında bir Razor dosyası derlenir.</span><span class="sxs-lookup"><span data-stu-id="9ee30-128">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="9ee30-129">Razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="9ee30-129">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="9ee30-130">Jilet derlemesi</span><span class="sxs-lookup"><span data-stu-id="9ee30-130">Razor compilation</span></span>

<span data-ttu-id="9ee30-131">Razor dosyalarının oluşturma ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9ee30-131">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="9ee30-132">Razor dosyalarının güncelleştirildikten sonra düzenlenmesi yapı zamanında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="9ee30-132">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="9ee30-133">Varsayılan olarak, yalnızca derlenmiş *Views.dll* ve razor dosyalarını derlemek için gereken *.cshtml* dosyaları veya referans derlemeleri uygulamanızla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="9ee30-133">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9ee30-134">Precompilation aracı amortismana kaldırılmıştır ve Core 3.0 ASP.NET kaldırılacaktır.</span><span class="sxs-lookup"><span data-stu-id="9ee30-134">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="9ee30-135">[Razor Sdk'ya](xref:razor-pages/sdk)göç etmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="9ee30-135">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="9ee30-136">Razor SDK, yalnızca proje dosyasında precompilation'a özgü özellikler ayarlanmadığında etkilidir.</span><span class="sxs-lookup"><span data-stu-id="9ee30-136">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="9ee30-137">Örneğin, *.csproj* dosyasının `MvcRazorCompileOnPublish` özelliğini `true` Razor SDK'yı devre dışı kılabilir şekilde ayarlamak.</span><span class="sxs-lookup"><span data-stu-id="9ee30-137">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="9ee30-138">Çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="9ee30-138">Runtime compilation</span></span>

<span data-ttu-id="9ee30-139">Oluşturma zamanı derlemesi, Razor dosyalarının çalışma zamanı derlemesi ile desteklenir.</span><span class="sxs-lookup"><span data-stu-id="9ee30-139">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="9ee30-140">ASP.NET Core MVC, *.cshtml* dosyasının içeriği değiştiğinde Razor dosyalarını yeniden derler.</span><span class="sxs-lookup"><span data-stu-id="9ee30-140">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ee30-141">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9ee30-141">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
