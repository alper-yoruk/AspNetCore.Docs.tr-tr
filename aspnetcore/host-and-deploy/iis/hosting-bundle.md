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
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343643"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="91def-103">.NET Core barındırma paketi</span><span class="sxs-lookup"><span data-stu-id="91def-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="91def-104">.NET Core barındırma paketi, .NET Core çalışma zamanı ve [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module)için bir yükleyicidir.</span><span class="sxs-lookup"><span data-stu-id="91def-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="91def-105">Paket, ASP.NET Core uygulamaların IIS ile çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="91def-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="91def-106">.NET Core barındırma paketi 'ni yükler</span><span class="sxs-lookup"><span data-stu-id="91def-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="91def-107">Barındırma paketi IIS 'den önce yüklendiyse, paket yüklemesi onarılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="91def-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="91def-108">IIS yükledikten sonra barındırma paketi yükleyicisini yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="91def-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="91def-109">.NET Core 'un 64 bit (x64) sürümünü yükledikten sonra barındırma paketi yüklenirse, SDK 'lar eksik gibi görünebilir ([hiçbir .NET Core SDK 'sı algılanmadı](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="91def-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="91def-110">Sorunu çözmek için bkz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> ..</span><span class="sxs-lookup"><span data-stu-id="91def-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

## <a name="direct-download-current-version"></a><span data-ttu-id="91def-111">Doğrudan indirme (geçerli sürüm)</span><span class="sxs-lookup"><span data-stu-id="91def-111">Direct download (current version)</span></span>

<span data-ttu-id="91def-112">Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:</span><span class="sxs-lookup"><span data-stu-id="91def-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="91def-113">Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)</span><span class="sxs-lookup"><span data-stu-id="91def-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a><span data-ttu-id="91def-114">Yeniden dağıtılabilir gereksinim Visual C++</span><span class="sxs-lookup"><span data-stu-id="91def-114">Visual C++ Redistributable Requirement</span></span>

<span data-ttu-id="91def-115">Windows 'un eski sürümlerinde (örneğin, Windows Server 2012 R2), Visual Studio C++ 2015, 2017, 2019 yeniden dağıtılabilir ' i yükler.</span><span class="sxs-lookup"><span data-stu-id="91def-115">On older versions of Windows, for example Windows Server 2012 R2, install the Visual Studio C++ 2015, 2017, 2019 Redistributable.</span></span> <span data-ttu-id="91def-116">Aksi takdirde, Windows olay günlüğündeki kafa karıştırıcı bir hata iletisi şunu bildirir `The data is the error.`</span><span class="sxs-lookup"><span data-stu-id="91def-116">Otherwise, a confusing error message in the Windows Event Log reports that `The data is the error.`</span></span>

<span data-ttu-id="91def-117">[Geçerli x64 vs C++ yeniden dağıtılabilir](https://aka.ms/vs/16/release/vc_redist.x64.exe) 
 [Geçerli x86 vs C++ yeniden dağıtılabilir](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span><span class="sxs-lookup"><span data-stu-id="91def-117">[Current x64 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Current x86 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span></span>

## <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="91def-118">Yükleyicinin önceki sürümleri</span><span class="sxs-lookup"><span data-stu-id="91def-118">Earlier versions of the installer</span></span>

<span data-ttu-id="91def-119">Yükleyicinin önceki bir sürümünü elde etmek için:</span><span class="sxs-lookup"><span data-stu-id="91def-119">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="91def-120">[.NET Core 'U indir](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="91def-120">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="91def-121">İstediğiniz .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="91def-121">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="91def-122">**Uygulamaları Çalıştır-çalışma zamanı** sütununda, Istenen .NET Core çalışma zamanı sürümünün satırını bulun.</span><span class="sxs-lookup"><span data-stu-id="91def-122">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="91def-123">**Barındırma paketi** bağlantısını kullanarak yükleyiciyi indirin.</span><span class="sxs-lookup"><span data-stu-id="91def-123">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="91def-124">Bazı yükleyicilerle yaşam süresi (EOL) gelmiş olan ve artık Microsoft tarafından desteklenmeyen yayın sürümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="91def-124">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="91def-125">Daha fazla bilgi için bkz. [destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="91def-125">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

## <a name="options"></a><span data-ttu-id="91def-126">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="91def-126">Options</span></span>

1. <span data-ttu-id="91def-127">Aşağıdaki parametreler, yükleyiciyi yönetici komut kabuğu 'ndan çalıştırırken kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="91def-127">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="91def-128">`OPT_NO_ANCM=1`: ASP.NET Core modülünü yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="91def-128">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="91def-129">`OPT_NO_RUNTIME=1`: .NET Core çalışma zamanını yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="91def-129">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="91def-130">Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="91def-130">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="91def-131">`OPT_NO_SHAREDFX=1`: ASP.NET paylaşılan çerçevesini (ASP.NET çalışma zamanı) yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="91def-131">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="91def-132">Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="91def-132">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="91def-133">`OPT_NO_X86=1`: X86 çalışma zamanlarını yüklemeyi atlayın.</span><span class="sxs-lookup"><span data-stu-id="91def-133">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="91def-134">32 bitlik uygulamalar barındırmayabildiğinizi bildiğiniz durumlarda bu parametreyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="91def-134">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="91def-135">Gelecekte 32-bit ve 64 bit uygulamaları barındırabilmeniz gereken herhangi bir şansınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="91def-135">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="91def-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Paylaşılan yapılandırma ( `applicationHost.config` ), IIS yüklemesiyle aynı makineli olduğunda IIS paylaşılan yapılandırması kullanma denetimini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="91def-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="91def-137">*Yalnızca ASP.NET Core 2,2 veya sonraki bir sürümü Paketcisi yükleyicilerini barındırmak için kullanılabilir.*</span><span class="sxs-lookup"><span data-stu-id="91def-137">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="91def-138">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="91def-138">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>

> [!NOTE]
> <span data-ttu-id="91def-139">IIS paylaşılan Yapılandırması hakkında bilgi için bkz. [IIS paylaşılan yapılandırması ile ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="91def-139">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="restart-iis"></a><span data-ttu-id="91def-140">IIS 'yi yeniden Başlat</span><span class="sxs-lookup"><span data-stu-id="91def-140">Restart IIS</span></span>

<span data-ttu-id="91def-141">Barındırma paketi yüklendikten sonra el ile IIS yeniden başlatma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="91def-141">After the Hosting Bundle is installed, a manual IIS restart may be required.</span></span> <span data-ttu-id="91def-142">Örneğin, `dotnet` CLI araçları (komut), IIS çalışan işlemlerinin ÇALıŞTıRıLMASı yolunda bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="91def-142">For example, the `dotnet` CLI tooling (command) might not exist on the PATH for running IIS worker processes.</span></span>

<span data-ttu-id="91def-143">IIS 'yi el ile durdurmak ve başlatmak için, yükseltilmiş bir komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="91def-143">To manually stop and start IIS, execute the following commands in an elevated command shell:</span></span>

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="91def-144">Modül sürümü ve barındırma paketi yükleyici günlükleri</span><span class="sxs-lookup"><span data-stu-id="91def-144">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="91def-145">Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:</span><span class="sxs-lookup"><span data-stu-id="91def-145">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="91def-146">Barındırma sisteminde öğesine gidin `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .</span><span class="sxs-lookup"><span data-stu-id="91def-146">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="91def-147">Dosyayı bulun `aspnetcorev2.dll` .</span><span class="sxs-lookup"><span data-stu-id="91def-147">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="91def-148">Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="91def-148">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="91def-149">**Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="91def-149">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="91def-150">Modülün barındırma paketi yükleyici günlükleri konumunda bulunur `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="91def-150">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="91def-151">Dosyanın adı `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , yer tutucunun `{TIMESTAMP}` dosyanın zaman damgası olduğu yerdir.</span><span class="sxs-lookup"><span data-stu-id="91def-151">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
