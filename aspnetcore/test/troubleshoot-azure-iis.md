---
title: Azure App Service ve IIS 'de ASP.NET Core sorunlarını giderme
author: rick-anderson
description: ASP.NET Core uygulamalarının Azure App Service ve Internet Information Services (IIS) dağıtımlarıyla ilgili sorunları tanılamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 17ada36c40997353528f922bece5acc34ce760d2
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445391"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="967f4-103">Azure App Service ve IIS 'de ASP.NET Core sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="967f4-104">, [Kotin Kotalik](https://github.com/jkotalik) tarafından</span><span class="sxs-lookup"><span data-stu-id="967f4-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="967f4-105">Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="967f4-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="967f4-106">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="967f4-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="967f4-107">Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="967f4-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="967f4-108">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="967f4-109">Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="967f4-110">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="967f4-111">IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="967f4-112">Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="967f4-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="967f4-113">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="967f4-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="967f4-114">Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="967f4-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="967f4-115">Ek Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="967f4-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="967f4-116">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="967f4-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="967f4-117">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="967f4-117">App startup errors</span></span>

<span data-ttu-id="967f4-118">Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="967f4-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="967f4-119">*502,5-Işlem hatası* veya yerel olarak hata ayıklarken oluşan *500,30-başlatma hatası* , bu konudaki öneri kullanılarak tanılanabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="967f4-120">403,14 yasak</span><span class="sxs-lookup"><span data-stu-id="967f4-120">403.14 Forbidden</span></span>

<span data-ttu-id="967f4-121">Uygulama başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-121">The app fails to start.</span></span> <span data-ttu-id="967f4-122">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="967f4-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="967f4-123">Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:</span><span class="sxs-lookup"><span data-stu-id="967f4-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="967f4-124">Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="967f4-125">Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="967f4-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="967f4-126">*web.config* dosyası dağıtımda yok veya *web.config* dosya içeriği hatalı biçimlendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="967f4-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="967f4-127">Aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="967f4-127">Perform the following steps:</span></span>

1. <span data-ttu-id="967f4-128">Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.</span><span class="sxs-lookup"><span data-stu-id="967f4-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="967f4-129">Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="967f4-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="967f4-130">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="967f4-131">Azure App Service barındırırken, uygulamanın klasöre dağıtıldığını doğrulayın `D:\home\site\wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="967f4-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="967f4-132">Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında**gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="967f4-133">Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="967f4-134">Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..</span><span class="sxs-lookup"><span data-stu-id="967f4-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="967f4-135">*web.config* dosyası hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="967f4-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="967f4-136">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-136">500 Internal Server Error</span></span>

<span data-ttu-id="967f4-137">Uygulama başlıyor, ancak bir hata sunucunun isteği yerine getirmesini engelliyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="967f4-138">Bu hata, başlangıç sırasında veya Yanıt oluştururken uygulamanın kodunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="967f4-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="967f4-139">Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="967f4-140">Uygulama olay günlüğü genellikle uygulamanın normal olarak başlatıldığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="967f4-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="967f4-141">Sunucu perspektifinden doğru.</span><span class="sxs-lookup"><span data-stu-id="967f4-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="967f4-142">Uygulama başlatıldı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="967f4-143">Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="967f4-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="967f4-144">500,0 Işlem içi Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="967f4-145">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-145">The worker process fails.</span></span> <span data-ttu-id="967f4-146">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-146">The app doesn't start.</span></span>

<span data-ttu-id="967f4-147">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) bileşenleri yüklenirken bilinmeyen bir hata oluştu.</span><span class="sxs-lookup"><span data-stu-id="967f4-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="967f4-148">Aşağıdaki eylemlerden birini uygulayın:</span><span class="sxs-lookup"><span data-stu-id="967f4-148">Take one of the following actions:</span></span>

* <span data-ttu-id="967f4-149">[Microsoft desteği](https://support.microsoft.com/oas/default.aspx?prid=15832) iletişim kurun ( **Geliştirici Araçları** ve **ASP.NET Core**' i seçin).</span><span class="sxs-lookup"><span data-stu-id="967f4-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="967f4-150">Stack Overflow soru sorun.</span><span class="sxs-lookup"><span data-stu-id="967f4-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="967f4-151">[GitHub deponuzda](https://github.com/dotnet/AspNetCore)bir sorun yapın.</span><span class="sxs-lookup"><span data-stu-id="967f4-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="967f4-152">500,30 Işlem içi başlatma hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="967f4-153">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-153">The worker process fails.</span></span> <span data-ttu-id="967f4-154">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-154">The app doesn't start.</span></span>

<span data-ttu-id="967f4-155">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR 'yi işlem içi başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="967f4-156">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="967f4-157">Yaygın hata koşulları:</span><span class="sxs-lookup"><span data-stu-id="967f4-157">Common failure conditions:</span></span>

* <span data-ttu-id="967f4-158">Mevcut olmayan ASP.NET Core paylaşılan çerçevesinin bir sürümünün hedeflenmesi nedeniyle uygulama yanlış yapılandırılmış.</span><span class="sxs-lookup"><span data-stu-id="967f4-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="967f4-159">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="967f4-160">Azure Key Vault kullanarak Key Vault izinlerin bulunmaması.</span><span class="sxs-lookup"><span data-stu-id="967f4-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="967f4-161">Doğru izinlerin verildiğinden emin olmak için hedeflenen Key Vault erişim ilkelerini kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="967f4-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="967f4-162">500,31 ANCM yerel bağımlılıklar bulunamadı</span><span class="sxs-lookup"><span data-stu-id="967f4-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="967f4-163">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-163">The worker process fails.</span></span> <span data-ttu-id="967f4-164">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-164">The app doesn't start.</span></span>

<span data-ttu-id="967f4-165">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) , .NET Core çalışma zamanını işlem içinde başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="967f4-166">Bu başlatma hatasının en yaygın nedeni, `Microsoft.NETCore.App` veya `Microsoft.AspNetCore.App` çalışma zamanının yüklenmemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="967f4-167">Uygulama, hedef ASP.NET Core 3,0 ' ye dağıtılmışsa ve bu sürüm makinede yoksa, bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="967f4-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="967f4-168">Örnek bir hata iletisi aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="967f4-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="967f4-169">Hata iletisi, yüklü tüm .NET Core sürümlerini ve uygulama tarafından istenen sürümü listeler.</span><span class="sxs-lookup"><span data-stu-id="967f4-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="967f4-170">Bu hatayı onarmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="967f4-170">To fix this error, either:</span></span>

* <span data-ttu-id="967f4-171">Uygun .NET Core sürümünü makineye yükler.</span><span class="sxs-lookup"><span data-stu-id="967f4-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="967f4-172">Uygulamayı, makinede bulunan .NET Core 'un bir sürümünü hedefleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="967f4-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="967f4-173">Uygulamayı [kendi kendine kapsanan bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)olarak yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="967f4-174">Geliştirme aşamasında çalışırken ( `ASPNETCORE_ENVIRONMENT` ortam değişkeni olarak ayarlandığında `Development` ), özel hata HTTP yanıtına yazılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="967f4-175">İşlem başlatma hatasının nedeni uygulama olay günlüğünde de bulunur.</span><span class="sxs-lookup"><span data-stu-id="967f4-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="967f4-176">500,32 ANCM dll yüklenemedi</span><span class="sxs-lookup"><span data-stu-id="967f4-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="967f4-177">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-177">The worker process fails.</span></span> <span data-ttu-id="967f4-178">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-178">The app doesn't start.</span></span>

<span data-ttu-id="967f4-179">Bu hatanın en yaygın nedeni, uygulamanın uyumsuz bir işlemci mimarisi için yayımlanmakta olması olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="967f4-180">Çalışan işlemi 32 bitlik bir uygulama olarak çalışıyorsa ve uygulama 64 bit hedef için yayımlandıysa, bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="967f4-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="967f4-181">Bu hatayı onarmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="967f4-181">To fix this error, either:</span></span>

* <span data-ttu-id="967f4-182">Çalışan işlemle aynı işlemci mimarisi için uygulamayı yeniden yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="967f4-183">Uygulamayı [çerçeveye bağlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-executables-fde)olarak yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="967f4-184">500,33 ANCM Istek Işleyicisi yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="967f4-185">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-185">The worker process fails.</span></span> <span data-ttu-id="967f4-186">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-186">The app doesn't start.</span></span>

<span data-ttu-id="967f4-187">Uygulama çerçeveye başvurmadı `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="967f4-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="967f4-188">Yalnızca çerçeveyi hedefleyen uygulamalar `Microsoft.AspNetCore.App` [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module)tarafından barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="967f4-189">Bu hatayı düzeltemedi, uygulamanın çerçeveyi hedeflediğinden emin olun `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="967f4-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="967f4-190">`.runtimeconfig.json`Uygulamanın hedeflediği çerçeveyi doğrulamak için ' i işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="967f4-191">500,34 ANCM karışık barındırma modelleri desteklenmez</span><span class="sxs-lookup"><span data-stu-id="967f4-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="967f4-192">Çalışan işlem, aynı işlemde hem işlem içi uygulama hem de işlem dışı bir uygulama çalıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="967f4-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="967f4-193">Bu hatayı onarmak için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="967f4-194">500,35 ANCM birden çok işlem Içi uygulama aynı Işlemde</span><span class="sxs-lookup"><span data-stu-id="967f4-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="967f4-195">Çalışan işlemi aynı işlemde birden çok işlem içi uygulama çalıştıramıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="967f4-196">Bu hatayı onarmak için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="967f4-197">500,36 ANCM Işlem dışı Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="967f4-198">İşlem dışı istek işleyicisi *aspnetcorev2_outofprocess.dll*, *aspnetcorev2.dll* dosyasının yanında değildir.</span><span class="sxs-lookup"><span data-stu-id="967f4-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="967f4-199">Bu, [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)bozuk bir yüklemesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="967f4-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="967f4-200">Bu hatayı gidermek için [.NET Core barındırma paketi](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS için) veya Visual Studio (IIS Express için) yüklemesini onarın.</span><span class="sxs-lookup"><span data-stu-id="967f4-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="967f4-201">500,37 ANCM başlangıç zamanı sınırı Içinde başlatılamadı</span><span class="sxs-lookup"><span data-stu-id="967f4-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="967f4-202">ANCM, kısımları başlangıç süresi sınırı içinde başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="967f4-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="967f4-203">Varsayılan olarak, zaman aşımı 120 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="967f4-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="967f4-204">Aynı makinede çok sayıda uygulama başlatılırken bu hata oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="967f4-205">Başlangıç sırasında sunucuda CPU/bellek kullanımı artışlarını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="967f4-206">Birden çok uygulamanın başlatma işlemini şaşırtmayı yapmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="967f4-207">500,38 ANCM uygulama DLL 'SI bulunamadı</span><span class="sxs-lookup"><span data-stu-id="967f4-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="967f4-208">ANCM, yürütülebilir dosyanın yanında olması gereken uygulama DLL 'sini bulamadı.</span><span class="sxs-lookup"><span data-stu-id="967f4-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="967f4-209">Bu hata, işlem içi barındırma modelini kullanarak [tek dosya yürütülebilir dosyası](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) olarak paketlenmiş bir uygulama barındırdığında oluşur.</span><span class="sxs-lookup"><span data-stu-id="967f4-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="967f4-210">İşlem içi modelde, ANCM 'nin .NET Core uygulamasını mevcut IIS işlemine yüklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="967f4-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="967f4-211">Bu senaryo, tek dosya dağıtım modeli tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="967f4-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="967f4-212">Bu hatayı onarmak için uygulamanın proje dosyasında aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="967f4-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="967f4-213">MSBuild özelliğini olarak ayarlayarak tek dosya yayımlamayı devre dışı bırakın `PublishSingleFile` `false` .</span><span class="sxs-lookup"><span data-stu-id="967f4-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="967f4-214">MSBuild özelliğini olarak ayarlayarak işlem dışı barındırma modeline geçiş yapın `AspNetCoreHostingModel` `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="967f4-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="967f4-215">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-215">502.5 Process Failure</span></span>

