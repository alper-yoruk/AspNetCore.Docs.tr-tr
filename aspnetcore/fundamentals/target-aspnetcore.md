---
title: Sınıf kitaplığında ASP.NET Çekirdek API'lerini kullanma
author: scottaddie
description: Sınıf kitaplığında ASP.NET Core API'leri nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977203"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="ba348-103">Sınıf kitaplığında ASP.NET Çekirdek API'lerini kullanma</span><span class="sxs-lookup"><span data-stu-id="ba348-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="ba348-104">Yazar: [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="ba348-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="ba348-105">Bu belge, bir sınıf kitaplığında ASP.NET Çekirdek API'leri kullanmak için kılavuz sağlar.</span><span class="sxs-lookup"><span data-stu-id="ba348-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="ba348-106">Diğer tüm kitaplık kılavuzu için [Açık kaynak kitaplık kılavuzuna](/dotnet/standard/library-guidance/)bakın.</span><span class="sxs-lookup"><span data-stu-id="ba348-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="ba348-107">Hangi ASP.NET Core sürümlerini destekleyeceğini belirleme</span><span class="sxs-lookup"><span data-stu-id="ba348-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="ba348-108">ASP.NET Core [.NET Core destek politikasına](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ba348-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="ba348-109">Kitaplıkta hangi ASP.NET Core sürümlerini destekleyeceğini belirlerken destek ilkesine başvurun.</span><span class="sxs-lookup"><span data-stu-id="ba348-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="ba348-110">Bir kitaplık şunları yapmalı:</span><span class="sxs-lookup"><span data-stu-id="ba348-110">A library should:</span></span>

* <span data-ttu-id="ba348-111">*Uzun Vadeli Destek* (LTS) olarak sınıflandırılan tüm ASP.NET Core sürümlerini desteklemek için çaba gösterin.</span><span class="sxs-lookup"><span data-stu-id="ba348-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="ba348-112">*Yaşam Sonu* (EOL) olarak sınıflandırılan ASP.NET Core sürümlerini desteklemek zorunda hissetmiyorum.</span><span class="sxs-lookup"><span data-stu-id="ba348-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="ba348-113">ASP.NET Core'un önizleme sürümleri kullanıma sunulduğundan, kesme değişiklikleri [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub deposunda yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="ba348-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="ba348-114">Çerçeve özellikleri geliştirildikçe kitaplıkların uyumluluk testleri yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="ba348-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="ba348-115">ASP.NET Core paylaşılan çerçevesini kullanma</span><span class="sxs-lookup"><span data-stu-id="ba348-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="ba348-116">.NET Core 3.0 sürümüyle, birçok ASP.NET Core derlemesi artık NuGet'de paket olarak yayınlanmaz.</span><span class="sxs-lookup"><span data-stu-id="ba348-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="ba348-117">Bunun yerine, derlemeler .NET `Microsoft.AspNetCore.App` Core SDK ve runtime yükleyiciler ile yüklenen paylaşılan çerçeveye dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="ba348-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="ba348-118">Artık yayınlanmamaktadır paketlerin listesi için [bkz.](xref:migration/22-to-30#remove-obsolete-package-references)</span><span class="sxs-lookup"><span data-stu-id="ba348-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="ba348-119">.NET Core 3.0 itibariyle, `Microsoft.NET.Sdk.Web` MSBuild SDK'yı kullanan projeler paylaşılan çerçeveye dolaylı olarak başvurur.</span><span class="sxs-lookup"><span data-stu-id="ba348-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="ba348-120">`Microsoft.NET.Sdk` Veya `Microsoft.NET.Sdk.Razor` SDK kullanan projeler, paylaşılan çerçevede ASP.NET Core API'lerini kullanmak için ASP.NET Core'a başvurmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba348-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="ba348-121">Core ASP.NET başvurmak için `<FrameworkReference>` proje dosyanıza aşağıdaki öğeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ba348-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="ba348-122">Bu şekilde ASP.NET Core'a başvurmak yalnızca .NET Core 3.x'i hedefleyen projeler için desteklenir.</span><span class="sxs-lookup"><span data-stu-id="ba348-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="ba348-123">Blazor genişletilebilirlik dahil</span><span class="sxs-lookup"><span data-stu-id="ba348-123">Include Blazor extensibility</span></span>

<span data-ttu-id="ba348-124">Blazor WebAssembly (WASM) ve Sunucu [barındırma modellerini](xref:blazor/hosting-models)destekler.</span><span class="sxs-lookup"><span data-stu-id="ba348-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="ba348-125">Belirli bir neden olmadığı sürece, bir [Razor bileşenleri](xref:blazor/components) kitaplığı her iki barındırma modellerini desteklemelidir.</span><span class="sxs-lookup"><span data-stu-id="ba348-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components) library should support both hosting models.</span></span> <span data-ttu-id="ba348-126">Bir Razor bileşenleri kitaplığı [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk)kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ba348-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="ba348-127">Her iki barındırma modelini de destekleyin</span><span class="sxs-lookup"><span data-stu-id="ba348-127">Support both hosting models</span></span>

