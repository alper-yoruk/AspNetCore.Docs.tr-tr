---
title: Microsoft.AspNetCore.ASP.NET Core 2.0 için tüm metapackage
author: Rick-Anderson
description: Microsoft.AspNetCore.All metapackage ASP.NET Core 2.1 ve sonrası için tavsiye edilmez.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: cc00c075909da5c17a4aa2fd252c9e662e5a0fc9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511073"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="45b81-103">Microsoft.AspNetCore.ASP.NET Core 2.0 için tüm metapackage</span><span class="sxs-lookup"><span data-stu-id="45b81-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="45b81-104">Metapaket `Microsoft.AspNetCore.All` ASP.NET Core 3.0 ve sonraki dahildir.</span><span class="sxs-lookup"><span data-stu-id="45b81-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="45b81-105">Daha fazla bilgi için [bu GitHub sorununa](https://github.com/aspnet/Announcements/issues/314)bakın.</span><span class="sxs-lookup"><span data-stu-id="45b81-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="45b81-106">Core 2.1ASP.NET hedefleyen uygulamaları öneririz ve daha sonra bu paket yerine [Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app) kullanırız.</span><span class="sxs-lookup"><span data-stu-id="45b81-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="45b81-107">Bu makalede [Bkz. Microsoft.AspNetCore.All'dan Microsoft.AspNetCore.App'e geçiş.](#migrate)</span><span class="sxs-lookup"><span data-stu-id="45b81-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="45b81-108">Bu özellik ASP.NET Core 2.x hedefleme .NET Core 2.x gerektirir.</span><span class="sxs-lookup"><span data-stu-id="45b81-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="45b81-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) paylaşılan bir çerçeveye başvuran bir metapakettir.</span><span class="sxs-lookup"><span data-stu-id="45b81-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="45b81-110">*Paylaşılan çerçeve,* uygulamanın klasörlerinde *.dll* olmayan bir derlemeler kümesidir.</span><span class="sxs-lookup"><span data-stu-id="45b81-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="45b81-111">Uygulamayı çalıştırmak için paylaşılan çerçevenin makineye yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="45b81-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="45b81-112">Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.</span><span class="sxs-lookup"><span data-stu-id="45b81-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="45b81-113">`Microsoft.AspNetCore.All` Başvuran paylaşılan çerçeve şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="45b81-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="45b81-114">tüm desteklenmiş paketler ASP.NET Core ekibi tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="45b81-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="45b81-115">Tüm desteklenen paketler Entity Framework Core tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="45b81-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="45b81-116">ASP.NET Çekirdek ve Varlık Çerçeve Çekirdeği tarafından kullanılan iç ve üçüncü taraf bağımlılıkları.</span><span class="sxs-lookup"><span data-stu-id="45b81-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="45b81-117">ASP.NET Core 2.x ve Entity Framework Core 2.x'in `Microsoft.AspNetCore.All` tüm özellikleri pakete dahildir.</span><span class="sxs-lookup"><span data-stu-id="45b81-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="45b81-118">Core 2.0 ASP.NET hedefleyen varsayılan proje şablonları bu paketi kullanır.</span><span class="sxs-lookup"><span data-stu-id="45b81-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="45b81-119">`Microsoft.AspNetCore.All` Metapaketin sürüm numarası minimum ASP.NET Core sürümünü ve Entity Framework Core sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="45b81-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="45b81-120">Aşağıdaki *.csproj* dosyası `Microsoft.AspNetCore.All` ASP.NET Core için meta paketine başvurur:</span><span class="sxs-lookup"><span data-stu-id="45b81-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="45b81-121">Örtük sürüm</span><span class="sxs-lookup"><span data-stu-id="45b81-121">Implicit versioning</span></span>