<span data-ttu-id="967f4-216">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-216">The worker process fails.</span></span> <span data-ttu-id="967f4-217">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-217">The app doesn't start.</span></span>

<span data-ttu-id="967f4-218">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="967f4-219">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="967f4-220">Yaygın bir hata durumu, ASP.NET Core paylaşılan bir Framework 'ün mevcut olmayan bir sürümünü hedeflediğinden, uygulamanın yanlış yapılandırılmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="967f4-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="967f4-221">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="967f4-222">*Paylaşılan çerçeve* , makinede yüklü olan ve bir metapackage tarafından başvurulan derleme (*. dll* dosyaları) kümesidir `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="967f4-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="967f4-223">Metapackage başvurusu, gerekli en düşük sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="967f4-224">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="967f4-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="967f4-225">Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:</span><span class="sxs-lookup"><span data-stu-id="967f4-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="967f4-226">Uygulama başlatılamadı (hata kodu ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="967f4-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="967f4-227">Uygulamanın derlemesi (*. dll*) yüklenemediğinden uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="967f4-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="967f4-228">Bu hata, yayımlanan uygulama ile W3wp/iisexpress işlemi arasında bir bit durumu uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="967f4-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="967f4-229">Uygulama havuzunun 32 bit ayarının doğru olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="967f4-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="967f4-230">IIS yöneticisinin **uygulama havuzlarında**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="967f4-231">**Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="967f4-232">**Enable 32 bit uygulamalarını**ayarla:</span><span class="sxs-lookup"><span data-stu-id="967f4-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="967f4-233">32-bit (x86) bir uygulama dağıtıyorsanız, değerini olarak ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="967f4-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="967f4-234">64 bit (x64) uygulaması dağıtıyorsanız, değerini olarak ayarlayın `False` .</span><span class="sxs-lookup"><span data-stu-id="967f4-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="967f4-235">`<Platform>`Proje dosyasındaki bir MSBuild özelliği ve uygulamanın yayınlanan bit durumu arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="967f4-236">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="967f4-236">Connection reset</span></span>