<span data-ttu-id="ba348-128">[Hem Blazor Server](xref:blazor/hosting-models#blazor-server) hem de [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projelerinden Razor bileşen tüketimini desteklemek için düzenleyiciniz için aşağıdaki yönergeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba348-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ba348-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba348-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ba348-130">Razor **Class Kitaplığı** proje şablonunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba348-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="ba348-131">Şablonun **Destek sayfaları ve görünümleri** onay kutusu seçili olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba348-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ba348-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba348-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ba348-133">Tümleşik terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ba348-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ba348-134">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba348-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ba348-135">Razor **Class Kitaplığı** proje şablonunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba348-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="ba348-136">Şablondan oluşturulan proje aşağıdaki leri yapar:</span><span class="sxs-lookup"><span data-stu-id="ba348-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="ba348-137">Hedefler .NET Standart 2.0.</span><span class="sxs-lookup"><span data-stu-id="ba348-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="ba348-138">Özelliği `RazorLangVersion` ' `3.0`ye ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ba348-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="ba348-139">`3.0`.NET Core 3.x için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="ba348-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="ba348-140">Aşağıdaki paket referansları ekler:</span><span class="sxs-lookup"><span data-stu-id="ba348-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="ba348-141">Microsoft.AspNetCore.Components</span><span class="sxs-lookup"><span data-stu-id="ba348-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="ba348-142">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="ba348-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="ba348-143">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba348-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="ba348-144">Belirli bir barındırma modelini destekleme</span><span class="sxs-lookup"><span data-stu-id="ba348-144">Support a specific hosting model</span></span>

<span data-ttu-id="ba348-145">Tek bir Blazor barındırma modelini desteklemek çok daha az yaygındır.</span><span class="sxs-lookup"><span data-stu-id="ba348-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="ba348-146">Örnek olarak, [yalnızca Blazor Server](xref:blazor/hosting-models#blazor-server) projelerinden Razor bileşen tüketimini desteklemek için:</span><span class="sxs-lookup"><span data-stu-id="ba348-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="ba348-147">Hedef .NET Çekirdek 3.x.</span><span class="sxs-lookup"><span data-stu-id="ba348-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="ba348-148">Paylaşılan `<FrameworkReference>` çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="ba348-149">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba348-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="ba348-150">Razor bileşenleri içeren kitaplıklar hakkında daha fazla bilgi için [core razor bileşenleri sınıf kitaplıkları ASP.NET](xref:blazor/class-libraries)bakın.</span><span class="sxs-lookup"><span data-stu-id="ba348-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="ba348-151">MVC genişletilebilirlik dahil</span><span class="sxs-lookup"><span data-stu-id="ba348-151">Include MVC extensibility</span></span>

<span data-ttu-id="ba348-152">Bu bölümde kitaplıklar için öneriler sıralanıyor:</span><span class="sxs-lookup"><span data-stu-id="ba348-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="ba348-153">Jilet görünümleri veya Jilet Sayfaları</span><span class="sxs-lookup"><span data-stu-id="ba348-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="ba348-154">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="ba348-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="ba348-155">Görünüm bileşenleri</span><span class="sxs-lookup"><span data-stu-id="ba348-155">View components</span></span>](#view-components)

<span data-ttu-id="ba348-156">Bu bölümde, MVC'nin birden çok sürümü desteklemek için çoklu hedefleme tartışılmıyor.</span><span class="sxs-lookup"><span data-stu-id="ba348-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="ba348-157">Birden çok ASP.NET Core sürümlerini destekleme kılavuzu için, [birden çok ASP.NET Çekirdek sürümlerini destekle' ye](#support-multiple-aspnet-core-versions)bakın.</span><span class="sxs-lookup"><span data-stu-id="ba348-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="ba348-158">Jilet görünümleri veya Jilet Sayfaları</span><span class="sxs-lookup"><span data-stu-id="ba348-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="ba348-159">[Razor görünümleri](xref:mvc/views/overview) veya [Razor Pages](xref:razor-pages/index) içeren bir proje [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk)kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ba348-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="ba348-160">Proje .NET Core 3.x'i hedefliyorsa, şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="ba348-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="ba348-161">Bir `AddRazorSupportForMvc` MSBuild özelliği `true`.</span><span class="sxs-lookup"><span data-stu-id="ba348-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="ba348-162">Paylaşılan `<FrameworkReference>` çerçeve için bir öğe.</span><span class="sxs-lookup"><span data-stu-id="ba348-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="ba348-163">**Razor Class Library** proje şablonu .NET Core 3.x'i hedefleyen projeler için önceki gereksinimleri karşılar.</span><span class="sxs-lookup"><span data-stu-id="ba348-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="ba348-164">Düzenleyiciniz için aşağıdaki yönergeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba348-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ba348-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba348-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ba348-166">Razor **Class Kitaplığı** proje şablonunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba348-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="ba348-167">Şablonun **Destek sayfaları ve görünümleri** onay kutusu seçilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ba348-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ba348-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba348-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ba348-169">Tümleşik terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ba348-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ba348-170">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba348-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ba348-171">Şu anda proje şablonu desteği yok.</span><span class="sxs-lookup"><span data-stu-id="ba348-171">No project template support at this time.</span></span>

---

<span data-ttu-id="ba348-172">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba348-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="ba348-173">Proje bunun yerine .NET Standard'ı hedefliyorsa, [bir Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) paket başvurusu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ba348-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="ba348-174">Paket, `Microsoft.AspNetCore.Mvc` Core 3.0 ASP.NET paylaşılan çerçeveye taşındı ve bu nedenle artık yayınlanmadı.</span><span class="sxs-lookup"><span data-stu-id="ba348-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="ba348-175">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba348-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="ba348-176">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="ba348-176">Tag Helpers</span></span>

<span data-ttu-id="ba348-177">[Tag Helpers](xref:mvc/views/tag-helpers/intro) içeren bir `Microsoft.NET.Sdk` proje SDK kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba348-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="ba348-178">.NET Core 3.x hedefleme, `<FrameworkReference>` paylaşılan çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="ba348-179">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba348-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="ba348-180">.NET Standard hedefleme (ASP.NET Core 3.x'ten önceki sürümleri desteklemek için), [Microsoft.AspNetCore.Mvc.Razor'a](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="ba348-181">Paket `Microsoft.AspNetCore.Mvc.Razor` paylaşılan çerçeveye taşındı ve bu nedenle artık yayınlanmaz.</span><span class="sxs-lookup"><span data-stu-id="ba348-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="ba348-182">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba348-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="ba348-183">Görünüm bileşenleri</span><span class="sxs-lookup"><span data-stu-id="ba348-183">View components</span></span>

<span data-ttu-id="ba348-184">[Görünüm bileşenlerini](xref:mvc/views/view-components) içeren bir `Microsoft.NET.Sdk` proje SDK'yı kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba348-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="ba348-185">.NET Core 3.x hedefleme, `<FrameworkReference>` paylaşılan çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="ba348-186">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba348-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="ba348-187">.NET Standard hedefleme (ASP.NET Core 3.x'ten önceki sürümleri desteklemek için), [Microsoft.AspNetCore.Mvc.ViewFeatures'e](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures)bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="ba348-188">Paket `Microsoft.AspNetCore.Mvc.ViewFeatures` paylaşılan çerçeveye taşındı ve bu nedenle artık yayınlanmaz.</span><span class="sxs-lookup"><span data-stu-id="ba348-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="ba348-189">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba348-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="ba348-190">Birden çok ASP.NET Core sürümlerini destekleyin</span><span class="sxs-lookup"><span data-stu-id="ba348-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="ba348-191">ASP.NET Core'un birden çok türevlerini destekleyen bir kitaplığı yazarsa, çoklu hedefleme gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ba348-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="ba348-192">Etiket Yardımcıları kitaplığı aşağıdaki ASP.NET Çekirdek türevlerini desteklemesi gereken bir senaryo düşünün:</span><span class="sxs-lookup"><span data-stu-id="ba348-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="ba348-193">ASP.NET Core 2.1 hedefleme .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="ba348-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="ba348-194">ASP.NET Core 2.x hedefleme .NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="ba348-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="ba348-195">ASP.NET Core 3.x hedefleme .NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="ba348-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="ba348-196">Aşağıdaki proje dosyası `TargetFrameworks` özellik üzerinden bu türevleri destekler:</span><span class="sxs-lookup"><span data-stu-id="ba348-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="ba348-197">Önceki proje dosyası ile:</span><span class="sxs-lookup"><span data-stu-id="ba348-197">With the preceding project file:</span></span>

* <span data-ttu-id="ba348-198">Paket `Markdig` tüm tüketiciler için eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="ba348-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="ba348-199">.NET Framework 4.6.1 veya sonraki veya .NET Core 2.x'i hedefleyen tüketiciler için [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) adresine bir başvuru eklenir.</span><span class="sxs-lookup"><span data-stu-id="ba348-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="ba348-200">Paketin Sürüm 2.1.0 geriye dönük uyumluluk nedeniyle ASP.NET Core 2.2 ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="ba348-200">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="ba348-201">Paylaşılan çerçeve,.NET Core 3.x'i hedefleyen tüketiciler için başvurulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ba348-201">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="ba348-202">Paket `Microsoft.AspNetCore.Mvc.Razor` paylaşılan çerçeveye dahildir.</span><span class="sxs-lookup"><span data-stu-id="ba348-202">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="ba348-203">Alternatif olarak, .NET Standart 2.0 hem .NET Core 2.1 ve .NET Framework 4.6.1 hedefleme yerine hedeflenebilir:</span><span class="sxs-lookup"><span data-stu-id="ba348-203">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="ba348-204">Önceki proje dosyasında aşağıdaki uyarılar vardır:</span><span class="sxs-lookup"><span data-stu-id="ba348-204">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="ba348-205">Kitaplık yalnızca Tag Helpers içerdiğinden, ASP.NET Core'un çalıştığı belirli platformları hedeflemek daha kolaydır: .NET Core ve .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ba348-205">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="ba348-206">Tag Yardımcıları Birlik, UWP ve Xamarin gibi diğer .NET Standart 2.0 uyumlu hedef çerçeveler tarafından kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="ba348-206">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="ba348-207">.NET Framework'den .NET Standard 2.0'ın kullanılmasında .NET Framework 4.7.2'de ele alınan bazı sorunlar vardır.</span><span class="sxs-lookup"><span data-stu-id="ba348-207">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="ba348-208">.NET Framework 4.6.1 ile 4.7.1'i kullanarak tüketicilerin deneyimini geliştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba348-208">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="ba348-209">Kitaplığınız platforma özgü API'leri çağırması gerekiyorsa, .NET Standardı yerine belirli .NET uygulamalarını hedefle.</span><span class="sxs-lookup"><span data-stu-id="ba348-209">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="ba348-210">Daha fazla bilgi için çoklu [hedefleme](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)ye bakın.</span><span class="sxs-lookup"><span data-stu-id="ba348-210">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="ba348-211">Değişmemiş bir API kullanma</span><span class="sxs-lookup"><span data-stu-id="ba348-211">Use an API that hasn't changed</span></span>

<span data-ttu-id="ba348-212">Bir ara yazılım kitaplığını .NET Core 2.2'den 3.0'a yükselttiğinizi bir senaryo düşünün.</span><span class="sxs-lookup"><span data-stu-id="ba348-212">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="ba348-213">Kitaplıkta kullanılan ASP.NET Core ara yazılım API'leri ASP.NET Core 2.2 ile 3.0 arasında değişmedi.</span><span class="sxs-lookup"><span data-stu-id="ba348-213">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="ba348-214">.NET Core 3.0'daki ara yazılım kitaplığını desteklemeye devam etmek için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="ba348-214">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="ba348-215">Standart [kitaplık kılavuzunu](/dotnet/standard/library-guidance/)izleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-215">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="ba348-216">İlgili derleme paylaşılan çerçevede yoksa, her API'nın NuGet paketi için bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-216">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="ba348-217">Değiştirilen bir API kullanma</span><span class="sxs-lookup"><span data-stu-id="ba348-217">Use an API that changed</span></span>

<span data-ttu-id="ba348-218">Bir kitaplığı .NET Core 2.2'den .NET Core 3.0'a yükselttiğinizi bir senaryo düşünün.</span><span class="sxs-lookup"><span data-stu-id="ba348-218">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="ba348-219">Kitaplıkta kullanılan ASP.NET Core API'nın ASP.NET Core 3.0'da [bir kırılma değişikliği](/dotnet/core/compatibility/breaking-changes) vardır.</span><span class="sxs-lookup"><span data-stu-id="ba348-219">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="ba348-220">Kitaplığın bozuk API'yi tüm sürümlerde kullanmamak için yeniden yazıp yazamayacağını düşünün.</span><span class="sxs-lookup"><span data-stu-id="ba348-220">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="ba348-221">Kitaplığı yeniden yazabilirseniz, bunu yapın ve paket referansları içeren daha önceki bir hedef çerçeveyi (örneğin, .NET Standart 2.0 veya .NET Framework 4.6.1) hedeflemeye devam edin.</span><span class="sxs-lookup"><span data-stu-id="ba348-221">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="ba348-222">Kitaplığı yeniden yazamıyorsanız, aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="ba348-222">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="ba348-223">.NET Core 3.0 için bir hedef ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-223">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="ba348-224">Paylaşılan `<FrameworkReference>` çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-224">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="ba348-225">Kodu koşullu olarak derlemek için uygun hedef çerçeve sembolüne sahip [#if önişlemci yönergesini](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba348-225">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="ba348-226">Örneğin, http istek ve yanıt akışları üzerinde senkron okuma ve yazma core 3.0 ASP.NET varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="ba348-226">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="ba348-227">ASP.NET Core 2.2 varsayılan olarak senkron davranışı destekler.</span><span class="sxs-lookup"><span data-stu-id="ba348-227">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="ba348-228">G/Ç'nin gerçekleştiği yerde eşzamanlı okuma ve yazmanın etkinleştirilmesi gereken bir ara yazılım kitaplığı düşünün.</span><span class="sxs-lookup"><span data-stu-id="ba348-228">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="ba348-229">Kitaplık, senkron özellikleri uygun önişlemci yönergesinde etkinleştirmek için kodu içine almalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba348-229">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="ba348-230">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba348-230">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="ba348-231">3.0'da tanıtılan bir API kullanma</span><span class="sxs-lookup"><span data-stu-id="ba348-231">Use an API introduced in 3.0</span></span>

<span data-ttu-id="ba348-232">Core 3.0'ASP.NET tanıtılan ASP.NET Core API kullanmak istediğinizi düşünün.</span><span class="sxs-lookup"><span data-stu-id="ba348-232">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="ba348-233">Aşağıdaki soruları göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="ba348-233">Consider the following questions:</span></span>

1. <span data-ttu-id="ba348-234">Kitaplık işlevsel olarak yeni API gerektirir mi?</span><span class="sxs-lookup"><span data-stu-id="ba348-234">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="ba348-235">Kitaplık bu özelliği farklı bir şekilde uygulayabilir mi?</span><span class="sxs-lookup"><span data-stu-id="ba348-235">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="ba348-236">Kitaplık işlevsel olarak API gerektiriyorsa ve bunu alt düzey uygulamanın bir yolu yoksa:</span><span class="sxs-lookup"><span data-stu-id="ba348-236">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="ba348-237">Hedef .NET Core 3.x yalnızca.</span><span class="sxs-lookup"><span data-stu-id="ba348-237">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="ba348-238">Paylaşılan `<FrameworkReference>` çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-238">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="ba348-239">Kitaplık özelliği farklı bir şekilde uygulayabiliyorsa:</span><span class="sxs-lookup"><span data-stu-id="ba348-239">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="ba348-240">Hedef çerçeve olarak .NET Core 3.x ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-240">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="ba348-241">Paylaşılan `<FrameworkReference>` çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-241">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="ba348-242">Kodu koşullu olarak derlemek için uygun hedef çerçeve sembolüne sahip [#if önişlemci yönergesini](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba348-242">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="ba348-243">Örneğin, aşağıdaki Tag Helper, <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> Core 3.0ASP.NET tanıtılan arabirimi kullanır.</span><span class="sxs-lookup"><span data-stu-id="ba348-243">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="ba348-244">.NET Core 3.0'ı hedefleyen `NETCOREAPP3_0` tüketiciler, hedef çerçeve simgesi tarafından tanımlanan kod yolunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="ba348-244">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="ba348-245">Etiket Yardımcısı'nın oluşturucu parametre türü <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> .NET Core 2.1 ve .NET Framework 4.6.1 tüketicileri için değişir.</span><span class="sxs-lookup"><span data-stu-id="ba348-245">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="ba348-246">ASP.NET Core 3.0 eski olarak `IHostingEnvironment` işaretlenmiş ve yedek `IWebHostEnvironment` olarak önerilen çünkü bu değişiklik gerekliydi.</span><span class="sxs-lookup"><span data-stu-id="ba348-246">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="ba348-247">Aşağıdaki çok hedefli proje dosyası bu Tag Helper senaryosunu destekler:</span><span class="sxs-lookup"><span data-stu-id="ba348-247">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="ba348-248">Paylaşılan çerçeveden kaldırılan bir API kullanma</span><span class="sxs-lookup"><span data-stu-id="ba348-248">Use an API removed from the shared framework</span></span>

<span data-ttu-id="ba348-249">Paylaşılan çerçeveden kaldırılan bir ASP.NET Çekirdek derlemesi kullanmak için uygun paket başvuruclarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba348-249">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="ba348-250">ASP.NET Core 3.0'da paylaşılan çerçeveden kaldırılan [paketlerin](xref:migration/22-to-30#remove-obsolete-package-references)listesi için bkz.</span><span class="sxs-lookup"><span data-stu-id="ba348-250">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="ba348-251">Örneğin, web API istemcisi eklemek için:</span><span class="sxs-lookup"><span data-stu-id="ba348-251">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="ba348-252">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ba348-252">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [<span data-ttu-id="ba348-253">.NET uygulama desteği</span><span class="sxs-lookup"><span data-stu-id="ba348-253">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="ba348-254">.NET destek politikaları</span><span class="sxs-lookup"><span data-stu-id="ba348-254">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