<span data-ttu-id="45b81-122">ASP.NET Core 2.1 veya sonraki sürümlerde paket başvurularını `Microsoft.AspNetCore.All` belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="45b81-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="45b81-123">Sürüm belirtilmediği zaman, Örtülü bir sürüm SDK`Microsoft.NET.Sdk.Web`( ) tarafından belirtilir.</span><span class="sxs-lookup"><span data-stu-id="45b81-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="45b81-124">SDK tarafından belirtilen örtük sürüme güvenmenizi ve sürüm numarasını paket başvurusuna açıkça ayarlamamanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="45b81-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="45b81-125">Bu yaklaşım la ilgili sorularınız varsa, [Microsoft.AspNetCore.App örtülü sürümü için Tartışma](https://github.com/dotnet/AspNetCore.Docs/issues/6430)bir GitHub yorum bırakın.</span><span class="sxs-lookup"><span data-stu-id="45b81-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="45b81-126">Örtük sürüm taşınabilir `major.minor.0` uygulamalar için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="45b81-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="45b81-127">Paylaşılan çerçeve roll-forward mekanizması, uygulamayı yüklenen paylaşılan çerçeveler arasında en son uyumlu sürümde çalıştırıyor.</span><span class="sxs-lookup"><span data-stu-id="45b81-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="45b81-128">Aynı sürümün geliştirme, test ve üretimde kullanıldığını garanti etmek için paylaşılan çerçevenin aynı sürümünün tüm ortamlarda yüklendiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="45b81-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="45b81-129">Bağımsız uygulamalar için örtük sürüm numarası, `major.minor.patch` yüklenen SDK'da bulunan paylaşılan çerçevenin bölümüne ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="45b81-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="45b81-130">`Microsoft.AspNetCore.All` Paket başvurusuüzerinde bir sürüm numarası belirtmek, paylaşılan çerçevenin sürümünün seçildiğini garanti **etmez.**</span><span class="sxs-lookup"><span data-stu-id="45b81-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="45b81-131">Örneğin, "2.1.1" sürümünün belirtildiğini, ancak "2.1.3" sürümünün yüklü olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="45b81-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="45b81-132">Bu durumda, uygulama "2.1.3" kullanır.</span><span class="sxs-lookup"><span data-stu-id="45b81-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="45b81-133">Önerilmese de, ileri yuvarlamayı (yama ve/veya küçük) devre dışı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="45b81-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="45b81-134">Dotnet ana bilgisayar roll-forward ve davranışını nasıl yapılandırışla ilgili daha fazla bilgi için [dotnet ana bilgisayar adedine](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="45b81-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="45b81-135">Projenin SDK'sı, `Microsoft.NET.Sdk.Web` `Microsoft.AspNetCore.All`'nin örtülü sürümünü kullanmak için proje dosyasında ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="45b81-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="45b81-136">`Microsoft.NET.Sdk` SDK belirtildiğinde (proje`<Project Sdk="Microsoft.NET.Sdk">` dosyasının üst kısmında), aşağıdaki uyarı oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="45b81-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="45b81-137">*Uyarı NU1604: Proje bağımlılığı Microsoft.AspNetCore.All kapsayıcı bir alt sınır içermez. Tutarlı geri yükleme sonuçları sağlamak için bağımlılık sürümüne daha düşük bir sınır ekleyin.*</span><span class="sxs-lookup"><span data-stu-id="45b81-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="45b81-138">Bu.NET Core 2.1 SDK ile bilinen bir sorundur ve .NET Core 2.2 SDK'da düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="45b81-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="45b81-139">Microsoft.AspNetCore.All'dan Microsoft.AspNetCore.App'e Geçiş</span><span class="sxs-lookup"><span data-stu-id="45b81-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="45b81-140">Aşağıdaki paketler pakete `Microsoft.AspNetCore.All` dahil edilir, ancak pakete `Microsoft.AspNetCore.App` dahil edilmez.</span><span class="sxs-lookup"><span data-stu-id="45b81-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

<span data-ttu-id="45b81-141">`Microsoft.AspNetCore.All` Uygulamanız yukarıdaki paketlerden veya bu paketler tarafından getirilen paketlerden herhangi bir API kullanıyorsa, projenizdeki paketlere referanslar `Microsoft.AspNetCore.App`ekleyin.</span><span class="sxs-lookup"><span data-stu-id="45b81-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="45b81-142">Önceki paketlerin bağımlılıkları dolaylı `Microsoft.AspNetCore.App` olarak dahil edilmez.</span><span class="sxs-lookup"><span data-stu-id="45b81-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="45b81-143">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="45b81-143">For example:</span></span>

* <span data-ttu-id="45b81-144">`StackExchange.Redis`bir bağımlılık olarak`Microsoft.Extensions.Caching.Redis`</span><span class="sxs-lookup"><span data-stu-id="45b81-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="45b81-145">`Microsoft.ApplicationInsights`bir bağımlılık olarak`Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span><span class="sxs-lookup"><span data-stu-id="45b81-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="45b81-146">Core 2.1ASP.NET güncelleştir</span><span class="sxs-lookup"><span data-stu-id="45b81-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="45b81-147">Meta pakete `Microsoft.AspNetCore.App` 2.1 ve sonrası için geçiş yapmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="45b81-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="45b81-148">`Microsoft.AspNetCore.All` Metapaketi kullanmaya devam etmek ve en son yama sürümünün dağıtıldığından emin olmak için:</span><span class="sxs-lookup"><span data-stu-id="45b81-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="45b81-149">Geliştirme makineleri ve yapı sunucularında: En son [.NET Core SDK'yı](https://dotnet.microsoft.com/download)yükleyin.</span><span class="sxs-lookup"><span data-stu-id="45b81-149">On development machines and build servers: Install the latest [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="45b81-150">Dağıtım sunucularında: En son [.NET Core çalışma süresini](https://dotnet.microsoft.com/download)yükleyin.</span><span class="sxs-lookup"><span data-stu-id="45b81-150">On deployment servers: Install the latest [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>
 <span data-ttu-id="45b81-151">Uygulamanız, uygulamanın yeniden başlatılmasında en son yüklenen sürüme geçecektir.</span><span class="sxs-lookup"><span data-stu-id="45b81-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
