---
title: Azure App Service ve IIS 'de ASP.NET Core sorunlarını giderme
author: rick-anderson
description: ASP.NET Core uygulamalarının Azure App Service ve Internet Information Services (IIS) dağıtımlarıyla ilgili sorunları tanılamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: d51a4a43f585b0a0b7e3aab2c5de1b2d215de494
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059604"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="babca-103">Azure App Service ve IIS 'de ASP.NET Core sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="babca-104">, [Kotin Kotalik](https://github.com/jkotalik) tarafından</span><span class="sxs-lookup"><span data-stu-id="babca-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="babca-105">Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="babca-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="babca-106">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="babca-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="babca-107">Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="babca-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="babca-108">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="babca-109">Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="babca-110">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="babca-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="babca-111">IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="babca-112">Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="babca-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="babca-113">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="babca-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="babca-114">Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="babca-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="babca-115">Ek Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="babca-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="babca-116">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="babca-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="babca-117">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="babca-117">App startup errors</span></span>

<span data-ttu-id="babca-118">Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="babca-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="babca-119">*502,5-Işlem hatası* veya yerel olarak hata ayıklarken oluşan *500,30-başlatma hatası* , bu konudaki öneri kullanılarak tanılanabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="babca-120">403,14 yasak</span><span class="sxs-lookup"><span data-stu-id="babca-120">403.14 Forbidden</span></span>

<span data-ttu-id="babca-121">Uygulama başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-121">The app fails to start.</span></span> <span data-ttu-id="babca-122">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="babca-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="babca-123">Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:</span><span class="sxs-lookup"><span data-stu-id="babca-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="babca-124">Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="babca-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="babca-125">Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="babca-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="babca-126">*web.config* dosyası dağıtımda yok veya *web.config* dosya içeriği hatalı biçimlendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="babca-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="babca-127">Aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="babca-127">Perform the following steps:</span></span>

1. <span data-ttu-id="babca-128">Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.</span><span class="sxs-lookup"><span data-stu-id="babca-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="babca-129">Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="babca-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="babca-130">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="babca-131">Azure App Service barındırırken, uygulamanın klasöre dağıtıldığını doğrulayın `D:\home\site\wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="babca-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="babca-132">Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında** gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager** 's **Basic Settings** .</span></span>
1. <span data-ttu-id="babca-133">Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="babca-134">Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..</span><span class="sxs-lookup"><span data-stu-id="babca-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="babca-135">*web.config* dosyası hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="babca-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="babca-136">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="babca-136">500 Internal Server Error</span></span>

<span data-ttu-id="babca-137">Uygulama başlıyor, ancak bir hata sunucunun isteği yerine getirmesini engelliyor.</span><span class="sxs-lookup"><span data-stu-id="babca-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="babca-138">Bu hata, başlangıç sırasında veya Yanıt oluştururken uygulamanın kodunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="babca-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="babca-139">Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="babca-140">Uygulama olay günlüğü genellikle uygulamanın normal olarak başlatıldığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="babca-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="babca-141">Sunucu perspektifinden doğru.</span><span class="sxs-lookup"><span data-stu-id="babca-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="babca-142">Uygulama başlatıldı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="babca-143">Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="babca-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="babca-144">500,0 In-Process Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="babca-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="babca-145">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="babca-145">The worker process fails.</span></span> <span data-ttu-id="babca-146">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-146">The app doesn't start.</span></span>

<span data-ttu-id="babca-147">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) bileşenleri yüklenirken bilinmeyen bir hata oluştu.</span><span class="sxs-lookup"><span data-stu-id="babca-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="babca-148">Aşağıdaki eylemlerden birini uygulayın:</span><span class="sxs-lookup"><span data-stu-id="babca-148">Take one of the following actions:</span></span>

* <span data-ttu-id="babca-149">[Microsoft desteği](https://support.microsoft.com/oas/default.aspx?prid=15832) iletişim kurun ( **Geliştirici Araçları** ve **ASP.NET Core** ' i seçin).</span><span class="sxs-lookup"><span data-stu-id="babca-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core** ).</span></span>
* <span data-ttu-id="babca-150">Stack Overflow soru sorun.</span><span class="sxs-lookup"><span data-stu-id="babca-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="babca-151">[GitHub deponuzda](https://github.com/dotnet/AspNetCore)bir sorun yapın.</span><span class="sxs-lookup"><span data-stu-id="babca-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="babca-152">500,30 In-Process başlatma hatası</span><span class="sxs-lookup"><span data-stu-id="babca-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="babca-153">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="babca-153">The worker process fails.</span></span> <span data-ttu-id="babca-154">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-154">The app doesn't start.</span></span>

<span data-ttu-id="babca-155">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR 'yi işlem içi başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="babca-156">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="babca-157">Yaygın hata koşulları:</span><span class="sxs-lookup"><span data-stu-id="babca-157">Common failure conditions:</span></span>

* <span data-ttu-id="babca-158">Mevcut olmayan ASP.NET Core paylaşılan çerçevesinin bir sürümünün hedeflenmesi nedeniyle uygulama yanlış yapılandırılmış.</span><span class="sxs-lookup"><span data-stu-id="babca-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="babca-159">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="babca-160">Azure Key Vault kullanarak Key Vault izinlerin bulunmaması.</span><span class="sxs-lookup"><span data-stu-id="babca-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="babca-161">Doğru izinlerin verildiğinden emin olmak için hedeflenen Key Vault erişim ilkelerini kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="babca-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="babca-162">500,31 ANCM yerel bağımlılıklar bulunamadı</span><span class="sxs-lookup"><span data-stu-id="babca-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="babca-163">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="babca-163">The worker process fails.</span></span> <span data-ttu-id="babca-164">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-164">The app doesn't start.</span></span>

<span data-ttu-id="babca-165">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) , .NET Core çalışma zamanını işlem içinde başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="babca-166">Bu başlatma hatasının en yaygın nedeni, `Microsoft.NETCore.App` veya `Microsoft.AspNetCore.App` çalışma zamanının yüklenmemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="babca-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="babca-167">Uygulama, hedef ASP.NET Core 3,0 ' ye dağıtılmışsa ve bu sürüm makinede yoksa, bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="babca-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="babca-168">Örnek bir hata iletisi aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="babca-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="babca-169">Hata iletisi, yüklü tüm .NET Core sürümlerini ve uygulama tarafından istenen sürümü listeler.</span><span class="sxs-lookup"><span data-stu-id="babca-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="babca-170">Bu hatayı onarmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="babca-170">To fix this error, either:</span></span>

* <span data-ttu-id="babca-171">Uygun .NET Core sürümünü makineye yükler.</span><span class="sxs-lookup"><span data-stu-id="babca-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="babca-172">Uygulamayı, makinede bulunan .NET Core 'un bir sürümünü hedefleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="babca-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="babca-173">Uygulamayı [kendi kendine kapsanan bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)olarak yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="babca-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="babca-174">Geliştirme aşamasında çalışırken ( `ASPNETCORE_ENVIRONMENT` ortam değişkeni olarak ayarlandığında `Development` ), özel hata HTTP yanıtına yazılır.</span><span class="sxs-lookup"><span data-stu-id="babca-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="babca-175">İşlem başlatma hatasının nedeni uygulama olay günlüğünde de bulunur.</span><span class="sxs-lookup"><span data-stu-id="babca-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="babca-176">500,32 ANCM dll yüklenemedi</span><span class="sxs-lookup"><span data-stu-id="babca-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="babca-177">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="babca-177">The worker process fails.</span></span> <span data-ttu-id="babca-178">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-178">The app doesn't start.</span></span>

<span data-ttu-id="babca-179">Bu hatanın en yaygın nedeni, uygulamanın uyumsuz bir işlemci mimarisi için yayımlanmakta olması olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="babca-180">Çalışan işlemi 32 bitlik bir uygulama olarak çalışıyorsa ve uygulama 64 bit hedef için yayımlandıysa, bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="babca-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="babca-181">Bu hatayı onarmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="babca-181">To fix this error, either:</span></span>

* <span data-ttu-id="babca-182">Çalışan işlemle aynı işlemci mimarisi için uygulamayı yeniden yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="babca-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="babca-183">Uygulamayı [çerçeveye bağlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-executables-fde)olarak yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="babca-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="babca-184">500,33 ANCM Istek Işleyicisi yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="babca-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="babca-185">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="babca-185">The worker process fails.</span></span> <span data-ttu-id="babca-186">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-186">The app doesn't start.</span></span>

<span data-ttu-id="babca-187">Uygulama çerçeveye başvurmadı `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="babca-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="babca-188">Yalnızca çerçeveyi hedefleyen uygulamalar `Microsoft.AspNetCore.App` [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module)tarafından barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="babca-189">Bu hatayı düzeltemedi, uygulamanın çerçeveyi hedeflediğinden emin olun `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="babca-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="babca-190">`.runtimeconfig.json`Uygulamanın hedeflediği çerçeveyi doğrulamak için ' i işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="babca-191">500,34 ANCM karışık barındırma modelleri desteklenmez</span><span class="sxs-lookup"><span data-stu-id="babca-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="babca-192">Çalışan işlem, aynı işlemde hem işlem içi uygulama hem de işlem dışı bir uygulama çalıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="babca-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="babca-193">Bu hatayı onarmak için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="babca-194">aynı Işlemdeki 500,35 ANCM birden çok In-Process uygulaması</span><span class="sxs-lookup"><span data-stu-id="babca-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="babca-195">Çalışan işlemi aynı işlemde birden çok işlem içi uygulama çalıştıramıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="babca-196">Bu hatayı onarmak için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="babca-197">500,36 ANCM Işlem dışı Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="babca-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="babca-198">İşlem dışı istek işleyicisi *aspnetcorev2_outofprocess.dll* , *aspnetcorev2.dll* dosyasının yanında değildir.</span><span class="sxs-lookup"><span data-stu-id="babca-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll* , isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="babca-199">Bu, [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)bozuk bir yüklemesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="babca-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="babca-200">Bu hatayı gidermek için [.NET Core barındırma paketi](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS için) veya Visual Studio (IIS Express için) yüklemesini onarın.</span><span class="sxs-lookup"><span data-stu-id="babca-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="babca-201">500,37 ANCM başlangıç zamanı sınırı Içinde başlatılamadı</span><span class="sxs-lookup"><span data-stu-id="babca-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="babca-202">ANCM, belirtilen başlatma süresi sınırı içinde başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="babca-202">ANCM failed to start within the provided startup time limit.</span></span> <span data-ttu-id="babca-203">Varsayılan olarak, zaman aşımı 120 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="babca-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="babca-204">Aynı makinede çok sayıda uygulama başlatılırken bu hata oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="babca-205">Başlangıç sırasında sunucuda CPU/bellek kullanımı artışlarını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="babca-206">Birden çok uygulamanın başlatma işlemini şaşırtmayı yapmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="babca-207">500,38 ANCM uygulama DLL 'SI bulunamadı</span><span class="sxs-lookup"><span data-stu-id="babca-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="babca-208">ANCM, yürütülebilir dosyanın yanında olması gereken uygulama DLL 'sini bulamadı.</span><span class="sxs-lookup"><span data-stu-id="babca-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="babca-209">Bu hata, işlem içi barındırma modelini kullanarak [tek dosya yürütülebilir dosyası](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) olarak paketlenmiş bir uygulama barındırdığında oluşur.</span><span class="sxs-lookup"><span data-stu-id="babca-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="babca-210">İşlem içi modelde, ANCM 'nin .NET Core uygulamasını mevcut IIS işlemine yüklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="babca-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="babca-211">Bu senaryo, tek dosya dağıtım modeli tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="babca-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="babca-212">Bu hatayı onarmak için uygulamanın proje dosyasında aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="babca-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="babca-213">MSBuild özelliğini olarak ayarlayarak tek dosya yayımlamayı devre dışı bırakın `PublishSingleFile` `false` .</span><span class="sxs-lookup"><span data-stu-id="babca-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="babca-214">MSBuild özelliğini olarak ayarlayarak işlem dışı barındırma modeline geçiş yapın `AspNetCoreHostingModel` `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="babca-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="babca-215">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="babca-215">502.5 Process Failure</span></span>

