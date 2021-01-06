---
title: Bir sınıf kitaplığında ASP.NET Core API 'Leri kullanma
author: scottaddie
description: Bir sınıf kitaplığında ASP.NET Core API 'Leri kullanmayı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: c012658a6f48247af60c8bfd56a7d987f6aa8a68
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061515"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="429bc-103">Bir sınıf kitaplığında ASP.NET Core API 'Leri kullanma</span><span class="sxs-lookup"><span data-stu-id="429bc-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="429bc-104">[Scott Ade](https://github.com/scottaddie) tarafından</span><span class="sxs-lookup"><span data-stu-id="429bc-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="429bc-105">Bu belge bir sınıf kitaplığında ASP.NET Core API 'Leri kullanmaya yönelik rehberlik sağlar.</span><span class="sxs-lookup"><span data-stu-id="429bc-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="429bc-106">Diğer tüm kitaplık Kılavuzu için bkz. [Açık Kaynak Kitaplığı Kılavuzu](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="429bc-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="429bc-107">Hangi ASP.NET Core sürümlerinin destekleyeceğini belirleme</span><span class="sxs-lookup"><span data-stu-id="429bc-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="429bc-108">ASP.NET Core [.NET Core destek ilkesine](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)uyar.</span><span class="sxs-lookup"><span data-stu-id="429bc-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="429bc-109">Kitaplıkta hangi ASP.NET Core sürümlerinin destekleyeceği belirlenirken destek ilkesine başvurun.</span><span class="sxs-lookup"><span data-stu-id="429bc-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="429bc-110">Bir kitaplık:</span><span class="sxs-lookup"><span data-stu-id="429bc-110">A library should:</span></span>

* <span data-ttu-id="429bc-111">*Uzun süreli destek* (LTS) olarak sınıflandırılan tüm ASP.NET Core sürümlerini desteklemeye yönelik bir çaba oluşturun.</span><span class="sxs-lookup"><span data-stu-id="429bc-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="429bc-112">*Yaşam süresi* (EOL) olarak sınıflandırılan ASP.NET Core sürümlerini desteklemeye yönelik değildir.</span><span class="sxs-lookup"><span data-stu-id="429bc-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="429bc-113">ASP.NET Core önizleme sürümleri kullanılabilir hale getirilirse, [ASPNET/Duyurular](https://github.com/aspnet/Announcements/issues) GitHub deposunda son değişiklikler gönderilir.</span><span class="sxs-lookup"><span data-stu-id="429bc-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="429bc-114">Çerçeve özellikleri geliştirildiği için kitaplıkların Uyumluluk testi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="429bc-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="429bc-115">ASP.NET Core paylaşılan çerçevesini kullanma</span><span class="sxs-lookup"><span data-stu-id="429bc-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="429bc-116">.NET Core 3,0 sürümü sayesinde, birçok ASP.NET Core derlemesi artık NuGet 'e paket olarak yayımlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="429bc-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="429bc-117">Bunun yerine, derlemeler `Microsoft.AspNetCore.App` .NET Core SDK ve çalışma zamanı yükleyicilerle birlikte yüklenen paylaşılan çerçeveye dahil edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="429bc-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="429bc-118">Artık yayımlanmayan paketlerin listesi için bkz. [artık kullanılmayan paket başvurularını kaldır](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="429bc-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="429bc-119">.NET Core 3,0 itibariyle, `Microsoft.NET.Sdk.Web` MSBuild SDK 'sını kullanan projeler, paylaşılan çerçeveye örtük olarak başvurur.</span><span class="sxs-lookup"><span data-stu-id="429bc-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="429bc-120">Veya SDK kullanan projeler, `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` paylaşılan çerçevede ASP.NET Core API 'leri kullanmak için ASP.NET Core başvurmalıdır.</span><span class="sxs-lookup"><span data-stu-id="429bc-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="429bc-121">ASP.NET Core başvurmak için, `<FrameworkReference>` proje dosyanıza aşağıdaki öğeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="429bc-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="429bc-122">ASP.NET Core buna bu şekilde başvurulması yalnızca .NET Core 3. x 'i hedefleyen projeler için desteklenir.</span><span class="sxs-lookup"><span data-stu-id="429bc-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-no-locblazor-extensibility"></a><span data-ttu-id="429bc-123">Genişletilebilirlik dahil et Blazor</span><span class="sxs-lookup"><span data-stu-id="429bc-123">Include Blazor extensibility</span></span>

<span data-ttu-id="429bc-124">Blazor WebAssembly (ıSSTREAM) ve sunucu [barındırma modellerini](xref:blazor/hosting-models)destekler.</span><span class="sxs-lookup"><span data-stu-id="429bc-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="429bc-125">Belirli bir neden olmadığı müddetçe, bir [ Razor bileşen](xref:blazor/components/index) Kitaplığı hem barındırma modellerini desteklemelidir.</span><span class="sxs-lookup"><span data-stu-id="429bc-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components/index) library should support both hosting models.</span></span> <span data-ttu-id="429bc-126">Bir Razor Bileşen kitaplığı [MICROSOFT. net. SDK kullanmalıdır. Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="429bc-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="429bc-127">Barındırma modellerini destekler</span><span class="sxs-lookup"><span data-stu-id="429bc-127">Support both hosting models</span></span>

<span data-ttu-id="429bc-128">Hem hem Razor de olan projelerden gelen bileşen tüketimini desteklemek için [Blazor Server](xref:blazor/hosting-models#blazor-server) , düzenleyiciniz için aşağıdaki yönergeleri kullanın. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="429bc-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="429bc-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="429bc-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="429bc-130">**Razor Sınıf kitaplığı** proje şablonunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="429bc-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="429bc-131">Şablonun **destek sayfaları ve görünümleri** onay kutusu seçili olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="429bc-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="429bc-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="429bc-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="429bc-133">Tümleşik terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="429bc-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="429bc-134">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="429bc-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="429bc-135">**Razor Sınıf kitaplığı** proje şablonunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="429bc-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="429bc-136">Şablondan oluşturulan proje aşağıdaki işlemleri yapar:</span><span class="sxs-lookup"><span data-stu-id="429bc-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="429bc-137">2,0 .NET Standard hedefler.</span><span class="sxs-lookup"><span data-stu-id="429bc-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="429bc-138">`RazorLangVersion`Özelliğini olarak ayarlar `3.0` .</span><span class="sxs-lookup"><span data-stu-id="429bc-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="429bc-139">`3.0` , .NET Core 3. x için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="429bc-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="429bc-140">Aşağıdaki paket başvurularını ekler:</span><span class="sxs-lookup"><span data-stu-id="429bc-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="429bc-141">Microsoft. AspNetCore. Components</span><span class="sxs-lookup"><span data-stu-id="429bc-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="429bc-142">Microsoft. AspNetCore. components. Web</span><span class="sxs-lookup"><span data-stu-id="429bc-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="429bc-143">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="429bc-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="429bc-144">Belirli bir barındırma modelini destekleme</span><span class="sxs-lookup"><span data-stu-id="429bc-144">Support a specific hosting model</span></span>

<span data-ttu-id="429bc-145">Tek bir barındırma modelini desteklemek çok daha az yaygındır Blazor .</span><span class="sxs-lookup"><span data-stu-id="429bc-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="429bc-146">Örnek olarak, Razor yalnızca projeden bileşen kullanımını desteklemek için [Blazor Server](xref:blazor/hosting-models#blazor-server) :</span><span class="sxs-lookup"><span data-stu-id="429bc-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="429bc-147">Hedef .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="429bc-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="429bc-148">`<FrameworkReference>`Paylaşılan çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="429bc-149">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="429bc-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="429bc-150">Bileşenleri içeren kitaplıklar hakkında daha fazla bilgi için Razor bkz. [ASP.NET Core Razor Components sınıf kitaplıkları](xref:blazor/components/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="429bc-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/components/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="429bc-151">MVC genişletilebilirliği Ekle</span><span class="sxs-lookup"><span data-stu-id="429bc-151">Include MVC extensibility</span></span>

<span data-ttu-id="429bc-152">Bu bölümde şunları içeren kitaplıklara ilişkin öneriler özetlenmektedir:</span><span class="sxs-lookup"><span data-stu-id="429bc-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="429bc-153">Razor görünümler veya Razor Sayfalar</span><span class="sxs-lookup"><span data-stu-id="429bc-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="429bc-154">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="429bc-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="429bc-155">Görünüm bileşenleri</span><span class="sxs-lookup"><span data-stu-id="429bc-155">View components</span></span>](#view-components)

<span data-ttu-id="429bc-156">Bu bölüm, MVC 'nin birden çok sürümünü desteklemek için Çoklu hedefleme ile açıklanmamaktadır.</span><span class="sxs-lookup"><span data-stu-id="429bc-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="429bc-157">Birden çok ASP.NET Core sürümünün desteklenmesi hakkında rehberlik için bkz. [çoklu ASP.NET Core sürümlerini](#support-multiple-aspnet-core-versions)destekleme.</span><span class="sxs-lookup"><span data-stu-id="429bc-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="no-locrazor-views-or-no-locrazor-pages"></a><span data-ttu-id="429bc-158">Razor görünümler veya Razor Sayfalar</span><span class="sxs-lookup"><span data-stu-id="429bc-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="429bc-159">[ Razor Görünümler](xref:mvc/views/overview) veya [ Razor Sayfalar](xref:razor-pages/index) içeren bir projenin [Microsoft. net. SDK kullanması gerekir. Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="429bc-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="429bc-160">Proje .NET Core 3. x 'i hedefliyorsa şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="429bc-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="429bc-161">`AddRazorSupportForMvc`Olarak ayarlanan MSBuild özelliği `true` .</span><span class="sxs-lookup"><span data-stu-id="429bc-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="429bc-162">`<FrameworkReference>`Paylaşılan çerçeve için bir öğe.</span><span class="sxs-lookup"><span data-stu-id="429bc-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="429bc-163">**Razor Sınıf kitaplığı** proje şablonu, .NET Core 3. x 'i hedefleyen projeler için önceki gereksinimleri karşılar.</span><span class="sxs-lookup"><span data-stu-id="429bc-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="429bc-164">Düzenleyiciniz için aşağıdaki yönergeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="429bc-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="429bc-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="429bc-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="429bc-166">**Razor Sınıf kitaplığı** proje şablonunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="429bc-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="429bc-167">Şablonun **destek sayfaları ve görünümleri** onay kutusu seçilmelidir.</span><span class="sxs-lookup"><span data-stu-id="429bc-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="429bc-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="429bc-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="429bc-169">Tümleşik terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="429bc-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="429bc-170">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="429bc-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="429bc-171">Şu anda proje şablonu desteği yok.</span><span class="sxs-lookup"><span data-stu-id="429bc-171">No project template support at this time.</span></span>

---

<span data-ttu-id="429bc-172">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="429bc-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="429bc-173">Proje bunun yerine .NET Standard hedefliyorsa, bir [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) paket başvurusu gerekir.</span><span class="sxs-lookup"><span data-stu-id="429bc-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="429bc-174">`Microsoft.AspNetCore.Mvc`Paket ASP.NET Core 3,0 ' de paylaşılan çerçeveye taşındı ve bu nedenle artık yayımlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="429bc-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="429bc-175">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="429bc-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="429bc-176">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="429bc-176">Tag Helpers</span></span>

<span data-ttu-id="429bc-177">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) içeren bir projenin SDK kullanması gerekir `Microsoft.NET.Sdk` .</span><span class="sxs-lookup"><span data-stu-id="429bc-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="429bc-178">.NET Core 3. x ' i hedefliyorsanız, `<FrameworkReference>` paylaşılan çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="429bc-179">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="429bc-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="429bc-180">.NET Standard hedefliyorsanız (ASP.NET Core 3. x sürümünden önceki sürümleri desteklemek için), [Microsoft. AspNetCore. Mvc Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="429bc-181">`Microsoft.AspNetCore.Mvc.Razor`Paket, paylaşılan çerçeveye taşındı ve bu nedenle artık yayınlanmadı.</span><span class="sxs-lookup"><span data-stu-id="429bc-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="429bc-182">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="429bc-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="429bc-183">Görünüm bileşenleri</span><span class="sxs-lookup"><span data-stu-id="429bc-183">View components</span></span>

<span data-ttu-id="429bc-184">[Görünüm bileşenlerini](xref:mvc/views/view-components) içeren bir projenin SDK kullanması gerekir `Microsoft.NET.Sdk` .</span><span class="sxs-lookup"><span data-stu-id="429bc-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="429bc-185">.NET Core 3. x ' i hedefliyorsanız, `<FrameworkReference>` paylaşılan çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="429bc-186">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="429bc-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="429bc-187">.NET Standard hedefliyorsanız (ASP.NET Core 3. x sürümünden önceki sürümleri desteklemek için), [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="429bc-188">`Microsoft.AspNetCore.Mvc.ViewFeatures`Paket, paylaşılan çerçeveye taşındı ve bu nedenle artık yayınlanmadı.</span><span class="sxs-lookup"><span data-stu-id="429bc-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="429bc-189">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="429bc-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="429bc-190">Çoklu ASP.NET Core sürümlerini destekleme</span><span class="sxs-lookup"><span data-stu-id="429bc-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="429bc-191">ASP.NET Core birden çok çeşidini destekleyen bir kitaplığı yazmak için Çoklu hedefleme gereklidir.</span><span class="sxs-lookup"><span data-stu-id="429bc-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="429bc-192">Etiket Yardımcıları kitaplığı 'nın aşağıdaki ASP.NET Core türevlerini desteklemesi gereken bir senaryo düşünün:</span><span class="sxs-lookup"><span data-stu-id="429bc-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="429bc-193">ASP.NET Core 2,1 hedefleme .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="429bc-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="429bc-194">.NET Core 2. x 'i hedefleyen 2. x ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="429bc-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="429bc-195">ASP.NET Core 3. x .NET Core 3. x 'i hedefleme</span><span class="sxs-lookup"><span data-stu-id="429bc-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="429bc-196">Aşağıdaki proje dosyası bu değişkenleri özelliği aracılığıyla destekler `TargetFrameworks` :</span><span class="sxs-lookup"><span data-stu-id="429bc-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="429bc-197">Önceki proje dosyası ile:</span><span class="sxs-lookup"><span data-stu-id="429bc-197">With the preceding project file:</span></span>

* <span data-ttu-id="429bc-198">`Markdig`Paket tüm tüketiciler için eklenir.</span><span class="sxs-lookup"><span data-stu-id="429bc-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="429bc-199">[Microsoft. AspNetCore. Mvc Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) öğesine bir başvuru.</span><span class="sxs-lookup"><span data-stu-id="429bc-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span></span> <span data-ttu-id="429bc-200">.NET Framework 4.6.1 veya üzeri ya da .NET Core 2. x ' i hedefleyen tüketiciler için eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="429bc-200">is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="429bc-201">Paketin 2.1.0 sürümü, geriye dönük uyumluluk nedeniyle ASP.NET Core 2,2 ile birlikte çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="429bc-201">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="429bc-202">Paylaşılan çerçeveye .NET Core 3. x 'i hedefleyen tüketiciler için başvurulur.</span><span class="sxs-lookup"><span data-stu-id="429bc-202">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="429bc-203">`Microsoft.AspNetCore.Mvc.Razor`Paket, paylaşılan çerçeveye dahildir.</span><span class="sxs-lookup"><span data-stu-id="429bc-203">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="429bc-204">Alternatif olarak, .NET Standard 2,0 hem .NET Core 2,1 hem de .NET Framework 4.6.1 hedeflemek yerine hedeflenebilir:</span><span class="sxs-lookup"><span data-stu-id="429bc-204">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="429bc-205">Önceki proje dosyası ile aşağıdaki uyarılar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="429bc-205">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="429bc-206">Kitaplık yalnızca etiket yardımcıları içerdiğinden, ASP.NET Core çalıştığı belirli platformları hedeflemek daha basittir: .NET Core ve .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="429bc-206">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="429bc-207">Etiket Yardımcıları, Unity, UWP ve Xamarin gibi diğer .NET Standard 2,0 uyumlu hedef çerçeveler tarafından kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="429bc-207">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="429bc-208">.NET Framework .NET Standard 2,0 ' in kullanılması .NET Framework 4.7.2 giderilen bazı sorunları içerir.</span><span class="sxs-lookup"><span data-stu-id="429bc-208">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="429bc-209">.NET Framework 4.6.1 'yi hedefleyerek .NET Framework 4.6.1 aracılığıyla 4.7.1 kullanarak tüketicilerle ilgili deneyimi geliştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="429bc-209">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="429bc-210">Kitaplığınızın platforma özgü API 'Leri çağırması gerekiyorsa, .NET Standard yerine belirli .NET uygulamalarını hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-210">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="429bc-211">Daha fazla bilgi için bkz. [Çoklu hedefleme](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="429bc-211">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="429bc-212">Değiştirilmemiş bir API kullanma</span><span class="sxs-lookup"><span data-stu-id="429bc-212">Use an API that hasn't changed</span></span>

<span data-ttu-id="429bc-213">.NET Core 2,2 ' den 3,0 ' e bir ara yazılım kitaplığı yükselttiğiniz bir senaryo düşünün.</span><span class="sxs-lookup"><span data-stu-id="429bc-213">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="429bc-214">Kitaplıkta kullanılan ASP.NET Core ara yazılım API 'Leri, ASP.NET Core 2,2 ve 3,0 arasında değişmemiştir.</span><span class="sxs-lookup"><span data-stu-id="429bc-214">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="429bc-215">.NET Core 3,0 ' de ara yazılım kitaplığını desteklemeye devam etmek için aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="429bc-215">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="429bc-216">[Standart kitaplık kılavuzunu](/dotnet/standard/library-guidance/)izleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-216">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="429bc-217">Karşılık gelen derleme paylaşılan çerçevede yoksa her bir API 'nin NuGet paketi için bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-217">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="429bc-218">Değiştirilen bir API kullanma</span><span class="sxs-lookup"><span data-stu-id="429bc-218">Use an API that changed</span></span>

<span data-ttu-id="429bc-219">.NET Core 2,2 ' den .NET Core 3,0 ' ye bir kitaplığı yükseltmekte olduğunuz bir senaryoyu düşünün.</span><span class="sxs-lookup"><span data-stu-id="429bc-219">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="429bc-220">Kitaplıkta kullanılan bir ASP.NET Core API 'SI, ASP.NET Core 3,0 ' de bir [son değişikliğe](/dotnet/core/compatibility/breaking-changes) sahiptir.</span><span class="sxs-lookup"><span data-stu-id="429bc-220">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="429bc-221">Kitaplığın, tüm sürümlerde bozulmuş API 'yi kullanmadan yeniden yazılabilir olup olmayacağını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="429bc-221">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="429bc-222">Kitaplığı yeniden yazabilmeniz için bunu yapın ve paket başvurularıyla daha önceki bir hedef çerçevesini (örneğin, .NET Standard 2,0 veya .NET Framework 4.6.1) hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-222">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="429bc-223">Kitaplığı yeniden yazamıyoruz aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="429bc-223">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="429bc-224">.NET Core 3,0 için bir hedef ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-224">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="429bc-225">`<FrameworkReference>`Paylaşılan çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-225">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="429bc-226">Kodu koşullu olarak derlemek için uygun hedef çerçeve simgesiyle [#if Önişlemci yönergesini](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) kullanın.</span><span class="sxs-lookup"><span data-stu-id="429bc-226">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="429bc-227">Örneğin, HTTP istek ve yanıt akışlarındaki zaman uyumlu okuma ve yazma işlemleri, ASP.NET Core 3,0 itibariyle varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="429bc-227">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="429bc-228">ASP.NET Core 2,2, varsayılan olarak zaman uyumlu davranışı destekler.</span><span class="sxs-lookup"><span data-stu-id="429bc-228">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="429bc-229">Zaman uyumlu okuma ve yazma işlemlerinin, g/ç 'nin gerçekleştiği durumlarda etkinleştirilmesi gereken bir ara yazılım kitaplığı düşünün.</span><span class="sxs-lookup"><span data-stu-id="429bc-229">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="429bc-230">Kitaplık, uygun Önişlemci yönergesinde zaman uyumlu özellikleri etkinleştirmek için kodu içermelidir.</span><span class="sxs-lookup"><span data-stu-id="429bc-230">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="429bc-231">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="429bc-231">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="429bc-232">3,0 ' de tanıtılan bir API kullanma</span><span class="sxs-lookup"><span data-stu-id="429bc-232">Use an API introduced in 3.0</span></span>

<span data-ttu-id="429bc-233">ASP.NET Core 3,0 ' de tanıtılan ASP.NET Core bir API kullanmak istediğinizi düşünün.</span><span class="sxs-lookup"><span data-stu-id="429bc-233">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="429bc-234">Aşağıdaki soruları göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="429bc-234">Consider the following questions:</span></span>

1. <span data-ttu-id="429bc-235">Kitaplık işlevi yeni API 'YI gerektiriyor mu?</span><span class="sxs-lookup"><span data-stu-id="429bc-235">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="429bc-236">Kitaplık bu özelliği farklı bir şekilde uygulayabilir miyim?</span><span class="sxs-lookup"><span data-stu-id="429bc-236">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="429bc-237">Kitaplık işlevi için API gerekiyorsa ve bu, alt düzey uygulamanın bir yolu yoktur:</span><span class="sxs-lookup"><span data-stu-id="429bc-237">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="429bc-238">Yalnızca .NET Core 3. x 'i hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-238">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="429bc-239">`<FrameworkReference>`Paylaşılan çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-239">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="429bc-240">Kitaplık, özelliği farklı bir şekilde uygulayabilirler:</span><span class="sxs-lookup"><span data-stu-id="429bc-240">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="429bc-241">.NET Core 3. x ' i hedef çerçeve olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-241">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="429bc-242">`<FrameworkReference>`Paylaşılan çerçeve için bir öğe ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-242">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="429bc-243">Kodu koşullu olarak derlemek için uygun hedef çerçeve simgesiyle [#if Önişlemci yönergesini](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) kullanın.</span><span class="sxs-lookup"><span data-stu-id="429bc-243">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="429bc-244">Örneğin, aşağıdaki etiket Yardımcısı <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> ASP.NET Core 3,0 ' de tanıtılan arabirimi kullanır.</span><span class="sxs-lookup"><span data-stu-id="429bc-244">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="429bc-245">.NET Core 3,0 ' i hedefleyen tüketiciler, hedef çerçeve sembolü tarafından tanımlanan kod yolunu yürütür `NETCOREAPP3_0` .</span><span class="sxs-lookup"><span data-stu-id="429bc-245">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="429bc-246">Etiket Yardımcısı 'nın Oluşturucu parametre türü, <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> .NET Core 2,1 ve .NET Framework 4.6.1 tüketicileri için olarak değişir.</span><span class="sxs-lookup"><span data-stu-id="429bc-246">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="429bc-247">Bu değişiklik gereklidir çünkü ASP.NET Core 3,0 `IHostingEnvironment` eski olarak işaretlendi ve `IWebHostEnvironment` değiştirme olarak önerilmişti.</span><span class="sxs-lookup"><span data-stu-id="429bc-247">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

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

<span data-ttu-id="429bc-248">Aşağıdaki çok hedefli proje dosyası bu etiket yardımcı senaryosunu destekler:</span><span class="sxs-lookup"><span data-stu-id="429bc-248">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="429bc-249">Paylaşılan çerçeveden kaldırılmış bir API kullanma</span><span class="sxs-lookup"><span data-stu-id="429bc-249">Use an API removed from the shared framework</span></span>

<span data-ttu-id="429bc-250">Paylaşılan çerçeveden kaldırılmış bir ASP.NET Core derlemesi kullanmak için, uygun paket başvurusunu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="429bc-250">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="429bc-251">ASP.NET Core 3,0 ' de paylaşılan çerçeveden kaldırılan paketlerin listesi için bkz. [artık kullanılmayan paket başvurularını kaldır](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="429bc-251">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="429bc-252">Örneğin, Web API istemcisini eklemek için:</span><span class="sxs-lookup"><span data-stu-id="429bc-252">For example, to add the web API client:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="429bc-253">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="429bc-253">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [<span data-ttu-id="429bc-254">.NET uygulama desteği</span><span class="sxs-lookup"><span data-stu-id="429bc-254">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="429bc-255">.NET destek ilkeleri</span><span class="sxs-lookup"><span data-stu-id="429bc-255">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
