---
title: ASP.NET Core 1. x ile 2,0 arasında geçiş yapın
author: scottaddie
description: Bu makalede, ASP.NET Core 1. x projesini ASP.NET Core 2,0 ' ye geçirmek için ön koşullar ve en sık kullanılan adımlar özetlenmektedir.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: migration/1x-to-2x/index
ms.openlocfilehash: 6d67924d87cdbe72cb08c5305dfe45c5b22b31bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057122"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="22cef-103">ASP.NET Core 1. x ile 2,0 arasında geçiş yapın</span><span class="sxs-lookup"><span data-stu-id="22cef-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="22cef-104">[Scott Ade](https://github.com/scottaddie) tarafından</span><span class="sxs-lookup"><span data-stu-id="22cef-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="22cef-105">Bu makalede, mevcut bir ASP.NET Core 1. x projesini ASP.NET Core 2,0 ' ye güncelleştirme konusunda size kılavuzluk ederiz.</span><span class="sxs-lookup"><span data-stu-id="22cef-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="22cef-106">Uygulamanızı ASP.NET Core 2,0 ' ye geçirmek, [birçok yeni özellikten ve performans iyileştirmelerinden](xref:aspnetcore-2.0)yararlanmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="22cef-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="22cef-107">Mevcut ASP.NET Core 1. x uygulamaları sürüme özgü proje şablonlarını temel alınır.</span><span class="sxs-lookup"><span data-stu-id="22cef-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="22cef-108">ASP.NET Core Framework geliştikçe, proje şablonlarını ve bunların içinde yer alan başlangıç kodunu yapın.</span><span class="sxs-lookup"><span data-stu-id="22cef-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="22cef-109">ASP.NET Core çerçevesini güncelleştirmenin yanı sıra, uygulamanız için kodu güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="22cef-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="22cef-110">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="22cef-110">Prerequisites</span></span>

<span data-ttu-id="22cef-111">Bkz. [ASP.NET Core kullanmaya başlama](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="22cef-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="22cef-112">Hedef Framework bilinen adını güncelleştir (tfd)</span><span class="sxs-lookup"><span data-stu-id="22cef-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="22cef-113">.NET Core 'un hedeflediği projeler, .NET Core 2,0 ' den büyük veya buna eşit bir sürümün [tfd](/dotnet/standard/frameworks) 'sini kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="22cef-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="22cef-114">`<TargetFramework>` *. Csproj* dosyasında düğümünü arayın ve iç metnini ile değiştirin `netcoreapp2.0` :</span><span class="sxs-lookup"><span data-stu-id="22cef-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="22cef-115">.NET Framework hedefleyen projeler, .NET Framework 4.6.1 daha büyük veya buna eşit bir sürümün tfd 'sini kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="22cef-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="22cef-116">`<TargetFramework>` *. Csproj* dosyasında düğümünü arayın ve iç metnini ile değiştirin `net461` :</span><span class="sxs-lookup"><span data-stu-id="22cef-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="22cef-117">.NET Core 2,0, .NET Core 1. x ' ten çok daha büyük bir yüzey alanı sunar.</span><span class="sxs-lookup"><span data-stu-id="22cef-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="22cef-118">.NET Core 1. x içinde eksik API 'Ler nedeniyle yalnızca .NET Framework hedefliyorsanız, .NET Core 2,0 ' i hedeflemek büyük olasılıkla işe çalışabilmektedir.</span><span class="sxs-lookup"><span data-stu-id="22cef-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="22cef-119">Proje dosyası içeriyorsa `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>` , [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268)bakın.</span><span class="sxs-lookup"><span data-stu-id="22cef-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="22cef-120">global.jsüzerinde .NET Core SDK sürümü Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="22cef-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="22cef-121">Çözümünüz belirli bir .NET Core SDK sürümünü hedeflemek için dosyadaki bir [global.js](/dotnet/core/tools/global-json) kullanıyorsa, `version` özelliğini makinenizde yüklü 2,0 sürümünü kullanacak şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="22cef-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="22cef-122">Paket başvurularını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="22cef-122">Update package references</span></span>

<span data-ttu-id="22cef-123">1. x projesindeki *. csproj* dosyası, proje tarafından kullanılan her bir NuGet paketini listeler.</span><span class="sxs-lookup"><span data-stu-id="22cef-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="22cef-124">.NET Core 2,0 ' i hedefleyen ASP.NET Core 2,0 projesinde, *. csproj* dosyasındaki tek bir [metapackage](xref:fundamentals/metapackage) başvurusu, paket koleksiyonunun yerini alır:</span><span class="sxs-lookup"><span data-stu-id="22cef-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="22cef-125">ASP.NET Core 2,0 ve Entity Framework Core 2,0 ' nin tüm özellikleri metapackage 'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="22cef-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="22cef-126">.NET Framework hedefleyen ASP.NET Core 2,0 projeleri bireysel NuGet paketlerine başvurmasına devam etmelidir.</span><span class="sxs-lookup"><span data-stu-id="22cef-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="22cef-127">`Version`Her `<PackageReference />` düğümün özniteliğini 2.0.0 olarak güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="22cef-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="22cef-128">Örneğin, `<PackageReference />` tipik bir ASP.NET Core 2,0 proje hedefleme .NET Framework için kullanılan düğümlerin listesi aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="22cef-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="22cef-129">.NET Core CLI araçlarını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="22cef-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="22cef-130">*. Csproj* dosyasında, `Version` her bir `<DotNetCliToolReference />` düğümün özniteliğini 2.0.0 olarak güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="22cef-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="22cef-131">Örneğin, .NET Core 2,0 ' i hedefleyen tipik bir ASP.NET Core 2,0 projesinde kullanılan CLı araçlarının listesi aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="22cef-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="22cef-132">Paket hedefi geri dönüş özelliğini yeniden adlandır</span><span class="sxs-lookup"><span data-stu-id="22cef-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="22cef-133">Bir 1. x projesinin *. csproj* dosyası bir `PackageTargetFallback` düğüm ve değişken kullandı:</span><span class="sxs-lookup"><span data-stu-id="22cef-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="22cef-134">Hem node hem de değişkenini olarak yeniden adlandırın `AssetTargetFallback` :</span><span class="sxs-lookup"><span data-stu-id="22cef-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="22cef-135">Program.cs 'de ana yöntemi Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="22cef-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="22cef-136">1. x projelerinde, `Main` *program.cs* yöntemi şunun gibi aranır:</span><span class="sxs-lookup"><span data-stu-id="22cef-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="22cef-137">2,0 projesinde `Main` *program.cs* yöntemi basitleştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="22cef-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="22cef-138">Bu yeni 2,0 deseninin benimsenmesi önemle önerilir ve [Entity Framework (EF) çekirdek geçişleri](xref:data/ef-mvc/migrations) gibi ürün özellikleri için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="22cef-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="22cef-139">Örneğin, `Update-Database` Paket Yöneticisi konsol penceresinden veya `dotnet ef database update` komut satırından (ASP.NET Core 2,0 ' e dönüştürülen projelerde), şu hatayı üretir:</span><span class="sxs-lookup"><span data-stu-id="22cef-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="22cef-140">Yapılandırma sağlayıcıları Ekle</span><span class="sxs-lookup"><span data-stu-id="22cef-140">Add configuration providers</span></span>

<span data-ttu-id="22cef-141">1. x projelerinde, bir uygulamaya yapılandırma sağlayıcıları eklemek Oluşturucu aracılığıyla gerçekleştirildi `Startup` .</span><span class="sxs-lookup"><span data-stu-id="22cef-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="22cef-142">Öğesinin bir örneğini oluşturma `ConfigurationBuilder` , ilgili sağlayıcıları yükleme (ortam değişkenleri, uygulama ayarları vb.) ve bir üyesini başlatma adımları `IConfigurationRoot` .</span><span class="sxs-lookup"><span data-stu-id="22cef-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="22cef-143">Önceki örnek, `Configuration` üyeyi yapılandırma ayarları ile birlikte, *appsettings.json* herhangi bir appSettings ile yükler *. \<EnvironmentName\>* özelliği ile eşleşen JSON dosyası `IHostingEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="22cef-143">The preceding example loads the `Configuration` member with configuration settings from *appsettings.json* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="22cef-144">Bu dosyaların konumu *Startup.cs* ile aynı yoldur.</span><span class="sxs-lookup"><span data-stu-id="22cef-144">The location of these files is at the same path as *Startup.cs* .</span></span>

<span data-ttu-id="22cef-145">2,0 projesinde, 1. x projelerine devralınan ortak yapılandırma kodu, arka planda çalışır.</span><span class="sxs-lookup"><span data-stu-id="22cef-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="22cef-146">Örneğin, ortam değişkenleri ve uygulama ayarları başlangıçta yüklenir.</span><span class="sxs-lookup"><span data-stu-id="22cef-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="22cef-147">Denk *Startup.cs* kodu, `IConfiguration` eklenen örnekle başlatılacak şekilde azaltılır:</span><span class="sxs-lookup"><span data-stu-id="22cef-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="22cef-148">Tarafından eklenen varsayılan sağlayıcıları kaldırmak için `WebHostBuilder.CreateDefaultBuilder` , `Clear` `IConfigurationBuilder.Sources` içindeki özelliğindeki yöntemi çağırın `ConfigureAppConfiguration` .</span><span class="sxs-lookup"><span data-stu-id="22cef-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="22cef-149">Sağlayıcıları geri eklemek için `ConfigureAppConfiguration` *program.cs* içindeki yöntemi kullanın:</span><span class="sxs-lookup"><span data-stu-id="22cef-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="22cef-150">`CreateDefaultBuilder`Önceki kod parçacığında yöntemi tarafından kullanılan yapılandırma [burada](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152)görülebilir.</span><span class="sxs-lookup"><span data-stu-id="22cef-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="22cef-151">Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="22cef-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="22cef-152">Veritabanı başlatma kodunu taşı</span><span class="sxs-lookup"><span data-stu-id="22cef-152">Move database initialization code</span></span>

<span data-ttu-id="22cef-153">EF Core 1. x kullanan 1. x projelerinde, gibi bir komut `dotnet ef migrations add` şunları yapar:</span><span class="sxs-lookup"><span data-stu-id="22cef-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="22cef-154">Bir `Startup` örneği başlatır</span><span class="sxs-lookup"><span data-stu-id="22cef-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="22cef-155">`ConfigureServices`Bağımlılık ekleme (türler dahil) tüm hizmetleri kaydetmek için yöntemini çağırır `DbContext`</span><span class="sxs-lookup"><span data-stu-id="22cef-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="22cef-156">Önkoşul görevlerini gerçekleştirir</span><span class="sxs-lookup"><span data-stu-id="22cef-156">Performs its requisite tasks</span></span>

<span data-ttu-id="22cef-157">EF Core 2,0 kullanan 2,0 projesinde `Program.BuildWebHost` uygulama hizmetleri elde etmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="22cef-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="22cef-158">1. x aksine, bu, çağırma için ek yan etkiye sahiptir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="22cef-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="22cef-159">1. x uygulamanız yönteminde veritabanı başlatma kodu çağırırdı `Configure` , beklenmeyen sorunlar meydana gelebilir.</span><span class="sxs-lookup"><span data-stu-id="22cef-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="22cef-160">Örneğin, veritabanı henüz yoksa, dengeli dağıtım kodu EF Core geçiş komutu yürütmeden önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="22cef-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="22cef-161">Bu sorun `dotnet ef migrations list` , veritabanı henüz yoksa bir komutun başarısız olmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="22cef-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="22cef-162">Startup.cs yönteminde aşağıdaki 1. x çekirdek başlatma kodunu göz önünde bulundurun `Configure` : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="22cef-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="22cef-163">2,0 projesinde, `SeedData.Initialize` çağrıyı `Main` *program.cs* yöntemine taşıyın:</span><span class="sxs-lookup"><span data-stu-id="22cef-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="22cef-164">2,0 itibariyle, `BuildWebHost` Web konağını derleme ve yapılandırma dışında içinde herhangi bir şey yapmak hatalı bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="22cef-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="22cef-165">Uygulamayı çalıştırmaya ilişkin her şey, `BuildWebHost` &mdash; genellikle `Main` *program.cs* yönteminde dışında işlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="22cef-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs* .</span></span>

<a name="view-compilation"></a>

## <a name="review-no-locrazor-view-compilation-setting"></a><span data-ttu-id="22cef-166">RazorGörünüm derleme ayarını gözden geçir</span><span class="sxs-lookup"><span data-stu-id="22cef-166">Review Razor view compilation setting</span></span>

<span data-ttu-id="22cef-167">Daha hızlı uygulama başlangıç süresi ve daha küçük yayımlanmış paketleri size en önemli öneme sahiptir.</span><span class="sxs-lookup"><span data-stu-id="22cef-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="22cef-168">Bu nedenlerden dolayı, ASP.NET Core 2,0 ' de [ Razor derleme görünümü](xref:mvc/views/view-compilation) varsayılan olarak etkinleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="22cef-168">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="22cef-169">`MvcRazorCompileOnPublish`Özelliği true olarak ayarlamak artık gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="22cef-169">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="22cef-170">Görünümü derlemeyi devre dışı bırakmadığınız takdirde, özelliği *. csproj* dosyasından kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="22cef-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="22cef-171">.NET Framework hedeflenirken, yine de [Microsoft. AspNetCore. Mvc öğesine açıkça başvurmanız gerekir. Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) *. Csproj* dosyanızdaki viewcompilation NuGet paketi:</span><span class="sxs-lookup"><span data-stu-id="22cef-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="22cef-172">Application Insights "hafif" özelliklere güvenin</span><span class="sxs-lookup"><span data-stu-id="22cef-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="22cef-173">Uygulama performansı izleme için daha kolay bir kurulum önemlidir.</span><span class="sxs-lookup"><span data-stu-id="22cef-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="22cef-174">Artık, Visual Studio 2017 Tooling ' de bulunan yeni [Application Insights](/azure/application-insights/app-insights-overview) "hafif" özelliklerine güvenebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="22cef-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="22cef-175">Visual Studio 2017 ' de oluşturulan ASP.NET Core 1,1 projeleri varsayılan olarak Application Insights eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="22cef-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="22cef-176">Application Insights SDK 'yı doğrudan *program.cs* ve *Startup.cs* dışında kullanmıyorsanız, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="22cef-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs* , follow these steps:</span></span>

1. <span data-ttu-id="22cef-177">.NET Core 'u hedefliyorsanız, `<PackageReference />` *. csproj* dosyasından aşağıdaki düğümü kaldırın:</span><span class="sxs-lookup"><span data-stu-id="22cef-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="22cef-178">.NET Core 'u hedefliyorsanız, `UseApplicationInsights` *program.cs* öğesinden genişletme yöntemi çağrısını kaldırın:</span><span class="sxs-lookup"><span data-stu-id="22cef-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs* :</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="22cef-179">*_Layout. cshtml* 'den Application Insights ISTEMCI tarafı API çağrısını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="22cef-179">Remove the Application Insights client-side API call from *_Layout.cshtml* .</span></span> <span data-ttu-id="22cef-180">Aşağıdaki iki satır kodu içerir:</span><span class="sxs-lookup"><span data-stu-id="22cef-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="22cef-181">Application Insights SDK 'Yı doğrudan kullanıyorsanız, bunu yapmaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="22cef-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="22cef-182">2,0 [metapackage](xref:fundamentals/metapackage) , en son Application Insights sürümünü içerir, bu nedenle eski bir sürüme başvursanız bir paket düşürme hatası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="22cef-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationno-locidentity-improvements"></a><span data-ttu-id="22cef-183">Kimlik doğrulaması/ Identity iyileştirmeleri benimseyin</span><span class="sxs-lookup"><span data-stu-id="22cef-183">Adopt authentication/Identity improvements</span></span>

<span data-ttu-id="22cef-184">ASP.NET Core 2,0 ' de yeni bir kimlik doğrulama modeli ve bazı önemli değişiklikler vardır ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="22cef-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="22cef-185">Projenizi ayrı ayrı kullanıcı hesaplarıyla oluşturduysanız veya el ile kimlik doğrulaması eklediyseniz veya Identity [ Identity ASP.NET Core 2,0 ' ye](xref:migration/1x-to-2x/identity-2x)bakın.</span><span class="sxs-lookup"><span data-stu-id="22cef-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22cef-186">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="22cef-186">Additional resources</span></span>

* [<span data-ttu-id="22cef-187">ASP.NET Core 2,0 ' deki Son değişiklikler</span><span class="sxs-lookup"><span data-stu-id="22cef-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