<span data-ttu-id="babca-216">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="babca-216">The worker process fails.</span></span> <span data-ttu-id="babca-217">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-217">The app doesn't start.</span></span>

<span data-ttu-id="babca-218">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="babca-219">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="babca-220">Yaygın bir hata durumu, ASP.NET Core paylaşılan bir Framework 'ün mevcut olmayan bir sürümünü hedeflediğinden, uygulamanın yanlış yapılandırılmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="babca-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="babca-221">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="babca-222">*Paylaşılan çerçeve* , makinede yüklü olan ve bir metapackage tarafından başvurulan derleme ( *. dll* dosyaları) kümesidir `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="babca-222">The *shared framework* is the set of assemblies ( *.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="babca-223">Metapackage başvurusu, gerekli en düşük sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="babca-224">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="babca-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="babca-225">Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:</span><span class="sxs-lookup"><span data-stu-id="babca-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="babca-226">Uygulama başlatılamadı (hata kodu ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="babca-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="babca-227">Uygulamanın derlemesi ( *. dll* ) yüklenemediğinden uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="babca-227">The app failed to start because the app's assembly ( *.dll* ) couldn't be loaded.</span></span>

<span data-ttu-id="babca-228">Bu hata, yayımlanan uygulama ile W3wp/iisexpress işlemi arasında bir bit durumu uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="babca-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="babca-229">Uygulama havuzunun 32 bit ayarının doğru olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="babca-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="babca-230">IIS yöneticisinin **uygulama havuzlarında** uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-230">Select the app pool in IIS Manager's **Application Pools** .</span></span>
1. <span data-ttu-id="babca-231">**Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="babca-232">**Enable 32 bit uygulamalarını** ayarla:</span><span class="sxs-lookup"><span data-stu-id="babca-232">Set **Enable 32-Bit Applications** :</span></span>
   * <span data-ttu-id="babca-233">32-bit (x86) bir uygulama dağıtıyorsanız, değerini olarak ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="babca-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="babca-234">64 bit (x64) uygulaması dağıtıyorsanız, değerini olarak ayarlayın `False` .</span><span class="sxs-lookup"><span data-stu-id="babca-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="babca-235">`<Platform>`Proje dosyasındaki bir MSBuild özelliği ve uygulamanın yayınlanan bit durumu arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="babca-236">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="babca-236">Connection reset</span></span>

<span data-ttu-id="babca-237">Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="babca-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="babca-238">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirilmesi sırasında bir hata oluştuğunda meydana gelir.</span><span class="sxs-lookup"><span data-stu-id="babca-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="babca-239">Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="babca-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="babca-240">[Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="babca-241">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="babca-241">Default startup limits</span></span>

<span data-ttu-id="babca-242">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="babca-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="babca-243">Varsayılan değerde sol tarafta, modül bir işlem hatası günlüğe kaydedilmeden önce uygulamanın başlaması iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="babca-244">Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="babca-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="babca-245">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="babca-246">Uygulama olay günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="babca-247">Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="babca-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="babca-248">Azure portal uygulama **Hizmetleri** ' nde uygulamayı açın.</span><span class="sxs-lookup"><span data-stu-id="babca-248">In the Azure portal, open the app in **App Services** .</span></span>
1. <span data-ttu-id="babca-249">**Sorunları tanılama ve çözme** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-249">Select **Diagnose and solve problems** .</span></span>
1. <span data-ttu-id="babca-250">**Tanılama araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="babca-251">**Destek Araçları** ' nın altında, **uygulama olayları** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-251">Under **Support Tools** , select the **Application Events** button.</span></span>
1. <span data-ttu-id="babca-252">**Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="babca-253">**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="babca-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="babca-254">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="babca-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="babca-255">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-256">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-257">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
1. <span data-ttu-id="babca-258">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="babca-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="babca-259">*eventlog.xml* dosyasının yanındaki kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="babca-260">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-260">Examine the log.</span></span> <span data-ttu-id="babca-261">En son olayları görmek için günlüğün en altına gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="babca-262">Uygulamayı kudu konsolunda çalıştırma</span><span class="sxs-lookup"><span data-stu-id="babca-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="babca-263">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="babca-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="babca-264">Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="babca-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="babca-265">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="babca-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="babca-266">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-267">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-268">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="babca-269">32 bit (x86) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="babca-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="babca-270">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="babca-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="babca-271">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-271">Run the app:</span></span>
   * <span data-ttu-id="babca-272">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="babca-273">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="babca-274">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="babca-275">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="babca-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="babca-276">*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="babca-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="babca-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="babca-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="babca-278">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="babca-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="babca-279">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="babca-280">64 bit (x64) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="babca-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="babca-281">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="babca-281">**Current release**</span></span>

* <span data-ttu-id="babca-282">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="babca-283">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="babca-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="babca-284">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="babca-285">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="babca-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="babca-286">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="babca-287">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="babca-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="babca-288">*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="babca-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="babca-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="babca-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="babca-290">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="babca-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="babca-291">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="babca-292">ASP.NET Core modülü stdout günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

> [!WARNING]
> <span data-ttu-id="babca-293">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-293">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="babca-294">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-294">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="babca-295">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-295">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="babca-296">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-296">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-297">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-297">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="babca-298">ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="babca-298">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="babca-299">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="babca-299">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="babca-300">Azure portalında Web uygulamasına gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-300">In the Azure Portal, navigate to the web app.</span></span>
1. <span data-ttu-id="babca-301">**App Service** dikey penceresinde, arama kutusuna **kudu** girin.</span><span class="sxs-lookup"><span data-stu-id="babca-301">In the **App Service** blade, enter **kudu** in the search box.</span></span>
1. <span data-ttu-id="babca-302">**Gelişmiş Araçlar** > **Git** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-302">Select **Advanced Tools** > **Go** .</span></span>
1. <span data-ttu-id="babca-303">**CMD > hata ayıklama konsolunu** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-303">Select  **Debug console > CMD** .</span></span>
1. <span data-ttu-id="babca-304">*Site/Wwwroot* 'a git</span><span class="sxs-lookup"><span data-stu-id="babca-304">Navigate to *site/wwwroot*</span></span>
1. <span data-ttu-id="babca-305">*web.config* dosyasını düzenlemek için kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-305">Select the pencil icon to edit the *web.config* file.</span></span>
1. <span data-ttu-id="babca-306">`<aspNetCore />`Öğesinde, öğesini ayarlayın `stdoutLogEnabled="true"` ve **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-306">In the `<aspNetCore />` element, set `stdoutLogEnabled="true"` and select **Save** .</span></span>

<span data-ttu-id="babca-307">Ayar ile sorun giderme tamamlandığında stdout günlüğünü devre dışı bırakın `stdoutLogEnabled="false"` .</span><span class="sxs-lookup"><span data-stu-id="babca-307">Disable stdout logging when troubleshooting is complete by setting `stdoutLogEnabled="false"`.</span></span>