<span data-ttu-id="967f4-237">Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="967f4-238">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirilmesi sırasında bir hata oluştuğunda meydana gelir.</span><span class="sxs-lookup"><span data-stu-id="967f4-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="967f4-239">Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="967f4-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="967f4-240">[Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="967f4-241">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="967f4-241">Default startup limits</span></span>

<span data-ttu-id="967f4-242">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="967f4-243">Varsayılan değerde sol tarafta, modül bir işlem hatası günlüğe kaydedilmeden önce uygulamanın başlaması iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="967f4-244">Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="967f4-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="967f4-245">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="967f4-246">Uygulama olay günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="967f4-247">Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="967f4-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="967f4-248">Azure portal uygulama **Hizmetleri**' nde uygulamayı açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="967f4-249">**Sorunları tanılama ve çözme**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="967f4-250">**Tanılama araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="967f4-251">**Destek Araçları**' nın altında, **uygulama olayları** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="967f4-252">**Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="967f4-253">**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="967f4-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="967f4-254">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="967f4-255">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-256">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-257">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="967f4-258">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="967f4-259">*eventlog.xml* dosyasının yanındaki kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="967f4-260">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-260">Examine the log.</span></span> <span data-ttu-id="967f4-261">En son olayları görmek için günlüğün en altına gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="967f4-262">Uygulamayı kudu konsolunda çalıştırma</span><span class="sxs-lookup"><span data-stu-id="967f4-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="967f4-263">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="967f4-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="967f4-264">Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="967f4-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="967f4-265">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="967f4-266">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-267">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-268">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="967f4-269">32 bit (x86) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="967f4-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="967f4-270">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="967f4-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="967f4-271">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-271">Run the app:</span></span>
   * <span data-ttu-id="967f4-272">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="967f4-273">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="967f4-274">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="967f4-275">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="967f4-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="967f4-276">*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="967f4-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="967f4-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="967f4-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="967f4-278">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="967f4-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="967f4-279">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="967f4-280">64 bit (x64) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="967f4-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="967f4-281">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="967f4-281">**Current release**</span></span>

* <span data-ttu-id="967f4-282">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="967f4-283">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="967f4-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="967f4-284">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="967f4-285">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="967f4-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="967f4-286">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="967f4-287">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="967f4-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="967f4-288">*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="967f4-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="967f4-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="967f4-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="967f4-290">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="967f4-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="967f4-291">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="967f4-292">ASP.NET Core modülü stdout günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-293">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-293">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-294">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-294">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="967f4-295">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-295">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="967f4-296">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-296">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-297">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-297">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="967f4-298">ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967f4-298">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="967f4-299">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="967f4-299">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="967f4-300">Azure portalında Web uygulamasına gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-300">In the Azure Portal, navigate to the web app.</span></span>
1. <span data-ttu-id="967f4-301">**App Service** dikey penceresinde, arama kutusuna **kudu** girin.</span><span class="sxs-lookup"><span data-stu-id="967f4-301">In the **App Service** blade, enter **kudu** in the search box.</span></span>
1. <span data-ttu-id="967f4-302">**Gelişmiş Araçlar** > **Git**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-302">Select **Advanced Tools** > **Go**.</span></span>
1. <span data-ttu-id="967f4-303">**CMD > hata ayıklama konsolunu**seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-303">Select  **Debug console > CMD**.</span></span>
1. <span data-ttu-id="967f4-304">*Site/Wwwroot* 'a git</span><span class="sxs-lookup"><span data-stu-id="967f4-304">Navigate to *site/wwwroot*</span></span>
1. <span data-ttu-id="967f4-305">*web.config* dosyasını düzenlemek için kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-305">Select the pencil icon to edit the *web.config* file.</span></span>
1. <span data-ttu-id="967f4-306">`<aspNetCore />`Öğesinde, öğesini ayarlayın `stdoutLogEnabled="true"` ve **Kaydet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-306">In the `<aspNetCore />` element, set `stdoutLogEnabled="true"` and select **Save**.</span></span>

<span data-ttu-id="967f4-307">Ayar ile sorun giderme tamamlandığında stdout günlüğünü devre dışı bırakın `stdoutLogEnabled="false"` .</span><span class="sxs-lookup"><span data-stu-id="967f4-307">Disable stdout logging when troubleshooting is complete by setting `stdoutLogEnabled="false"`.</span></span>

<span data-ttu-id="967f4-308">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="967f4-308">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="967f4-309">ASP.NET Core modülü hata ayıklama günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-309">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="967f4-310">ASP.NET Core Module hata ayıklama günlüğü, ASP.NET Core modülünden daha ayrıntılı günlük kaydı sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-310">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="967f4-311">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="967f4-311">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="967f4-312">Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="967f4-312">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="967f4-313">Uygulamayı gelişmiş tanılama günlüğü için yapılandırmak üzere [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-313">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="967f4-314">Uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="967f4-314">Redeploy the app.</span></span>
   * <span data-ttu-id="967f4-315">`<handlerSettings>` [Gelişmiş tanılama günlüklerini](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilen, kudu konsolunu kullanarak canlı uygulamanın *web.config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-315">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="967f4-316">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-316">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="967f4-317">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-317">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-318">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-318">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="967f4-319">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-319">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="967f4-320">Klasörü Wwwroot **yoluna açın**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="967f4-320">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="967f4-321">*web.config* dosyasını, kurşun kalem düğmesini seçerek düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-321">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="967f4-322">Bölümü, `<handlerSettings>` [Gelişmiş tanılama günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-322">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="967f4-323">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-323">Select the **Save** button.</span></span>
1. <span data-ttu-id="967f4-324">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-324">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="967f4-325">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-325">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-326">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-326">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-327">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-327">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="967f4-328">Klasörü Wwwroot **yoluna açın**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="967f4-328">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="967f4-329">*Aspnetcore-Debug. log* dosyası için bir yol sağlamadıysanız dosya listede görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="967f4-329">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="967f4-330">Bir yol sağladıysanız, günlük dosyasının konumuna gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-330">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="967f4-331">Dosya adının yanındaki kurşun kalem düğmesiyle günlük dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-331">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="967f4-332">Sorun giderme tamamlandığında hata ayıklama günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="967f4-332">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="967f4-333">Gelişmiş hata ayıklama günlüğünü devre dışı bırakmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="967f4-333">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="967f4-334">`<handlerSettings>` *web.config* dosyasından yerel olarak kaldırın ve uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="967f4-334">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="967f4-335">*web.config* dosyasını düzenlemek ve bölümünü kaldırmak Için kudu konsolunu kullanın `<handlerSettings>` .</span><span class="sxs-lookup"><span data-stu-id="967f4-335">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="967f4-336">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="967f4-336">Save the file.</span></span>

<span data-ttu-id="967f4-337">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="967f4-337">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-338">Hata ayıklama günlüğünü devre dışı bırakma hatası, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-338">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-339">Günlük dosyası boyutunda sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-339">There's no limit on log file size.</span></span> <span data-ttu-id="967f4-340">Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-340">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="967f4-341">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-341">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-342">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-342">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="967f4-343">Yavaş veya askıda olan uygulama (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-343">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="967f4-344">Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="967f4-344">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="967f4-345">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-345">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="967f4-346">Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın</span><span class="sxs-lookup"><span data-stu-id="967f4-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="967f4-347">İzleme kanatları</span><span class="sxs-lookup"><span data-stu-id="967f4-347">Monitoring blades</span></span>

<span data-ttu-id="967f4-348">İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-348">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="967f4-349">Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-349">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="967f4-350">ASP.NET Core uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-350">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="967f4-351">Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="967f4-351">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="967f4-352">**GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-352">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="967f4-353">**ASP.NET Core uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="967f4-353">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="967f4-354">Uzantılar yüklü değilse, **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-354">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="967f4-355">Listeden **ASP.NET Core uzantılarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-355">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="967f4-356">Yasal koşulları kabul etmek için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-356">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="967f4-357">**Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-357">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="967f4-358">Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="967f4-358">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="967f4-359">Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-359">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="967f4-360">Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-360">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="967f4-361">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-361">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-362">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-362">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-363">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-363">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="967f4-364">Klasörü Wwwroot **yoluna açın** > **wwwroot** ve listenin altındaki *web.config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-364">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="967f4-365">*web.config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-365">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="967f4-366">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="967f4-366">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="967f4-367">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-367">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="967f4-368">Tanılama günlüğünü etkinleştirmek için ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-368">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="967f4-369">Azure portal **tanılama günlükleri** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-369">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="967f4-370">**Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri**için **bir anahtar seçin** .</span><span class="sxs-lookup"><span data-stu-id="967f4-370">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="967f4-371">Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-371">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="967f4-372">Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme**anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-372">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="967f4-373">Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-373">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="967f4-374">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-374">Make a request to the app.</span></span>
1. <span data-ttu-id="967f4-375">Günlük akışı verileri içinde hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-375">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="967f4-376">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="967f4-376">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="967f4-377">Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):</span><span class="sxs-lookup"><span data-stu-id="967f4-377">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="967f4-378">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-378">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="967f4-379">Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-379">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="967f4-380">[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-380">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="967f4-381">Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="967f4-381">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-382">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-382">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-383">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-383">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="967f4-384">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-385">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="967f4-386">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-386">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="967f4-387">Uygulama olay günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-387">Application Event Log (IIS)</span></span>

<span data-ttu-id="967f4-388">Uygulama olay günlüğüne erişin:</span><span class="sxs-lookup"><span data-stu-id="967f4-388">Access the Application Event Log:</span></span>

1. <span data-ttu-id="967f4-389">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-389">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="967f4-390">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-390">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="967f4-391">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-391">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="967f4-392">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-392">Search for errors associated with the failing app.</span></span> <span data-ttu-id="967f4-393">Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="967f4-393">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="967f4-394">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="967f4-394">Run the app at a command prompt</span></span>

<span data-ttu-id="967f4-395">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="967f4-395">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="967f4-396">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="967f4-396">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="967f4-397">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="967f4-397">Framework-dependent deployment</span></span>

<span data-ttu-id="967f4-398">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-398">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="967f4-399">Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *dotnet.exe*yürüterek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-399">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="967f4-400">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="967f4-400">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="967f4-401">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-401">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="967f4-402">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-402">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="967f4-403">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="967f4-403">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="967f4-404">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-404">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="967f4-405">Kendi kendine kapsanan dağıtım</span><span class="sxs-lookup"><span data-stu-id="967f4-405">Self-contained deployment</span></span>

<span data-ttu-id="967f4-406">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-406">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="967f4-407">Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-407">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="967f4-408">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="967f4-408">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="967f4-409">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-409">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="967f4-410">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-410">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="967f4-411">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="967f4-411">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="967f4-412">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-412">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="967f4-413">ASP.NET Core Module stdout günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-413">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="967f4-414">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="967f4-414">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="967f4-415">Barındırma sistemindeki sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-415">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="967f4-416">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-416">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="967f4-417">MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.</span><span class="sxs-lookup"><span data-stu-id="967f4-417">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="967f4-418">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-418">Edit the *web.config* file.</span></span> <span data-ttu-id="967f4-419">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="967f4-419">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="967f4-420">`stdout`yolunda günlük dosyası adı ön eki bulunur.</span><span class="sxs-lookup"><span data-stu-id="967f4-420">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="967f4-421">Günlük oluşturulduğunda zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="967f4-421">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="967f4-422">`stdout`Dosya adı ön eki olarak kullanıldığında, tipik bir günlük dosyası *stdout_20180205184032_5412. log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-422">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="967f4-423">Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="967f4-423">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="967f4-424">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="967f4-424">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="967f4-425">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-425">Make a request to the app.</span></span>
1. <span data-ttu-id="967f4-426">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-426">Navigate to the *logs* folder.</span></span> <span data-ttu-id="967f4-427">En son stdout günlüğünü bulup açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-427">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="967f4-428">Günlüğü hatalara karşı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-428">Study the log for errors.</span></span>

<span data-ttu-id="967f4-429">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="967f4-429">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="967f4-430">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-430">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="967f4-431">**StdoutLogEnabled** olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="967f4-431">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="967f4-432">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="967f4-432">Save the file.</span></span>

<span data-ttu-id="967f4-433">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="967f4-433">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-434">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-434">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-435">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-435">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="967f4-436">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-436">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-437">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-437">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="967f4-438">ASP.NET Core modülü hata ayıklama günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-438">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="967f4-439">ASP.NET Core modülü hata ayıklama günlüğünü etkinleştirmek için aşağıdaki işleyici ayarlarını uygulamanın *web.config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-439">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="967f4-440">Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-440">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="967f4-441">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="967f4-441">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="967f4-442">Geliştirici özel durum sayfasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="967f4-442">Enable the Developer Exception Page</span></span>

<span data-ttu-id="967f4-443">`ASPNETCORE_ENVIRONMENT` [Ortam değişkeni](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , uygulamayı geliştirme ortamında çalıştırmak için web.configeklenebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-443">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="967f4-444">Ortam, ana bilgisayar Oluşturucu 'da uygulama başlangıcında geçersiz kılınmadığı sürece `UseEnvironment` , ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="967f4-444">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="967f4-445">Ortam değişkeninin ayarlanması `ASPNETCORE_ENVIRONMENT` yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-445">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="967f4-446">Sorun giderme işleminden sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-446">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="967f4-447">*web.config*ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="967f4-447">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="967f4-448">Uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="967f4-448">Obtain data from an app</span></span>

<span data-ttu-id="967f4-449">Bir uygulamanın isteklere yanıt verme yeteneği varsa, Terminal satır içi ara yazılımı kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="967f4-449">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="967f4-450">Daha fazla bilgi ve örnek kod için bkz <xref:test/troubleshoot#obtain-data-from-an-app> ..</span><span class="sxs-lookup"><span data-stu-id="967f4-450">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="967f4-451">Yavaş veya askıda olan uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-451">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="967f4-452">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-452">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="967f4-453">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="967f4-453">App crashes or encounters an exception</span></span>

<span data-ttu-id="967f4-454">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="967f4-454">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="967f4-455">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="967f4-455">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="967f4-456">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967f4-456">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="967f4-457">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-457">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="967f4-458">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-458">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="967f4-459">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-459">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="967f4-460">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-460">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="967f4-461">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-461">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="967f4-462">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-462">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="967f4-463">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-463">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="967f4-464">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-464">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="967f4-465">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="967f4-465">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-466">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="967f4-466">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="967f4-467">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="967f4-467">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="967f4-468">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="967f4-468">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="967f4-469">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="967f4-469">Analyze the dump</span></span>

<span data-ttu-id="967f4-470">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-470">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="967f4-471">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="967f4-471">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="967f4-472">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="967f4-472">Clear package caches</span></span>

<span data-ttu-id="967f4-473">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-473">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="967f4-474">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-474">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="967f4-475">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="967f4-475">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="967f4-476">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="967f4-476">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="967f4-477">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-477">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="967f4-478">Paket önbelleklerini Temizleme, [nuget.exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komutu yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="967f4-478">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="967f4-479">*nuget.exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967f4-479">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="967f4-480">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-480">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="967f4-481">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="967f4-481">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="967f4-482">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="967f4-482">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="967f4-483">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="967f4-483">Azure documentation</span></span>

* [<span data-ttu-id="967f4-484">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="967f4-484">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="967f4-485">Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü</span><span class="sxs-lookup"><span data-stu-id="967f4-485">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="967f4-486">Azure App Service tanılamada genel bakış</span><span class="sxs-lookup"><span data-stu-id="967f4-486">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="967f4-487">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="967f4-487">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="967f4-488">Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-488">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="967f4-489">Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-489">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="967f4-490">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-490">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="967f4-491">Azure 'da Web Apps için uygulama performansı SSS</span><span class="sxs-lookup"><span data-stu-id="967f4-491">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="967f4-492">Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="967f4-492">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="967f4-493">Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="967f4-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="967f4-494">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="967f4-494">Visual Studio documentation</span></span>

* [<span data-ttu-id="967f4-495">Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="967f4-495">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="967f4-496">Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="967f4-496">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="967f4-497">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="967f4-497">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="967f4-498">Visual Studio Code belgeleri</span><span class="sxs-lookup"><span data-stu-id="967f4-498">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="967f4-499">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="967f4-499">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="967f4-500">Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="967f4-500">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="967f4-501">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="967f4-501">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="967f4-502">Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="967f4-502">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="967f4-503">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-503">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="967f4-504">Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-504">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="967f4-505">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-505">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="967f4-506">IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-506">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="967f4-507">Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="967f4-507">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="967f4-508">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="967f4-508">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="967f4-509">Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="967f4-509">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="967f4-510">Ek Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="967f4-510">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="967f4-511">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="967f4-511">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="967f4-512">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="967f4-512">App startup errors</span></span>

<span data-ttu-id="967f4-513">Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="967f4-513">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="967f4-514">*502,5-Işlem hatası* veya yerel olarak hata ayıklarken oluşan *500,30-başlatma hatası* , bu konudaki öneri kullanılarak tanılanabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-514">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="967f4-515">403,14 yasak</span><span class="sxs-lookup"><span data-stu-id="967f4-515">403.14 Forbidden</span></span>

<span data-ttu-id="967f4-516">Uygulama başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-516">The app fails to start.</span></span> <span data-ttu-id="967f4-517">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="967f4-517">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="967f4-518">Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:</span><span class="sxs-lookup"><span data-stu-id="967f4-518">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="967f4-519">Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-519">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="967f4-520">Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="967f4-520">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="967f4-521">*web.config* dosyası dağıtımda yok veya *web.config* dosya içeriği hatalı biçimlendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="967f4-521">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="967f4-522">Aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="967f4-522">Perform the following steps:</span></span>

1. <span data-ttu-id="967f4-523">Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.</span><span class="sxs-lookup"><span data-stu-id="967f4-523">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="967f4-524">Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="967f4-524">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="967f4-525">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-525">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="967f4-526">Azure App Service barındırırken, uygulamanın klasöre dağıtıldığını doğrulayın `D:\home\site\wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="967f4-526">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="967f4-527">Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında**gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-527">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="967f4-528">Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-528">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="967f4-529">Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..</span><span class="sxs-lookup"><span data-stu-id="967f4-529">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="967f4-530">*web.config* dosyası hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="967f4-530">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="967f4-531">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-531">500 Internal Server Error</span></span>

<span data-ttu-id="967f4-532">Uygulama başlıyor, ancak bir hata sunucunun isteği yerine getirmesini engelliyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-532">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="967f4-533">Bu hata, başlangıç sırasında veya Yanıt oluştururken uygulamanın kodunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="967f4-533">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="967f4-534">Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-534">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="967f4-535">Uygulama olay günlüğü genellikle uygulamanın normal olarak başlatıldığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="967f4-535">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="967f4-536">Sunucu perspektifinden doğru.</span><span class="sxs-lookup"><span data-stu-id="967f4-536">From the server's perspective, that's correct.</span></span> <span data-ttu-id="967f4-537">Uygulama başlatıldı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-537">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="967f4-538">Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="967f4-538">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="967f4-539">500,0 Işlem içi Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-539">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="967f4-540">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-540">The worker process fails.</span></span> <span data-ttu-id="967f4-541">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-541">The app doesn't start.</span></span>

<span data-ttu-id="967f4-542">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR 'yi bulamıyor ve işlem içi istek işleyicisini (*aspnetcorev2_inprocess.dll*) bulamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-542">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="967f4-543">Şunları denetleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-543">Check that:</span></span>

* <span data-ttu-id="967f4-544">Uygulama [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet paketini ya da [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'i hedefler.</span><span class="sxs-lookup"><span data-stu-id="967f4-544">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="967f4-545">Uygulamanın hedeflediği ASP.NET Core paylaşılan çerçevenin sürümü hedef makineye yüklendi.</span><span class="sxs-lookup"><span data-stu-id="967f4-545">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="967f4-546">500,0 Işlem dışı Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-546">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="967f4-547">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-547">The worker process fails.</span></span> <span data-ttu-id="967f4-548">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-548">The app doesn't start.</span></span>

<span data-ttu-id="967f4-549">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) işlem dışı barındırma isteği işleyicisini bulamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-549">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="967f4-550">*aspnetcorev2_outofprocess.dll* *aspnetcorev2.dll*yanındaki bir alt klasörde bulunduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="967f4-550">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="967f4-551">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-551">502.5 Process Failure</span></span>

<span data-ttu-id="967f4-552">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-552">The worker process fails.</span></span> <span data-ttu-id="967f4-553">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-553">The app doesn't start.</span></span>

<span data-ttu-id="967f4-554">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-554">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="967f4-555">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-555">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="967f4-556">Yaygın bir hata durumu, ASP.NET Core paylaşılan bir Framework 'ün mevcut olmayan bir sürümünü hedeflediğinden, uygulamanın yanlış yapılandırılmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="967f4-556">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="967f4-557">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-557">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="967f4-558">*Paylaşılan çerçeve* , makinede yüklü olan ve bir metapackage tarafından başvurulan derleme (*. dll* dosyaları) kümesidir `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="967f4-558">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="967f4-559">Metapackage başvurusu, gerekli en düşük sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-559">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="967f4-560">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="967f4-560">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="967f4-561">Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:</span><span class="sxs-lookup"><span data-stu-id="967f4-561">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="967f4-562">Uygulama başlatılamadı (hata kodu ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="967f4-562">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="967f4-563">Uygulamanın derlemesi (*. dll*) yüklenemediğinden uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="967f4-563">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="967f4-564">Bu hata, yayımlanan uygulama ile W3wp/iisexpress işlemi arasında bir bit durumu uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="967f4-564">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="967f4-565">Uygulama havuzunun 32 bit ayarının doğru olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="967f4-565">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="967f4-566">IIS yöneticisinin **uygulama havuzlarında**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-566">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="967f4-567">**Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-567">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="967f4-568">**Enable 32 bit uygulamalarını**ayarla:</span><span class="sxs-lookup"><span data-stu-id="967f4-568">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="967f4-569">32-bit (x86) bir uygulama dağıtıyorsanız, değerini olarak ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="967f4-569">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="967f4-570">64 bit (x64) uygulaması dağıtıyorsanız, değerini olarak ayarlayın `False` .</span><span class="sxs-lookup"><span data-stu-id="967f4-570">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="967f4-571">`<Platform>`Proje dosyasındaki bir MSBuild özelliği ve uygulamanın yayınlanan bit durumu arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-571">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="967f4-572">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="967f4-572">Connection reset</span></span>

<span data-ttu-id="967f4-573">Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-573">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="967f4-574">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirilmesi sırasında bir hata oluştuğunda meydana gelir.</span><span class="sxs-lookup"><span data-stu-id="967f4-574">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="967f4-575">Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="967f4-575">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="967f4-576">[Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-576">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="967f4-577">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="967f4-577">Default startup limits</span></span>

<span data-ttu-id="967f4-578">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="967f4-579">Varsayılan değerde sol tarafta, modül bir işlem hatası günlüğe kaydedilmeden önce uygulamanın başlaması iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-579">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="967f4-580">Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="967f4-580">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="967f4-581">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-581">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="967f4-582">Uygulama olay günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-582">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="967f4-583">Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="967f4-583">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="967f4-584">Azure portal uygulama **Hizmetleri**' nde uygulamayı açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-584">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="967f4-585">**Sorunları tanılama ve çözme**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-585">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="967f4-586">**Tanılama araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-586">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="967f4-587">**Destek Araçları**' nın altında, **uygulama olayları** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-587">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="967f4-588">**Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-588">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="967f4-589">**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="967f4-589">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="967f4-590">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-590">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="967f4-591">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-591">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-592">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-592">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-593">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-593">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="967f4-594">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-594">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="967f4-595">*eventlog.xml* dosyasının yanındaki kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-595">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="967f4-596">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-596">Examine the log.</span></span> <span data-ttu-id="967f4-597">En son olayları görmek için günlüğün en altına gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-597">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="967f4-598">Uygulamayı kudu konsolunda çalıştırma</span><span class="sxs-lookup"><span data-stu-id="967f4-598">Run the app in the Kudu console</span></span>

<span data-ttu-id="967f4-599">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="967f4-599">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="967f4-600">Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="967f4-600">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="967f4-601">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-601">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="967f4-602">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-602">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-603">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-603">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-604">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-604">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="967f4-605">32 bit (x86) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="967f4-605">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="967f4-606">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="967f4-606">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="967f4-607">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-607">Run the app:</span></span>
   * <span data-ttu-id="967f4-608">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-608">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="967f4-609">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-609">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="967f4-610">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-610">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="967f4-611">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="967f4-611">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="967f4-612">*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="967f4-612">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="967f4-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="967f4-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="967f4-614">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="967f4-614">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="967f4-615">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-615">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="967f4-616">64 bit (x64) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="967f4-616">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="967f4-617">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="967f4-617">**Current release**</span></span>

* <span data-ttu-id="967f4-618">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-618">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="967f4-619">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="967f4-619">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="967f4-620">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-620">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="967f4-621">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="967f4-621">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="967f4-622">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-622">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="967f4-623">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="967f4-623">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="967f4-624">*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="967f4-624">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="967f4-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="967f4-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="967f4-626">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="967f4-626">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="967f4-627">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-627">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="967f4-628">ASP.NET Core modülü stdout günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-628">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="967f4-629">ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967f4-629">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="967f4-630">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="967f4-630">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="967f4-631">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-631">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="967f4-632">**Sorun kategorisini seçin**altında **Web uygulaması aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-632">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="967f4-633">**Önerilen çözümler** > **stdout günlük yeniden yönlendirmeyi etkinleştirmek**için, \*\*Web.Configdüzenlemek üzere kudu konsolunu açmak \*\*için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-633">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="967f4-634">Kudu **Tanılama konsolunda**, klasör Wwwroot **yolunu açın**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="967f4-634">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="967f4-635">Listenin altındaki *web.config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-635">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="967f4-636">*web.config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-636">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="967f4-637">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="967f4-637">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="967f4-638">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-638">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="967f4-639">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-639">Make a request to the app.</span></span>
1. <span data-ttu-id="967f4-640">Azure portala dönün.</span><span class="sxs-lookup"><span data-stu-id="967f4-640">Return to the Azure portal.</span></span> <span data-ttu-id="967f4-641">**GELIŞTIRME araçları** alanında **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-641">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="967f4-642">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-642">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-643">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-643">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-644">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-644">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="967f4-645">**LogFiles** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-645">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="967f4-646">**Değiştirilen** sütunu inceleyin ve son değiştirilme tarihiyle stdout günlüğünü düzenlemek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-646">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="967f4-647">Günlük dosyası açıldığında hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="967f4-647">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="967f4-648">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="967f4-648">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="967f4-649">Kudu **Tanılama Konsolu**'nda, **site**  >  *web.config* dosyasını açığa çıkarmak için**Wwwroot** yolu sitesine dönün.</span><span class="sxs-lookup"><span data-stu-id="967f4-649">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="967f4-650">Kalem simgesini seçerek **web.config** dosyasını tekrar açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-650">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="967f4-651">**StdoutLogEnabled** olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="967f4-651">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="967f4-652">Dosyayı kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-652">Select **Save** to save the file.</span></span>

<span data-ttu-id="967f4-653">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="967f4-653">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-654">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-654">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-655">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-655">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="967f4-656">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-656">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="967f4-657">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-657">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-658">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-658">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="967f4-659">ASP.NET Core modülü hata ayıklama günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-659">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="967f4-660">ASP.NET Core Module hata ayıklama günlüğü, ASP.NET Core modülünden daha ayrıntılı günlük kaydı sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-660">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="967f4-661">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="967f4-661">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="967f4-662">Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="967f4-662">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="967f4-663">Uygulamayı gelişmiş tanılama günlüğü için yapılandırmak üzere [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-663">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="967f4-664">Uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="967f4-664">Redeploy the app.</span></span>
   * <span data-ttu-id="967f4-665">`<handlerSettings>` [Gelişmiş tanılama günlüklerini](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilen, kudu konsolunu kullanarak canlı uygulamanın *web.config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-665">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="967f4-666">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-666">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="967f4-667">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-667">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-668">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-668">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="967f4-669">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-669">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="967f4-670">Klasörü Wwwroot **yoluna açın**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="967f4-670">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="967f4-671">*web.config* dosyasını, kurşun kalem düğmesini seçerek düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-671">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="967f4-672">Bölümü, `<handlerSettings>` [Gelişmiş tanılama günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-672">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="967f4-673">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-673">Select the **Save** button.</span></span>
1. <span data-ttu-id="967f4-674">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-674">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="967f4-675">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-675">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-676">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-676">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-677">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-677">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="967f4-678">Klasörü Wwwroot **yoluna açın**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="967f4-678">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="967f4-679">*Aspnetcore-Debug. log* dosyası için bir yol sağlamadıysanız dosya listede görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="967f4-679">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="967f4-680">Bir yol sağladıysanız, günlük dosyasının konumuna gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-680">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="967f4-681">Dosya adının yanındaki kurşun kalem düğmesiyle günlük dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-681">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="967f4-682">Sorun giderme tamamlandığında hata ayıklama günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="967f4-682">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="967f4-683">Gelişmiş hata ayıklama günlüğünü devre dışı bırakmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="967f4-683">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="967f4-684">`<handlerSettings>` *web.config* dosyasından yerel olarak kaldırın ve uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="967f4-684">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="967f4-685">*web.config* dosyasını düzenlemek ve bölümünü kaldırmak Için kudu konsolunu kullanın `<handlerSettings>` .</span><span class="sxs-lookup"><span data-stu-id="967f4-685">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="967f4-686">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="967f4-686">Save the file.</span></span>

<span data-ttu-id="967f4-687">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="967f4-687">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-688">Hata ayıklama günlüğünü devre dışı bırakma hatası, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-688">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-689">Günlük dosyası boyutunda sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-689">There's no limit on log file size.</span></span> <span data-ttu-id="967f4-690">Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-690">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="967f4-691">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-691">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-692">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-692">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="967f4-693">Yavaş veya askıda olan uygulama (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-693">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="967f4-694">Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="967f4-694">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="967f4-695">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-695">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="967f4-696">Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın</span><span class="sxs-lookup"><span data-stu-id="967f4-696">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="967f4-697">İzleme kanatları</span><span class="sxs-lookup"><span data-stu-id="967f4-697">Monitoring blades</span></span>

<span data-ttu-id="967f4-698">İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-698">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="967f4-699">Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-699">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="967f4-700">ASP.NET Core uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-700">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="967f4-701">Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="967f4-701">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="967f4-702">**GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-702">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="967f4-703">**ASP.NET Core uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="967f4-703">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="967f4-704">Uzantılar yüklü değilse, **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-704">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="967f4-705">Listeden **ASP.NET Core uzantılarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-705">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="967f4-706">Yasal koşulları kabul etmek için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-706">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="967f4-707">**Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-707">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="967f4-708">Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="967f4-708">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="967f4-709">Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-709">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="967f4-710">Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-710">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="967f4-711">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-711">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-712">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-712">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-713">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-713">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="967f4-714">Klasörü Wwwroot **yoluna açın** > **wwwroot** ve listenin altındaki *web.config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-714">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="967f4-715">*web.config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-715">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="967f4-716">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="967f4-716">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="967f4-717">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-717">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="967f4-718">Tanılama günlüğünü etkinleştirmek için ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-718">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="967f4-719">Azure portal **tanılama günlükleri** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-719">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="967f4-720">**Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri**için **bir anahtar seçin** .</span><span class="sxs-lookup"><span data-stu-id="967f4-720">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="967f4-721">Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-721">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="967f4-722">Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme**anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-722">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="967f4-723">Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-723">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="967f4-724">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-724">Make a request to the app.</span></span>
1. <span data-ttu-id="967f4-725">Günlük akışı verileri içinde hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-725">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="967f4-726">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="967f4-726">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="967f4-727">Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):</span><span class="sxs-lookup"><span data-stu-id="967f4-727">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="967f4-728">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-728">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="967f4-729">Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-729">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="967f4-730">[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-730">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="967f4-731">Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="967f4-731">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-732">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-732">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-733">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-733">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="967f4-734">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-734">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-735">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-735">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="967f4-736">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-736">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="967f4-737">Uygulama olay günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-737">Application Event Log (IIS)</span></span>

<span data-ttu-id="967f4-738">Uygulama olay günlüğüne erişin:</span><span class="sxs-lookup"><span data-stu-id="967f4-738">Access the Application Event Log:</span></span>

1. <span data-ttu-id="967f4-739">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-739">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="967f4-740">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-740">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="967f4-741">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-741">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="967f4-742">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-742">Search for errors associated with the failing app.</span></span> <span data-ttu-id="967f4-743">Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="967f4-743">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="967f4-744">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="967f4-744">Run the app at a command prompt</span></span>

<span data-ttu-id="967f4-745">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="967f4-745">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="967f4-746">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="967f4-746">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="967f4-747">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="967f4-747">Framework-dependent deployment</span></span>

<span data-ttu-id="967f4-748">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-748">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="967f4-749">Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *dotnet.exe*yürüterek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-749">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="967f4-750">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="967f4-750">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="967f4-751">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-751">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="967f4-752">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-752">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="967f4-753">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="967f4-753">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="967f4-754">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-754">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="967f4-755">Kendi kendine kapsanan dağıtım</span><span class="sxs-lookup"><span data-stu-id="967f4-755">Self-contained deployment</span></span>

<span data-ttu-id="967f4-756">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-756">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="967f4-757">Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-757">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="967f4-758">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="967f4-758">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="967f4-759">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-759">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="967f4-760">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-760">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="967f4-761">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="967f4-761">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="967f4-762">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-762">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="967f4-763">ASP.NET Core Module stdout günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-763">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="967f4-764">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="967f4-764">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="967f4-765">Barındırma sistemindeki sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-765">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="967f4-766">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-766">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="967f4-767">MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.</span><span class="sxs-lookup"><span data-stu-id="967f4-767">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="967f4-768">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-768">Edit the *web.config* file.</span></span> <span data-ttu-id="967f4-769">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="967f4-769">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="967f4-770">`stdout`yolunda günlük dosyası adı ön eki bulunur.</span><span class="sxs-lookup"><span data-stu-id="967f4-770">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="967f4-771">Günlük oluşturulduğunda zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="967f4-771">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="967f4-772">`stdout`Dosya adı ön eki olarak kullanıldığında, tipik bir günlük dosyası *stdout_20180205184032_5412. log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-772">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="967f4-773">Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="967f4-773">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="967f4-774">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="967f4-774">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="967f4-775">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-775">Make a request to the app.</span></span>
1. <span data-ttu-id="967f4-776">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-776">Navigate to the *logs* folder.</span></span> <span data-ttu-id="967f4-777">En son stdout günlüğünü bulup açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-777">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="967f4-778">Günlüğü hatalara karşı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-778">Study the log for errors.</span></span>

<span data-ttu-id="967f4-779">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="967f4-779">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="967f4-780">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-780">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="967f4-781">**StdoutLogEnabled** olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="967f4-781">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="967f4-782">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="967f4-782">Save the file.</span></span>

<span data-ttu-id="967f4-783">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="967f4-783">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-784">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-784">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-785">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-785">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="967f4-786">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-786">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-787">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-787">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="967f4-788">ASP.NET Core modülü hata ayıklama günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-788">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="967f4-789">ASP.NET Core modülü hata ayıklama günlüğünü etkinleştirmek için aşağıdaki işleyici ayarlarını uygulamanın *web.config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-789">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="967f4-790">Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-790">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="967f4-791">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="967f4-791">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="967f4-792">Geliştirici özel durum sayfasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="967f4-792">Enable the Developer Exception Page</span></span>

<span data-ttu-id="967f4-793">`ASPNETCORE_ENVIRONMENT` [Ortam değişkeni](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , uygulamayı geliştirme ortamında çalıştırmak için web.configeklenebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-793">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="967f4-794">Ortam, ana bilgisayar Oluşturucu 'da uygulama başlangıcında geçersiz kılınmadığı sürece `UseEnvironment` , ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="967f4-794">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="967f4-795">Ortam değişkeninin ayarlanması `ASPNETCORE_ENVIRONMENT` yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-795">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="967f4-796">Sorun giderme işleminden sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-796">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="967f4-797">*web.config*ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="967f4-797">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="967f4-798">Uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="967f4-798">Obtain data from an app</span></span>

<span data-ttu-id="967f4-799">Bir uygulamanın isteklere yanıt verme yeteneği varsa, Terminal satır içi ara yazılımı kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="967f4-799">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="967f4-800">Daha fazla bilgi ve örnek kod için bkz <xref:test/troubleshoot#obtain-data-from-an-app> ..</span><span class="sxs-lookup"><span data-stu-id="967f4-800">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="967f4-801">Yavaş veya askıda olan uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-801">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="967f4-802">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-802">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="967f4-803">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="967f4-803">App crashes or encounters an exception</span></span>

<span data-ttu-id="967f4-804">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="967f4-804">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="967f4-805">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="967f4-805">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="967f4-806">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967f4-806">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="967f4-807">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-807">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="967f4-808">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-808">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="967f4-809">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-809">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="967f4-810">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-810">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="967f4-811">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-811">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="967f4-812">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-812">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="967f4-813">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-813">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="967f4-814">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-814">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="967f4-815">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="967f4-815">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-816">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="967f4-816">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="967f4-817">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="967f4-817">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="967f4-818">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="967f4-818">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="967f4-819">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="967f4-819">Analyze the dump</span></span>

<span data-ttu-id="967f4-820">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-820">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="967f4-821">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="967f4-821">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="967f4-822">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="967f4-822">Clear package caches</span></span>

<span data-ttu-id="967f4-823">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-823">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="967f4-824">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-824">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="967f4-825">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="967f4-825">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="967f4-826">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="967f4-826">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="967f4-827">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-827">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="967f4-828">Paket önbelleklerini Temizleme, [nuget.exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komutu yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="967f4-828">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="967f4-829">*nuget.exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967f4-829">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="967f4-830">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-830">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="967f4-831">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="967f4-831">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="967f4-832">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="967f4-832">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="967f4-833">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="967f4-833">Azure documentation</span></span>

* [<span data-ttu-id="967f4-834">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="967f4-834">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="967f4-835">Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü</span><span class="sxs-lookup"><span data-stu-id="967f4-835">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="967f4-836">Azure App Service tanılamada genel bakış</span><span class="sxs-lookup"><span data-stu-id="967f4-836">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="967f4-837">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="967f4-837">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="967f4-838">Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-838">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="967f4-839">Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-839">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="967f4-840">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-840">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="967f4-841">Azure 'da Web Apps için uygulama performansı SSS</span><span class="sxs-lookup"><span data-stu-id="967f4-841">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="967f4-842">Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="967f4-842">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="967f4-843">Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="967f4-843">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="967f4-844">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="967f4-844">Visual Studio documentation</span></span>

* [<span data-ttu-id="967f4-845">Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="967f4-845">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="967f4-846">Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="967f4-846">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="967f4-847">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="967f4-847">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="967f4-848">Visual Studio Code belgeleri</span><span class="sxs-lookup"><span data-stu-id="967f4-848">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="967f4-849">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="967f4-849">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="967f4-850">Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="967f4-850">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="967f4-851">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="967f4-851">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="967f4-852">Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="967f4-852">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="967f4-853">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-853">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="967f4-854">Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-854">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="967f4-855">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-855">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="967f4-856">IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-856">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="967f4-857">Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="967f4-857">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="967f4-858">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="967f4-858">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="967f4-859">Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="967f4-859">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="967f4-860">Ek Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="967f4-860">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="967f4-861">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="967f4-861">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="967f4-862">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="967f4-862">App startup errors</span></span>

<span data-ttu-id="967f4-863">Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="967f4-863">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="967f4-864">Yerel olarak hata ayıklamada oluşan *502,5 Işlem hatası* , bu konudaki öneri kullanılarak tanılanabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-864">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="967f4-865">403,14 yasak</span><span class="sxs-lookup"><span data-stu-id="967f4-865">403.14 Forbidden</span></span>

<span data-ttu-id="967f4-866">Uygulama başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-866">The app fails to start.</span></span> <span data-ttu-id="967f4-867">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="967f4-867">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="967f4-868">Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:</span><span class="sxs-lookup"><span data-stu-id="967f4-868">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="967f4-869">Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-869">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="967f4-870">Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="967f4-870">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="967f4-871">*web.config* dosyası dağıtımda yok veya *web.config* dosya içeriği hatalı biçimlendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="967f4-871">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="967f4-872">Aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="967f4-872">Perform the following steps:</span></span>

1. <span data-ttu-id="967f4-873">Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.</span><span class="sxs-lookup"><span data-stu-id="967f4-873">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="967f4-874">Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="967f4-874">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="967f4-875">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-875">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="967f4-876">Azure App Service barındırırken, uygulamanın klasöre dağıtıldığını doğrulayın `D:\home\site\wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="967f4-876">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="967f4-877">Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında**gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-877">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="967f4-878">Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-878">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="967f4-879">Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..</span><span class="sxs-lookup"><span data-stu-id="967f4-879">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="967f4-880">*web.config* dosyası hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="967f4-880">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="967f4-881">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-881">500 Internal Server Error</span></span>

<span data-ttu-id="967f4-882">Uygulama başlıyor, ancak bir hata sunucunun isteği yerine getirmesini engelliyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-882">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="967f4-883">Bu hata, başlangıç sırasında veya Yanıt oluştururken uygulamanın kodunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="967f4-883">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="967f4-884">Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-884">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="967f4-885">Uygulama olay günlüğü genellikle uygulamanın normal olarak başlatıldığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="967f4-885">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="967f4-886">Sunucu perspektifinden doğru.</span><span class="sxs-lookup"><span data-stu-id="967f4-886">From the server's perspective, that's correct.</span></span> <span data-ttu-id="967f4-887">Uygulama başlatıldı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-887">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="967f4-888">Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="967f4-888">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="967f4-889">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="967f4-889">502.5 Process Failure</span></span>

<span data-ttu-id="967f4-890">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-890">The worker process fails.</span></span> <span data-ttu-id="967f4-891">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-891">The app doesn't start.</span></span>

<span data-ttu-id="967f4-892">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="967f4-892">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="967f4-893">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-893">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="967f4-894">Yaygın bir hata durumu, ASP.NET Core paylaşılan bir Framework 'ün mevcut olmayan bir sürümünü hedeflediğinden, uygulamanın yanlış yapılandırılmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="967f4-894">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="967f4-895">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-895">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="967f4-896">*Paylaşılan çerçeve* , makinede yüklü olan ve bir metapackage tarafından başvurulan derleme (*. dll* dosyaları) kümesidir `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="967f4-896">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="967f4-897">Metapackage başvurusu, gerekli en düşük sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-897">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="967f4-898">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="967f4-898">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="967f4-899">Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:</span><span class="sxs-lookup"><span data-stu-id="967f4-899">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="967f4-900">Uygulama başlatılamadı (hata kodu ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="967f4-900">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="967f4-901">Uygulamanın derlemesi (*. dll*) yüklenemediğinden uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="967f4-901">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="967f4-902">Bu hata, yayımlanan uygulama ile W3wp/iisexpress işlemi arasında bir bit durumu uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="967f4-902">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="967f4-903">Uygulama havuzunun 32 bit ayarının doğru olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="967f4-903">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="967f4-904">IIS yöneticisinin **uygulama havuzlarında**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-904">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="967f4-905">**Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-905">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="967f4-906">**Enable 32 bit uygulamalarını**ayarla:</span><span class="sxs-lookup"><span data-stu-id="967f4-906">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="967f4-907">32-bit (x86) bir uygulama dağıtıyorsanız, değerini olarak ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="967f4-907">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="967f4-908">64 bit (x64) uygulaması dağıtıyorsanız, değerini olarak ayarlayın `False` .</span><span class="sxs-lookup"><span data-stu-id="967f4-908">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="967f4-909">`<Platform>`Proje dosyasındaki bir MSBuild özelliği ve uygulamanın yayınlanan bit durumu arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-909">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="967f4-910">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="967f4-910">Connection reset</span></span>

<span data-ttu-id="967f4-911">Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="967f4-911">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="967f4-912">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirilmesi sırasında bir hata oluştuğunda meydana gelir.</span><span class="sxs-lookup"><span data-stu-id="967f4-912">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="967f4-913">Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="967f4-913">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="967f4-914">[Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-914">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="967f4-915">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="967f4-915">Default startup limits</span></span>

<span data-ttu-id="967f4-916">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-916">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="967f4-917">Varsayılan değerde sol tarafta, modül bir işlem hatası günlüğe kaydedilmeden önce uygulamanın başlaması iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-917">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="967f4-918">Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="967f4-918">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="967f4-919">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-919">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="967f4-920">Uygulama olay günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-920">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="967f4-921">Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="967f4-921">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="967f4-922">Azure portal uygulama **Hizmetleri**' nde uygulamayı açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-922">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="967f4-923">**Sorunları tanılama ve çözme**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-923">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="967f4-924">**Tanılama araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-924">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="967f4-925">**Destek Araçları**' nın altında, **uygulama olayları** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-925">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="967f4-926">**Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-926">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="967f4-927">**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="967f4-927">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="967f4-928">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-928">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="967f4-929">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-929">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-930">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-930">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-931">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-931">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="967f4-932">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-932">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="967f4-933">*eventlog.xml* dosyasının yanındaki kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-933">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="967f4-934">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-934">Examine the log.</span></span> <span data-ttu-id="967f4-935">En son olayları görmek için günlüğün en altına gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-935">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="967f4-936">Uygulamayı kudu konsolunda çalıştırma</span><span class="sxs-lookup"><span data-stu-id="967f4-936">Run the app in the Kudu console</span></span>

<span data-ttu-id="967f4-937">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="967f4-937">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="967f4-938">Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="967f4-938">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="967f4-939">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-939">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="967f4-940">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-940">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-941">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-941">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-942">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-942">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="967f4-943">32 bit (x86) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="967f4-943">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="967f4-944">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="967f4-944">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="967f4-945">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-945">Run the app:</span></span>
   * <span data-ttu-id="967f4-946">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-946">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="967f4-947">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-947">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="967f4-948">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-948">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="967f4-949">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="967f4-949">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="967f4-950">*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="967f4-950">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="967f4-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="967f4-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="967f4-952">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="967f4-952">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="967f4-953">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-953">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="967f4-954">64 bit (x64) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="967f4-954">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="967f4-955">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="967f4-955">**Current release**</span></span>

* <span data-ttu-id="967f4-956">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-956">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="967f4-957">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="967f4-957">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="967f4-958">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-958">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="967f4-959">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="967f4-959">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="967f4-960">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-960">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="967f4-961">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="967f4-961">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="967f4-962">*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="967f4-962">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="967f4-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`( `{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="967f4-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="967f4-964">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="967f4-964">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="967f4-965">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-965">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="967f4-966">ASP.NET Core modülü stdout günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-966">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="967f4-967">ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="967f4-967">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="967f4-968">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="967f4-968">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="967f4-969">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-969">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="967f4-970">**Sorun kategorisini seçin**altında **Web uygulaması aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-970">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="967f4-971">**Önerilen çözümler** > **stdout günlük yeniden yönlendirmeyi etkinleştirmek**için, \*\*Web.Configdüzenlemek üzere kudu konsolunu açmak \*\*için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-971">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="967f4-972">Kudu **Tanılama konsolunda**, klasör Wwwroot **yolunu açın**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="967f4-972">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="967f4-973">Listenin altındaki *web.config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-973">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="967f4-974">*web.config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-974">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="967f4-975">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="967f4-975">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="967f4-976">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-976">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="967f4-977">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-977">Make a request to the app.</span></span>
1. <span data-ttu-id="967f4-978">Azure portala dönün.</span><span class="sxs-lookup"><span data-stu-id="967f4-978">Return to the Azure portal.</span></span> <span data-ttu-id="967f4-979">**GELIŞTIRME araçları** alanında **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-979">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="967f4-980">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-980">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-981">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-981">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-982">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-982">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="967f4-983">**LogFiles** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-983">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="967f4-984">**Değiştirilen** sütunu inceleyin ve son değiştirilme tarihiyle stdout günlüğünü düzenlemek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-984">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="967f4-985">Günlük dosyası açıldığında hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="967f4-985">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="967f4-986">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="967f4-986">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="967f4-987">Kudu **Tanılama Konsolu**'nda, **site**  >  *web.config* dosyasını açığa çıkarmak için**Wwwroot** yolu sitesine dönün.</span><span class="sxs-lookup"><span data-stu-id="967f4-987">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="967f4-988">Kalem simgesini seçerek **web.config** dosyasını tekrar açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-988">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="967f4-989">**StdoutLogEnabled** olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="967f4-989">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="967f4-990">Dosyayı kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-990">Select **Save** to save the file.</span></span>

<span data-ttu-id="967f4-991">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="967f4-991">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-992">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-992">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-993">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-993">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="967f4-994">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-994">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="967f4-995">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-995">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-996">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-996">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="967f4-997">Yavaş veya askıda olan uygulama (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="967f4-997">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="967f4-998">Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="967f4-998">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="967f4-999">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-999">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="967f4-1000">Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın</span><span class="sxs-lookup"><span data-stu-id="967f4-1000">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="967f4-1001">İzleme kanatları</span><span class="sxs-lookup"><span data-stu-id="967f4-1001">Monitoring blades</span></span>

<span data-ttu-id="967f4-1002">İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="967f4-1002">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="967f4-1003">Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1003">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="967f4-1004">ASP.NET Core uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1004">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="967f4-1005">Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="967f4-1005">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="967f4-1006">**GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1006">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="967f4-1007">**ASP.NET Core uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1007">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="967f4-1008">Uzantılar yüklü değilse, **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1008">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="967f4-1009">Listeden **ASP.NET Core uzantılarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1009">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="967f4-1010">Yasal koşulları kabul etmek için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1010">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="967f4-1011">**Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1011">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="967f4-1012">Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1012">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="967f4-1013">Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-1013">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="967f4-1014">Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1014">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="967f4-1015">\*\*Git &rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1015">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="967f4-1016">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-1016">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="967f4-1017">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1017">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="967f4-1018">Klasörü Wwwroot **yoluna açın** > **wwwroot** ve listenin altındaki *web.config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1018">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="967f4-1019">*web.config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1019">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="967f4-1020">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="967f4-1020">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="967f4-1021">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1021">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="967f4-1022">Tanılama günlüğünü etkinleştirmek için ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="967f4-1022">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="967f4-1023">Azure portal **tanılama günlükleri** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1023">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="967f4-1024">**Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri**için **bir anahtar seçin** .</span><span class="sxs-lookup"><span data-stu-id="967f4-1024">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="967f4-1025">Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1025">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="967f4-1026">Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme**anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1026">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="967f4-1027">Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1027">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="967f4-1028">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-1028">Make a request to the app.</span></span>
1. <span data-ttu-id="967f4-1029">Günlük akışı verileri içinde hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1029">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="967f4-1030">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="967f4-1030">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="967f4-1031">Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):</span><span class="sxs-lookup"><span data-stu-id="967f4-1031">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="967f4-1032">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1032">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="967f4-1033">Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1033">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="967f4-1034">[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1034">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="967f4-1035">Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="967f4-1035">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-1036">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1036">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-1037">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-1037">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="967f4-1038">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1038">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-1039">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-1039">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="967f4-1040">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-1040">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="967f4-1041">Uygulama olay günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-1041">Application Event Log (IIS)</span></span>

<span data-ttu-id="967f4-1042">Uygulama olay günlüğüne erişin:</span><span class="sxs-lookup"><span data-stu-id="967f4-1042">Access the Application Event Log:</span></span>

1. <span data-ttu-id="967f4-1043">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1043">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="967f4-1044">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1044">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="967f4-1045">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1045">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="967f4-1046">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1046">Search for errors associated with the failing app.</span></span> <span data-ttu-id="967f4-1047">Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="967f4-1047">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="967f4-1048">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="967f4-1048">Run the app at a command prompt</span></span>

<span data-ttu-id="967f4-1049">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="967f4-1049">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="967f4-1050">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="967f4-1050">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="967f4-1051">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="967f4-1051">Framework-dependent deployment</span></span>

<span data-ttu-id="967f4-1052">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-1052">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="967f4-1053">Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *dotnet.exe*yürüterek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1053">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="967f4-1054">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="967f4-1054">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="967f4-1055">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-1055">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="967f4-1056">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-1056">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="967f4-1057">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="967f4-1057">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="967f4-1058">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1058">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="967f4-1059">Kendi kendine kapsanan dağıtım</span><span class="sxs-lookup"><span data-stu-id="967f4-1059">Self-contained deployment</span></span>

<span data-ttu-id="967f4-1060">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="967f4-1060">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="967f4-1061">Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1061">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="967f4-1062">Aşağıdaki komutta, için uygulama derlemesinin adını yerine koyun \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="967f4-1062">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="967f4-1063">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-1063">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="967f4-1064">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-1064">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="967f4-1065">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="967f4-1065">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="967f4-1066">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1066">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="967f4-1067">ASP.NET Core Module stdout günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-1067">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="967f4-1068">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="967f4-1068">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="967f4-1069">Barındırma sistemindeki sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1069">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="967f4-1070">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-1070">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="967f4-1071">MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.</span><span class="sxs-lookup"><span data-stu-id="967f4-1071">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="967f4-1072">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1072">Edit the *web.config* file.</span></span> <span data-ttu-id="967f4-1073">**StdoutLogEnabled** olarak ayarlayın `true` ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="967f4-1073">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="967f4-1074">`stdout`yolunda günlük dosyası adı ön eki bulunur.</span><span class="sxs-lookup"><span data-stu-id="967f4-1074">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="967f4-1075">Günlük oluşturulduğunda zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1075">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="967f4-1076">`stdout`Dosya adı ön eki olarak kullanıldığında, tipik bir günlük dosyası *stdout_20180205184032_5412. log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="967f4-1076">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="967f4-1077">Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="967f4-1077">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="967f4-1078">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1078">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="967f4-1079">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="967f4-1079">Make a request to the app.</span></span>
1. <span data-ttu-id="967f4-1080">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1080">Navigate to the *logs* folder.</span></span> <span data-ttu-id="967f4-1081">En son stdout günlüğünü bulup açın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1081">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="967f4-1082">Günlüğü hatalara karşı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1082">Study the log for errors.</span></span>

<span data-ttu-id="967f4-1083">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="967f4-1083">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="967f4-1084">*web.config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1084">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="967f4-1085">**StdoutLogEnabled** olarak ayarlayın `false` .</span><span class="sxs-lookup"><span data-stu-id="967f4-1085">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="967f4-1086">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1086">Save the file.</span></span>

<span data-ttu-id="967f4-1087">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="967f4-1087">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-1088">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1088">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="967f4-1089">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="967f4-1089">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="967f4-1090">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1090">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="967f4-1091">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="967f4-1091">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="967f4-1092">Geliştirici özel durum sayfasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="967f4-1092">Enable the Developer Exception Page</span></span>

<span data-ttu-id="967f4-1093">`ASPNETCORE_ENVIRONMENT` [Ortam değişkeni](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , uygulamayı geliştirme ortamında çalıştırmak için web.configeklenebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1093">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="967f4-1094">Ortam, ana bilgisayar Oluşturucu 'da uygulama başlangıcında geçersiz kılınmadığı sürece `UseEnvironment` , ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1094">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="967f4-1095">Ortam değişkeninin ayarlanması `ASPNETCORE_ENVIRONMENT` yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1095">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="967f4-1096">Sorun giderme işleminden sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1096">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="967f4-1097">*web.config*ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1097">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="967f4-1098">Uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="967f4-1098">Obtain data from an app</span></span>

<span data-ttu-id="967f4-1099">Bir uygulamanın isteklere yanıt verme yeteneği varsa, Terminal satır içi ara yazılımı kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1099">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="967f4-1100">Daha fazla bilgi ve örnek kod için bkz <xref:test/troubleshoot#obtain-data-from-an-app> ..</span><span class="sxs-lookup"><span data-stu-id="967f4-1100">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="967f4-1101">Yavaş veya askıda olan uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="967f4-1101">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="967f4-1102">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1102">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="967f4-1103">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="967f4-1103">App crashes or encounters an exception</span></span>

<span data-ttu-id="967f4-1104">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="967f4-1104">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="967f4-1105">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="967f4-1105">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="967f4-1106">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967f4-1106">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="967f4-1107">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-1107">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="967f4-1108">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-1108">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="967f4-1109">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-1109">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="967f4-1110">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="967f4-1110">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="967f4-1111">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-1111">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="967f4-1112">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-1112">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="967f4-1113">Uygulama, [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="967f4-1113">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="967f4-1114">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1114">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="967f4-1115">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="967f4-1115">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="967f4-1116">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="967f4-1116">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="967f4-1117">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="967f4-1117">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="967f4-1118">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="967f4-1118">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="967f4-1119">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="967f4-1119">Analyze the dump</span></span>

<span data-ttu-id="967f4-1120">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1120">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="967f4-1121">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="967f4-1121">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="967f4-1122">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="967f4-1122">Clear package caches</span></span>

<span data-ttu-id="967f4-1123">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1123">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="967f4-1124">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="967f4-1124">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="967f4-1125">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="967f4-1125">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="967f4-1126">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1126">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="967f4-1127">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1127">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="967f4-1128">Paket önbelleklerini Temizleme, [nuget.exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komutu yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="967f4-1128">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="967f4-1129">*nuget.exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="967f4-1129">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="967f4-1130">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1130">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="967f4-1131">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="967f4-1131">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="967f4-1132">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="967f4-1132">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="967f4-1133">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="967f4-1133">Azure documentation</span></span>

* [<span data-ttu-id="967f4-1134">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="967f4-1134">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="967f4-1135">Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü</span><span class="sxs-lookup"><span data-stu-id="967f4-1135">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="967f4-1136">Azure App Service tanılamada genel bakış</span><span class="sxs-lookup"><span data-stu-id="967f4-1136">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="967f4-1137">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="967f4-1137">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="967f4-1138">Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-1138">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="967f4-1139">Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-1139">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="967f4-1140">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="967f4-1140">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="967f4-1141">Azure 'da Web Apps için uygulama performansı SSS</span><span class="sxs-lookup"><span data-stu-id="967f4-1141">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="967f4-1142">Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="967f4-1142">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="967f4-1143">Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="967f4-1143">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="967f4-1144">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="967f4-1144">Visual Studio documentation</span></span>

* [<span data-ttu-id="967f4-1145">Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="967f4-1145">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="967f4-1146">Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="967f4-1146">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="967f4-1147">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="967f4-1147">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="967f4-1148">Visual Studio Code belgeleri</span><span class="sxs-lookup"><span data-stu-id="967f4-1148">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="967f4-1149">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="967f4-1149">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
