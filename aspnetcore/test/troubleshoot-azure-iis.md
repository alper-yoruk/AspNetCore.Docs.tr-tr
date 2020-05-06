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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 09b004abd423abc9cc8e83d3bb3fea1dddf09e14
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776636"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="00a87-103">Azure App Service ve IIS 'de ASP.NET Core sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="00a87-104">, [Kotin Kotalik](https://github.com/jkotalik) tarafından</span><span class="sxs-lookup"><span data-stu-id="00a87-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="00a87-105">Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="00a87-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="00a87-106">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="00a87-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="00a87-107">Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="00a87-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="00a87-108">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="00a87-109">Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="00a87-110">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="00a87-111">IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="00a87-112">Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="00a87-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="00a87-113">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="00a87-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="00a87-114">Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="00a87-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="00a87-115">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="00a87-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="00a87-116">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="00a87-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="00a87-117">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="00a87-117">App startup errors</span></span>

<span data-ttu-id="00a87-118">Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="00a87-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="00a87-119">*502,5-Işlem hatası* veya yerel olarak hata ayıklarken oluşan *500,30-başlatma hatası* , bu konudaki öneri kullanılarak tanılanabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="00a87-120">403,14 yasak</span><span class="sxs-lookup"><span data-stu-id="00a87-120">403.14 Forbidden</span></span>

<span data-ttu-id="00a87-121">Uygulama başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-121">The app fails to start.</span></span> <span data-ttu-id="00a87-122">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="00a87-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="00a87-123">Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:</span><span class="sxs-lookup"><span data-stu-id="00a87-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="00a87-124">Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="00a87-125">Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="00a87-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="00a87-126">*Web. config* dosyası dağıtımda yok veya *Web. config* dosyası içerikleri hatalı biçimlendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="00a87-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="00a87-127">Aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="00a87-127">Perform the following steps:</span></span>

1. <span data-ttu-id="00a87-128">Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.</span><span class="sxs-lookup"><span data-stu-id="00a87-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="00a87-129">Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="00a87-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="00a87-130">*Web. config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="00a87-131">Azure App Service barındırırken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="00a87-132">Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında**gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="00a87-133">Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="00a87-134">Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure>..</span><span class="sxs-lookup"><span data-stu-id="00a87-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="00a87-135">*Web. config* dosyası hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>..</span><span class="sxs-lookup"><span data-stu-id="00a87-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="00a87-136">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-136">500 Internal Server Error</span></span>

<span data-ttu-id="00a87-137">Uygulama başlıyor, ancak bir hata sunucunun isteği yerine getirmesini engelliyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="00a87-138">Bu hata, başlangıç sırasında veya Yanıt oluştururken uygulamanın kodunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="00a87-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="00a87-139">Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="00a87-140">Uygulama olay günlüğü genellikle uygulamanın normal olarak başlatıldığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="00a87-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="00a87-141">Sunucu perspektifinden doğru.</span><span class="sxs-lookup"><span data-stu-id="00a87-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="00a87-142">Uygulama başlatıldı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="00a87-143">Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="00a87-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="00a87-144">500,0 Işlem içi Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="00a87-145">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-145">The worker process fails.</span></span> <span data-ttu-id="00a87-146">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-146">The app doesn't start.</span></span>

<span data-ttu-id="00a87-147">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) bileşenleri yüklenirken bilinmeyen bir hata oluştu.</span><span class="sxs-lookup"><span data-stu-id="00a87-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="00a87-148">Aşağıdaki eylemlerden birini uygulayın:</span><span class="sxs-lookup"><span data-stu-id="00a87-148">Take one of the following actions:</span></span>

* <span data-ttu-id="00a87-149">[Microsoft desteği](https://support.microsoft.com/oas/default.aspx?prid=15832) iletişim kurun ( **Geliştirici Araçları** ve **ASP.NET Core**' i seçin).</span><span class="sxs-lookup"><span data-stu-id="00a87-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="00a87-150">Stack Overflow soru sorun.</span><span class="sxs-lookup"><span data-stu-id="00a87-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="00a87-151">[GitHub deponuzda](https://github.com/dotnet/AspNetCore)bir sorun yapın.</span><span class="sxs-lookup"><span data-stu-id="00a87-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="00a87-152">500,30 Işlem içi başlatma hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="00a87-153">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-153">The worker process fails.</span></span> <span data-ttu-id="00a87-154">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-154">The app doesn't start.</span></span>

<span data-ttu-id="00a87-155">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR 'yi işlem içi başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="00a87-156">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="00a87-157">Yaygın hata koşulları:</span><span class="sxs-lookup"><span data-stu-id="00a87-157">Common failure conditions:</span></span>

* <span data-ttu-id="00a87-158">Mevcut olmayan ASP.NET Core paylaşılan çerçevesinin bir sürümünün hedeflenmesi nedeniyle uygulama yanlış yapılandırılmış.</span><span class="sxs-lookup"><span data-stu-id="00a87-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="00a87-159">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="00a87-160">Azure Key Vault kullanarak Key Vault izinlerin bulunmaması.</span><span class="sxs-lookup"><span data-stu-id="00a87-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="00a87-161">Doğru izinlerin verildiğinden emin olmak için hedeflenen Key Vault erişim ilkelerini kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="00a87-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="00a87-162">500,31 ANCM yerel bağımlılıklar bulunamadı</span><span class="sxs-lookup"><span data-stu-id="00a87-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="00a87-163">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-163">The worker process fails.</span></span> <span data-ttu-id="00a87-164">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-164">The app doesn't start.</span></span>

<span data-ttu-id="00a87-165">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) , .NET Core çalışma zamanını işlem içinde başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="00a87-166">Bu başlatma hatasının en yaygın nedeni, `Microsoft.NETCore.App` veya `Microsoft.AspNetCore.App` çalışma zamanının yüklenmemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="00a87-167">Uygulama, hedef ASP.NET Core 3,0 ' ye dağıtılmışsa ve bu sürüm makinede yoksa, bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="00a87-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="00a87-168">Örnek bir hata iletisi aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="00a87-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="00a87-169">Hata iletisi, yüklü tüm .NET Core sürümlerini ve uygulama tarafından istenen sürümü listeler.</span><span class="sxs-lookup"><span data-stu-id="00a87-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="00a87-170">Bu hatayı onarmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="00a87-170">To fix this error, either:</span></span>

* <span data-ttu-id="00a87-171">Uygun .NET Core sürümünü makineye yükler.</span><span class="sxs-lookup"><span data-stu-id="00a87-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="00a87-172">Uygulamayı, makinede bulunan .NET Core 'un bir sürümünü hedefleyecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="00a87-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="00a87-173">Uygulamayı [kendi kendine kapsanan bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)olarak yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="00a87-174">Geliştirme aşamasında çalışırken ( `ASPNETCORE_ENVIRONMENT` ortam değişkeni olarak `Development`ayarlandığında), özel hata HTTP yanıtına yazılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="00a87-175">İşlem başlatma hatasının nedeni uygulama olay günlüğünde de bulunur.</span><span class="sxs-lookup"><span data-stu-id="00a87-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="00a87-176">500,32 ANCM dll yüklenemedi</span><span class="sxs-lookup"><span data-stu-id="00a87-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="00a87-177">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-177">The worker process fails.</span></span> <span data-ttu-id="00a87-178">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-178">The app doesn't start.</span></span>

<span data-ttu-id="00a87-179">Bu hatanın en yaygın nedeni, uygulamanın uyumsuz bir işlemci mimarisi için yayımlanmakta olması olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="00a87-180">Çalışan işlemi 32 bitlik bir uygulama olarak çalışıyorsa ve uygulama 64 bit hedef için yayımlandıysa, bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="00a87-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="00a87-181">Bu hatayı onarmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="00a87-181">To fix this error, either:</span></span>

* <span data-ttu-id="00a87-182">Çalışan işlemle aynı işlemci mimarisi için uygulamayı yeniden yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="00a87-183">Uygulamayı [çerçeveye bağlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-executables-fde)olarak yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="00a87-184">500,33 ANCM Istek Işleyicisi yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="00a87-185">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-185">The worker process fails.</span></span> <span data-ttu-id="00a87-186">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-186">The app doesn't start.</span></span>

<span data-ttu-id="00a87-187">Uygulama `Microsoft.AspNetCore.App` çerçeveye başvurmadı.</span><span class="sxs-lookup"><span data-stu-id="00a87-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="00a87-188">Yalnızca `Microsoft.AspNetCore.App` çerçeveyi hedefleyen uygulamalar [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module)tarafından barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="00a87-189">Bu hatayı düzeltemedi, uygulamanın `Microsoft.AspNetCore.App` çerçeveyi hedeflediğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="00a87-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="00a87-190">Uygulamanın hedeflediği `.runtimeconfig.json` çerçeveyi doğrulamak için ' i işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="00a87-191">500,34 ANCM karışık barındırma modelleri desteklenmez</span><span class="sxs-lookup"><span data-stu-id="00a87-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="00a87-192">Çalışan işlem, aynı işlemde hem işlem içi uygulama hem de işlem dışı bir uygulama çalıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="00a87-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="00a87-193">Bu hatayı onarmak için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="00a87-194">500,35 ANCM birden çok işlem Içi uygulama aynı Işlemde</span><span class="sxs-lookup"><span data-stu-id="00a87-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="00a87-195">Çalışan işlemi aynı işlemde birden çok işlem içi uygulama çalıştıramıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="00a87-196">Bu hatayı onarmak için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="00a87-197">500,36 ANCM Işlem dışı Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="00a87-198">İşlem dışı istek işleyicisi, *aspnetcorev2_outofprocess. dll*, *aspnetcorev2. dll* dosyasının yanında değildir.</span><span class="sxs-lookup"><span data-stu-id="00a87-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="00a87-199">Bu, [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)bozuk bir yüklemesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="00a87-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="00a87-200">Bu hatayı gidermek için [.NET Core barındırma paketi](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS için) veya Visual Studio (IIS Express için) yüklemesini onarın.</span><span class="sxs-lookup"><span data-stu-id="00a87-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="00a87-201">500,37 ANCM başlangıç zamanı sınırı Içinde başlatılamadı</span><span class="sxs-lookup"><span data-stu-id="00a87-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="00a87-202">ANCM, kısımları başlangıç süresi sınırı içinde başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="00a87-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="00a87-203">Varsayılan olarak, zaman aşımı 120 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="00a87-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="00a87-204">Aynı makinede çok sayıda uygulama başlatılırken bu hata oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="00a87-205">Başlangıç sırasında sunucuda CPU/bellek kullanımı artışlarını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="00a87-206">Birden çok uygulamanın başlatma işlemini şaşırtmayı yapmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="00a87-207">500,38 ANCM uygulama DLL 'SI bulunamadı</span><span class="sxs-lookup"><span data-stu-id="00a87-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="00a87-208">ANCM, yürütülebilir dosyanın yanında olması gereken uygulama DLL 'sini bulamadı.</span><span class="sxs-lookup"><span data-stu-id="00a87-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="00a87-209">Bu hata, işlem içi barındırma modelini kullanarak [tek dosya yürütülebilir dosyası](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) olarak paketlenmiş bir uygulama barındırdığında oluşur.</span><span class="sxs-lookup"><span data-stu-id="00a87-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="00a87-210">İşlem içi modelde, ANCM 'nin .NET Core uygulamasını mevcut IIS işlemine yüklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="00a87-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="00a87-211">Bu senaryo, tek dosya dağıtım modeli tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="00a87-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="00a87-212">Bu hatayı onarmak için uygulamanın proje dosyasında aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="00a87-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="00a87-213">`PublishSingleFile` MSBuild özelliğini olarak `false`ayarlayarak tek dosya yayımlamayı devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="00a87-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="00a87-214">`AspNetCoreHostingModel` MSBuild özelliğini olarak `OutOfProcess`ayarlayarak işlem dışı barındırma modeline geçiş yapın.</span><span class="sxs-lookup"><span data-stu-id="00a87-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="00a87-215">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-215">502.5 Process Failure</span></span>

