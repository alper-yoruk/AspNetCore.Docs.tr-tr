---
title: ASP.NET Core'da jilet dosya derlemesi
author: rick-anderson
description: Razor dosyalarının derlemesinin ASP.NET Core uygulamasında nasıl oluştuğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994603"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="91ba1-103">ASP.NET Core'da jilet dosya derlemesi</span><span class="sxs-lookup"><span data-stu-id="91ba1-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="91ba1-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91ba1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="91ba1-105">*.cshtml* uzantılı razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="91ba1-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="91ba1-106">Çalışma süresi derlemesi, uygulamanızı yapılandırarak isteğe bağlı olarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="91ba1-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="91ba1-107">Jilet derlemesi</span><span class="sxs-lookup"><span data-stu-id="91ba1-107">Razor compilation</span></span>

<span data-ttu-id="91ba1-108">Razor dosyalarının oluşturma ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="91ba1-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="91ba1-109">Etkinleştirildiğinde, runtime derlemesi yapı zamanı derlemesini tamamlayarak Razor dosyalarının düzenlenirse güncelleştirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="91ba1-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="91ba1-110">Çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="91ba1-110">Runtime compilation</span></span>

<span data-ttu-id="91ba1-111">Tüm ortamlar ve yapılandırma modları için çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="91ba1-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="91ba1-112">[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="91ba1-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="91ba1-113">Bir çağrı içerecek `Startup.ConfigureServices` şekilde projenin yöntemini `AddRazorRuntimeCompilation`güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="91ba1-113">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="91ba1-114">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="91ba1-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="91ba1-115">Koşullu etkinleştirme çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="91ba1-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="91ba1-116">Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilecek şekilde etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="91ba1-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="91ba1-117">Bu şekilde koşullu olarak etkinleştirmek, yayımlanmış çıktının aşağıdakileri sağlamasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="91ba1-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="91ba1-118">Derlenmiş görünümleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="91ba1-118">Uses compiled views.</span></span>
* <span data-ttu-id="91ba1-119">Boyutu daha küçüktür.</span><span class="sxs-lookup"><span data-stu-id="91ba1-119">Is smaller in size.</span></span>
* <span data-ttu-id="91ba1-120">Üretimde dosya izleyicilerine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="91ba1-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="91ba1-121">Ortam ve yapılandırma moduna göre çalışma zamanı derlemesini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="91ba1-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="91ba1-122">Etkin `Configuration` değere dayalı [olarak Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) paketine koşullu olarak başvurun:</span><span class="sxs-lookup"><span data-stu-id="91ba1-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="91ba1-123">Bir çağrı içerecek `Startup.ConfigureServices` şekilde projenin yöntemini `AddRazorRuntimeCompilation`güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="91ba1-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="91ba1-124">Koşullu `AddRazorRuntimeCompilation` olarak, `ASPNETCORE_ENVIRONMENT` yalnızca değişken ayarlandığında Hata Ayıklama `Development`modunda çalışır:</span><span class="sxs-lookup"><span data-stu-id="91ba1-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="91ba1-125">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="91ba1-125">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="91ba1-126">İlgili Razor Page veya MVC görünümü çağrıldığı çalışma zamanında bir Razor dosyası derlenir.</span><span class="sxs-lookup"><span data-stu-id="91ba1-126">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="91ba1-127">Razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir.</span><span class="sxs-lookup"><span data-stu-id="91ba1-127">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="91ba1-128">Jilet derlemesi</span><span class="sxs-lookup"><span data-stu-id="91ba1-128">Razor compilation</span></span>

<span data-ttu-id="91ba1-129">Razor dosyalarının oluşturma ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="91ba1-129">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="91ba1-130">Razor dosyalarının güncelleştirildikten sonra düzenlenmesi yapı zamanında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="91ba1-130">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="91ba1-131">Varsayılan olarak, yalnızca derlenmiş *Views.dll* ve razor dosyalarını derlemek için gereken *.cshtml* dosyaları veya referans derlemeleri uygulamanızla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="91ba1-131">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="91ba1-132">Precompilation aracı amortismana kaldırılmıştır ve Core 3.0 ASP.NET kaldırılacaktır.</span><span class="sxs-lookup"><span data-stu-id="91ba1-132">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="91ba1-133">[Razor Sdk'ya](xref:razor-pages/sdk)göç etmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="91ba1-133">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="91ba1-134">Razor SDK, yalnızca proje dosyasında precompilation'a özgü özellikler ayarlanmadığında etkilidir.</span><span class="sxs-lookup"><span data-stu-id="91ba1-134">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="91ba1-135">Örneğin, *.csproj* dosyasının `MvcRazorCompileOnPublish` özelliğini `true` Razor SDK'yı devre dışı kılabilir şekilde ayarlamak.</span><span class="sxs-lookup"><span data-stu-id="91ba1-135">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="91ba1-136">Çalışma zamanı derlemesi</span><span class="sxs-lookup"><span data-stu-id="91ba1-136">Runtime compilation</span></span>

<span data-ttu-id="91ba1-137">Oluşturma zamanı derlemesi, Razor dosyalarının çalışma zamanı derlemesi ile desteklenir.</span><span class="sxs-lookup"><span data-stu-id="91ba1-137">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="91ba1-138">ASP.NET Core MVC, *.cshtml* dosyasının içeriği değiştiğinde Razor dosyalarını yeniden derler.</span><span class="sxs-lookup"><span data-stu-id="91ba1-138">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="91ba1-139">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="91ba1-139">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end