---
title: Hosting Paketi
author: rick-anderson
description: .NET Core barındırma paketi 'nin nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: ecf3dd45575390eee263a275e7f1fb9ec50011bb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058447"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="74ee6-103">.NET Core barındırma paketi</span><span class="sxs-lookup"><span data-stu-id="74ee6-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="74ee6-104">.NET Core barındırma paketi, .NET Core çalışma zamanı ve [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module)için bir yükleyicidir.</span><span class="sxs-lookup"><span data-stu-id="74ee6-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="74ee6-105">Paket, ASP.NET Core uygulamaların IIS ile çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="74ee6-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="74ee6-106">.NET Core barındırma paketi 'ni yükler</span><span class="sxs-lookup"><span data-stu-id="74ee6-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="74ee6-107">Barındırma paketi IIS 'den önce yüklendiyse, paket yüklemesi onarılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="74ee6-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="74ee6-108">IIS yükledikten sonra barındırma paketi yükleyicisini yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="74ee6-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="74ee6-109">.NET Core 'un 64 bit (x64) sürümünü yükledikten sonra barındırma paketi yüklenirse, SDK 'lar eksik gibi görünebilir ([hiçbir .NET Core SDK 'sı algılanmadı](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="74ee6-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="74ee6-110">Sorunu çözmek için bkz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> ..</span><span class="sxs-lookup"><span data-stu-id="74ee6-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="74ee6-111">Doğrudan indirme (geçerli sürüm)</span><span class="sxs-lookup"><span data-stu-id="74ee6-111">Direct download (current version)</span></span>

<span data-ttu-id="74ee6-112">Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:</span><span class="sxs-lookup"><span data-stu-id="74ee6-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="74ee6-113">Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)</span><span class="sxs-lookup"><span data-stu-id="74ee6-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="74ee6-114">Yükleyicinin önceki sürümleri</span><span class="sxs-lookup"><span data-stu-id="74ee6-114">Earlier versions of the installer</span></span>

<span data-ttu-id="74ee6-115">Yükleyicinin önceki bir sürümünü elde etmek için:</span><span class="sxs-lookup"><span data-stu-id="74ee6-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="74ee6-116">[.NET Core 'U indir](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="74ee6-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="74ee6-117">İstediğiniz .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="74ee6-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="74ee6-118">**Uygulamaları Çalıştır-çalışma zamanı** sütununda, Istenen .NET Core çalışma zamanı sürümünün satırını bulun.</span><span class="sxs-lookup"><span data-stu-id="74ee6-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="74ee6-119">**Barındırma paketi** bağlantısını kullanarak yükleyiciyi indirin.</span><span class="sxs-lookup"><span data-stu-id="74ee6-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="74ee6-120">Bazı yükleyicilerle yaşam süresi (EOL) gelmiş olan ve artık Microsoft tarafından desteklenmeyen yayın sürümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="74ee6-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="74ee6-121">Daha fazla bilgi için bkz. [destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="74ee6-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="74ee6-122">Barındırma paketini yükler</span><span class="sxs-lookup"><span data-stu-id="74ee6-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="74ee6-123">Yükleyiciyi sunucuda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="74ee6-123">Run the installer on the server.</span></span> <span data-ttu-id="74ee6-124">Aşağıdaki parametreler, yükleyiciyi yönetici komut kabuğu 'ndan çalıştırırken kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="74ee6-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="74ee6-125">`OPT_NO_ANCM=1`: ASP.NET Core modülünü yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="74ee6-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="74ee6-126">`OPT_NO_RUNTIME=1`: .NET Core çalışma zamanını yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="74ee6-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="74ee6-127">Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="74ee6-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="74ee6-128">`OPT_NO_SHAREDFX=1`: ASP.NET paylaşılan çerçevesini (ASP.NET çalışma zamanı) yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="74ee6-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="74ee6-129">Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="74ee6-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="74ee6-130">`OPT_NO_X86=1`: X86 çalışma zamanlarını yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="74ee6-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="74ee6-131">32 bitlik uygulamalar barındırmayabildiğinizi bildiğiniz durumlarda bu parametreyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="74ee6-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="74ee6-132">Gelecekte 32-bit ve 64 bit uygulamaları barındırabilmeniz gereken herhangi bir şansınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="74ee6-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="74ee6-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Paylaşılan yapılandırma ( `applicationHost.config` ), IIS yüklemesiyle aynı makineli olduğunda IIS paylaşılan yapılandırması kullanma denetimini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="74ee6-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="74ee6-134">*Yalnızca ASP.NET Core 2,2 veya sonraki bir sürümü Paketcisi yükleyicilerini barındırmak için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="74ee6-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="74ee6-135">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="74ee6-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="74ee6-136">Sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="74ee6-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="74ee6-137">IIS 'nin yeniden başlatılması, yükleyici tarafından oluşturulan bir ortam değişkeni olan sistem yolunda bir değişiklik seçer.</span><span class="sxs-lookup"><span data-stu-id="74ee6-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="74ee6-138">ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için geri iletme davranışını benimsemez.</span><span class="sxs-lookup"><span data-stu-id="74ee6-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="74ee6-139">Yeni bir barındırma paketi yükleyerek paylaşılan çerçeveyi yükselttikten sonra, sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="74ee6-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="74ee6-140">IIS paylaşılan Yapılandırması hakkında bilgi için bkz. [IIS paylaşılan yapılandırması ile ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="74ee6-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="74ee6-141">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="74ee6-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="74ee6-142">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="74ee6-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="74ee6-143">Barındırma sisteminde öğesine gidin `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .</span><span class="sxs-lookup"><span data-stu-id="74ee6-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="74ee6-144">Dosyayı bulun `aspnetcorev2.dll` .</span><span class="sxs-lookup"><span data-stu-id="74ee6-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="74ee6-145">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="74ee6-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="74ee6-146">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="74ee6-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="74ee6-147">Modülün barındırma paketi yükleyici günlükleri konumunda bulunur `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="74ee6-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="74ee6-148">Dosyanın adı `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , yer tutucunun `{TIMESTAMP}` dosyanın zaman damgası olduğu yerdir.</span><span class="sxs-lookup"><span data-stu-id="74ee6-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
