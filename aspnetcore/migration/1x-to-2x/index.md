---
title: ASP.NET Core 1.x'ten 2.0'a geçirin
author: scottaddie
description: Bu makalede, core 2.0'ASP.NET ASP.NET Core 1.x projesinin geçişinin ön koşulları ve en yaygın adımları özetgündür.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/1x-to-2x/index
ms.openlocfilehash: c46f50a418cf630980ac2ba94407e4370d36e7d5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667617"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="40db7-103">ASP.NET Core 1.x'ten 2.0'a geçirin</span><span class="sxs-lookup"><span data-stu-id="40db7-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="40db7-104">Yazar: [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="40db7-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="40db7-105">Bu makalede, core 2.0 ASP.NET mevcut bir ASP.NET Core 1.x proje güncelleme ile size yol.</span><span class="sxs-lookup"><span data-stu-id="40db7-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="40db7-106">Uygulamanızı core 2.0'a ASP.NET geçirmek birçok [yeni özellik ve performans iyileştirmelerinden](xref:aspnetcore-2.0)yararlanmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="40db7-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="40db7-107">Varolan ASP.NET Core 1.x uygulamaları, sürüme özgü proje şablonları temel alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="40db7-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="40db7-108">ASP.NET Çekirdek çerçevesi geliştikçe, proje şablonları ve bunların içinde bulunan başlangıç kodu da gelişir.</span><span class="sxs-lookup"><span data-stu-id="40db7-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="40db7-109">ASP.NET Core çerçevesini güncelleştirmenin yanı sıra, uygulamanızın kodunu güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="40db7-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="40db7-110">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="40db7-110">Prerequisites</span></span>

<span data-ttu-id="40db7-111">Bkz. [ASP.NET Core ile Başlayın.](xref:getting-started)</span><span class="sxs-lookup"><span data-stu-id="40db7-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="40db7-112">Hedef Çerçeve Takma Aparatı (TFM) Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="40db7-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="40db7-113">.NET Core'u hedefleyen projeler, .NET Core 2.0'dan büyük veya eşit bir sürümün [TFM'sini](/dotnet/standard/frameworks) kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="40db7-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="40db7-114">*.csproj* dosyasındaki `<TargetFramework>` düğümü arayın ve iç metnini `netcoreapp2.0`şuyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="40db7-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="40db7-115">.NET Framework'ü hedefleyen projeler, .NET Framework 4.6.1'den daha büyük veya eşit bir sürümün TFM'sini kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="40db7-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="40db7-116">*.csproj* dosyasındaki `<TargetFramework>` düğümü arayın ve iç metnini `net461`şuyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="40db7-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="40db7-117">.NET Core 2.0 ,NET Core 1.x'ten çok daha büyük bir yüzey alanı sunar.</span><span class="sxs-lookup"><span data-stu-id="40db7-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="40db7-118">.NET Framework'ü yalnızca .NET Core 1.x'teki eksik API'ler nedeniyle hedefliyorsanız, .NET Core 2.0'ı hedeflemek olasıdır.</span><span class="sxs-lookup"><span data-stu-id="40db7-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="40db7-119">Proje dosyası içeriyorsa, `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`bu [GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268)bakın.</span><span class="sxs-lookup"><span data-stu-id="40db7-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="40db7-120">global.json'da .NET Core SDK sürümünü güncelleyin</span><span class="sxs-lookup"><span data-stu-id="40db7-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="40db7-121">Çözümünüz belirli bir .NET Core SDK sürümünü hedeflemek için [global.json](/dotnet/core/tools/global-json) dosyasına güveniyorsa, makinenizde yüklü olan 2.0 sürümünü kullanmak için özelliğini `version` güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="40db7-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="40db7-122">Paket referanslarını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="40db7-122">Update package references</span></span>

<span data-ttu-id="40db7-123">1.x projedeki *.csproj* dosyası, proje tarafından kullanılan her NuGet paketini listeler.</span><span class="sxs-lookup"><span data-stu-id="40db7-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="40db7-124">.NET Core 2.0'ı hedefleyen ASP.NET Core 2.0 projesinde, *.csproj* dosyasındaki tek bir [metapaket](xref:fundamentals/metapackage) başvurusu, paketlerin toplanmasının yerini alır:</span><span class="sxs-lookup"><span data-stu-id="40db7-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="40db7-125">ASP.NET Core 2.0 ve Entity Framework Core 2.0'ın tüm özellikleri meta pakete dahildir.</span><span class="sxs-lookup"><span data-stu-id="40db7-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="40db7-126">ASP.NET.NET Framework'u hedefleyen Core 2.0 projeleri tek tek NuGet paketlerine başvurmaya devam etmelidir.</span><span class="sxs-lookup"><span data-stu-id="40db7-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="40db7-127">Her `<PackageReference />` `Version` düğümün özniteliğini 2.0.0 olarak güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="40db7-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="40db7-128">Örneğin, .NET Framework'ü `<PackageReference />` hedefleyen tipik bir ASP.NET Core 2.0 projesinde kullanılan düğümlerin listesi aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="40db7-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="40db7-129">Güncelleme .NET Core CLI araçları</span><span class="sxs-lookup"><span data-stu-id="40db7-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="40db7-130">*.csproj* dosyasında, her `Version` `<DotNetCliToolReference />` düğümün özniteliğini 2.0.0 olarak güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="40db7-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="40db7-131">Örneğin, .NET Core 2.0'ı hedefleyen tipik bir ASP.NET Core 2.0 projesinde kullanılan CLI araçlarının listesi aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="40db7-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="40db7-132">Paket Hedef Geri Dönüş özelliğini yeniden adlandırın</span><span class="sxs-lookup"><span data-stu-id="40db7-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="40db7-133">1.x projesinin *.csproj* dosyasında `PackageTargetFallback` düğüm ve değişken kullanılmıştır:</span><span class="sxs-lookup"><span data-stu-id="40db7-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="40db7-134">Düğümü ve değişkeni yeniden `AssetTargetFallback`adlandırın:</span><span class="sxs-lookup"><span data-stu-id="40db7-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="40db7-135">ana yöntemi Program.cs güncelleştir</span><span class="sxs-lookup"><span data-stu-id="40db7-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="40db7-136">1.x projelerde `Main` *Program.cs* yöntemi şu na benzer:</span><span class="sxs-lookup"><span data-stu-id="40db7-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="40db7-137">2.0 projelerde `Main` *Program.cs* yöntemi basitleştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="40db7-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="40db7-138">Bu yeni 2.0 deseninin benimsenmesi şiddetle tavsiye edilir ve [Entity Framework (EF) Çekirdek Geçişler](xref:data/ef-mvc/migrations) gibi ürün özelliklerinin çalışması için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="40db7-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="40db7-139">Örneğin, Paket `Update-Database` Yöneticisi Konsolu penceresinden veya `dotnet ef database update` komut satırından (ASP.NET Core 2.0'a dönüştürülen projelerde) çalışan aşağıdaki hatayı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="40db7-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="40db7-140">Yapılandırma sağlayıcıları ekleme</span><span class="sxs-lookup"><span data-stu-id="40db7-140">Add configuration providers</span></span>

<span data-ttu-id="40db7-141">1.x projelerde, yapılandırma sağlayıcılarının bir uygulamaya `Startup` eklenmesi yapıcı aracılığıyla gerçekleştirildi.</span><span class="sxs-lookup"><span data-stu-id="40db7-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="40db7-142">Adımlar, uygulanabilir sağlayıcıların `ConfigurationBuilder`(ortam değişkenleri, uygulama ayarları, vb.) bir örneğini `IConfigurationRoot`oluşturmayı ve bir üyenin başlatılmasını içeriyordu.</span><span class="sxs-lookup"><span data-stu-id="40db7-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="40db7-143">Önceki örnek, üyeyi `Configuration` *appsettings.json'dan* ve herhangi *bir uygulama\< ayarlarından yapılandırma ayarlarıyla yükler. EnvironmentName\>.json* dosyası `IHostingEnvironment.EnvironmentName` özelliği yle eşleşmektedir.</span><span class="sxs-lookup"><span data-stu-id="40db7-143">The preceding example loads the `Configuration` member with configuration settings from *appsettings.json* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="40db7-144">Bu dosyaların konumu *Startup.cs*ile aynı yolda.</span><span class="sxs-lookup"><span data-stu-id="40db7-144">The location of these files is at the same path as *Startup.cs*.</span></span>

<span data-ttu-id="40db7-145">2.0 projelerinde, 1.x projelerinin doğasında bulunan ortak yapılandırma kodu perde arkasında çalışır.</span><span class="sxs-lookup"><span data-stu-id="40db7-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="40db7-146">Örneğin, ortam değişkenleri ve uygulama ayarları başlangıçta yüklenir.</span><span class="sxs-lookup"><span data-stu-id="40db7-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="40db7-147">Eşdeğer *Startup.cs* kodu enjekte `IConfiguration` edilen örnekle başlatmaya indirgenir:</span><span class="sxs-lookup"><span data-stu-id="40db7-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="40db7-148">Tarafından eklenen varsayılan sağlayıcıları `WebHostBuilder.CreateDefaultBuilder`kaldırmak `Clear` için, `IConfigurationBuilder.Sources` içindeki özellik `ConfigureAppConfiguration`te yöntemi ni çağırın.</span><span class="sxs-lookup"><span data-stu-id="40db7-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="40db7-149">Sağlayıcıları geri eklemek için, `ConfigureAppConfiguration` *Program.cs*yöntemini kullanmak:</span><span class="sxs-lookup"><span data-stu-id="40db7-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="40db7-150">Önceki kod snippet `CreateDefaultBuilder` yöntemi tarafından kullanılan yapılandırma [burada](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152)görülebilir.</span><span class="sxs-lookup"><span data-stu-id="40db7-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="40db7-151">Daha fazla bilgi için [ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="40db7-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="40db7-152">Veritabanı başlatma kodunu taşıma</span><span class="sxs-lookup"><span data-stu-id="40db7-152">Move database initialization code</span></span>

<span data-ttu-id="40db7-153">EF Core 1.x'i kullanan 1.x `dotnet ef migrations add` projelerinde aşağıdaki gibi bir komut oluşur:</span><span class="sxs-lookup"><span data-stu-id="40db7-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="40db7-154">Bir `Startup` örneği anında</span><span class="sxs-lookup"><span data-stu-id="40db7-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="40db7-155">Bağımlılık enjeksiyonu `ConfigureServices` `DbContext` (türleri dahil) ile tüm hizmetleri kaydetmek için yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="40db7-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="40db7-156">Gerekli görevleri gerçekleştirir</span><span class="sxs-lookup"><span data-stu-id="40db7-156">Performs its requisite tasks</span></span>

<span data-ttu-id="40db7-157">EF Core 2.0 kullanan 2.0 projelerde, `Program.BuildWebHost` uygulama hizmetlerini almak için çağrılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="40db7-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="40db7-158">1.x aksine, bu çağıran ek yan `Startup.Configure`etkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="40db7-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="40db7-159">1.x uygulamanız yönteminde `Configure` veritabanı başlatma kodunu çağırırsa, beklenmeyen sorunlar oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="40db7-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="40db7-160">Örneğin, veritabanı henüz yoksa, tohumlama kodu EF Çekirdek Geçişleri komut yürütme önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="40db7-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="40db7-161">Bu sorun, `dotnet ef migrations list` veritabanı henüz yoksa bir komutun başarısız olmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="40db7-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="40db7-162">`Configure` *Startup.cs*yönteminde aşağıdaki 1.x tohum başlatma kodunu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="40db7-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="40db7-163">2.0 projelerde, `SeedData.Initialize` çağrıyı `Main` *Program.cs*yöntemine taşıyın:</span><span class="sxs-lookup"><span data-stu-id="40db7-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="40db7-164">2.0 itibariyle, web barındırma oluşturmak ve `BuildWebHost` yapılandırmak dışında bir şey yapmak kötü bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="40db7-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="40db7-165">Uygulamayı çalıştırmakla ilgili her *şey,* Program.cs `BuildWebHost` &mdash; `Main` yönteminde genellikle dışında ele alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="40db7-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs*.</span></span>

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a><span data-ttu-id="40db7-166">Razor görünüm derleme ayarını gözden geçirin</span><span class="sxs-lookup"><span data-stu-id="40db7-166">Review Razor view compilation setting</span></span>

<span data-ttu-id="40db7-167">Daha hızlı uygulama başlangıç süresi ve daha küçük yayınlanan demetleri sizin için son derece önemlidir.</span><span class="sxs-lookup"><span data-stu-id="40db7-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="40db7-168">Bu nedenlerden dolayı, [Razor görünüm derlemesi](xref:mvc/views/view-compilation) varsayılan olarak ASP.NET Core 2.0'da etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="40db7-168">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="40db7-169">Özelliği `MvcRazorCompileOnPublish` niçin doğru ayarlanın artık gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="40db7-169">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="40db7-170">Görünüm derlemesini devre dışı bırakmadığınız sürece, özellik *.csproj* dosyasından kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="40db7-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="40db7-171">.NET Framework'ü hedef alırken, *.csproj* dosyanızda [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet paketine açıkça başvurmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="40db7-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="40db7-172">Uygulama Öngörüleri 'ışık-up" özelliklerine güvenme</span><span class="sxs-lookup"><span data-stu-id="40db7-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="40db7-173">Uygulama performans enstrümantasyonunun zahmetsizce kurulumu önemlidir.</span><span class="sxs-lookup"><span data-stu-id="40db7-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="40db7-174">Artık Visual Studio 2017 aracında bulunan yeni [Application Insights](/azure/application-insights/app-insights-overview) "Light-up" özelliklerine güvenebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="40db7-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="40db7-175">Visual Studio 2017'de oluşturulan ASP.NET Core 1.1 projeleri varsayılan olarak Application Insights'ı ekledi.</span><span class="sxs-lookup"><span data-stu-id="40db7-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="40db7-176">Uygulama Öngörüleri SDK'yı *doğrudan, Program.cs* ve *Startup.cs*dışında kullanmıyorsanız aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="40db7-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs*, follow these steps:</span></span>

1. <span data-ttu-id="40db7-177">.NET Core'u hedefliyorsanız, `<PackageReference />` *.csproj* dosyasından aşağıdaki düğümü kaldırın:</span><span class="sxs-lookup"><span data-stu-id="40db7-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="40db7-178">.NET Core'u hedefliyorsanız, `UseApplicationInsights` *Program.cs*uzantı yöntemini kaldırın:</span><span class="sxs-lookup"><span data-stu-id="40db7-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs*:</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="40db7-179">*_Layout.cshtml'den*Application Insights istemci tarafındaki API çağrısını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="40db7-179">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="40db7-180">Aşağıdaki iki kod satırından oluşur:</span><span class="sxs-lookup"><span data-stu-id="40db7-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="40db7-181">Doğrudan Application Insights SDK kullanıyorsanız, bunu yapmaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="40db7-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="40db7-182">2.0 [metapaketi](xref:fundamentals/metapackage) Application Insights'ın en son sürümünü içerir, bu nedenle eski bir sürüme başvuruyorsanız bir paket düşürme hatası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="40db7-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a><span data-ttu-id="40db7-183">Kimlik doğrulama/kimlik iyileştirmelerini benimseme</span><span class="sxs-lookup"><span data-stu-id="40db7-183">Adopt authentication/Identity improvements</span></span>

<span data-ttu-id="40db7-184">ASP.NET Core 2.0 yeni bir kimlik doğrulama modeli ve ASP.NET Çekirdek Kimlik önemli değişiklikler bir dizi vardır.</span><span class="sxs-lookup"><span data-stu-id="40db7-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="40db7-185">Projenizi Bireysel Kullanıcı Hesapları etkinleştirilmiş olarak oluşturduysanız veya el ile kimlik doğrulama veya kimlik eklediyseniz, [Bkz. ASP.NET Core 2.0'a Kimlik Doğrulamayı ve Kimliği Geçir](xref:migration/1x-to-2x/identity-2x).</span><span class="sxs-lookup"><span data-stu-id="40db7-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40db7-186">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="40db7-186">Additional resources</span></span>

* [<span data-ttu-id="40db7-187">ASP.NET Core 2.0'daki Son Değişiklikler</span><span class="sxs-lookup"><span data-stu-id="40db7-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