<span data-ttu-id="00a87-216">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-216">The worker process fails.</span></span> <span data-ttu-id="00a87-217">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-217">The app doesn't start.</span></span>

<span data-ttu-id="00a87-218">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="00a87-219">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="00a87-220">Yaygın bir hata durumu, ASP.NET Core paylaşılan bir Framework 'ün mevcut olmayan bir sürümünü hedeflediğinden, uygulamanın yanlış yapılandırılmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="00a87-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="00a87-221">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="00a87-222">*Paylaşılan çerçeve* , makinede yüklü olan ve bir metapackage tarafından başvurulan derleme (*. dll* dosyaları) kümesidir `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="00a87-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="00a87-223">Metapackage başvurusu, gerekli en düşük sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="00a87-224">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="00a87-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="00a87-225">Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:</span><span class="sxs-lookup"><span data-stu-id="00a87-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="00a87-226">Uygulama başlatılamadı (hata kodu ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="00a87-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="00a87-227">Uygulamanın derlemesi (*. dll*) yüklenemediğinden uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="00a87-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="00a87-228">Bu hata, yayımlanan uygulama ile W3wp/iisexpress işlemi arasında bir bit durumu uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="00a87-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="00a87-229">Uygulama havuzunun 32 bit ayarının doğru olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="00a87-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="00a87-230">IIS yöneticisinin **uygulama havuzlarında**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="00a87-231">**Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="00a87-232">**Enable 32 bit uygulamalarını**ayarla:</span><span class="sxs-lookup"><span data-stu-id="00a87-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="00a87-233">32-bit (x86) bir uygulama dağıtıyorsanız, değerini olarak `True`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="00a87-234">64 bit (x64) uygulaması dağıtıyorsanız, değerini olarak `False`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="00a87-235">Proje dosyasındaki bir `<Platform>` MSBuild özelliği ve uygulamanın yayınlanan bit durumu arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="00a87-236">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="00a87-236">Connection reset</span></span>

<span data-ttu-id="00a87-237">Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="00a87-238">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirilmesi sırasında bir hata oluştuğunda meydana gelir.</span><span class="sxs-lookup"><span data-stu-id="00a87-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="00a87-239">Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="00a87-240">[Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="00a87-241">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="00a87-241">Default startup limits</span></span>

<span data-ttu-id="00a87-242">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="00a87-243">Varsayılan değerde sol tarafta, modül bir işlem hatası günlüğe kaydedilmeden önce uygulamanın başlaması iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="00a87-244">Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="00a87-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="00a87-245">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="00a87-246">Uygulama olay günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="00a87-247">Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="00a87-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="00a87-248">Azure portal uygulama **Hizmetleri**' nde uygulamayı açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="00a87-249">**Sorunları tanılama ve çözme** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="00a87-250">**Tanılama araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="00a87-251">**Destek Araçları**' nın altında, **uygulama olayları** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="00a87-252">**Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="00a87-253">**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="00a87-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="00a87-254">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="00a87-255">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-256">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-257">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-258">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="00a87-259">*EventLog. xml* dosyasının yanındaki kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="00a87-260">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-260">Examine the log.</span></span> <span data-ttu-id="00a87-261">En son olayları görmek için günlüğün en altına gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="00a87-262">Uygulamayı kudu konsolunda çalıştırma</span><span class="sxs-lookup"><span data-stu-id="00a87-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="00a87-263">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="00a87-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="00a87-264">Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="00a87-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="00a87-265">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="00a87-266">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-267">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-268">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="00a87-269">32 bit (x86) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="00a87-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="00a87-270">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="00a87-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="00a87-271">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-271">Run the app:</span></span>
   * <span data-ttu-id="00a87-272">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="00a87-273">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="00a87-274">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="00a87-275">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="00a87-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="00a87-276">*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="00a87-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="00a87-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="00a87-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="00a87-278">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="00a87-279">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="00a87-280">64 bit (x64) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="00a87-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="00a87-281">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="00a87-281">**Current release**</span></span>

* <span data-ttu-id="00a87-282">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="00a87-283">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="00a87-284">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="00a87-285">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="00a87-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="00a87-286">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="00a87-287">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="00a87-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="00a87-288">*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="00a87-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="00a87-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="00a87-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="00a87-290">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="00a87-291">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="00a87-292">ASP.NET Core modülü stdout günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="00a87-293">ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="00a87-293">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="00a87-294">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="00a87-294">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="00a87-295">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-295">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="00a87-296">**Sorun kategorisini seçin**altında **Web uygulaması aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-296">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="00a87-297">**Önerilen çözümler** > **stdout günlük yeniden yönlendirmeyi etkinleştirmek**için, **Web. config dosyasını düzenlemek üzere kudu konsolunu açmak**için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-297">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="00a87-298">Kudu **Tanılama konsolunda**, klasör > **Wwwroot** **yolunu açın**.</span><span class="sxs-lookup"><span data-stu-id="00a87-298">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="00a87-299">Listenin altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-299">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="00a87-300">*Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-300">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="00a87-301">**StdoutLogEnabled** olarak `true` ayarlayın ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="00a87-301">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="00a87-302">Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-302">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="00a87-303">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-303">Make a request to the app.</span></span>
1. <span data-ttu-id="00a87-304">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="00a87-304">Return to the Azure portal.</span></span> <span data-ttu-id="00a87-305">**GELIŞTIRME araçları** alanında **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-305">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="00a87-306">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-306">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-307">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-307">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-308">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-308">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-309">**LogFiles** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-309">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="00a87-310">**Değiştirilen** sütunu inceleyin ve son değiştirilme tarihiyle stdout günlüğünü düzenlemek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-310">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="00a87-311">Günlük dosyası açıldığında hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-311">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="00a87-312">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="00a87-312">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="00a87-313">Kudu **Tanılama konsolunda**, *Web. config* dosyasını açığa çıkarmak için**Wwwroot** yolu **sitesine** > dönün.</span><span class="sxs-lookup"><span data-stu-id="00a87-313">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="00a87-314">Kalem simgesini seçerek **Web. config** dosyasını tekrar açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-314">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="00a87-315">**StdoutLogEnabled** olarak `false`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-315">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="00a87-316">Dosyayı kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-316">Select **Save** to save the file.</span></span>

