---
title: Microsoft.AspNetCore.App ASP.NET Core için metapackage
author: Rick-Anderson
description: Microsoft.AspNetCore.App paylaşılan çerçeve
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: b30c90116f5a53ba487f88544514f36e388233d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511385"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="4986b-103">Microsoft.AspNetCore.App ASP.NET Core için</span><span class="sxs-lookup"><span data-stu-id="4986b-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="4986b-104">ASP.NET Core paylaşılan`Microsoft.AspNetCore.App`çerçevesi , Microsoft tarafından geliştirilen ve desteklenen derlemeler içerir.</span><span class="sxs-lookup"><span data-stu-id="4986b-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="4986b-105">`Microsoft.AspNetCore.App`[.NET Core 3.0 veya sonraki SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) yüklendiğinde yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4986b-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="4986b-106">*Paylaşılan çerçeve,* makineye yüklenen *.dll* ve çalışma zamanı bileşeni ve hedefleme paketi içeren derlemeler kümesidir.</span><span class="sxs-lookup"><span data-stu-id="4986b-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="4986b-107">Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.</span><span class="sxs-lookup"><span data-stu-id="4986b-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="4986b-108">SDK'yı `Microsoft.NET.Sdk.Web` hedefleyen projeler çerçeveye dolaylı olarak başvurur. `Microsoft.AspNetCore.App`</span><span class="sxs-lookup"><span data-stu-id="4986b-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="4986b-109">Bu projeler için ek başvuru gerekmez:</span><span class="sxs-lookup"><span data-stu-id="4986b-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="4986b-110">ASP.NET Core paylaşılan çerçeve:</span><span class="sxs-lookup"><span data-stu-id="4986b-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="4986b-111">Üçüncü taraf bağımlılıklarını içermez.</span><span class="sxs-lookup"><span data-stu-id="4986b-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="4986b-112">ASP.NET Core ekibi tarafından desteklenen tüm paketleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4986b-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4986b-113">Bu özellik ASP.NET Core 2.x hedefleme .NET Core 2.x gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4986b-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="4986b-114">ASP.NET Core için [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapaketi:](/dotnet/core/packages#metapackages)</span><span class="sxs-lookup"><span data-stu-id="4986b-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="4986b-115">[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/)ve [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/)dışında üçüncü taraf bağımlılıkları içermez.</span><span class="sxs-lookup"><span data-stu-id="4986b-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="4986b-116">Bu üçüncü taraf bağımlılıkları, ana çerçeve özelliklerinin işlevini sağlamak için gerekli kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="4986b-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="4986b-117">Üçüncü taraf bağımlılıkları (daha önce bahsedilenler dışında) içerenler dışında ASP.NET Çekirdek ekibi tarafından desteklenen tüm paketleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4986b-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="4986b-118">Üçüncü taraf bağımlılıkları (daha önce bahsedilenler dışında) içerenler dışında Entity Framework Core ekibi tarafından desteklenen tüm paketleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4986b-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="4986b-119">ASP.NET Core 2.x ve Entity Framework Core 2.x'in `Microsoft.AspNetCore.App` tüm özellikleri pakete dahildir.</span><span class="sxs-lookup"><span data-stu-id="4986b-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="4986b-120">Core 2.x ASP.NET hedefleyen varsayılan proje şablonları bu paketi kullanır.</span><span class="sxs-lookup"><span data-stu-id="4986b-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="4986b-121">Core 2.x ASP.NET hedefleyen uygulamalar ve Entity Framework Core `Microsoft.AspNetCore.App` 2.x'in paketi kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="4986b-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="4986b-122">`Microsoft.AspNetCore.App` Metapaketin sürüm numarası minimum ASP.NET Core sürümünü ve Entity Framework Core sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="4986b-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="4986b-123">`Microsoft.AspNetCore.App` Metapaketi kullanmak, uygulamanızı koruyan sürüm kısıtlamaları sağlar:</span><span class="sxs-lookup"><span data-stu-id="4986b-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="4986b-124">Bir pakette `Microsoft.AspNetCore.App`geçişli (doğrudan değil) bir bağımlılığa sahip bir paket eklenmişse ve bu sürüm numaraları farklıysa, NuGet bir hata oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4986b-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="4986b-125">Uygulamanıza eklenen diğer paketler, 'de `Microsoft.AspNetCore.App`yer alan paketlerin sürümünü değiştiremez.</span><span class="sxs-lookup"><span data-stu-id="4986b-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="4986b-126">Sürüm tutarlılığı güvenilir bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="4986b-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="4986b-127">`Microsoft.AspNetCore.App`ilgili bitlerin test edilmemiş sürüm birleşimlerinin aynı uygulamada birlikte kullanılmasını önlemek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="4986b-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="4986b-128">`Microsoft.AspNetCore.App` Metapaketi kullanan uygulamalar otomatik olarak ASP.NET Core paylaşılan çerçevesinden yararlanır.</span><span class="sxs-lookup"><span data-stu-id="4986b-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="4986b-129">Meta paketi kullandığınızda, başvurulan ASP.NET Core NuGet paketlerinden **hiçbir** varlık, ASP.NET Core paylaşılan çerçevesinin bu varlıkları içerdiği uygulamayla&mdash;birlikte dağıtılmaz. `Microsoft.AspNetCore.App`</span><span class="sxs-lookup"><span data-stu-id="4986b-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="4986b-130">Paylaşılan çerçevedeki varlıklar, uygulama başlatma süresini iyileştirmek için önceden derlenir.</span><span class="sxs-lookup"><span data-stu-id="4986b-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="4986b-131">Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.</span><span class="sxs-lookup"><span data-stu-id="4986b-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="4986b-132">Aşağıdaki proje dosyası `Microsoft.AspNetCore.App` ASP.NET Core için meta paketine başvurur ve tipik bir ASP.NET Core 2.2 şablonu temsil eder:</span><span class="sxs-lookup"><span data-stu-id="4986b-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="4986b-133">Önceki biçimlendirme tipik bir ASP.NET Core 2.x şablonu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="4986b-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="4986b-134">Paket başvurusu için bir sürüm `Microsoft.AspNetCore.App` numarası belirtmez.</span><span class="sxs-lookup"><span data-stu-id="4986b-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="4986b-135">Sürüm belirtilmediğinde, [Örtülü](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) bir sürüm SDK tarafından belirtilir, `Microsoft.NET.Sdk.Web`yani.</span><span class="sxs-lookup"><span data-stu-id="4986b-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="4986b-136">SDK tarafından belirtilen örtük sürüme güvenmenizi ve sürüm numarasını paket başvurusuna açıkça ayarlamamanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="4986b-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="4986b-137">Bu yaklaşımla ilgili sorularınız varsa, [Microsoft.AspNetCore.App örtülü sürümü için Tartışma](https://github.com/dotnet/AspNetCore.Docs/issues/6430)bir GitHub yorum bırakın.</span><span class="sxs-lookup"><span data-stu-id="4986b-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="4986b-138">Örtük sürüm taşınabilir `major.minor.0` uygulamalar için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4986b-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="4986b-139">Paylaşılan çerçeve roll-forward mekanizması, uygulamayı yüklenen paylaşılan çerçeveler arasında en son uyumlu sürümde çalıştıracaktır.</span><span class="sxs-lookup"><span data-stu-id="4986b-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="4986b-140">Aynı sürümün geliştirme, test ve üretimde kullanıldığını garanti etmek için paylaşılan çerçevenin aynı sürümünün tüm ortamlarda yüklendiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="4986b-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="4986b-141">Kendi kendine yeten uygulamalar için, örtülü `major.minor.patch` sürüm numarası yüklenen SDK'da bulunan paylaşılan çerçevenin olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4986b-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="4986b-142">`Microsoft.AspNetCore.App` Başvuruüzerinde bir sürüm numarası belirtmek, paylaşılan çerçevenin sürümünün seçileceğigaranti **etmez.**</span><span class="sxs-lookup"><span data-stu-id="4986b-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="4986b-143">Örneğin, "2.2.1" sürümünün belirtildiğini, ancak "2.2.3" sürümünün yüklü olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="4986b-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="4986b-144">Bu durumda, uygulama "2.2.3" kullanır.</span><span class="sxs-lookup"><span data-stu-id="4986b-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="4986b-145">Önerilmese de, ileri yuvarlamayı (yama ve/veya küçük) devre dışı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4986b-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="4986b-146">Dotnet ana bilgisayar roll-forward ve davranışını nasıl yapılandırışla ilgili daha fazla bilgi için [dotnet ana bilgisayar adedine](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="4986b-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="4986b-147">`<Project Sdk`örtülü sürümü `Microsoft.AspNetCore.App` `Microsoft.NET.Sdk.Web` kullanacak şekilde ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4986b-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="4986b-148">Ne `<Project Sdk="Microsoft.NET.Sdk">` zaman (izleme `.Web`olmadan) kullanılır:</span><span class="sxs-lookup"><span data-stu-id="4986b-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="4986b-149">Aşağıdaki uyarı oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="4986b-149">The following warning is generated:</span></span>

  <span data-ttu-id="4986b-150">*Uyarı NU1604: Proje bağımlılığı Microsoft.AspNetCore.App kapsayıcı bir alt sınır içermez. Tutarlı geri yükleme sonuçları sağlamak için bağımlılık sürümüne daha düşük bir sınır ekleyin.*</span><span class="sxs-lookup"><span data-stu-id="4986b-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="4986b-151">Bu.NET Core 2.1 SDK ile bilinen bir sorundur.</span><span class="sxs-lookup"><span data-stu-id="4986b-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="4986b-152">ASP.NET Çekirdek güncelleme</span><span class="sxs-lookup"><span data-stu-id="4986b-152">Update ASP.NET Core</span></span>

<span data-ttu-id="4986b-153">`Microsoft.AspNetCore.App` [Meta paket](/dotnet/core/packages#metapackages) NuGet'den güncellenen geleneksel bir paket değildir.</span><span class="sxs-lookup"><span data-stu-id="4986b-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="4986b-154">`Microsoft.NETCore.App`Benzer , `Microsoft.AspNetCore.App` NuGet dışında ele özel sürüm semantik olan paylaşılan bir çalışma süresini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="4986b-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="4986b-155">Daha fazla bilgi için [paketlere, meta paketlere ve çerçevelere](/dotnet/core/packages)bakın.</span><span class="sxs-lookup"><span data-stu-id="4986b-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="4986b-156">Core ASP.NET güncellemek için:</span><span class="sxs-lookup"><span data-stu-id="4986b-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="4986b-157">Geliştirme makineleri ve yapı sunucularında: [.NET Core SDK'yı](https://dotnet.microsoft.com/download)indirin ve kurun.</span><span class="sxs-lookup"><span data-stu-id="4986b-157">On development machines and build servers: Download and install the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="4986b-158">Dağıtım sunucularında: [.NET Core çalışma süresini](https://dotnet.microsoft.com/download)indirin ve kurun.</span><span class="sxs-lookup"><span data-stu-id="4986b-158">On deployment servers: Download and install the [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>

 <span data-ttu-id="4986b-159">Uygulamalar, uygulama nın yeniden başlatılmasında en son yüklenen sürüme doğru iletilir.</span><span class="sxs-lookup"><span data-stu-id="4986b-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="4986b-160">Proje dosyasındaki `Microsoft.AspNetCore.App` sürüm numarasını güncelleştirmek gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4986b-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="4986b-161">Daha fazla bilgi için [Çerçeveye bağımlı uygulamaların ileri ye doğru ilerlemesine](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward)bakın.</span><span class="sxs-lookup"><span data-stu-id="4986b-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="4986b-162">Uygulamanız daha önce `Microsoft.AspNetCore.All`kullanılmışsa, [Microsoft.AspNetCore.All'dan Microsoft.AspNetCore.App'a Geçiş](xref:fundamentals/metapackage#migrate)'e bakın.</span><span class="sxs-lookup"><span data-stu-id="4986b-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