<span data-ttu-id="babca-308">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="babca-308">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="babca-309">ASP.NET Core modülü hata ayıklama günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-309">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="babca-310">ASP.NET Core Module hata ayıklama günlüğü, ASP.NET Core modülünden daha ayrıntılı günlük kaydı sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-310">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="babca-311">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="babca-311">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="babca-312">Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="babca-312">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="babca-313">Uygulamayı gelişmiş tanılama günlüğü için yapılandırmak üzere [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-313">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="babca-314">Uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="babca-314">Redeploy the app.</span></span>
   * <span data-ttu-id="babca-315">`<handlerSettings>` [Gelişmiş tanılama günlüklerini](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilen, kudu konsolunu kullanarak canlı uygulamanın *web.config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-315">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="babca-316">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="babca-316">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="babca-317">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-317">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-318">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-318">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="babca-319">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-319">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
     1. <span data-ttu-id="babca-320">Klasörü Wwwroot **yoluna açın**  >  **wwwroot** .</span><span class="sxs-lookup"><span data-stu-id="babca-320">Open the folders to the path **site** > **wwwroot** .</span></span> <span data-ttu-id="babca-321">*web.config* dosyasını, kurşun kalem düğmesini seçerek düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-321">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="babca-322">Bölümü, `<handlerSettings>` [Gelişmiş tanılama günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-322">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="babca-323">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-323">Select the **Save** button.</span></span>
1. <span data-ttu-id="babca-324">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="babca-324">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="babca-325">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-325">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-326">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-326">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-327">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-327">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
1. <span data-ttu-id="babca-328">Klasörü Wwwroot **yoluna açın**  >  **wwwroot** .</span><span class="sxs-lookup"><span data-stu-id="babca-328">Open the folders to the path **site** > **wwwroot** .</span></span> <span data-ttu-id="babca-329">*Aspnetcore-Debug. log* dosyası için bir yol sağlamadıysanız dosya listede görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="babca-329">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="babca-330">Bir yol sağladıysanız, günlük dosyasının konumuna gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-330">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="babca-331">Dosya adının yanındaki kurşun kalem düğmesiyle günlük dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="babca-331">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="babca-332">Sorun giderme tamamlandığında hata ayıklama günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="babca-332">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="babca-333">Gelişmiş hata ayıklama günlüğünü devre dışı bırakmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="babca-333">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="babca-334">`<handlerSettings>` *web.config* dosyasından yerel olarak kaldırın ve uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="babca-334">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="babca-335">*web.config* dosyasını düzenlemek ve bölümünü kaldırmak Için kudu konsolunu kullanın `<handlerSettings>` .</span><span class="sxs-lookup"><span data-stu-id="babca-335">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="babca-336">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="babca-336">Save the file.</span></span>

<span data-ttu-id="babca-337">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="babca-337">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="babca-338">Hata ayıklama günlüğünü devre dışı bırakma hatası, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-338">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="babca-339">Günlük dosyası boyutunda sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-339">There's no limit on log file size.</span></span> <span data-ttu-id="babca-340">Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-340">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="babca-341">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-341">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-342">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-342">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="babca-343">Yavaş veya askıda olan uygulama (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-343">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="babca-344">Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="babca-344">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="babca-345">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-345">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="babca-346">Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın</span><span class="sxs-lookup"><span data-stu-id="babca-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="babca-347">İzleme kanatları</span><span class="sxs-lookup"><span data-stu-id="babca-347">Monitoring blades</span></span>

<span data-ttu-id="babca-348">İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-348">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="babca-349">Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-349">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="babca-350">ASP.NET Core uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-350">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="babca-351">Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="babca-351">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="babca-352">**GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-352">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="babca-353">**ASP.NET Core uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="babca-353">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="babca-354">Uzantılar yüklü değilse, **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-354">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="babca-355">Listeden **ASP.NET Core uzantılarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-355">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="babca-356">Yasal koşulları kabul etmek için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-356">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="babca-357">**Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-357">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="babca-358">Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="babca-358">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="babca-359">Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-359">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="babca-360">Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-360">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="babca-361">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-361">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-362">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-362">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-363">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-363">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
1. <span data-ttu-id="babca-364">Klasörü Wwwroot **yoluna açın** > **wwwroot** ve listenin altındaki *web.config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="babca-364">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="babca-365">*web.config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="babca-365">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="babca-366">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="babca-366">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="babca-367">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-367">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="babca-368">Tanılama günlüğünü etkinleştirmek için ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-368">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="babca-369">Azure portal **tanılama günlükleri** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-369">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="babca-370">**Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri** için **bir anahtar seçin** .</span><span class="sxs-lookup"><span data-stu-id="babca-370">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages** .</span></span> <span data-ttu-id="babca-371">Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-371">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="babca-372">Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-372">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing** .</span></span>
1. <span data-ttu-id="babca-373">Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-373">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="babca-374">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-374">Make a request to the app.</span></span>
1. <span data-ttu-id="babca-375">Günlük akışı verileri içinde hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="babca-375">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="babca-376">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="babca-376">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="babca-377">Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):</span><span class="sxs-lookup"><span data-stu-id="babca-377">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="babca-378">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-378">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="babca-379">Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-379">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="babca-380">[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.</span><span class="sxs-lookup"><span data-stu-id="babca-380">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="babca-381">Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="babca-381">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="babca-382">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-382">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="babca-383">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-383">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="babca-384">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-385">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="babca-386">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="babca-386">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="babca-387">Uygulama olay günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-387">Application Event Log (IIS)</span></span>

<span data-ttu-id="babca-388">Uygulama olay günlüğüne erişin:</span><span class="sxs-lookup"><span data-stu-id="babca-388">Access the Application Event Log:</span></span>

1. <span data-ttu-id="babca-389">Başlat menüsünü açın, *Olay Görüntüleyicisi* araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-389">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="babca-390">**Olay Görüntüleyicisi** , **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="babca-390">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="babca-391">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-391">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="babca-392">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="babca-392">Search for errors associated with the failing app.</span></span> <span data-ttu-id="babca-393">Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="babca-393">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="babca-394">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="babca-394">Run the app at a command prompt</span></span>

<span data-ttu-id="babca-395">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="babca-395">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="babca-396">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="babca-396">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="babca-397">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="babca-397">Framework-dependent deployment</span></span>

<span data-ttu-id="babca-398">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-398">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="babca-399">Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *dotnet.exe* yürüterek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-399">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe* .</span></span> <span data-ttu-id="babca-400">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="babca-400">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="babca-401">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="babca-401">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="babca-402">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-402">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="babca-403">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="babca-403">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="babca-404">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-404">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="babca-405">Kendi kendine kapsanan dağıtım</span><span class="sxs-lookup"><span data-stu-id="babca-405">Self-contained deployment</span></span>

<span data-ttu-id="babca-406">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-406">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="babca-407">Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-407">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="babca-408">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="babca-408">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="babca-409">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="babca-409">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="babca-410">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-410">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="babca-411">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="babca-411">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="babca-412">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-412">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="babca-413">ASP.NET Core Module stdout günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-413">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="babca-414">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="babca-414">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="babca-415">Barındırma sistemindeki sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-415">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="babca-416">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-416">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="babca-417">MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.</span><span class="sxs-lookup"><span data-stu-id="babca-417">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="babca-418">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-418">Edit the *web.config* file.</span></span> <span data-ttu-id="babca-419">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="babca-419">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="babca-420">`stdout` yolunda günlük dosyası adı ön eki bulunur.</span><span class="sxs-lookup"><span data-stu-id="babca-420">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="babca-421">Günlük oluşturulduğunda zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="babca-421">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="babca-422">`stdout`Dosya adı ön eki olarak kullanıldığında, tipik bir günlük dosyası *stdout_20180205184032_5412. log* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="babca-422">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log* .</span></span>
1. <span data-ttu-id="babca-423">Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="babca-423">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="babca-424">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="babca-424">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="babca-425">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-425">Make a request to the app.</span></span>
1. <span data-ttu-id="babca-426">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-426">Navigate to the *logs* folder.</span></span> <span data-ttu-id="babca-427">En son stdout günlüğünü bulup açın.</span><span class="sxs-lookup"><span data-stu-id="babca-427">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="babca-428">Günlüğü hatalara karşı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-428">Study the log for errors.</span></span>

<span data-ttu-id="babca-429">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="babca-429">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="babca-430">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-430">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="babca-431">**StdoutLogEnabled** olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="babca-431">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="babca-432">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="babca-432">Save the file.</span></span>

<span data-ttu-id="babca-433">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="babca-433">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="babca-434">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-434">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="babca-435">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-435">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="babca-436">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-436">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-437">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-437">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="babca-438">ASP.NET Core modülü hata ayıklama günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-438">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="babca-439">ASP.NET Core modülü hata ayıklama günlüğünü etkinleştirmek için aşağıdaki işleyici ayarlarını uygulamanın *web.config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-439">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="babca-440">Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-440">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="babca-441">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="babca-441">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="babca-442">Geliştirici özel durum sayfasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="babca-442">Enable the Developer Exception Page</span></span>

<span data-ttu-id="babca-443">`ASPNETCORE_ENVIRONMENT` [Ortam değişkeni](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , uygulamayı geliştirme ortamında çalıştırmak için web.configeklenebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-443">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="babca-444">Ortam, ana bilgisayar Oluşturucu 'da uygulama başlangıcında geçersiz kılınmadığı sürece `UseEnvironment` , ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="babca-444">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="babca-445">Ortam değişkeninin ayarlanması `ASPNETCORE_ENVIRONMENT` yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="babca-445">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="babca-446">Sorun giderme işleminden sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="babca-446">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="babca-447">*web.config* ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="babca-447">For information on setting environment variables in *web.config* , see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="babca-448">Uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="babca-448">Obtain data from an app</span></span>

<span data-ttu-id="babca-449">Bir uygulamanın isteklere yanıt verme yeteneği varsa, Terminal satır içi ara yazılımı kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="babca-449">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="babca-450">Daha fazla bilgi ve örnek kod için bkz <xref:test/troubleshoot#obtain-data-from-an-app> ..</span><span class="sxs-lookup"><span data-stu-id="babca-450">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="babca-451">Yavaş veya askıda olan uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-451">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="babca-452">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-452">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="babca-453">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="babca-453">App crashes or encounters an exception</span></span>

<span data-ttu-id="babca-454">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="babca-454">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="babca-455">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="babca-455">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="babca-456">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="babca-456">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="babca-457">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-457">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="babca-458">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-458">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="babca-459">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-459">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="babca-460">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-460">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="babca-461">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-461">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="babca-462">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-462">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="babca-463">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-463">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="babca-464">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="babca-464">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="babca-465">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="babca-465">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="babca-466">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="babca-466">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="babca-467">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="babca-467">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="babca-468">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="babca-468">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="babca-469">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="babca-469">Analyze the dump</span></span>

<span data-ttu-id="babca-470">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-470">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="babca-471">Daha fazla bilgi için bkz. [User-Mode döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="babca-471">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="babca-472">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="babca-472">Clear package caches</span></span>

<span data-ttu-id="babca-473">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-473">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="babca-474">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-474">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="babca-475">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="babca-475">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="babca-476">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="babca-476">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="babca-477">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-477">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="babca-478">Paket önbelleklerini Temizleme, [nuget.exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komutu yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="babca-478">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="babca-479">*nuget.exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="babca-479">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="babca-480">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-480">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="babca-481">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="babca-481">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="babca-482">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="babca-482">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="babca-483">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="babca-483">Azure documentation</span></span>

* [<span data-ttu-id="babca-484">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="babca-484">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="babca-485">Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü</span><span class="sxs-lookup"><span data-stu-id="babca-485">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="babca-486">Azure App Service tanılamada genel bakış</span><span class="sxs-lookup"><span data-stu-id="babca-486">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="babca-487">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="babca-487">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="babca-488">Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-488">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="babca-489">Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme</span><span class="sxs-lookup"><span data-stu-id="babca-489">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="babca-490">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-490">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="babca-491">Azure 'da Web Apps için uygulama performansı SSS</span><span class="sxs-lookup"><span data-stu-id="babca-491">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="babca-492">Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="babca-492">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="babca-493">Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="babca-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="babca-494">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="babca-494">Visual Studio documentation</span></span>

* [<span data-ttu-id="babca-495">Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="babca-495">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="babca-496">Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="babca-496">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="babca-497">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="babca-497">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="babca-498">Visual Studio Code belgeleri</span><span class="sxs-lookup"><span data-stu-id="babca-498">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="babca-499">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="babca-499">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="babca-500">Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="babca-500">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="babca-501">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="babca-501">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="babca-502">Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="babca-502">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="babca-503">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-503">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="babca-504">Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-504">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="babca-505">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="babca-505">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="babca-506">IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-506">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="babca-507">Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="babca-507">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="babca-508">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="babca-508">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="babca-509">Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="babca-509">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="babca-510">Ek Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="babca-510">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="babca-511">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="babca-511">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="babca-512">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="babca-512">App startup errors</span></span>

<span data-ttu-id="babca-513">Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="babca-513">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="babca-514">*502,5-Işlem hatası* veya yerel olarak hata ayıklarken oluşan *500,30-başlatma hatası* , bu konudaki öneri kullanılarak tanılanabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-514">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="babca-515">403,14 yasak</span><span class="sxs-lookup"><span data-stu-id="babca-515">403.14 Forbidden</span></span>

<span data-ttu-id="babca-516">Uygulama başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-516">The app fails to start.</span></span> <span data-ttu-id="babca-517">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="babca-517">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="babca-518">Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:</span><span class="sxs-lookup"><span data-stu-id="babca-518">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="babca-519">Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="babca-519">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="babca-520">Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="babca-520">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="babca-521">*web.config* dosyası dağıtımda yok veya *web.config* dosya içeriği hatalı biçimlendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="babca-521">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="babca-522">Aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="babca-522">Perform the following steps:</span></span>

1. <span data-ttu-id="babca-523">Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.</span><span class="sxs-lookup"><span data-stu-id="babca-523">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="babca-524">Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="babca-524">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="babca-525">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-525">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="babca-526">Azure App Service barındırırken, uygulamanın klasöre dağıtıldığını doğrulayın `D:\home\site\wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="babca-526">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="babca-527">Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında** gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-527">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager** 's **Basic Settings** .</span></span>
1. <span data-ttu-id="babca-528">Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-528">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="babca-529">Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..</span><span class="sxs-lookup"><span data-stu-id="babca-529">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="babca-530">*web.config* dosyası hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="babca-530">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="babca-531">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="babca-531">500 Internal Server Error</span></span>

<span data-ttu-id="babca-532">Uygulama başlıyor, ancak bir hata sunucunun isteği yerine getirmesini engelliyor.</span><span class="sxs-lookup"><span data-stu-id="babca-532">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="babca-533">Bu hata, başlangıç sırasında veya Yanıt oluştururken uygulamanın kodunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="babca-533">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="babca-534">Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-534">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="babca-535">Uygulama olay günlüğü genellikle uygulamanın normal olarak başlatıldığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="babca-535">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="babca-536">Sunucu perspektifinden doğru.</span><span class="sxs-lookup"><span data-stu-id="babca-536">From the server's perspective, that's correct.</span></span> <span data-ttu-id="babca-537">Uygulama başlatıldı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-537">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="babca-538">Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="babca-538">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="babca-539">500,0 In-Process Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="babca-539">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="babca-540">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="babca-540">The worker process fails.</span></span> <span data-ttu-id="babca-541">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-541">The app doesn't start.</span></span>

<span data-ttu-id="babca-542">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR 'yi bulamıyor ve işlem içi istek işleyicisini ( *aspnetcorev2_inprocess.dll* ) bulamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-542">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler ( *aspnetcorev2_inprocess.dll* ).</span></span> <span data-ttu-id="babca-543">Şunları denetleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-543">Check that:</span></span>

* <span data-ttu-id="babca-544">Uygulama [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet paketini ya da [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'i hedefler.</span><span class="sxs-lookup"><span data-stu-id="babca-544">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="babca-545">Uygulamanın hedeflediği ASP.NET Core paylaşılan çerçevenin sürümü hedef makineye yüklendi.</span><span class="sxs-lookup"><span data-stu-id="babca-545">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="babca-546">500,0 Işlem dışı Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="babca-546">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="babca-547">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="babca-547">The worker process fails.</span></span> <span data-ttu-id="babca-548">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-548">The app doesn't start.</span></span>

<span data-ttu-id="babca-549">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) işlem dışı barındırma isteği işleyicisini bulamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-549">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="babca-550">*aspnetcorev2_outofprocess.dll* *aspnetcorev2.dll* yanındaki bir alt klasörde bulunduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="babca-550">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll* .</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="babca-551">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="babca-551">502.5 Process Failure</span></span>

<span data-ttu-id="babca-552">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="babca-552">The worker process fails.</span></span> <span data-ttu-id="babca-553">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-553">The app doesn't start.</span></span>

<span data-ttu-id="babca-554">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-554">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="babca-555">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-555">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="babca-556">Yaygın bir hata durumu, ASP.NET Core paylaşılan bir Framework 'ün mevcut olmayan bir sürümünü hedeflediğinden, uygulamanın yanlış yapılandırılmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="babca-556">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="babca-557">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-557">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="babca-558">*Paylaşılan çerçeve* , makinede yüklü olan ve bir metapackage tarafından başvurulan derleme ( *. dll* dosyaları) kümesidir `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="babca-558">The *shared framework* is the set of assemblies ( *.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="babca-559">Metapackage başvurusu, gerekli en düşük sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-559">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="babca-560">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="babca-560">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="babca-561">Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:</span><span class="sxs-lookup"><span data-stu-id="babca-561">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="babca-562">Uygulama başlatılamadı (hata kodu ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="babca-562">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="babca-563">Uygulamanın derlemesi ( *. dll* ) yüklenemediğinden uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="babca-563">The app failed to start because the app's assembly ( *.dll* ) couldn't be loaded.</span></span>

<span data-ttu-id="babca-564">Bu hata, yayımlanan uygulama ile W3wp/iisexpress işlemi arasında bir bit durumu uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="babca-564">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="babca-565">Uygulama havuzunun 32 bit ayarının doğru olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="babca-565">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="babca-566">IIS yöneticisinin **uygulama havuzlarında** uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-566">Select the app pool in IIS Manager's **Application Pools** .</span></span>
1. <span data-ttu-id="babca-567">**Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-567">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="babca-568">**Enable 32 bit uygulamalarını** ayarla:</span><span class="sxs-lookup"><span data-stu-id="babca-568">Set **Enable 32-Bit Applications** :</span></span>
   * <span data-ttu-id="babca-569">32-bit (x86) bir uygulama dağıtıyorsanız, değerini olarak ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="babca-569">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="babca-570">64 bit (x64) uygulaması dağıtıyorsanız, değerini olarak ayarlayın `False` .</span><span class="sxs-lookup"><span data-stu-id="babca-570">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="babca-571">`<Platform>`Proje dosyasındaki bir MSBuild özelliği ve uygulamanın yayınlanan bit durumu arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-571">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="babca-572">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="babca-572">Connection reset</span></span>

<span data-ttu-id="babca-573">Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="babca-573">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="babca-574">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirilmesi sırasında bir hata oluştuğunda meydana gelir.</span><span class="sxs-lookup"><span data-stu-id="babca-574">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="babca-575">Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="babca-575">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="babca-576">[Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-576">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="babca-577">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="babca-577">Default startup limits</span></span>

<span data-ttu-id="babca-578">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="babca-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="babca-579">Varsayılan değerde sol tarafta, modül bir işlem hatası günlüğe kaydedilmeden önce uygulamanın başlaması iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-579">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="babca-580">Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="babca-580">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="babca-581">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-581">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="babca-582">Uygulama olay günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-582">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="babca-583">Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="babca-583">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="babca-584">Azure portal uygulama **Hizmetleri** ' nde uygulamayı açın.</span><span class="sxs-lookup"><span data-stu-id="babca-584">In the Azure portal, open the app in **App Services** .</span></span>
1. <span data-ttu-id="babca-585">**Sorunları tanılama ve çözme** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-585">Select **Diagnose and solve problems** .</span></span>
1. <span data-ttu-id="babca-586">**Tanılama araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-586">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="babca-587">**Destek Araçları** ' nın altında, **uygulama olayları** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-587">Under **Support Tools** , select the **Application Events** button.</span></span>
1. <span data-ttu-id="babca-588">**Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-588">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="babca-589">**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="babca-589">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="babca-590">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="babca-590">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="babca-591">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-591">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-592">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-592">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-593">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-593">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
1. <span data-ttu-id="babca-594">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="babca-594">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="babca-595">*eventlog.xml* dosyasının yanındaki kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-595">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="babca-596">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-596">Examine the log.</span></span> <span data-ttu-id="babca-597">En son olayları görmek için günlüğün en altına gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-597">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="babca-598">Uygulamayı kudu konsolunda çalıştırma</span><span class="sxs-lookup"><span data-stu-id="babca-598">Run the app in the Kudu console</span></span>

<span data-ttu-id="babca-599">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="babca-599">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="babca-600">Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="babca-600">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="babca-601">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="babca-601">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="babca-602">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-602">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-603">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-603">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-604">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-604">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="babca-605">32 bit (x86) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="babca-605">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="babca-606">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="babca-606">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="babca-607">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-607">Run the app:</span></span>
   * <span data-ttu-id="babca-608">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-608">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="babca-609">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-609">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="babca-610">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-610">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="babca-611">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="babca-611">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="babca-612">*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="babca-612">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="babca-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="babca-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="babca-614">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="babca-614">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="babca-615">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-615">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="babca-616">64 bit (x64) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="babca-616">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="babca-617">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="babca-617">**Current release**</span></span>

* <span data-ttu-id="babca-618">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-618">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="babca-619">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="babca-619">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="babca-620">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-620">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="babca-621">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="babca-621">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="babca-622">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-622">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="babca-623">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="babca-623">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="babca-624">*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="babca-624">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="babca-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="babca-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="babca-626">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="babca-626">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="babca-627">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-627">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="babca-628">ASP.NET Core modülü stdout günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-628">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="babca-629">ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="babca-629">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="babca-630">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="babca-630">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="babca-631">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-631">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="babca-632">**Sorun kategorisini seçin** altında **Web uygulaması aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-632">Under **SELECT PROBLEM CATEGORY** , select the **Web App Down** button.</span></span>
1. <span data-ttu-id="babca-633">**Önerilen çözümler** > **stdout günlük yeniden yönlendirmeyi etkinleştirmek** için, **Web.Configdüzenlemek üzere kudu konsolunu açmak** için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-633">Under **Suggested Solutions** > **Enable Stdout Log Redirection** , select the button to **Open Kudu Console to edit Web.Config** .</span></span>
1. <span data-ttu-id="babca-634">Kudu **Tanılama konsolunda** , klasör Wwwroot **yolunu açın**  >  **wwwroot** .</span><span class="sxs-lookup"><span data-stu-id="babca-634">In the Kudu **Diagnostic Console** , open the folders to the path **site** > **wwwroot** .</span></span> <span data-ttu-id="babca-635">Listenin altındaki *web.config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="babca-635">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="babca-636">*web.config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="babca-636">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="babca-637">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="babca-637">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="babca-638">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-638">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="babca-639">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-639">Make a request to the app.</span></span>
1. <span data-ttu-id="babca-640">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="babca-640">Return to the Azure portal.</span></span> <span data-ttu-id="babca-641">**GELIŞTIRME araçları** alanında **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-641">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="babca-642">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-642">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-643">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-643">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-644">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-644">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
1. <span data-ttu-id="babca-645">**LogFiles** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-645">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="babca-646">**Değiştirilen** sütunu inceleyin ve son değiştirilme tarihiyle stdout günlüğünü düzenlemek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-646">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="babca-647">Günlük dosyası açıldığında hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="babca-647">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="babca-648">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="babca-648">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="babca-649">Kudu **Tanılama Konsolu** 'nda, **site**  >  *web.config* dosyasını açığa çıkarmak için **Wwwroot** yolu sitesine dönün.</span><span class="sxs-lookup"><span data-stu-id="babca-649">In the Kudu **Diagnostic Console** , return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="babca-650">Kalem simgesini seçerek **web.config** dosyasını tekrar açın.</span><span class="sxs-lookup"><span data-stu-id="babca-650">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="babca-651">**StdoutLogEnabled** olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="babca-651">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="babca-652">Dosyayı kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-652">Select **Save** to save the file.</span></span>

<span data-ttu-id="babca-653">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="babca-653">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="babca-654">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-654">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="babca-655">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-655">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="babca-656">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-656">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="babca-657">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-657">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-658">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-658">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="babca-659">ASP.NET Core modülü hata ayıklama günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-659">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="babca-660">ASP.NET Core Module hata ayıklama günlüğü, ASP.NET Core modülünden daha ayrıntılı günlük kaydı sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-660">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="babca-661">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="babca-661">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="babca-662">Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="babca-662">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="babca-663">Uygulamayı gelişmiş tanılama günlüğü için yapılandırmak üzere [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-663">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="babca-664">Uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="babca-664">Redeploy the app.</span></span>
   * <span data-ttu-id="babca-665">`<handlerSettings>` [Gelişmiş tanılama günlüklerini](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilen, kudu konsolunu kullanarak canlı uygulamanın *web.config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-665">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="babca-666">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="babca-666">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="babca-667">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-667">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-668">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-668">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="babca-669">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-669">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
     1. <span data-ttu-id="babca-670">Klasörü Wwwroot **yoluna açın**  >  **wwwroot** .</span><span class="sxs-lookup"><span data-stu-id="babca-670">Open the folders to the path **site** > **wwwroot** .</span></span> <span data-ttu-id="babca-671">*web.config* dosyasını, kurşun kalem düğmesini seçerek düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-671">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="babca-672">Bölümü, `<handlerSettings>` [Gelişmiş tanılama günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-672">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="babca-673">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-673">Select the **Save** button.</span></span>
1. <span data-ttu-id="babca-674">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="babca-674">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="babca-675">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-675">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-676">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-676">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-677">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-677">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
1. <span data-ttu-id="babca-678">Klasörü Wwwroot **yoluna açın**  >  **wwwroot** .</span><span class="sxs-lookup"><span data-stu-id="babca-678">Open the folders to the path **site** > **wwwroot** .</span></span> <span data-ttu-id="babca-679">*Aspnetcore-Debug. log* dosyası için bir yol sağlamadıysanız dosya listede görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="babca-679">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="babca-680">Bir yol sağladıysanız, günlük dosyasının konumuna gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-680">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="babca-681">Dosya adının yanındaki kurşun kalem düğmesiyle günlük dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="babca-681">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="babca-682">Sorun giderme tamamlandığında hata ayıklama günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="babca-682">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="babca-683">Gelişmiş hata ayıklama günlüğünü devre dışı bırakmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="babca-683">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="babca-684">`<handlerSettings>` *web.config* dosyasından yerel olarak kaldırın ve uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="babca-684">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="babca-685">*web.config* dosyasını düzenlemek ve bölümünü kaldırmak Için kudu konsolunu kullanın `<handlerSettings>` .</span><span class="sxs-lookup"><span data-stu-id="babca-685">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="babca-686">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="babca-686">Save the file.</span></span>

<span data-ttu-id="babca-687">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="babca-687">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="babca-688">Hata ayıklama günlüğünü devre dışı bırakma hatası, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-688">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="babca-689">Günlük dosyası boyutunda sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-689">There's no limit on log file size.</span></span> <span data-ttu-id="babca-690">Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-690">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="babca-691">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-691">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-692">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-692">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="babca-693">Yavaş veya askıda olan uygulama (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-693">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="babca-694">Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="babca-694">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="babca-695">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-695">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="babca-696">Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın</span><span class="sxs-lookup"><span data-stu-id="babca-696">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="babca-697">İzleme kanatları</span><span class="sxs-lookup"><span data-stu-id="babca-697">Monitoring blades</span></span>

<span data-ttu-id="babca-698">İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-698">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="babca-699">Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-699">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="babca-700">ASP.NET Core uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-700">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="babca-701">Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="babca-701">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="babca-702">**GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-702">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="babca-703">**ASP.NET Core uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="babca-703">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="babca-704">Uzantılar yüklü değilse, **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-704">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="babca-705">Listeden **ASP.NET Core uzantılarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-705">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="babca-706">Yasal koşulları kabul etmek için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-706">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="babca-707">**Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-707">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="babca-708">Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="babca-708">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="babca-709">Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-709">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="babca-710">Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-710">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="babca-711">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-711">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-712">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-712">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-713">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-713">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
1. <span data-ttu-id="babca-714">Klasörü Wwwroot **yoluna açın** > **wwwroot** ve listenin altındaki *web.config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="babca-714">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="babca-715">*web.config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="babca-715">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="babca-716">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="babca-716">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="babca-717">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-717">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="babca-718">Tanılama günlüğünü etkinleştirmek için ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-718">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="babca-719">Azure portal **tanılama günlükleri** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-719">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="babca-720">**Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri** için **bir anahtar seçin** .</span><span class="sxs-lookup"><span data-stu-id="babca-720">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages** .</span></span> <span data-ttu-id="babca-721">Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-721">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="babca-722">Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-722">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing** .</span></span>
1. <span data-ttu-id="babca-723">Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-723">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="babca-724">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-724">Make a request to the app.</span></span>
1. <span data-ttu-id="babca-725">Günlük akışı verileri içinde hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="babca-725">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="babca-726">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="babca-726">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="babca-727">Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):</span><span class="sxs-lookup"><span data-stu-id="babca-727">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="babca-728">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-728">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="babca-729">Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-729">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="babca-730">[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.</span><span class="sxs-lookup"><span data-stu-id="babca-730">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="babca-731">Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="babca-731">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="babca-732">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-732">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="babca-733">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-733">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="babca-734">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-734">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-735">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-735">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="babca-736">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="babca-736">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="babca-737">Uygulama olay günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-737">Application Event Log (IIS)</span></span>

<span data-ttu-id="babca-738">Uygulama olay günlüğüne erişin:</span><span class="sxs-lookup"><span data-stu-id="babca-738">Access the Application Event Log:</span></span>

1. <span data-ttu-id="babca-739">Başlat menüsünü açın, *Olay Görüntüleyicisi* araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-739">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="babca-740">**Olay Görüntüleyicisi** , **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="babca-740">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="babca-741">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-741">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="babca-742">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="babca-742">Search for errors associated with the failing app.</span></span> <span data-ttu-id="babca-743">Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="babca-743">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="babca-744">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="babca-744">Run the app at a command prompt</span></span>

<span data-ttu-id="babca-745">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="babca-745">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="babca-746">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="babca-746">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="babca-747">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="babca-747">Framework-dependent deployment</span></span>

<span data-ttu-id="babca-748">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-748">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="babca-749">Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *dotnet.exe* yürüterek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-749">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe* .</span></span> <span data-ttu-id="babca-750">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="babca-750">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="babca-751">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="babca-751">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="babca-752">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-752">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="babca-753">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="babca-753">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="babca-754">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-754">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="babca-755">Kendi kendine kapsanan dağıtım</span><span class="sxs-lookup"><span data-stu-id="babca-755">Self-contained deployment</span></span>

<span data-ttu-id="babca-756">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-756">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="babca-757">Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-757">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="babca-758">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="babca-758">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="babca-759">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="babca-759">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="babca-760">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-760">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="babca-761">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="babca-761">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="babca-762">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-762">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="babca-763">ASP.NET Core Module stdout günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-763">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="babca-764">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="babca-764">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="babca-765">Barındırma sistemindeki sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-765">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="babca-766">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-766">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="babca-767">MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.</span><span class="sxs-lookup"><span data-stu-id="babca-767">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="babca-768">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-768">Edit the *web.config* file.</span></span> <span data-ttu-id="babca-769">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="babca-769">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="babca-770">`stdout` yolunda günlük dosyası adı ön eki bulunur.</span><span class="sxs-lookup"><span data-stu-id="babca-770">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="babca-771">Günlük oluşturulduğunda zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="babca-771">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="babca-772">`stdout`Dosya adı ön eki olarak kullanıldığında, tipik bir günlük dosyası *stdout_20180205184032_5412. log* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="babca-772">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log* .</span></span>
1. <span data-ttu-id="babca-773">Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="babca-773">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="babca-774">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="babca-774">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="babca-775">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-775">Make a request to the app.</span></span>
1. <span data-ttu-id="babca-776">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-776">Navigate to the *logs* folder.</span></span> <span data-ttu-id="babca-777">En son stdout günlüğünü bulup açın.</span><span class="sxs-lookup"><span data-stu-id="babca-777">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="babca-778">Günlüğü hatalara karşı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-778">Study the log for errors.</span></span>

<span data-ttu-id="babca-779">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="babca-779">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="babca-780">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-780">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="babca-781">**StdoutLogEnabled** olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="babca-781">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="babca-782">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="babca-782">Save the file.</span></span>

<span data-ttu-id="babca-783">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="babca-783">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="babca-784">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-784">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="babca-785">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-785">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="babca-786">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-786">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-787">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-787">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="babca-788">ASP.NET Core modülü hata ayıklama günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-788">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="babca-789">ASP.NET Core modülü hata ayıklama günlüğünü etkinleştirmek için aşağıdaki işleyici ayarlarını uygulamanın *web.config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-789">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="babca-790">Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-790">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="babca-791">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="babca-791">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="babca-792">Geliştirici özel durum sayfasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="babca-792">Enable the Developer Exception Page</span></span>

<span data-ttu-id="babca-793">`ASPNETCORE_ENVIRONMENT` [Ortam değişkeni](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , uygulamayı geliştirme ortamında çalıştırmak için web.configeklenebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-793">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="babca-794">Ortam, ana bilgisayar Oluşturucu 'da uygulama başlangıcında geçersiz kılınmadığı sürece `UseEnvironment` , ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="babca-794">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="babca-795">Ortam değişkeninin ayarlanması `ASPNETCORE_ENVIRONMENT` yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="babca-795">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="babca-796">Sorun giderme işleminden sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="babca-796">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="babca-797">*web.config* ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="babca-797">For information on setting environment variables in *web.config* , see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="babca-798">Uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="babca-798">Obtain data from an app</span></span>

<span data-ttu-id="babca-799">Bir uygulamanın isteklere yanıt verme yeteneği varsa, Terminal satır içi ara yazılımı kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="babca-799">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="babca-800">Daha fazla bilgi ve örnek kod için bkz <xref:test/troubleshoot#obtain-data-from-an-app> ..</span><span class="sxs-lookup"><span data-stu-id="babca-800">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="babca-801">Yavaş veya askıda olan uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-801">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="babca-802">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-802">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="babca-803">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="babca-803">App crashes or encounters an exception</span></span>

<span data-ttu-id="babca-804">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="babca-804">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="babca-805">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="babca-805">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="babca-806">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="babca-806">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="babca-807">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-807">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="babca-808">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-808">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="babca-809">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-809">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="babca-810">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-810">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="babca-811">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-811">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="babca-812">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-812">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="babca-813">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-813">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="babca-814">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="babca-814">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="babca-815">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="babca-815">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="babca-816">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="babca-816">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="babca-817">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="babca-817">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="babca-818">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="babca-818">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="babca-819">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="babca-819">Analyze the dump</span></span>

<span data-ttu-id="babca-820">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-820">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="babca-821">Daha fazla bilgi için bkz. [User-Mode döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="babca-821">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="babca-822">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="babca-822">Clear package caches</span></span>

<span data-ttu-id="babca-823">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-823">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="babca-824">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-824">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="babca-825">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="babca-825">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="babca-826">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="babca-826">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="babca-827">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-827">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="babca-828">Paket önbelleklerini Temizleme, [nuget.exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komutu yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="babca-828">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="babca-829">*nuget.exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="babca-829">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="babca-830">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-830">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="babca-831">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="babca-831">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="babca-832">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="babca-832">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="babca-833">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="babca-833">Azure documentation</span></span>

* [<span data-ttu-id="babca-834">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="babca-834">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="babca-835">Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü</span><span class="sxs-lookup"><span data-stu-id="babca-835">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="babca-836">Azure App Service tanılamada genel bakış</span><span class="sxs-lookup"><span data-stu-id="babca-836">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="babca-837">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="babca-837">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="babca-838">Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-838">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="babca-839">Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme</span><span class="sxs-lookup"><span data-stu-id="babca-839">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="babca-840">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-840">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="babca-841">Azure 'da Web Apps için uygulama performansı SSS</span><span class="sxs-lookup"><span data-stu-id="babca-841">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="babca-842">Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="babca-842">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="babca-843">Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="babca-843">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="babca-844">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="babca-844">Visual Studio documentation</span></span>

* [<span data-ttu-id="babca-845">Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="babca-845">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="babca-846">Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="babca-846">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="babca-847">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="babca-847">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="babca-848">Visual Studio Code belgeleri</span><span class="sxs-lookup"><span data-stu-id="babca-848">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="babca-849">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="babca-849">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="babca-850">Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="babca-850">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="babca-851">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="babca-851">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="babca-852">Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="babca-852">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="babca-853">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-853">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="babca-854">Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-854">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="babca-855">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="babca-855">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="babca-856">IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-856">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="babca-857">Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="babca-857">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="babca-858">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="babca-858">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="babca-859">Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="babca-859">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="babca-860">Ek Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="babca-860">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="babca-861">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="babca-861">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="babca-862">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="babca-862">App startup errors</span></span>

<span data-ttu-id="babca-863">Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="babca-863">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="babca-864">Yerel olarak hata ayıklamada oluşan *502,5 Işlem hatası* , bu konudaki öneri kullanılarak tanılanabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-864">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="babca-865">403,14 yasak</span><span class="sxs-lookup"><span data-stu-id="babca-865">403.14 Forbidden</span></span>

<span data-ttu-id="babca-866">Uygulama başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-866">The app fails to start.</span></span> <span data-ttu-id="babca-867">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="babca-867">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="babca-868">Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:</span><span class="sxs-lookup"><span data-stu-id="babca-868">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="babca-869">Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="babca-869">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="babca-870">Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="babca-870">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="babca-871">*web.config* dosyası dağıtımda yok veya *web.config* dosya içeriği hatalı biçimlendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="babca-871">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="babca-872">Aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="babca-872">Perform the following steps:</span></span>

1. <span data-ttu-id="babca-873">Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.</span><span class="sxs-lookup"><span data-stu-id="babca-873">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="babca-874">Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="babca-874">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="babca-875">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-875">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="babca-876">Azure App Service barındırırken, uygulamanın klasöre dağıtıldığını doğrulayın `D:\home\site\wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="babca-876">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="babca-877">Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında** gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-877">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager** 's **Basic Settings** .</span></span>
1. <span data-ttu-id="babca-878">Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-878">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="babca-879">Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..</span><span class="sxs-lookup"><span data-stu-id="babca-879">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="babca-880">*web.config* dosyası hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="babca-880">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="babca-881">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="babca-881">500 Internal Server Error</span></span>

<span data-ttu-id="babca-882">Uygulama başlıyor, ancak bir hata sunucunun isteği yerine getirmesini engelliyor.</span><span class="sxs-lookup"><span data-stu-id="babca-882">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="babca-883">Bu hata, başlangıç sırasında veya Yanıt oluştururken uygulamanın kodunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="babca-883">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="babca-884">Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-884">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="babca-885">Uygulama olay günlüğü genellikle uygulamanın normal olarak başlatıldığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="babca-885">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="babca-886">Sunucu perspektifinden doğru.</span><span class="sxs-lookup"><span data-stu-id="babca-886">From the server's perspective, that's correct.</span></span> <span data-ttu-id="babca-887">Uygulama başlatıldı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-887">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="babca-888">Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="babca-888">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="babca-889">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="babca-889">502.5 Process Failure</span></span>

<span data-ttu-id="babca-890">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="babca-890">The worker process fails.</span></span> <span data-ttu-id="babca-891">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-891">The app doesn't start.</span></span>

<span data-ttu-id="babca-892">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="babca-892">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="babca-893">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-893">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="babca-894">Yaygın bir hata durumu, ASP.NET Core paylaşılan bir Framework 'ün mevcut olmayan bir sürümünü hedeflediğinden, uygulamanın yanlış yapılandırılmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="babca-894">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="babca-895">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-895">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="babca-896">*Paylaşılan çerçeve* , makinede yüklü olan ve bir metapackage tarafından başvurulan derleme ( *. dll* dosyaları) kümesidir `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="babca-896">The *shared framework* is the set of assemblies ( *.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="babca-897">Metapackage başvurusu, gerekli en düşük sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-897">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="babca-898">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="babca-898">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="babca-899">Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:</span><span class="sxs-lookup"><span data-stu-id="babca-899">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="babca-900">Uygulama başlatılamadı (hata kodu ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="babca-900">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="babca-901">Uygulamanın derlemesi ( *. dll* ) yüklenemediğinden uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="babca-901">The app failed to start because the app's assembly ( *.dll* ) couldn't be loaded.</span></span>

<span data-ttu-id="babca-902">Bu hata, yayımlanan uygulama ile W3wp/iisexpress işlemi arasında bir bit durumu uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="babca-902">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="babca-903">Uygulama havuzunun 32 bit ayarının doğru olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="babca-903">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="babca-904">IIS yöneticisinin **uygulama havuzlarında** uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-904">Select the app pool in IIS Manager's **Application Pools** .</span></span>
1. <span data-ttu-id="babca-905">**Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-905">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="babca-906">**Enable 32 bit uygulamalarını** ayarla:</span><span class="sxs-lookup"><span data-stu-id="babca-906">Set **Enable 32-Bit Applications** :</span></span>
   * <span data-ttu-id="babca-907">32-bit (x86) bir uygulama dağıtıyorsanız, değerini olarak ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="babca-907">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="babca-908">64 bit (x64) uygulaması dağıtıyorsanız, değerini olarak ayarlayın `False` .</span><span class="sxs-lookup"><span data-stu-id="babca-908">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="babca-909">`<Platform>`Proje dosyasındaki bir MSBuild özelliği ve uygulamanın yayınlanan bit durumu arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-909">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="babca-910">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="babca-910">Connection reset</span></span>

<span data-ttu-id="babca-911">Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="babca-911">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="babca-912">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirilmesi sırasında bir hata oluştuğunda meydana gelir.</span><span class="sxs-lookup"><span data-stu-id="babca-912">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="babca-913">Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="babca-913">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="babca-914">[Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-914">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="babca-915">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="babca-915">Default startup limits</span></span>

<span data-ttu-id="babca-916">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="babca-916">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="babca-917">Varsayılan değerde sol tarafta, modül bir işlem hatası günlüğe kaydedilmeden önce uygulamanın başlaması iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-917">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="babca-918">Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="babca-918">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="babca-919">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-919">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="babca-920">Uygulama olay günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-920">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="babca-921">Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="babca-921">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="babca-922">Azure portal uygulama **Hizmetleri** ' nde uygulamayı açın.</span><span class="sxs-lookup"><span data-stu-id="babca-922">In the Azure portal, open the app in **App Services** .</span></span>
1. <span data-ttu-id="babca-923">**Sorunları tanılama ve çözme** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-923">Select **Diagnose and solve problems** .</span></span>
1. <span data-ttu-id="babca-924">**Tanılama araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-924">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="babca-925">**Destek Araçları** ' nın altında, **uygulama olayları** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-925">Under **Support Tools** , select the **Application Events** button.</span></span>
1. <span data-ttu-id="babca-926">**Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-926">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="babca-927">**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="babca-927">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="babca-928">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="babca-928">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="babca-929">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-929">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-930">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-930">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-931">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-931">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
1. <span data-ttu-id="babca-932">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="babca-932">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="babca-933">*eventlog.xml* dosyasının yanındaki kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-933">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="babca-934">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-934">Examine the log.</span></span> <span data-ttu-id="babca-935">En son olayları görmek için günlüğün en altına gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-935">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="babca-936">Uygulamayı kudu konsolunda çalıştırma</span><span class="sxs-lookup"><span data-stu-id="babca-936">Run the app in the Kudu console</span></span>

<span data-ttu-id="babca-937">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="babca-937">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="babca-938">Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="babca-938">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="babca-939">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="babca-939">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="babca-940">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-940">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-941">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-941">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-942">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-942">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="babca-943">32 bit (x86) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="babca-943">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="babca-944">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="babca-944">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="babca-945">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-945">Run the app:</span></span>
   * <span data-ttu-id="babca-946">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-946">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="babca-947">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-947">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="babca-948">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-948">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="babca-949">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="babca-949">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="babca-950">*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="babca-950">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="babca-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="babca-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="babca-952">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="babca-952">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="babca-953">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-953">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="babca-954">64 bit (x64) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="babca-954">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="babca-955">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="babca-955">**Current release**</span></span>

* <span data-ttu-id="babca-956">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-956">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="babca-957">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="babca-957">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="babca-958">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-958">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="babca-959">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="babca-959">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="babca-960">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-960">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="babca-961">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="babca-961">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="babca-962">*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="babca-962">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="babca-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="babca-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="babca-964">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="babca-964">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="babca-965">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="babca-965">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="babca-966">ASP.NET Core modülü stdout günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-966">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="babca-967">ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="babca-967">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="babca-968">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="babca-968">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="babca-969">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-969">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="babca-970">**Sorun kategorisini seçin** altında **Web uygulaması aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-970">Under **SELECT PROBLEM CATEGORY** , select the **Web App Down** button.</span></span>
1. <span data-ttu-id="babca-971">**Önerilen çözümler** > **stdout günlük yeniden yönlendirmeyi etkinleştirmek** için, **Web.Configdüzenlemek üzere kudu konsolunu açmak** için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-971">Under **Suggested Solutions** > **Enable Stdout Log Redirection** , select the button to **Open Kudu Console to edit Web.Config** .</span></span>
1. <span data-ttu-id="babca-972">Kudu **Tanılama konsolunda** , klasör Wwwroot **yolunu açın**  >  **wwwroot** .</span><span class="sxs-lookup"><span data-stu-id="babca-972">In the Kudu **Diagnostic Console** , open the folders to the path **site** > **wwwroot** .</span></span> <span data-ttu-id="babca-973">Listenin altındaki *web.config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="babca-973">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="babca-974">*web.config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="babca-974">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="babca-975">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="babca-975">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="babca-976">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-976">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="babca-977">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-977">Make a request to the app.</span></span>
1. <span data-ttu-id="babca-978">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="babca-978">Return to the Azure portal.</span></span> <span data-ttu-id="babca-979">**GELIŞTIRME araçları** alanında **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-979">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="babca-980">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-980">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-981">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-981">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-982">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-982">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
1. <span data-ttu-id="babca-983">**LogFiles** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-983">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="babca-984">**Değiştirilen** sütunu inceleyin ve son değiştirilme tarihiyle stdout günlüğünü düzenlemek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-984">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="babca-985">Günlük dosyası açıldığında hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="babca-985">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="babca-986">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="babca-986">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="babca-987">Kudu **Tanılama Konsolu** 'nda, **site**  >  *web.config* dosyasını açığa çıkarmak için **Wwwroot** yolu sitesine dönün.</span><span class="sxs-lookup"><span data-stu-id="babca-987">In the Kudu **Diagnostic Console** , return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="babca-988">Kalem simgesini seçerek **web.config** dosyasını tekrar açın.</span><span class="sxs-lookup"><span data-stu-id="babca-988">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="babca-989">**StdoutLogEnabled** olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="babca-989">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="babca-990">Dosyayı kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-990">Select **Save** to save the file.</span></span>

<span data-ttu-id="babca-991">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="babca-991">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="babca-992">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-992">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="babca-993">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-993">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="babca-994">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-994">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="babca-995">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-995">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-996">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-996">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="babca-997">Yavaş veya askıda olan uygulama (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="babca-997">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="babca-998">Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="babca-998">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="babca-999">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-999">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="babca-1000">Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın</span><span class="sxs-lookup"><span data-stu-id="babca-1000">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="babca-1001">İzleme kanatları</span><span class="sxs-lookup"><span data-stu-id="babca-1001">Monitoring blades</span></span>

<span data-ttu-id="babca-1002">İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="babca-1002">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="babca-1003">Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1003">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="babca-1004">ASP.NET Core uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="babca-1004">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="babca-1005">Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="babca-1005">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="babca-1006">**GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1006">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="babca-1007">**ASP.NET Core uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="babca-1007">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="babca-1008">Uzantılar yüklü değilse, **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1008">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="babca-1009">Listeden **ASP.NET Core uzantılarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1009">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="babca-1010">Yasal koşulları kabul etmek için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1010">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="babca-1011">**Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1011">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="babca-1012">Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="babca-1012">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="babca-1013">Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-1013">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="babca-1014">Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1014">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="babca-1015">**Git &rarr;** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1015">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="babca-1016">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="babca-1016">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="babca-1017">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1017">Using the navigation bar at the top of the page, open **Debug console** and select **CMD** .</span></span>
1. <span data-ttu-id="babca-1018">Klasörü Wwwroot **yoluna açın** > **wwwroot** ve listenin altındaki *web.config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="babca-1018">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="babca-1019">*web.config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="babca-1019">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="babca-1020">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="babca-1020">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="babca-1021">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1021">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="babca-1022">Tanılama günlüğünü etkinleştirmek için ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="babca-1022">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="babca-1023">Azure portal **tanılama günlükleri** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1023">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="babca-1024">**Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri** için **bir anahtar seçin** .</span><span class="sxs-lookup"><span data-stu-id="babca-1024">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages** .</span></span> <span data-ttu-id="babca-1025">Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1025">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="babca-1026">Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1026">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing** .</span></span>
1. <span data-ttu-id="babca-1027">Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1027">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="babca-1028">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-1028">Make a request to the app.</span></span>
1. <span data-ttu-id="babca-1029">Günlük akışı verileri içinde hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1029">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="babca-1030">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="babca-1030">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="babca-1031">Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):</span><span class="sxs-lookup"><span data-stu-id="babca-1031">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="babca-1032">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-1032">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="babca-1033">Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1033">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="babca-1034">[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.</span><span class="sxs-lookup"><span data-stu-id="babca-1034">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="babca-1035">Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="babca-1035">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="babca-1036">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1036">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="babca-1037">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-1037">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="babca-1038">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-1038">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-1039">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-1039">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="babca-1040">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="babca-1040">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="babca-1041">Uygulama olay günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-1041">Application Event Log (IIS)</span></span>

<span data-ttu-id="babca-1042">Uygulama olay günlüğüne erişin:</span><span class="sxs-lookup"><span data-stu-id="babca-1042">Access the Application Event Log:</span></span>

1. <span data-ttu-id="babca-1043">Başlat menüsünü açın, *Olay Görüntüleyicisi* araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1043">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="babca-1044">**Olay Görüntüleyicisi** , **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="babca-1044">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="babca-1045">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="babca-1045">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="babca-1046">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="babca-1046">Search for errors associated with the failing app.</span></span> <span data-ttu-id="babca-1047">Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="babca-1047">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="babca-1048">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="babca-1048">Run the app at a command prompt</span></span>

<span data-ttu-id="babca-1049">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="babca-1049">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="babca-1050">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="babca-1050">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="babca-1051">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="babca-1051">Framework-dependent deployment</span></span>

<span data-ttu-id="babca-1052">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-1052">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="babca-1053">Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *dotnet.exe* yürüterek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-1053">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe* .</span></span> <span data-ttu-id="babca-1054">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="babca-1054">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="babca-1055">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="babca-1055">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="babca-1056">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-1056">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="babca-1057">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="babca-1057">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="babca-1058">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1058">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="babca-1059">Kendi kendine kapsanan dağıtım</span><span class="sxs-lookup"><span data-stu-id="babca-1059">Self-contained deployment</span></span>

<span data-ttu-id="babca-1060">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="babca-1060">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="babca-1061">Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-1061">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="babca-1062">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="babca-1062">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="babca-1063">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="babca-1063">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="babca-1064">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-1064">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="babca-1065">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="babca-1065">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="babca-1066">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1066">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="babca-1067">ASP.NET Core Module stdout günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-1067">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="babca-1068">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="babca-1068">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="babca-1069">Barındırma sistemindeki sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-1069">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="babca-1070">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-1070">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="babca-1071">MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.</span><span class="sxs-lookup"><span data-stu-id="babca-1071">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="babca-1072">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-1072">Edit the *web.config* file.</span></span> <span data-ttu-id="babca-1073">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="babca-1073">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="babca-1074">`stdout` yolunda günlük dosyası adı ön eki bulunur.</span><span class="sxs-lookup"><span data-stu-id="babca-1074">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="babca-1075">Günlük oluşturulduğunda zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="babca-1075">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="babca-1076">`stdout`Dosya adı ön eki olarak kullanıldığında, tipik bir günlük dosyası *stdout_20180205184032_5412. log* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="babca-1076">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log* .</span></span>
1. <span data-ttu-id="babca-1077">Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="babca-1077">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="babca-1078">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="babca-1078">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="babca-1079">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="babca-1079">Make a request to the app.</span></span>
1. <span data-ttu-id="babca-1080">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="babca-1080">Navigate to the *logs* folder.</span></span> <span data-ttu-id="babca-1081">En son stdout günlüğünü bulup açın.</span><span class="sxs-lookup"><span data-stu-id="babca-1081">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="babca-1082">Günlüğü hatalara karşı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-1082">Study the log for errors.</span></span>

<span data-ttu-id="babca-1083">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="babca-1083">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="babca-1084">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-1084">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="babca-1085">**StdoutLogEnabled** olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="babca-1085">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="babca-1086">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="babca-1086">Save the file.</span></span>

<span data-ttu-id="babca-1087">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="babca-1087">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="babca-1088">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1088">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="babca-1089">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="babca-1089">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="babca-1090">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="babca-1090">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="babca-1091">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="babca-1091">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="babca-1092">Geliştirici özel durum sayfasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="babca-1092">Enable the Developer Exception Page</span></span>

<span data-ttu-id="babca-1093">`ASPNETCORE_ENVIRONMENT` [Ortam değişkeni](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , uygulamayı geliştirme ortamında çalıştırmak için web.configeklenebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1093">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="babca-1094">Ortam, ana bilgisayar Oluşturucu 'da uygulama başlangıcında geçersiz kılınmadığı sürece `UseEnvironment` , ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="babca-1094">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="babca-1095">Ortam değişkeninin ayarlanması `ASPNETCORE_ENVIRONMENT` yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1095">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="babca-1096">Sorun giderme işleminden sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="babca-1096">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="babca-1097">*web.config* ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="babca-1097">For information on setting environment variables in *web.config* , see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="babca-1098">Uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="babca-1098">Obtain data from an app</span></span>

<span data-ttu-id="babca-1099">Bir uygulamanın isteklere yanıt verme yeteneği varsa, Terminal satır içi ara yazılımı kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="babca-1099">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="babca-1100">Daha fazla bilgi ve örnek kod için bkz <xref:test/troubleshoot#obtain-data-from-an-app> ..</span><span class="sxs-lookup"><span data-stu-id="babca-1100">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="babca-1101">Yavaş veya askıda olan uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="babca-1101">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="babca-1102">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1102">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="babca-1103">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="babca-1103">App crashes or encounters an exception</span></span>

<span data-ttu-id="babca-1104">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="babca-1104">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="babca-1105">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="babca-1105">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="babca-1106">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="babca-1106">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="babca-1107">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-1107">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="babca-1108">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-1108">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="babca-1109">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-1109">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="babca-1110">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="babca-1110">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="babca-1111">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-1111">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="babca-1112">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-1112">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="babca-1113">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe* için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="babca-1113">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="babca-1114">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1114">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="babca-1115">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="babca-1115">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="babca-1116">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="babca-1116">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="babca-1117">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="babca-1117">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="babca-1118">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="babca-1118">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="babca-1119">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="babca-1119">Analyze the dump</span></span>

<span data-ttu-id="babca-1120">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1120">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="babca-1121">Daha fazla bilgi için bkz. [User-Mode döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="babca-1121">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="babca-1122">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="babca-1122">Clear package caches</span></span>

<span data-ttu-id="babca-1123">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1123">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="babca-1124">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="babca-1124">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="babca-1125">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="babca-1125">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="babca-1126">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="babca-1126">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="babca-1127">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-1127">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="babca-1128">Paket önbelleklerini Temizleme, [nuget.exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komutu yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="babca-1128">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="babca-1129">*nuget.exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="babca-1129">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="babca-1130">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="babca-1130">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="babca-1131">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="babca-1131">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="babca-1132">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="babca-1132">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="babca-1133">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="babca-1133">Azure documentation</span></span>

* [<span data-ttu-id="babca-1134">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="babca-1134">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="babca-1135">Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü</span><span class="sxs-lookup"><span data-stu-id="babca-1135">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="babca-1136">Azure App Service tanılamada genel bakış</span><span class="sxs-lookup"><span data-stu-id="babca-1136">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="babca-1137">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="babca-1137">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="babca-1138">Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-1138">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="babca-1139">Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme</span><span class="sxs-lookup"><span data-stu-id="babca-1139">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="babca-1140">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="babca-1140">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="babca-1141">Azure 'da Web Apps için uygulama performansı SSS</span><span class="sxs-lookup"><span data-stu-id="babca-1141">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="babca-1142">Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="babca-1142">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="babca-1143">Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="babca-1143">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="babca-1144">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="babca-1144">Visual Studio documentation</span></span>

* [<span data-ttu-id="babca-1145">Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="babca-1145">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="babca-1146">Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="babca-1146">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="babca-1147">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="babca-1147">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="babca-1148">Visual Studio Code belgeleri</span><span class="sxs-lookup"><span data-stu-id="babca-1148">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="babca-1149">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="babca-1149">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