<span data-ttu-id="00a87-317">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="00a87-317">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-318">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-318">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-319">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-319">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="00a87-320">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-320">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="00a87-321">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-321">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-322">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-322">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="00a87-323">ASP.NET Core modülü hata ayıklama günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-323">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="00a87-324">ASP.NET Core Module hata ayıklama günlüğü, ASP.NET Core modülünden daha ayrıntılı günlük kaydı sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-324">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="00a87-325">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="00a87-325">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="00a87-326">Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="00a87-326">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="00a87-327">Uygulamayı gelişmiş tanılama günlüğü için yapılandırmak üzere [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-327">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="00a87-328">Uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="00a87-328">Redeploy the app.</span></span>
   * <span data-ttu-id="00a87-329">`<handlerSettings>` [Gelişmiş tanılama günlüklerini](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilen, kudu konsolunu kullanarak canlı uygulamanın *Web. config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-329">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="00a87-330">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="00a87-331">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-332">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-332">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="00a87-333">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="00a87-334">Klasörü > **Wwwroot** **yoluna açın**.</span><span class="sxs-lookup"><span data-stu-id="00a87-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="00a87-335">*Web. config* dosyasını, kurşun kalem düğmesini seçerek düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-335">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="00a87-336">Bölümü, `<handlerSettings>` [Gelişmiş tanılama günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-336">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="00a87-337">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-337">Select the **Save** button.</span></span>
1. <span data-ttu-id="00a87-338">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-338">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="00a87-339">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-339">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-340">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-340">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-341">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-341">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-342">Klasörü > **Wwwroot** **yoluna açın**.</span><span class="sxs-lookup"><span data-stu-id="00a87-342">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="00a87-343">*Aspnetcore-Debug. log* dosyası için bir yol sağlamadıysanız dosya listede görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-343">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="00a87-344">Bir yol sağladıysanız, günlük dosyasının konumuna gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-344">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="00a87-345">Dosya adının yanındaki kurşun kalem düğmesiyle günlük dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-345">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="00a87-346">Sorun giderme tamamlandığında hata ayıklama günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="00a87-346">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="00a87-347">Gelişmiş hata ayıklama günlüğünü devre dışı bırakmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="00a87-347">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="00a87-348">`<handlerSettings>` *Web. config* dosyasından yerel olarak kaldırın ve uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="00a87-348">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="00a87-349">*Web. config* dosyasını düzenlemek ve `<handlerSettings>` bölümünü kaldırmak için kudu konsolunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-349">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="00a87-350">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="00a87-350">Save the file.</span></span>

<span data-ttu-id="00a87-351">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="00a87-351">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-352">Hata ayıklama günlüğünü devre dışı bırakma hatası, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-352">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-353">Günlük dosyası boyutunda sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-353">There's no limit on log file size.</span></span> <span data-ttu-id="00a87-354">Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-354">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="00a87-355">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-355">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-356">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-356">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="00a87-357">Yavaş veya askıda olan uygulama (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-357">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="00a87-358">Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="00a87-358">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="00a87-359">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-359">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="00a87-360">Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın</span><span class="sxs-lookup"><span data-stu-id="00a87-360">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="00a87-361">İzleme kanatları</span><span class="sxs-lookup"><span data-stu-id="00a87-361">Monitoring blades</span></span>

<span data-ttu-id="00a87-362">İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-362">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="00a87-363">Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-363">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="00a87-364">ASP.NET Core uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-364">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="00a87-365">Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="00a87-365">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="00a87-366">**GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-366">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="00a87-367">**ASP.NET Core uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="00a87-367">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="00a87-368">Uzantılar yüklü değilse, **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-368">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="00a87-369">Listeden **ASP.NET Core uzantılarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-369">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="00a87-370">Yasal koşulları kabul etmek için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-370">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="00a87-371">**Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-371">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="00a87-372">Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="00a87-372">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="00a87-373">Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-373">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="00a87-374">Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-374">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="00a87-375">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-375">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-376">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-376">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-377">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-377">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-378">Klasörü > **Wwwroot** **yoluna açın** ve listenin altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-378">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="00a87-379">*Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-379">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="00a87-380">**StdoutLogEnabled** olarak `true` ayarlayın ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="00a87-380">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="00a87-381">Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-381">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="00a87-382">Tanılama günlüğünü etkinleştirmek için ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-382">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="00a87-383">Azure portal **tanılama günlükleri** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-383">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="00a87-384">**Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri**için **bir anahtar seçin** .</span><span class="sxs-lookup"><span data-stu-id="00a87-384">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="00a87-385">Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-385">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="00a87-386">Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme**anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-386">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="00a87-387">Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-387">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="00a87-388">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-388">Make a request to the app.</span></span>
1. <span data-ttu-id="00a87-389">Günlük akışı verileri içinde hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-389">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="00a87-390">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="00a87-390">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="00a87-391">Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):</span><span class="sxs-lookup"><span data-stu-id="00a87-391">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="00a87-392">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-392">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="00a87-393">Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-393">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="00a87-394">[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-394">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="00a87-395">Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="00a87-395">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-396">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-396">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-397">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-397">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="00a87-398">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-398">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-399">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-399">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="00a87-400">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-400">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="00a87-401">Uygulama olay günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-401">Application Event Log (IIS)</span></span>

<span data-ttu-id="00a87-402">Uygulama olay günlüğüne erişin:</span><span class="sxs-lookup"><span data-stu-id="00a87-402">Access the Application Event Log:</span></span>

1. <span data-ttu-id="00a87-403">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-403">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="00a87-404">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-404">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="00a87-405">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-405">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="00a87-406">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-406">Search for errors associated with the failing app.</span></span> <span data-ttu-id="00a87-407">Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="00a87-407">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="00a87-408">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="00a87-408">Run the app at a command prompt</span></span>

<span data-ttu-id="00a87-409">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="00a87-409">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="00a87-410">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="00a87-410">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="00a87-411">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="00a87-411">Framework-dependent deployment</span></span>

<span data-ttu-id="00a87-412">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-412">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="00a87-413">Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *DotNet. exe*ile yürüterek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-413">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="00a87-414">Aşağıdaki komutta, assembly_name> için \<uygulama derlemesinin adını yerine koyun:. `dotnet .\<assembly_name>.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="00a87-415">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="00a87-416">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="00a87-417">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="00a87-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="00a87-418">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="00a87-419">Kendi kendine kapsanan dağıtım</span><span class="sxs-lookup"><span data-stu-id="00a87-419">Self-contained deployment</span></span>

<span data-ttu-id="00a87-420">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-420">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="00a87-421">Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-421">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="00a87-422">Aşağıdaki komutta, assembly_name> için \<uygulama derlemesinin adını yerine koyun:. `<assembly_name>.exe`</span><span class="sxs-lookup"><span data-stu-id="00a87-422">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="00a87-423">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-423">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="00a87-424">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-424">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="00a87-425">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="00a87-425">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="00a87-426">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-426">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="00a87-427">ASP.NET Core Module stdout günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-427">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="00a87-428">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="00a87-428">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="00a87-429">Barındırma sistemindeki sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-429">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="00a87-430">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-430">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="00a87-431">MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.</span><span class="sxs-lookup"><span data-stu-id="00a87-431">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="00a87-432">*Web. config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-432">Edit the *web.config* file.</span></span> <span data-ttu-id="00a87-433">**StdoutLogEnabled** olarak `true` ayarlayın ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="00a87-433">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="00a87-434">`stdout`yolunda günlük dosyası adı ön eki bulunur.</span><span class="sxs-lookup"><span data-stu-id="00a87-434">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="00a87-435">Günlük oluşturulduğunda zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-435">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="00a87-436">Dosya `stdout` adı ön eki olarak kullanıldığında, tipik bir günlük dosyası *stdout_20180205184032_5412. log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-436">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="00a87-437">Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="00a87-437">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="00a87-438">Güncelleştirilmiş *Web. config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="00a87-438">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="00a87-439">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-439">Make a request to the app.</span></span>
1. <span data-ttu-id="00a87-440">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-440">Navigate to the *logs* folder.</span></span> <span data-ttu-id="00a87-441">En son stdout günlüğünü bulup açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-441">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="00a87-442">Günlüğü hatalara karşı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-442">Study the log for errors.</span></span>

<span data-ttu-id="00a87-443">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="00a87-443">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="00a87-444">*Web. config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-444">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="00a87-445">**StdoutLogEnabled** olarak `false`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-445">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="00a87-446">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="00a87-446">Save the file.</span></span>

<span data-ttu-id="00a87-447">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="00a87-447">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-448">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-448">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-449">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-449">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="00a87-450">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-450">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-451">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-451">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="00a87-452">ASP.NET Core modülü hata ayıklama günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-452">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="00a87-453">ASP.NET Core modülü hata ayıklama günlüğünü etkinleştirmek için aşağıdaki işleyici ayarlarını uygulamanın *Web. config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-453">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="00a87-454">Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-454">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="00a87-455">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="00a87-455">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="00a87-456">Geliştirici özel durum sayfasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="00a87-456">Enable the Developer Exception Page</span></span>

<span data-ttu-id="00a87-457">`ASPNETCORE_ENVIRONMENT` Ortam değişkeni, uygulamayı geliştirme ortamında çalıştırmak için [Web. config dosyasına eklenebilir](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) .</span><span class="sxs-lookup"><span data-stu-id="00a87-457">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="00a87-458">Ortam, ana bilgisayar Oluşturucu `UseEnvironment` 'da uygulama başlangıcında geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="00a87-458">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="00a87-459">Ortam değişkeninin `ASPNETCORE_ENVIRONMENT` ayarlanması yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-459">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="00a87-460">Sorun giderme işleminden sonra *Web. config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-460">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="00a87-461">*Web. config*'de ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="00a87-461">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="00a87-462">Uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="00a87-462">Obtain data from an app</span></span>

<span data-ttu-id="00a87-463">Bir uygulamanın isteklere yanıt verme yeteneği varsa, Terminal satır içi ara yazılımı kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="00a87-463">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="00a87-464">Daha fazla bilgi ve örnek kod için bkz <xref:test/troubleshoot#obtain-data-from-an-app>..</span><span class="sxs-lookup"><span data-stu-id="00a87-464">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="00a87-465">Yavaş veya askıda olan uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-465">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="00a87-466">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-466">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="00a87-467">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="00a87-467">App crashes or encounters an exception</span></span>

<span data-ttu-id="00a87-468">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="00a87-468">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="00a87-469">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="00a87-469">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="00a87-470">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00a87-470">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="00a87-471">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-471">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="00a87-472">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-472">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="00a87-473">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-473">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="00a87-474">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-474">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="00a87-475">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-475">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="00a87-476">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-476">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="00a87-477">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-477">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="00a87-478">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-478">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="00a87-479">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="00a87-479">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-480">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="00a87-480">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="00a87-481">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="00a87-481">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="00a87-482">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="00a87-482">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="00a87-483">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="00a87-483">Analyze the dump</span></span>

<span data-ttu-id="00a87-484">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-484">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="00a87-485">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="00a87-485">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="00a87-486">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="00a87-486">Clear package caches</span></span>

<span data-ttu-id="00a87-487">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-487">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="00a87-488">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-488">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="00a87-489">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="00a87-489">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="00a87-490">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="00a87-490">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="00a87-491">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-491">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="00a87-492">Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut `nuget locals all -clear`yürütülebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-492">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="00a87-493">*NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00a87-493">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="00a87-494">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-494">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="00a87-495">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="00a87-495">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00a87-496">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="00a87-496">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="00a87-497">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="00a87-497">Azure documentation</span></span>

* [<span data-ttu-id="00a87-498">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="00a87-498">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="00a87-499">Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü</span><span class="sxs-lookup"><span data-stu-id="00a87-499">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="00a87-500">Azure App Service tanılamada genel bakış</span><span class="sxs-lookup"><span data-stu-id="00a87-500">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="00a87-501">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="00a87-501">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="00a87-502">Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-502">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="00a87-503">Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-503">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="00a87-504">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-504">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="00a87-505">Azure 'da Web Apps için uygulama performansı SSS</span><span class="sxs-lookup"><span data-stu-id="00a87-505">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="00a87-506">Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="00a87-506">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="00a87-507">Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="00a87-507">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="00a87-508">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="00a87-508">Visual Studio documentation</span></span>

* [<span data-ttu-id="00a87-509">Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00a87-509">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="00a87-510">Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00a87-510">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="00a87-511">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="00a87-511">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="00a87-512">Visual Studio Code belgeleri</span><span class="sxs-lookup"><span data-stu-id="00a87-512">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="00a87-513">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="00a87-513">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="00a87-514">Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="00a87-514">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="00a87-515">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="00a87-515">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="00a87-516">Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="00a87-516">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="00a87-517">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-517">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="00a87-518">Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-518">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="00a87-519">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-519">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="00a87-520">IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-520">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="00a87-521">Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="00a87-521">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="00a87-522">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="00a87-522">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="00a87-523">Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="00a87-523">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="00a87-524">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="00a87-524">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="00a87-525">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="00a87-525">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="00a87-526">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="00a87-526">App startup errors</span></span>

<span data-ttu-id="00a87-527">Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="00a87-527">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="00a87-528">*502,5-Işlem hatası* veya yerel olarak hata ayıklarken oluşan *500,30-başlatma hatası* , bu konudaki öneri kullanılarak tanılanabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-528">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="00a87-529">403,14 yasak</span><span class="sxs-lookup"><span data-stu-id="00a87-529">403.14 Forbidden</span></span>

<span data-ttu-id="00a87-530">Uygulama başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-530">The app fails to start.</span></span> <span data-ttu-id="00a87-531">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="00a87-531">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="00a87-532">Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:</span><span class="sxs-lookup"><span data-stu-id="00a87-532">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="00a87-533">Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-533">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="00a87-534">Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="00a87-534">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="00a87-535">*Web. config* dosyası dağıtımda yok veya *Web. config* dosyası içerikleri hatalı biçimlendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="00a87-535">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="00a87-536">Aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="00a87-536">Perform the following steps:</span></span>

1. <span data-ttu-id="00a87-537">Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.</span><span class="sxs-lookup"><span data-stu-id="00a87-537">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="00a87-538">Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="00a87-538">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="00a87-539">*Web. config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-539">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="00a87-540">Azure App Service barındırırken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-540">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="00a87-541">Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında**gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-541">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="00a87-542">Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-542">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="00a87-543">Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure>..</span><span class="sxs-lookup"><span data-stu-id="00a87-543">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="00a87-544">*Web. config* dosyası hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>..</span><span class="sxs-lookup"><span data-stu-id="00a87-544">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="00a87-545">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-545">500 Internal Server Error</span></span>

<span data-ttu-id="00a87-546">Uygulama başlıyor, ancak bir hata sunucunun isteği yerine getirmesini engelliyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-546">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="00a87-547">Bu hata, başlangıç sırasında veya Yanıt oluştururken uygulamanın kodunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="00a87-547">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="00a87-548">Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-548">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="00a87-549">Uygulama olay günlüğü genellikle uygulamanın normal olarak başlatıldığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="00a87-549">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="00a87-550">Sunucu perspektifinden doğru.</span><span class="sxs-lookup"><span data-stu-id="00a87-550">From the server's perspective, that's correct.</span></span> <span data-ttu-id="00a87-551">Uygulama başlatıldı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-551">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="00a87-552">Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="00a87-552">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="00a87-553">500,0 Işlem içi Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-553">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="00a87-554">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-554">The worker process fails.</span></span> <span data-ttu-id="00a87-555">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-555">The app doesn't start.</span></span>

<span data-ttu-id="00a87-556">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR 'yi bulamıyor ve işlem içi istek işleyicisini (*aspnetcorev2_inprocess. dll*) bulamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-556">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="00a87-557">Şunları denetleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-557">Check that:</span></span>

* <span data-ttu-id="00a87-558">Uygulama [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet paketini ya da [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'i hedefler.</span><span class="sxs-lookup"><span data-stu-id="00a87-558">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="00a87-559">Uygulamanın hedeflediği ASP.NET Core paylaşılan çerçevenin sürümü hedef makineye yüklendi.</span><span class="sxs-lookup"><span data-stu-id="00a87-559">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="00a87-560">500,0 Işlem dışı Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-560">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="00a87-561">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-561">The worker process fails.</span></span> <span data-ttu-id="00a87-562">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-562">The app doesn't start.</span></span>

<span data-ttu-id="00a87-563">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) işlem dışı barındırma isteği işleyicisini bulamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-563">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="00a87-564">*Aspnetcorev2_outofprocess. dll* ' nin *aspnetcorev2. dll*' nin yanındaki bir alt klasörde bulunduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="00a87-564">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="00a87-565">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-565">502.5 Process Failure</span></span>

<span data-ttu-id="00a87-566">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-566">The worker process fails.</span></span> <span data-ttu-id="00a87-567">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-567">The app doesn't start.</span></span>

<span data-ttu-id="00a87-568">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-568">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="00a87-569">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-569">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="00a87-570">Yaygın bir hata durumu, ASP.NET Core paylaşılan bir Framework 'ün mevcut olmayan bir sürümünü hedeflediğinden, uygulamanın yanlış yapılandırılmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="00a87-570">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="00a87-571">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-571">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="00a87-572">*Paylaşılan çerçeve* , makinede yüklü olan ve bir metapackage tarafından başvurulan derleme (*. dll* dosyaları) kümesidir `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="00a87-572">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="00a87-573">Metapackage başvurusu, gerekli en düşük sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-573">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="00a87-574">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="00a87-574">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="00a87-575">Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:</span><span class="sxs-lookup"><span data-stu-id="00a87-575">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="00a87-576">Uygulama başlatılamadı (hata kodu ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="00a87-576">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="00a87-577">Uygulamanın derlemesi (*. dll*) yüklenemediğinden uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="00a87-577">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="00a87-578">Bu hata, yayımlanan uygulama ile W3wp/iisexpress işlemi arasında bir bit durumu uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="00a87-578">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="00a87-579">Uygulama havuzunun 32 bit ayarının doğru olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="00a87-579">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="00a87-580">IIS yöneticisinin **uygulama havuzlarında**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-580">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="00a87-581">**Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-581">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="00a87-582">**Enable 32 bit uygulamalarını**ayarla:</span><span class="sxs-lookup"><span data-stu-id="00a87-582">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="00a87-583">32-bit (x86) bir uygulama dağıtıyorsanız, değerini olarak `True`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-583">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="00a87-584">64 bit (x64) uygulaması dağıtıyorsanız, değerini olarak `False`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-584">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="00a87-585">Proje dosyasındaki bir `<Platform>` MSBuild özelliği ve uygulamanın yayınlanan bit durumu arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-585">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="00a87-586">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="00a87-586">Connection reset</span></span>

<span data-ttu-id="00a87-587">Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-587">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="00a87-588">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirilmesi sırasında bir hata oluştuğunda meydana gelir.</span><span class="sxs-lookup"><span data-stu-id="00a87-588">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="00a87-589">Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-589">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="00a87-590">[Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-590">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="00a87-591">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="00a87-591">Default startup limits</span></span>

<span data-ttu-id="00a87-592">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-592">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="00a87-593">Varsayılan değerde sol tarafta, modül bir işlem hatası günlüğe kaydedilmeden önce uygulamanın başlaması iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-593">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="00a87-594">Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="00a87-594">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="00a87-595">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-595">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="00a87-596">Uygulama olay günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-596">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="00a87-597">Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="00a87-597">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="00a87-598">Azure portal uygulama **Hizmetleri**' nde uygulamayı açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-598">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="00a87-599">**Sorunları tanılama ve çözme** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-599">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="00a87-600">**Tanılama araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-600">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="00a87-601">**Destek Araçları**' nın altında, **uygulama olayları** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-601">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="00a87-602">**Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-602">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="00a87-603">**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="00a87-603">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="00a87-604">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-604">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="00a87-605">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-605">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-606">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-606">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-607">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-607">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-608">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-608">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="00a87-609">*EventLog. xml* dosyasının yanındaki kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-609">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="00a87-610">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-610">Examine the log.</span></span> <span data-ttu-id="00a87-611">En son olayları görmek için günlüğün en altına gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-611">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="00a87-612">Uygulamayı kudu konsolunda çalıştırma</span><span class="sxs-lookup"><span data-stu-id="00a87-612">Run the app in the Kudu console</span></span>

<span data-ttu-id="00a87-613">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="00a87-613">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="00a87-614">Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="00a87-614">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="00a87-615">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-615">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="00a87-616">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-616">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-617">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-617">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-618">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-618">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="00a87-619">32 bit (x86) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="00a87-619">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="00a87-620">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="00a87-620">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="00a87-621">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-621">Run the app:</span></span>
   * <span data-ttu-id="00a87-622">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-622">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="00a87-623">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-623">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="00a87-624">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-624">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="00a87-625">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="00a87-625">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="00a87-626">*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="00a87-626">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="00a87-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="00a87-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="00a87-628">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-628">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="00a87-629">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-629">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="00a87-630">64 bit (x64) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="00a87-630">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="00a87-631">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="00a87-631">**Current release**</span></span>

* <span data-ttu-id="00a87-632">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-632">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="00a87-633">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-633">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="00a87-634">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-634">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="00a87-635">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="00a87-635">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="00a87-636">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-636">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="00a87-637">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="00a87-637">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="00a87-638">*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="00a87-638">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="00a87-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="00a87-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="00a87-640">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-640">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="00a87-641">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-641">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="00a87-642">ASP.NET Core modülü stdout günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-642">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="00a87-643">ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="00a87-643">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="00a87-644">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="00a87-644">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="00a87-645">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-645">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="00a87-646">**Sorun kategorisini seçin**altında **Web uygulaması aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-646">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="00a87-647">**Önerilen çözümler** > **stdout günlük yeniden yönlendirmeyi etkinleştirmek**için, **Web. config dosyasını düzenlemek üzere kudu konsolunu açmak**için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-647">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="00a87-648">Kudu **Tanılama konsolunda**, klasör > **Wwwroot** **yolunu açın**.</span><span class="sxs-lookup"><span data-stu-id="00a87-648">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="00a87-649">Listenin altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-649">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="00a87-650">*Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-650">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="00a87-651">**StdoutLogEnabled** olarak `true` ayarlayın ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="00a87-651">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="00a87-652">Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-652">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="00a87-653">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-653">Make a request to the app.</span></span>
1. <span data-ttu-id="00a87-654">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="00a87-654">Return to the Azure portal.</span></span> <span data-ttu-id="00a87-655">**GELIŞTIRME araçları** alanında **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-655">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="00a87-656">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-656">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-657">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-657">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-658">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-658">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-659">**LogFiles** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-659">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="00a87-660">**Değiştirilen** sütunu inceleyin ve son değiştirilme tarihiyle stdout günlüğünü düzenlemek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-660">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="00a87-661">Günlük dosyası açıldığında hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-661">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="00a87-662">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="00a87-662">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="00a87-663">Kudu **Tanılama konsolunda**, *Web. config* dosyasını açığa çıkarmak için**Wwwroot** yolu **sitesine** > dönün.</span><span class="sxs-lookup"><span data-stu-id="00a87-663">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="00a87-664">Kalem simgesini seçerek **Web. config** dosyasını tekrar açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-664">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="00a87-665">**StdoutLogEnabled** olarak `false`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-665">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="00a87-666">Dosyayı kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-666">Select **Save** to save the file.</span></span>

<span data-ttu-id="00a87-667">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="00a87-667">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-668">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-668">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-669">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-669">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="00a87-670">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-670">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="00a87-671">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-671">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-672">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-672">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="00a87-673">ASP.NET Core modülü hata ayıklama günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-673">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="00a87-674">ASP.NET Core Module hata ayıklama günlüğü, ASP.NET Core modülünden daha ayrıntılı günlük kaydı sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-674">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="00a87-675">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="00a87-675">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="00a87-676">Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="00a87-676">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="00a87-677">Uygulamayı gelişmiş tanılama günlüğü için yapılandırmak üzere [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-677">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="00a87-678">Uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="00a87-678">Redeploy the app.</span></span>
   * <span data-ttu-id="00a87-679">`<handlerSettings>` [Gelişmiş tanılama günlüklerini](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilen, kudu konsolunu kullanarak canlı uygulamanın *Web. config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-679">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="00a87-680">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="00a87-681">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-682">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-682">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="00a87-683">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="00a87-684">Klasörü > **Wwwroot** **yoluna açın**.</span><span class="sxs-lookup"><span data-stu-id="00a87-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="00a87-685">*Web. config* dosyasını, kurşun kalem düğmesini seçerek düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-685">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="00a87-686">Bölümü, `<handlerSettings>` [Gelişmiş tanılama günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-686">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="00a87-687">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-687">Select the **Save** button.</span></span>
1. <span data-ttu-id="00a87-688">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-688">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="00a87-689">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-689">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-690">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-690">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-691">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-691">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-692">Klasörü > **Wwwroot** **yoluna açın**.</span><span class="sxs-lookup"><span data-stu-id="00a87-692">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="00a87-693">*Aspnetcore-Debug. log* dosyası için bir yol sağlamadıysanız dosya listede görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-693">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="00a87-694">Bir yol sağladıysanız, günlük dosyasının konumuna gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-694">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="00a87-695">Dosya adının yanındaki kurşun kalem düğmesiyle günlük dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-695">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="00a87-696">Sorun giderme tamamlandığında hata ayıklama günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="00a87-696">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="00a87-697">Gelişmiş hata ayıklama günlüğünü devre dışı bırakmak için aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="00a87-697">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="00a87-698">`<handlerSettings>` *Web. config* dosyasından yerel olarak kaldırın ve uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="00a87-698">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="00a87-699">*Web. config* dosyasını düzenlemek ve `<handlerSettings>` bölümünü kaldırmak için kudu konsolunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-699">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="00a87-700">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="00a87-700">Save the file.</span></span>

<span data-ttu-id="00a87-701">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="00a87-701">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-702">Hata ayıklama günlüğünü devre dışı bırakma hatası, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-702">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-703">Günlük dosyası boyutunda sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-703">There's no limit on log file size.</span></span> <span data-ttu-id="00a87-704">Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-704">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="00a87-705">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-705">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-706">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-706">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="00a87-707">Yavaş veya askıda olan uygulama (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-707">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="00a87-708">Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="00a87-708">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="00a87-709">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-709">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="00a87-710">Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın</span><span class="sxs-lookup"><span data-stu-id="00a87-710">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="00a87-711">İzleme kanatları</span><span class="sxs-lookup"><span data-stu-id="00a87-711">Monitoring blades</span></span>

<span data-ttu-id="00a87-712">İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-712">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="00a87-713">Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-713">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="00a87-714">ASP.NET Core uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-714">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="00a87-715">Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="00a87-715">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="00a87-716">**GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-716">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="00a87-717">**ASP.NET Core uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="00a87-717">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="00a87-718">Uzantılar yüklü değilse, **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-718">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="00a87-719">Listeden **ASP.NET Core uzantılarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-719">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="00a87-720">Yasal koşulları kabul etmek için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-720">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="00a87-721">**Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-721">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="00a87-722">Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="00a87-722">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="00a87-723">Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-723">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="00a87-724">Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-724">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="00a87-725">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-725">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-726">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-726">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-727">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-727">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-728">Klasörü > **Wwwroot** **yoluna açın** ve listenin altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-728">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="00a87-729">*Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-729">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="00a87-730">**StdoutLogEnabled** olarak `true` ayarlayın ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="00a87-730">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="00a87-731">Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-731">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="00a87-732">Tanılama günlüğünü etkinleştirmek için ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-732">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="00a87-733">Azure portal **tanılama günlükleri** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-733">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="00a87-734">**Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri**için **bir anahtar seçin** .</span><span class="sxs-lookup"><span data-stu-id="00a87-734">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="00a87-735">Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-735">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="00a87-736">Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme**anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-736">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="00a87-737">Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-737">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="00a87-738">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-738">Make a request to the app.</span></span>
1. <span data-ttu-id="00a87-739">Günlük akışı verileri içinde hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-739">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="00a87-740">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="00a87-740">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="00a87-741">Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):</span><span class="sxs-lookup"><span data-stu-id="00a87-741">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="00a87-742">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-742">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="00a87-743">Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-743">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="00a87-744">[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-744">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="00a87-745">Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="00a87-745">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-746">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-746">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-747">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-747">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="00a87-748">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-748">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-749">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-749">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="00a87-750">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-750">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="00a87-751">Uygulama olay günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-751">Application Event Log (IIS)</span></span>

<span data-ttu-id="00a87-752">Uygulama olay günlüğüne erişin:</span><span class="sxs-lookup"><span data-stu-id="00a87-752">Access the Application Event Log:</span></span>

1. <span data-ttu-id="00a87-753">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-753">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="00a87-754">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-754">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="00a87-755">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-755">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="00a87-756">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-756">Search for errors associated with the failing app.</span></span> <span data-ttu-id="00a87-757">Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="00a87-757">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="00a87-758">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="00a87-758">Run the app at a command prompt</span></span>

<span data-ttu-id="00a87-759">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="00a87-759">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="00a87-760">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="00a87-760">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="00a87-761">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="00a87-761">Framework-dependent deployment</span></span>

<span data-ttu-id="00a87-762">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-762">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="00a87-763">Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *DotNet. exe*ile yürüterek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-763">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="00a87-764">Aşağıdaki komutta, assembly_name> için \<uygulama derlemesinin adını yerine koyun:. `dotnet .\<assembly_name>.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="00a87-765">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="00a87-766">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="00a87-767">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="00a87-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="00a87-768">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="00a87-769">Kendi kendine kapsanan dağıtım</span><span class="sxs-lookup"><span data-stu-id="00a87-769">Self-contained deployment</span></span>

<span data-ttu-id="00a87-770">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-770">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="00a87-771">Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-771">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="00a87-772">Aşağıdaki komutta, assembly_name> için \<uygulama derlemesinin adını yerine koyun:. `<assembly_name>.exe`</span><span class="sxs-lookup"><span data-stu-id="00a87-772">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="00a87-773">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-773">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="00a87-774">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-774">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="00a87-775">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="00a87-775">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="00a87-776">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-776">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="00a87-777">ASP.NET Core Module stdout günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-777">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="00a87-778">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="00a87-778">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="00a87-779">Barındırma sistemindeki sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-779">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="00a87-780">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-780">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="00a87-781">MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.</span><span class="sxs-lookup"><span data-stu-id="00a87-781">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="00a87-782">*Web. config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-782">Edit the *web.config* file.</span></span> <span data-ttu-id="00a87-783">**StdoutLogEnabled** olarak `true` ayarlayın ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="00a87-783">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="00a87-784">`stdout`yolunda günlük dosyası adı ön eki bulunur.</span><span class="sxs-lookup"><span data-stu-id="00a87-784">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="00a87-785">Günlük oluşturulduğunda zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-785">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="00a87-786">Dosya `stdout` adı ön eki olarak kullanıldığında, tipik bir günlük dosyası *stdout_20180205184032_5412. log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-786">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="00a87-787">Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="00a87-787">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="00a87-788">Güncelleştirilmiş *Web. config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="00a87-788">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="00a87-789">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-789">Make a request to the app.</span></span>
1. <span data-ttu-id="00a87-790">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-790">Navigate to the *logs* folder.</span></span> <span data-ttu-id="00a87-791">En son stdout günlüğünü bulup açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-791">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="00a87-792">Günlüğü hatalara karşı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-792">Study the log for errors.</span></span>

<span data-ttu-id="00a87-793">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="00a87-793">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="00a87-794">*Web. config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-794">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="00a87-795">**StdoutLogEnabled** olarak `false`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-795">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="00a87-796">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="00a87-796">Save the file.</span></span>

<span data-ttu-id="00a87-797">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="00a87-797">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-798">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-798">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-799">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-799">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="00a87-800">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-800">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-801">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-801">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="00a87-802">ASP.NET Core modülü hata ayıklama günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-802">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="00a87-803">ASP.NET Core modülü hata ayıklama günlüğünü etkinleştirmek için aşağıdaki işleyici ayarlarını uygulamanın *Web. config* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-803">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="00a87-804">Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-804">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="00a87-805">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="00a87-805">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="00a87-806">Geliştirici özel durum sayfasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="00a87-806">Enable the Developer Exception Page</span></span>

<span data-ttu-id="00a87-807">`ASPNETCORE_ENVIRONMENT` Ortam değişkeni, uygulamayı geliştirme ortamında çalıştırmak için [Web. config dosyasına eklenebilir](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) .</span><span class="sxs-lookup"><span data-stu-id="00a87-807">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="00a87-808">Ortam, ana bilgisayar Oluşturucu `UseEnvironment` 'da uygulama başlangıcında geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="00a87-808">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="00a87-809">Ortam değişkeninin `ASPNETCORE_ENVIRONMENT` ayarlanması yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-809">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="00a87-810">Sorun giderme işleminden sonra *Web. config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-810">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="00a87-811">*Web. config*'de ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="00a87-811">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="00a87-812">Uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="00a87-812">Obtain data from an app</span></span>

<span data-ttu-id="00a87-813">Bir uygulamanın isteklere yanıt verme yeteneği varsa, Terminal satır içi ara yazılımı kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="00a87-813">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="00a87-814">Daha fazla bilgi ve örnek kod için bkz <xref:test/troubleshoot#obtain-data-from-an-app>..</span><span class="sxs-lookup"><span data-stu-id="00a87-814">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="00a87-815">Yavaş veya askıda olan uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-815">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="00a87-816">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-816">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="00a87-817">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="00a87-817">App crashes or encounters an exception</span></span>

<span data-ttu-id="00a87-818">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="00a87-818">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="00a87-819">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="00a87-819">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="00a87-820">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00a87-820">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="00a87-821">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-821">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="00a87-822">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-822">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="00a87-823">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-823">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="00a87-824">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-824">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="00a87-825">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-825">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="00a87-826">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-826">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="00a87-827">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-827">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="00a87-828">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-828">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="00a87-829">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="00a87-829">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-830">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="00a87-830">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="00a87-831">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="00a87-831">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="00a87-832">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="00a87-832">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="00a87-833">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="00a87-833">Analyze the dump</span></span>

<span data-ttu-id="00a87-834">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-834">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="00a87-835">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="00a87-835">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="00a87-836">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="00a87-836">Clear package caches</span></span>

<span data-ttu-id="00a87-837">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-837">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="00a87-838">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-838">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="00a87-839">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="00a87-839">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="00a87-840">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="00a87-840">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="00a87-841">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-841">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="00a87-842">Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut `nuget locals all -clear`yürütülebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-842">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="00a87-843">*NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00a87-843">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="00a87-844">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-844">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="00a87-845">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="00a87-845">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00a87-846">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="00a87-846">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="00a87-847">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="00a87-847">Azure documentation</span></span>

* [<span data-ttu-id="00a87-848">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="00a87-848">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="00a87-849">Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü</span><span class="sxs-lookup"><span data-stu-id="00a87-849">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="00a87-850">Azure App Service tanılamada genel bakış</span><span class="sxs-lookup"><span data-stu-id="00a87-850">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="00a87-851">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="00a87-851">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="00a87-852">Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-852">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="00a87-853">Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-853">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="00a87-854">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-854">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="00a87-855">Azure 'da Web Apps için uygulama performansı SSS</span><span class="sxs-lookup"><span data-stu-id="00a87-855">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="00a87-856">Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="00a87-856">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="00a87-857">Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="00a87-857">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="00a87-858">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="00a87-858">Visual Studio documentation</span></span>

* [<span data-ttu-id="00a87-859">Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00a87-859">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="00a87-860">Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00a87-860">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="00a87-861">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="00a87-861">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="00a87-862">Visual Studio Code belgeleri</span><span class="sxs-lookup"><span data-stu-id="00a87-862">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="00a87-863">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="00a87-863">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="00a87-864">Bu makalede, bir uygulama Azure App Service veya IIS 'ye dağıtıldığında hataların nasıl tanılanacağı hakkında genel uygulama başlatma hataları ve yönergeleri hakkında bilgi verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="00a87-864">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="00a87-865">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="00a87-865">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="00a87-866">Ortak Başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="00a87-866">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="00a87-867">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-867">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="00a87-868">Azure App Service dağıtılan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-868">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="00a87-869">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-869">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="00a87-870">IIS 'ye dağıtılan veya IIS Express yerel olarak çalışan uygulamalar için sorun giderme önerisi sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-870">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="00a87-871">Bu kılavuz hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="00a87-871">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="00a87-872">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="00a87-872">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="00a87-873">Önemli güncelleştirmeler gerçekleştirirken veya paket sürümlerini değiştirirken ne yapmanız gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="00a87-873">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="00a87-874">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="00a87-874">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="00a87-875">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="00a87-875">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="00a87-876">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="00a87-876">App startup errors</span></span>

<span data-ttu-id="00a87-877">Visual Studio 'da bir ASP.NET Core projesi, hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırmak için varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="00a87-877">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="00a87-878">Yerel olarak hata ayıklamada oluşan *502,5 Işlem hatası* , bu konudaki öneri kullanılarak tanılanabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-878">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="00a87-879">403,14 yasak</span><span class="sxs-lookup"><span data-stu-id="00a87-879">403.14 Forbidden</span></span>

<span data-ttu-id="00a87-880">Uygulama başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-880">The app fails to start.</span></span> <span data-ttu-id="00a87-881">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="00a87-881">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="00a87-882">Hata genellikle barındırma sisteminde, aşağıdaki senaryolardan birini içeren bozuk bir dağıtım nedeniyle oluşur:</span><span class="sxs-lookup"><span data-stu-id="00a87-882">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="00a87-883">Uygulama, barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-883">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="00a87-884">Dağıtım işlemi, uygulamanın tüm dosyalarını ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="00a87-884">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="00a87-885">*Web. config* dosyası dağıtımda yok veya *Web. config* dosyası içerikleri hatalı biçimlendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="00a87-885">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="00a87-886">Aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="00a87-886">Perform the following steps:</span></span>

1. <span data-ttu-id="00a87-887">Tüm dosya ve klasörleri barındırma sistemindeki dağıtım klasöründen silin.</span><span class="sxs-lookup"><span data-stu-id="00a87-887">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="00a87-888">Visual Studio, PowerShell veya el ile dağıtım gibi normal dağıtım yönteminizi kullanarak, uygulamanın *Yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="00a87-888">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="00a87-889">*Web. config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-889">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="00a87-890">Azure App Service barındırırken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-890">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="00a87-891">Uygulama IIS tarafından barındırılıyorsa, uygulamanın **IIS yöneticisinin** **temel ayarlarında**gösterilen IIS **fiziksel yoluna** dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-891">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="00a87-892">Barındırma sistemindeki dağıtımı projenin *Yayımla* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtıldığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-892">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="00a87-893">Yayımlanan ASP.NET Core uygulamasının düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure>..</span><span class="sxs-lookup"><span data-stu-id="00a87-893">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="00a87-894">*Web. config* dosyası hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>..</span><span class="sxs-lookup"><span data-stu-id="00a87-894">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="00a87-895">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-895">500 Internal Server Error</span></span>

<span data-ttu-id="00a87-896">Uygulama başlıyor, ancak bir hata sunucunun isteği yerine getirmesini engelliyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-896">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="00a87-897">Bu hata, başlangıç sırasında veya Yanıt oluştururken uygulamanın kodunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="00a87-897">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="00a87-898">Yanıtta içerik yok olabilir veya Yanıt, tarayıcıda *500 Iç sunucu hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-898">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="00a87-899">Uygulama olay günlüğü genellikle uygulamanın normal olarak başlatıldığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="00a87-899">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="00a87-900">Sunucu perspektifinden doğru.</span><span class="sxs-lookup"><span data-stu-id="00a87-900">From the server's perspective, that's correct.</span></span> <span data-ttu-id="00a87-901">Uygulama başlatıldı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-901">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="00a87-902">Uygulamayı sunucuda bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Core modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="00a87-902">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="00a87-903">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="00a87-903">502.5 Process Failure</span></span>

<span data-ttu-id="00a87-904">Çalışan işlemi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-904">The worker process fails.</span></span> <span data-ttu-id="00a87-905">Uygulama başlamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-905">The app doesn't start.</span></span>

<span data-ttu-id="00a87-906">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) çalışan işlemini başlatmaya çalışır, ancak başlatılamıyor.</span><span class="sxs-lookup"><span data-stu-id="00a87-906">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="00a87-907">İşlem başlatma hatasının nedeni genellikle uygulama olay günlüğündeki girişlerden ve ASP.NET Core modülü stdout günlüğünde belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-907">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="00a87-908">Yaygın bir hata durumu, ASP.NET Core paylaşılan bir Framework 'ün mevcut olmayan bir sürümünü hedeflediğinden, uygulamanın yanlış yapılandırılmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="00a87-908">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="00a87-909">ASP.NET Core paylaşılan Framework 'ün hangi sürümlerinin hedef makinede yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-909">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="00a87-910">*Paylaşılan çerçeve* , makinede yüklü olan ve bir metapackage tarafından başvurulan derleme (*. dll* dosyaları) kümesidir `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="00a87-910">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="00a87-911">Metapackage başvurusu, gerekli en düşük sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-911">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="00a87-912">Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="00a87-912">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="00a87-913">Bir barındırma veya uygulamanın yanlış yapılandırılması, çalışan işleminin başarısız olmasına neden olduğunda, *502,5 Işlem hata* hatası sayfası döndürülür:</span><span class="sxs-lookup"><span data-stu-id="00a87-913">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="00a87-914">Uygulama başlatılamadı (hata kodu ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="00a87-914">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="00a87-915">Uygulamanın derlemesi (*. dll*) yüklenemediğinden uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="00a87-915">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="00a87-916">Bu hata, yayımlanan uygulama ile W3wp/iisexpress işlemi arasında bir bit durumu uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="00a87-916">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="00a87-917">Uygulama havuzunun 32 bit ayarının doğru olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="00a87-917">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="00a87-918">IIS yöneticisinin **uygulama havuzlarında**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-918">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="00a87-919">**Eylemler** panelinde **uygulama havuzunu Düzenle** altında **Gelişmiş ayarlar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-919">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="00a87-920">**Enable 32 bit uygulamalarını**ayarla:</span><span class="sxs-lookup"><span data-stu-id="00a87-920">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="00a87-921">32-bit (x86) bir uygulama dağıtıyorsanız, değerini olarak `True`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-921">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="00a87-922">64 bit (x64) uygulaması dağıtıyorsanız, değerini olarak `False`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-922">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="00a87-923">Proje dosyasındaki bir `<Platform>` MSBuild özelliği ve uygulamanın yayınlanan bit durumu arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-923">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="00a87-924">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="00a87-924">Connection reset</span></span>

<span data-ttu-id="00a87-925">Üstbilgiler gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Iç sunucu hatası** gönderebilmesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="00a87-925">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="00a87-926">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirilmesi sırasında bir hata oluştuğunda meydana gelir.</span><span class="sxs-lookup"><span data-stu-id="00a87-926">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="00a87-927">Bu tür bir hata, istemcide bir *bağlantı sıfırlama* hatası olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-927">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="00a87-928">[Uygulama günlüğü](xref:fundamentals/logging/index) bu tür hataların giderilmesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-928">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="00a87-929">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="00a87-929">Default startup limits</span></span>

<span data-ttu-id="00a87-930">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) varsayılan bir *StartupTimeLimit* 120 saniye ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-930">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="00a87-931">Varsayılan değerde sol tarafta, modül bir işlem hatası günlüğe kaydedilmeden önce uygulamanın başlaması iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-931">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="00a87-932">Modülü yapılandırma hakkında daha fazla bilgi için bkz. [aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="00a87-932">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="00a87-933">Azure App Service sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-933">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="00a87-934">Uygulama olay günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-934">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="00a87-935">Uygulama olay günlüğüne erişmek için Azure portal **sorunları Tanıla ve çöz** dikey penceresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="00a87-935">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="00a87-936">Azure portal uygulama **Hizmetleri**' nde uygulamayı açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-936">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="00a87-937">**Sorunları tanılama ve çözme** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-937">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="00a87-938">**Tanılama araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-938">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="00a87-939">**Destek Araçları**' nın altında, **uygulama olayları** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-939">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="00a87-940">**Kaynak** sütununda *IIS AspNetCoreModule* veya *IIS Aspnetcoremodule v2* girişi tarafından belirtilen en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-940">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="00a87-941">**Sorunları Tanıla ve çöz** dikey penceresini kullanmanın bir alternatifi, uygulama olay günlüğü dosyasını doğrudan [kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="00a87-941">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="00a87-942">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-942">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="00a87-943">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-943">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-944">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-944">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-945">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-945">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-946">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-946">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="00a87-947">*EventLog. xml* dosyasının yanındaki kurşun kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-947">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="00a87-948">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-948">Examine the log.</span></span> <span data-ttu-id="00a87-949">En son olayları görmek için günlüğün en altına gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-949">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="00a87-950">Uygulamayı kudu konsolunda çalıştırma</span><span class="sxs-lookup"><span data-stu-id="00a87-950">Run the app in the Kudu console</span></span>

<span data-ttu-id="00a87-951">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="00a87-951">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="00a87-952">Bu hatayı saptamak için, uygulamayı [kudu](https://github.com/projectkudu/kudu/wiki) uzaktan yürütme konsolu 'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="00a87-952">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="00a87-953">**Gelişmiş araçları** **geliştirme araçları** alanında açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-953">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="00a87-954">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-954">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-955">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-955">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-956">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-956">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="00a87-957">32 bit (x86) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="00a87-957">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="00a87-958">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="00a87-958">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="00a87-959">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-959">Run the app:</span></span>
   * <span data-ttu-id="00a87-960">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-960">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="00a87-961">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-961">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="00a87-962">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-962">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="00a87-963">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="00a87-963">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="00a87-964">*ASP.NET Core {VERSION} (x86) çalışma zamanı site uzantısının yüklenmesini gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="00a87-964">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="00a87-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="00a87-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="00a87-966">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-966">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="00a87-967">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-967">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="00a87-968">64 bit (x64) uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="00a87-968">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="00a87-969">**Geçerli yayın**</span><span class="sxs-lookup"><span data-stu-id="00a87-969">**Current release**</span></span>

* <span data-ttu-id="00a87-970">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-970">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="00a87-971">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-971">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="00a87-972">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-972">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="00a87-973">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="00a87-973">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="00a87-974">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-974">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="00a87-975">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="00a87-975">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="00a87-976">*ASP.NET Core {VERSION} (x64) çalışma zamanı site uzantısını yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="00a87-976">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="00a87-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="00a87-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="00a87-978">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-978">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="00a87-979">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-979">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="00a87-980">ASP.NET Core modülü stdout günlüğü (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-980">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="00a87-981">ASP.NET Core Module stdout günlüğü genellikle uygulama olay günlüğünde bulunmayan yararlı hata iletilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="00a87-981">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="00a87-982">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="00a87-982">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="00a87-983">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-983">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="00a87-984">**Sorun kategorisini seçin**altında **Web uygulaması aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-984">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="00a87-985">**Önerilen çözümler** > **stdout günlük yeniden yönlendirmeyi etkinleştirmek**için, **Web. config dosyasını düzenlemek üzere kudu konsolunu açmak**için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-985">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="00a87-986">Kudu **Tanılama konsolunda**, klasör > **Wwwroot** **yolunu açın**.</span><span class="sxs-lookup"><span data-stu-id="00a87-986">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="00a87-987">Listenin altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-987">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="00a87-988">*Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-988">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="00a87-989">**StdoutLogEnabled** olarak `true` ayarlayın ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="00a87-989">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="00a87-990">Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-990">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="00a87-991">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-991">Make a request to the app.</span></span>
1. <span data-ttu-id="00a87-992">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="00a87-992">Return to the Azure portal.</span></span> <span data-ttu-id="00a87-993">**GELIŞTIRME araçları** alanında **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-993">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="00a87-994">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-994">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-995">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-995">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-996">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-996">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-997">**LogFiles** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-997">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="00a87-998">**Değiştirilen** sütunu inceleyin ve son değiştirilme tarihiyle stdout günlüğünü düzenlemek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-998">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="00a87-999">Günlük dosyası açıldığında hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-999">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="00a87-1000">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="00a87-1000">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="00a87-1001">Kudu **Tanılama konsolunda**, *Web. config* dosyasını açığa çıkarmak için**Wwwroot** yolu **sitesine** > dönün.</span><span class="sxs-lookup"><span data-stu-id="00a87-1001">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="00a87-1002">Kalem simgesini seçerek **Web. config** dosyasını tekrar açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1002">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="00a87-1003">**StdoutLogEnabled** olarak `false`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1003">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="00a87-1004">Dosyayı kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1004">Select **Save** to save the file.</span></span>

<span data-ttu-id="00a87-1005">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="00a87-1005">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-1006">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1006">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-1007">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-1007">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="00a87-1008">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1008">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="00a87-1009">Başlangıçtan sonra ASP.NET Core bir uygulamada genel günlüğe kaydetme için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1009">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-1010">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-1010">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="00a87-1011">Yavaş veya askıda olan uygulama (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="00a87-1011">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="00a87-1012">Bir uygulama bir istek üzerinde yavaş bir şekilde yanıt verdiğinde veya Kilitlenmelerinde, aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="00a87-1012">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="00a87-1013">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-1013">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="00a87-1014">Azure Web uygulamasında aralıklı özel durum sorunları veya performans sorunları için döküm yakalamak üzere kilitlenme tanılayıcı site uzantısı 'nı kullanın</span><span class="sxs-lookup"><span data-stu-id="00a87-1014">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="00a87-1015">İzleme kanatları</span><span class="sxs-lookup"><span data-stu-id="00a87-1015">Monitoring blades</span></span>

<span data-ttu-id="00a87-1016">İzleme dikey pencereleri, konusunda daha önce açıklanan yöntemlere alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="00a87-1016">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="00a87-1017">Bu kanatlar 500 serisi hataları tanılamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1017">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="00a87-1018">ASP.NET Core uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1018">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="00a87-1019">Uzantılar yüklü değilse, bunları el ile yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="00a87-1019">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="00a87-1020">**GELIŞTIRME araçları** dikey penceresinde **Uzantılar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1020">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="00a87-1021">**ASP.NET Core uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1021">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="00a87-1022">Uzantılar yüklü değilse, **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1022">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="00a87-1023">Listeden **ASP.NET Core uzantılarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1023">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="00a87-1024">Yasal koşulları kabul etmek için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1024">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="00a87-1025">**Uzantı Ekle** dikey penceresinde **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1025">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="00a87-1026">Bilgilendirici bir açılan ileti, uzantıların başarıyla yüklenip yüklenmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1026">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="00a87-1027">Stdout günlüğü etkinleştirilmemişse, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-1027">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="00a87-1028">Azure portal, **GELIŞTIRME araçları** alanındaki **Gelişmiş Araçlar** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1028">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="00a87-1029">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1029">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="00a87-1030">Kudu konsolu yeni bir tarayıcı sekmesi veya penceresinde açılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-1030">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="00a87-1031">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **hata ayıklama konsolu 'nu** açın ve **cmd**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1031">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="00a87-1032">Klasörü > **Wwwroot** **yoluna açın** ve listenin altındaki *Web. config* dosyasını açığa çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1032">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="00a87-1033">*Web. config* dosyasının yanındaki kurşun kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1033">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="00a87-1034">**StdoutLogEnabled** olarak `true` ayarlayın ve **stdoutLogFile** yolunu şu şekilde değiştirin: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="00a87-1034">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="00a87-1035">Güncelleştirilmiş *Web. config* dosyasını kaydetmek için **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1035">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="00a87-1036">Tanılama günlüğünü etkinleştirmek için ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="00a87-1036">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="00a87-1037">Azure portal **tanılama günlükleri** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1037">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="00a87-1038">**Uygulama günlüğü (dosya sistemi)** ve **ayrıntılı hata iletileri**için **bir anahtar seçin** .</span><span class="sxs-lookup"><span data-stu-id="00a87-1038">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="00a87-1039">Dikey pencerenin üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1039">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="00a87-1040">Başarısız istek izlemeyi, başarısız Istek olayı arabelleğe alma (FREB) günlüğü olarak da bilinen bir şekilde eklemek için **,** **başarısız istek izleme**anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1040">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="00a87-1041">Portalda **tanılama günlükleri** dikey penceresinde hemen listelenen **günlük akışı** dikey penceresini seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1041">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="00a87-1042">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-1042">Make a request to the app.</span></span>
1. <span data-ttu-id="00a87-1043">Günlük akışı verileri içinde hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1043">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="00a87-1044">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bıraktığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="00a87-1044">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="00a87-1045">Başarısız istek izleme günlüklerini görüntülemek için (FREB günlükleri):</span><span class="sxs-lookup"><span data-stu-id="00a87-1045">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="00a87-1046">Azure portal **sorunları Tanıla ve çöz** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1046">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="00a87-1047">Kenar çubuğunun **Destek Araçları** alanından **başarısız istek izleme günlüklerini** seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1047">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="00a87-1048">[Azure App Service konusundaki Web uygulamaları için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure 'Daki Web Apps Için uygulama performansı SSS](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) bölümündeki başarısız istek izlemeleri bölümüne bakın: daha fazla bilgi için nasıl yaparım? başarısız istek izlemeyi açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1048">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="00a87-1049">Daha fazla bilgi için bkz. [Azure App Service Web Apps için tanılama günlüğünü etkinleştirme](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="00a87-1049">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-1050">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1050">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-1051">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-1051">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="00a87-1052">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1052">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-1053">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-1053">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="00a87-1054">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-1054">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="00a87-1055">Uygulama olay günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-1055">Application Event Log (IIS)</span></span>

<span data-ttu-id="00a87-1056">Uygulama olay günlüğüne erişin:</span><span class="sxs-lookup"><span data-stu-id="00a87-1056">Access the Application Event Log:</span></span>

1. <span data-ttu-id="00a87-1057">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1057">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="00a87-1058">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1058">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="00a87-1059">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1059">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="00a87-1060">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1060">Search for errors associated with the failing app.</span></span> <span data-ttu-id="00a87-1061">Hataların, *kaynak* sütununda *IIS aspnetcore modülünün* veya *IIS Express aspnetcore modülünün* bir değeri vardır.</span><span class="sxs-lookup"><span data-stu-id="00a87-1061">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="00a87-1062">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="00a87-1062">Run the app at a command prompt</span></span>

<span data-ttu-id="00a87-1063">Birçok başlatma hatası, uygulama olay günlüğünde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="00a87-1063">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="00a87-1064">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="00a87-1064">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="00a87-1065">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="00a87-1065">Framework-dependent deployment</span></span>

<span data-ttu-id="00a87-1066">Uygulama, [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-1066">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="00a87-1067">Bir komut isteminde, dağıtım klasörüne gidin ve uygulamanın derlemesini *DotNet. exe*ile yürüterek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1067">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="00a87-1068">Aşağıdaki komutta, assembly_name> için \<uygulama derlemesinin adını yerine koyun:. `dotnet .\<assembly_name>.dll`</span><span class="sxs-lookup"><span data-stu-id="00a87-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="00a87-1069">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="00a87-1070">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="00a87-1071">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="00a87-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="00a87-1072">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="00a87-1073">Kendi kendine kapsanan dağıtım</span><span class="sxs-lookup"><span data-stu-id="00a87-1073">Self-contained deployment</span></span>

<span data-ttu-id="00a87-1074">Uygulama, [kendinden bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="00a87-1074">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="00a87-1075">Bir komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir dosyasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1075">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="00a87-1076">Aşağıdaki komutta, assembly_name> için \<uygulama derlemesinin adını yerine koyun:. `<assembly_name>.exe`</span><span class="sxs-lookup"><span data-stu-id="00a87-1076">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="00a87-1077">Uygulamanın konsol çıktısı, herhangi bir hata göstererek konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-1077">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="00a87-1078">Uygulama için bir istek yaparken hatalar oluşursa, Kestrel dinlediği ana bilgisayara ve bağlantı noktasına bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-1078">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="00a87-1079">Varsayılan konak ve gönderi kullanarak bir istek yapın `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="00a87-1079">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="00a87-1080">Uygulama normal olarak Kestrel uç nokta adresinde yanıt verirse, sorun büyük olasılıkla barındırma yapılandırmasıyla ilgili ve uygulamanın içinde daha az olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1080">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="00a87-1081">ASP.NET Core Module stdout günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-1081">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="00a87-1082">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="00a87-1082">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="00a87-1083">Barındırma sistemindeki sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1083">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="00a87-1084">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-1084">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="00a87-1085">MSBuild 'in dağıtımdaki *Günlükler* klasörünü otomatik olarak oluşturmak üzere nasıl etkinleştirileceği hakkında yönergeler için, bkz. [Dizin yapısı](xref:host-and-deploy/directory-structure) konusu.</span><span class="sxs-lookup"><span data-stu-id="00a87-1085">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="00a87-1086">*Web. config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1086">Edit the *web.config* file.</span></span> <span data-ttu-id="00a87-1087">**StdoutLogEnabled** olarak `true` ayarlayın ve **stdoutLogFile** yolunu *Günlükler* klasörünü işaret etmek üzere değiştirin (örneğin, `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="00a87-1087">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="00a87-1088">`stdout`yolunda günlük dosyası adı ön eki bulunur.</span><span class="sxs-lookup"><span data-stu-id="00a87-1088">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="00a87-1089">Günlük oluşturulduğunda zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1089">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="00a87-1090">Dosya `stdout` adı ön eki olarak kullanıldığında, tipik bir günlük dosyası *stdout_20180205184032_5412. log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="00a87-1090">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="00a87-1091">Uygulama havuzunuzun kimliğinin *Günlükler* klasörü için yazma izinlerine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="00a87-1091">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="00a87-1092">Güncelleştirilmiş *Web. config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1092">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="00a87-1093">Uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00a87-1093">Make a request to the app.</span></span>
1. <span data-ttu-id="00a87-1094">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1094">Navigate to the *logs* folder.</span></span> <span data-ttu-id="00a87-1095">En son stdout günlüğünü bulup açın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1095">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="00a87-1096">Günlüğü hatalara karşı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1096">Study the log for errors.</span></span>

<span data-ttu-id="00a87-1097">Sorun giderme tamamlandığında stdout günlüğünü devre dışı bırak:</span><span class="sxs-lookup"><span data-stu-id="00a87-1097">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="00a87-1098">*Web. config* dosyasını düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1098">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="00a87-1099">**StdoutLogEnabled** olarak `false`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1099">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="00a87-1100">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1100">Save the file.</span></span>

<span data-ttu-id="00a87-1101">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="00a87-1101">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-1102">Stdout günlüğünü devre dışı bırakma başarısız olması, uygulama veya sunucu hatasına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1102">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="00a87-1103">Günlük dosyası boyutu veya oluşturulan günlük dosyası sayısı için sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="00a87-1103">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="00a87-1104">ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1104">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="00a87-1105">Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="00a87-1105">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="00a87-1106">Geliştirici özel durum sayfasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="00a87-1106">Enable the Developer Exception Page</span></span>

<span data-ttu-id="00a87-1107">`ASPNETCORE_ENVIRONMENT` Ortam değişkeni, uygulamayı geliştirme ortamında çalıştırmak için [Web. config dosyasına eklenebilir](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) .</span><span class="sxs-lookup"><span data-stu-id="00a87-1107">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="00a87-1108">Ortam, ana bilgisayar Oluşturucu `UseEnvironment` 'da uygulama başlangıcında geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında [Geliştirici özel durum sayfasının](xref:fundamentals/error-handling) görünmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1108">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="00a87-1109">Ortam değişkeninin `ASPNETCORE_ENVIRONMENT` ayarlanması yalnızca Internet 'e açık olmayan hazırlama ve test etme sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1109">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="00a87-1110">Sorun giderme işleminden sonra *Web. config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1110">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="00a87-1111">*Web. config*'de ortam değişkenlerini ayarlama hakkında daha fazla bilgi Için, [Aspnetcore 'un EnvironmentVariables alt öğesi](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1111">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="00a87-1112">Uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="00a87-1112">Obtain data from an app</span></span>

<span data-ttu-id="00a87-1113">Bir uygulamanın isteklere yanıt verme yeteneği varsa, Terminal satır içi ara yazılımı kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1113">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="00a87-1114">Daha fazla bilgi ve örnek kod için bkz <xref:test/troubleshoot#obtain-data-from-an-app>..</span><span class="sxs-lookup"><span data-stu-id="00a87-1114">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="00a87-1115">Yavaş veya askıda olan uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="00a87-1115">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="00a87-1116">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1116">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="00a87-1117">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="00a87-1117">App crashes or encounters an exception</span></span>

<span data-ttu-id="00a87-1118">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="00a87-1118">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="00a87-1119">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="00a87-1119">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="00a87-1120">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00a87-1120">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="00a87-1121">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-1121">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="00a87-1122">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-1122">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="00a87-1123">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-1123">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="00a87-1124">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00a87-1124">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="00a87-1125">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-1125">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="00a87-1126">Uygulama, [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *W3wp. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-1126">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="00a87-1127">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *DotNet. exe*için betiği çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="00a87-1127">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="00a87-1128">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1128">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="00a87-1129">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="00a87-1129">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="00a87-1130">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="00a87-1130">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="00a87-1131">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="00a87-1131">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="00a87-1132">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="00a87-1132">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="00a87-1133">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="00a87-1133">Analyze the dump</span></span>

<span data-ttu-id="00a87-1134">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1134">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="00a87-1135">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="00a87-1135">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="00a87-1136">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="00a87-1136">Clear package caches</span></span>

<span data-ttu-id="00a87-1137">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1137">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="00a87-1138">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1138">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="00a87-1139">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="00a87-1139">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="00a87-1140">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1140">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="00a87-1141">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1141">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="00a87-1142">Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut `nuget locals all -clear`yürütülebilir.</span><span class="sxs-lookup"><span data-stu-id="00a87-1142">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="00a87-1143">*NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00a87-1143">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="00a87-1144">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1144">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="00a87-1145">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="00a87-1145">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00a87-1146">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="00a87-1146">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="00a87-1147">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="00a87-1147">Azure documentation</span></span>

* [<span data-ttu-id="00a87-1148">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="00a87-1148">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="00a87-1149">Visual Studio 'Yu kullanarak Azure App Service Web uygulamasının sorunlarını giderme bölümünde uzaktan hata ayıklama Web Apps bölümü</span><span class="sxs-lookup"><span data-stu-id="00a87-1149">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="00a87-1150">Azure App Service tanılamada genel bakış</span><span class="sxs-lookup"><span data-stu-id="00a87-1150">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="00a87-1151">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="00a87-1151">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="00a87-1152">Visual Studio 'Yu kullanarak Azure App Service bir Web uygulamasının sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-1152">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="00a87-1153">Azure Web uygulamalarınızda "502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" HTTP hatalarında sorun giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-1153">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="00a87-1154">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="00a87-1154">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="00a87-1155">Azure 'da Web Apps için uygulama performansı SSS</span><span class="sxs-lookup"><span data-stu-id="00a87-1155">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="00a87-1156">Azure Web uygulaması korumalı alanı (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="00a87-1156">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="00a87-1157">Azure Cuma: Azure App Service tanılama ve sorun giderme deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="00a87-1157">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="00a87-1158">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="00a87-1158">Visual Studio documentation</span></span>

* [<span data-ttu-id="00a87-1159">Visual Studio 2017 ' de Azure 'da IIS 'de uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00a87-1159">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="00a87-1160">Visual Studio 2017 ' de uzak IIS bilgisayarında uzaktan hata ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00a87-1160">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="00a87-1161">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="00a87-1161">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="00a87-1162">Visual Studio Code belgeleri</span><span class="sxs-lookup"><span data-stu-id="00a87-1162">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="00a87-1163">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="00a87-1163">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
