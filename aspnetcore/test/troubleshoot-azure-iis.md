---
title: Azure Uygulama Hizmeti ve IIS'de ASP.NET Core sorun giderme
author: rick-anderson
description: Azure Uygulama Hizmeti ve Internet Bilgi Hizmetleri (IIS) dağıtımlarıyla ilgili sorunları ASP.NET Temel uygulamalarla ilgili sorunları nasıl tanılayabileceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 671f68da2ea261cb8ae32a9d5ef875217859054d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655332"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="5abf5-103">Azure Uygulama Hizmeti ve IIS'de ASP.NET Core sorun giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="5abf5-104">Yazar: [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="5abf5-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5abf5-105">Bu makalede, bir uygulama Azure Uygulama Hizmeti veya IIS'ye dağıtıldığında hataların nasıl tanılanacağına ilişkin yaygın uygulama başlangıç hataları ve yönergeler hakkında bilgiler verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="5abf5-106">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="5abf5-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="5abf5-107">Ortak başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="5abf5-108">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="5abf5-109">Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="5abf5-110">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="5abf5-111">IIS'ye dağıtılan veya IIS Express'te yerel olarak çalışan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="5abf5-112">Kılavuz, hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="5abf5-113">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="5abf5-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="5abf5-114">Büyük yükseltmeler yaparken veya paket sürümlerini değiştirirken tutarsız paketler bir uygulamayı kırdığında ne yapması gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="5abf5-115">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5abf5-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="5abf5-116">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="5abf5-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="5abf5-117">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="5abf5-117">App startup errors</span></span>

<span data-ttu-id="5abf5-118">Visual Studio'da, ASP.NET Core projesi hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırma için varsayılandır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="5abf5-119">A *502.5 - İşlem Hatası* veya *500.30 -* Yerel hata ayıklama zaman ortaya çıkan Başlat Hatası bu konuda ki tavsiye kullanılarak teşhis edilebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="5abf5-120">403.14 Yasak</span><span class="sxs-lookup"><span data-stu-id="5abf5-120">403.14 Forbidden</span></span>

<span data-ttu-id="5abf5-121">Uygulama başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-121">The app fails to start.</span></span> <span data-ttu-id="5abf5-122">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="5abf5-123">Hata genellikle aşağıdaki senaryolardan herhangi birini içeren barındırma sisteminde ki bozuk dağıtımdan kaynaklanır:</span><span class="sxs-lookup"><span data-stu-id="5abf5-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="5abf5-124">Uygulama barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="5abf5-125">Dağıtım işlemi, uygulamanın tüm dosya ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="5abf5-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="5abf5-126">*web.config* dosyası dağıtımda eksik veya *web.config* dosyası içeriği yanlış biçimlendirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="5abf5-127">Aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-127">Perform the following steps:</span></span>

1. <span data-ttu-id="5abf5-128">Barındırma sistemindeki dağıtım klasöründeki tüm dosya ve klasörleri silin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="5abf5-129">Visual Studio, PowerShell veya manuel dağıtım gibi normal dağıtım yönteminizi kullanarak uygulamanın *yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="5abf5-130">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="5abf5-131">Azure Uygulama Hizmeti'nde barındırma yaparken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="5abf5-132">Uygulama IIS tarafından barındırıldığında, uygulamanın **IIS Yöneticisi'nin**Temel **Ayarları'nda**gösterilen IIS **Fiziksel yoluna** dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="5abf5-133">Barındırma sistemindeki dağıtımı projenin *yayımlama* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtılmış olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="5abf5-134">Yayınlanan bir ASP.NET Core uygulamasının düzeni hakkında <xref:host-and-deploy/directory-structure>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="5abf5-135">*web.config* dosyasında daha fazla <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="5abf5-136">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="5abf5-136">500 Internal Server Error</span></span>

<span data-ttu-id="5abf5-137">Uygulama başlar, ancak bir hata sunucunun isteği yerine getirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="5abf5-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="5abf5-138">Bu hata, başlangıç sırasında veya yanıt oluştururken uygulamanın kodu içinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="5abf5-139">Yanıt hiçbir içerik içermeyebilir veya yanıt tarayıcıda *500 Dahili Sunucu Hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="5abf5-140">Uygulama Olay Günlüğü genellikle uygulamanın normal olarak başladığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="5abf5-141">Sunucunun bakış açısından, bu doğru.</span><span class="sxs-lookup"><span data-stu-id="5abf5-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="5abf5-142">Uygulama başladı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="5abf5-143">Uygulamayı sunucudaki bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Çekirdek Modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="5abf5-144">500.0 İşlem Içi İşleyici Yük Arızası</span><span class="sxs-lookup"><span data-stu-id="5abf5-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="5abf5-145">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-145">The worker process fails.</span></span> <span data-ttu-id="5abf5-146">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-146">The app doesn't start.</span></span>

<span data-ttu-id="5abf5-147">[Core Module bileşenleriASP.NET](xref:host-and-deploy/aspnet-core-module) yüklemede bilinmeyen bir hata oluştu.</span><span class="sxs-lookup"><span data-stu-id="5abf5-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="5abf5-148">Aşağıdaki eylemlerden birini uygulayın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-148">Take one of the following actions:</span></span>

* <span data-ttu-id="5abf5-149">[Microsoft Desteği'ne](https://support.microsoft.com/oas/default.aspx?prid=15832) başvurun **(Geliştirici Araçları'nı** seçin ve **ardından ASP.NET Core' u**seçin).</span><span class="sxs-lookup"><span data-stu-id="5abf5-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="5abf5-150">Stack Taşma hakkında bir soru sorun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="5abf5-151">[GitHub depomuzda](https://github.com/dotnet/AspNetCore)bir sorun dosyala.</span><span class="sxs-lookup"><span data-stu-id="5abf5-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="5abf5-152">500.30 İşlem Içi Başlatma Hatası</span><span class="sxs-lookup"><span data-stu-id="5abf5-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="5abf5-153">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-153">The worker process fails.</span></span> <span data-ttu-id="5abf5-154">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-154">The app doesn't start.</span></span>

<span data-ttu-id="5abf5-155">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR'yi başlatmaya çalışır, ancak başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="5abf5-156">İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="5abf5-157">Yaygın arıza koşulları:</span><span class="sxs-lookup"><span data-stu-id="5abf5-157">Common failure conditions:</span></span>

* <span data-ttu-id="5abf5-158">Uygulama, ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="5abf5-159">hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="5abf5-160">Azure Key Vault'u kullanarak, Anahtar Kasası'na izin verilmemesi.</span><span class="sxs-lookup"><span data-stu-id="5abf5-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="5abf5-161">Doğru izinlerin verildiğinden emin olmak için hedeflenen Anahtar Kasası'ndaki erişim ilkelerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="5abf5-162">500.31 ANCM Yerel Bağımlılıkları Bulamayı Başaramadı</span><span class="sxs-lookup"><span data-stu-id="5abf5-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="5abf5-163">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-163">The worker process fails.</span></span> <span data-ttu-id="5abf5-164">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-164">The app doesn't start.</span></span>

<span data-ttu-id="5abf5-165">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) .NET Core çalışma süresini başlatmaya çalışır, ancak başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="5abf5-166">Bu başlangıç hatasının en yaygın `Microsoft.NETCore.App` nedeni, çalışma `Microsoft.AspNetCore.App` zamanının yüklenmemesidir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="5abf5-167">Uygulama Core 3.0 ASP.NET hedeflemek üzere dağıtılırsa ve bu sürüm makinede yoksa, bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="5abf5-168">Örnek bir hata iletisi aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="5abf5-169">Hata iletisi, yüklenen tüm .NET Core sürümlerini ve uygulama tarafından istenen sürümü listeler.</span><span class="sxs-lookup"><span data-stu-id="5abf5-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="5abf5-170">Bu hatayı düzeltmek için aşağıdakileri de:</span><span class="sxs-lookup"><span data-stu-id="5abf5-170">To fix this error, either:</span></span>

* <span data-ttu-id="5abf5-171">Makineye .NET Core'un uygun sürümünü yükleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="5abf5-172">Uygulamayı makinede bulunan .NET Core sürümünü hedef almak için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="5abf5-173">Uygulamayı [bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)olarak yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="5abf5-174">Geliştirme aşamasında çalışırken `ASPNETCORE_ENVIRONMENT` (ortam değişkeni `Development`ayarlanır), belirli bir hata HTTP yanıtına yazılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="5abf5-175">İşlem başlatma hatasının nedeni, Uygulama Olayı Günlüğü'nde de bulunur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="5abf5-176">500.32 ANCM dll Yüklemek için başarısız oldu</span><span class="sxs-lookup"><span data-stu-id="5abf5-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="5abf5-177">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-177">The worker process fails.</span></span> <span data-ttu-id="5abf5-178">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-178">The app doesn't start.</span></span>

<span data-ttu-id="5abf5-179">Bu hatanın en yaygın nedeni, uygulamanın uyumsuz bir işlemci mimarisi için yayımlanmış olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="5abf5-180">Alt işlem 32 bit uygulama olarak çalışıyorsa ve uygulama 64 bit hedef olarak yayımlanmışsa, bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="5abf5-181">Bu hatayı düzeltmek için aşağıdakileri de:</span><span class="sxs-lookup"><span data-stu-id="5abf5-181">To fix this error, either:</span></span>

* <span data-ttu-id="5abf5-182">Alt işlemle aynı işlemci mimarisi için uygulamayı yeniden yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="5abf5-183">Uygulamayı [çerçeveye bağımlı dağıtım](/dotnet/core/deploying/#framework-dependent-executables-fde)olarak yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="5abf5-184">500.33 ANCM İstek Işleyici Yük Arızası</span><span class="sxs-lookup"><span data-stu-id="5abf5-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="5abf5-185">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-185">The worker process fails.</span></span> <span data-ttu-id="5abf5-186">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-186">The app doesn't start.</span></span>

<span data-ttu-id="5abf5-187">Uygulama çerçeveye `Microsoft.AspNetCore.App` atıfta bulunmuyordu.</span><span class="sxs-lookup"><span data-stu-id="5abf5-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="5abf5-188">Yalnızca çerçeveyi `Microsoft.AspNetCore.App` hedefleyen uygulamalar ASP.NET [Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)tarafından barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="5abf5-189">Bu hatayı düzeltmek için, uygulamanın `Microsoft.AspNetCore.App` çerçeveyi hedeflediğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="5abf5-190">Uygulama `.runtimeconfig.json` tarafından hedeflenen çerçeveyi doğrulamak için kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="5abf5-191">500.34 ANCM Karma Hosting Modelleri Desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="5abf5-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="5abf5-192">Alt işlem, aynı işlemde hem bir işlem içi uygulama hem de işlem dışı bir uygulamayı çalıştıramaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="5abf5-193">Bu hatayı düzeltmek için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="5abf5-194">500.35 AYNı İşlemde ANCM Çoklu İşlem Uygulamaları</span><span class="sxs-lookup"><span data-stu-id="5abf5-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="5abf5-195">Alt işlem aynı işlemde birden çok işlem içi uygulama çalıştıramaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="5abf5-196">Bu hatayı düzeltmek için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="5abf5-197">500.36 ANCM İşlem Dışı İşleyici Yük Arızası</span><span class="sxs-lookup"><span data-stu-id="5abf5-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="5abf5-198">İşlem dışı istek işleyicisi, *aspnetcorev2_outofprocess.dll,* *aspnetcorev2.dll* dosyasının yanında değildir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="5abf5-199">Bu, [ASP.NET Çekirdek Modülü'nün](xref:host-and-deploy/aspnet-core-module)bozuk bir yüklemesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="5abf5-200">Bu hatayı gidermek için [.NET Core Hosting Paketinin](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS için) veya Visual Studio (IIS Express için) kurulumunu onarın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="5abf5-201">500.37 ANCM Başlangıç Süresi İçinde Başlayamamış</span><span class="sxs-lookup"><span data-stu-id="5abf5-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="5abf5-202">ANCM, önlenen başlangıç süresi içinde başlayamadı.</span><span class="sxs-lookup"><span data-stu-id="5abf5-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="5abf5-203">Varsayılan olarak, zaman ası 120 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="5abf5-204">Bu hata, aynı makinede çok sayıda uygulama başlatırken oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="5abf5-205">Başlangıç sırasında sunucuda CPU/Bellek kullanım artışlarını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="5abf5-206">Birden çok uygulamanın başlangıç işlemini sendemeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="5abf5-207">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="5abf5-207">502.5 Process Failure</span></span>

<span data-ttu-id="5abf5-208">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-208">The worker process fails.</span></span> <span data-ttu-id="5abf5-209">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-209">The app doesn't start.</span></span>

<span data-ttu-id="5abf5-210">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) alt işlemi başlatmaya çalışır, ancak başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-210">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="5abf5-211">İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-211">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="5abf5-212">Yaygın bir hata koşulu, uygulamanın ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmış olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-212">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="5abf5-213">hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-213">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="5abf5-214">*Paylaşılan çerçeve,* makineye yüklenen *.dll* ve meta package gibi `Microsoft.AspNetCore.App`bir meta paketle başvurulan derlemeler kümesidir .</span><span class="sxs-lookup"><span data-stu-id="5abf5-214">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="5abf5-215">Metapackage başvurusu en az gerekli sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-215">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="5abf5-216">Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-216">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="5abf5-217">*502.5 İşlem Hatası* hatası sayfası, bir barındırma veya uygulama yanlış yapılandırması alt işlemin başarısız lığa neden olduğunda döndürülür:</span><span class="sxs-lookup"><span data-stu-id="5abf5-217">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="5abf5-218">Uygulama başlatılamamış (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="5abf5-218">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="5abf5-219">Uygulamanın derlemesi *(.dll)* yüklenemediği için uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="5abf5-219">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="5abf5-220">Bu hata, yayınlanan uygulama ile w3wp/iisexpress işlemi arasında bir bitness uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-220">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="5abf5-221">Uygulama havuzunun 32 bit ayarı doğru olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-221">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="5abf5-222">IIS Manager'ın **Uygulama Havuzları'ndaki**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-222">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="5abf5-223">**Eylemler** panelinde **Uygulama Havuzu** altında Gelişmiş **Ayarlar'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-223">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="5abf5-224">**32 Bit Uygulamaları Etkinleştir'i**Ayarlayın :</span><span class="sxs-lookup"><span data-stu-id="5abf5-224">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="5abf5-225">32 bit (x86) bir uygulama dağıtıyorsanız, `True`değeri .</span><span class="sxs-lookup"><span data-stu-id="5abf5-225">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="5abf5-226">64 bit (x64) bir uygulama dağıtıyorsanız, `False`değeri .</span><span class="sxs-lookup"><span data-stu-id="5abf5-226">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="5abf5-227">Proje dosyasındaki BIR `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bitliği arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-227">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="5abf5-228">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="5abf5-228">Connection reset</span></span>

<span data-ttu-id="5abf5-229">Üstbilgi gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Dahili Sunucu Hatası** göndermesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-229">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="5abf5-230">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirme sırasında bir hata oluşur oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-230">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="5abf5-231">Bu hata türü istemcide *bağlantı sıfırlama* hatası olarak görünür.</span><span class="sxs-lookup"><span data-stu-id="5abf5-231">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="5abf5-232">[Uygulama günlüğe kaydetme,](xref:fundamentals/logging/index) bu tür hataları gidermede yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-232">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="5abf5-233">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="5abf5-233">Default startup limits</span></span>

<span data-ttu-id="5abf5-234">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) 120 saniyelik varsayılan *başlangıçTimeLimit* ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-234">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="5abf5-235">Varsayılan değerde bırakıldığında, bir uygulamanın bir işlem hatası günlüğe kaydetmesi iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-235">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="5abf5-236">Modülü yapılandırma hakkında daha fazla bilgi için [aspNetCore öğesinin Öznitelikleri'ne](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-236">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="5abf5-237">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-237">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="5abf5-238">Uygulama Etkinlik Günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-238">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="5abf5-239">Uygulama Olay Günlüğü'ne erişmek için Azure portalındaki **Tanılama ve sorunları çözme** sorununu kullanın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-239">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="5abf5-240">Azure portalında Uygulamayı Uygulama **Hizmetleri'nde**açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-240">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="5abf5-241">**Sorunları tanılama ve çözme** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-241">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="5abf5-242">**Tanılama Araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-242">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="5abf5-243">**Destek Araçları** **altında, Uygulama Etkinlikleri** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-243">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="5abf5-244">**Kaynak** sütundaki *IIS AspNetCoreModule* veya *IIS AspNetCoreModule V2* girişi tarafından sağlanan en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-244">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="5abf5-245">**Tanılama ve sorunları çözmenin** alternatifi, Uygulama Olay Günlüğü dosyasını doğrudan [Kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-245">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="5abf5-246">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-246">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5abf5-247">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-247">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-248">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-248">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-249">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-249">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-250">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-250">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="5abf5-251">*eventlog.xml* dosyasının yanındaki kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-251">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="5abf5-252">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-252">Examine the log.</span></span> <span data-ttu-id="5abf5-253">En son olayları görmek için günlüğün altına gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-253">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="5abf5-254">Uygulamayı Kudu konsolunda çalıştırın</span><span class="sxs-lookup"><span data-stu-id="5abf5-254">Run the app in the Kudu console</span></span>

<span data-ttu-id="5abf5-255">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="5abf5-255">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="5abf5-256">Hatayı bulmak için uygulamayı [Kudu](https://github.com/projectkudu/kudu/wiki) Uzaktan Yürütme Konsolu'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5abf5-256">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="5abf5-257">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5abf5-258">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-259">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-260">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="5abf5-261">32 bit (x86) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="5abf5-261">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="5abf5-262">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="5abf5-262">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="5abf5-263">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-263">Run the app:</span></span>
   * <span data-ttu-id="5abf5-264">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-264">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="5abf5-265">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-265">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="5abf5-266">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-266">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="5abf5-267">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="5abf5-267">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="5abf5-268">*Core {VERSION} (x86) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="5abf5-268">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="5abf5-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="5abf5-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="5abf5-270">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5abf5-270">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="5abf5-271">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-271">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="5abf5-272">64 bit (x64) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="5abf5-272">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="5abf5-273">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="5abf5-273">**Current release**</span></span>

* <span data-ttu-id="5abf5-274">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-274">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="5abf5-275">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5abf5-275">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="5abf5-276">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="5abf5-277">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="5abf5-277">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="5abf5-278">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-278">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="5abf5-279">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="5abf5-279">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="5abf5-280">*Core {VERSION} (x64) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="5abf5-280">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="5abf5-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="5abf5-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="5abf5-282">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5abf5-282">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="5abf5-283">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-283">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="5abf5-284">ASP.NET Çekirdek Modül stdout günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-284">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="5abf5-285">ASP.NET Çekirdek Modülü stdout günlüğü genellikle Uygulama Olay Günlüğü'nde bulunmayan yararlı hata iletileri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="5abf5-285">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="5abf5-286">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-286">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5abf5-287">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-287">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="5abf5-288">**PROBLEM KATEGORİsİ SEÇ'in** **altında, Web Uygulaması Aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-288">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="5abf5-289">**Önerilen Çözümler** > Altında **Stdout Log Redirection etkinleştirin**, **Web.Config'i yeniden etkinleştirmek için Kudu Konsolu'nu açın**düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-289">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="5abf5-290">Kudu **Tanı Konsolu,** yol **sitesi** > **wwwroot**klasörleri açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-290">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="5abf5-291">Listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-291">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="5abf5-292">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-292">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-293">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-293">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="5abf5-294">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-294">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-295">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-295">Make a request to the app.</span></span>
1. <span data-ttu-id="5abf5-296">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="5abf5-296">Return to the Azure portal.</span></span> <span data-ttu-id="5abf5-297">**GELİşTİrME ARAÇLARI** alanında **Gelişmiş Araçlar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-297">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="5abf5-298">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-298">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-299">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-299">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-300">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-300">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-301">Günlük **Dosyaları** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-301">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="5abf5-302">**Değiştirilen** sütunu inceleyin ve en son değişiklik tarihiyle birlikte stdout günlüğünü deletmek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-302">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="5abf5-303">Günlük dosyası açıldığında, hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-303">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="5abf5-304">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-304">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="5abf5-305">Kudu **Tanı Konsolu,** *web.config* dosyasını ortaya çıkarmak için yol **sitesi** > **wwwroot** dönün.</span><span class="sxs-lookup"><span data-stu-id="5abf5-305">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="5abf5-306">Kalem simgesini seçerek **web.config** dosyasını yeniden açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-306">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="5abf5-307">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="5abf5-307">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="5abf5-308">Dosyayı kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-308">Select **Save** to save the file.</span></span>

<span data-ttu-id="5abf5-309">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="5abf5-309">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-310">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-310">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-311">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-311">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="5abf5-312">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlük kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-312">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="5abf5-313">Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-313">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-314">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-314">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="5abf5-315">ASP.NET Çekirdek Modül hata ayıklama günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-315">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="5abf5-316">ASP.NET Çekirdek Modülü hata ayıklama günlüğü, ASP.NET Çekirdek Modülünden ek, daha derin günlüğe kaydetme sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-316">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="5abf5-317">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-317">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5abf5-318">Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-318">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="5abf5-319">Gelişmiş bir tanı günlüğü için uygulamayı yapılandırmak için [Gelişmiş tanı günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-319">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="5abf5-320">Uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-320">Redeploy the app.</span></span>
   * <span data-ttu-id="5abf5-321">Kudu `<handlerSettings>` konsolu kullanarak canlı uygulamanın *web.config* dosyasına [Gelişmiş tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilenleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-321">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="5abf5-322">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-322">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5abf5-323">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-323">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-324">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-324">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="5abf5-325">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-325">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="5abf5-326">Klasörleri yol **sitesine** > aç**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="5abf5-326">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="5abf5-327">Kalem düğmesini seçerek *web.config* dosyasını düzenleme.</span><span class="sxs-lookup"><span data-stu-id="5abf5-327">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="5abf5-328">Gelişmiş `<handlerSettings>` [tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi bölümü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-328">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="5abf5-329">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-329">Select the **Save** button.</span></span>
1. <span data-ttu-id="5abf5-330">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5abf5-331">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-332">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-332">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-333">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-334">Klasörleri yol **sitesine** > aç**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="5abf5-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="5abf5-335">*aspnetcore-debug.log* dosyası için bir yol sağlamadıysanız, dosya listede görünür.</span><span class="sxs-lookup"><span data-stu-id="5abf5-335">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="5abf5-336">Bir yol sağladıysanız, günlük dosyasının konumuna gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-336">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="5abf5-337">Dosya adının yanındaki kalem düğmesiyle günlük dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-337">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="5abf5-338">Sorun giderme tamamlandığında hata ayıklama günlemasını devre dışı kındırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-338">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="5abf5-339">Gelişmiş hata ayıklama günlüğünü devre dışı kalmak için aşağıdakilerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-339">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="5abf5-340">`<handlerSettings>` *Web.config* dosyasını yerel olarak kaldırın ve uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-340">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="5abf5-341">*Web.config* dosyasını ve bölümü kaldırmak için Kudu konsoluna `<handlerSettings>` bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-341">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="5abf5-342">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-342">Save the file.</span></span>

<span data-ttu-id="5abf5-343">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="5abf5-343">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-344">Hata ayıklama günlüğünün devre dışı edilmemesi uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-344">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-345">Günlük dosyası boyutunda bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-345">There's no limit on log file size.</span></span> <span data-ttu-id="5abf5-346">Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğe kaydetmeyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-346">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="5abf5-347">Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-347">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-348">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-348">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="5abf5-349">Yavaş veya asma uygulaması (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-349">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="5abf5-350">Bir uygulama yavaş yanıt verdiğinde veya bir isteğe bağlı kaldığında aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-350">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="5abf5-351">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-351">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="5abf5-352">Azure Web Uygulamasında Aralıklı Özel Durum sorunları veya performans sorunları için Döküm'ü yakalamak için Kilitlenme Tanılayıcı Site Uzantısı'nı kullanma</span><span class="sxs-lookup"><span data-stu-id="5abf5-352">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="5abf5-353">İzleme bıçakları</span><span class="sxs-lookup"><span data-stu-id="5abf5-353">Monitoring blades</span></span>

<span data-ttu-id="5abf5-354">İzleme bıçakları, konunun daha önce açıklanan yöntemlerine alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-354">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="5abf5-355">Bu bıçaklar 500 seri hataları teşhis etmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-355">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="5abf5-356">ASP.NET Çekirdek Uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-356">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="5abf5-357">Uzantılar yüklenmiyorsa, bunları el ile yükleyin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-357">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="5abf5-358">**GELİşİm ARAÇLARI** bıçak bölümünde **Uzantılar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-358">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="5abf5-359">**ASP.NET Çekirdek Uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-359">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="5abf5-360">Uzantılar yüklenmiyorsa **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-360">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="5abf5-361">Listeden **ASP.NET Çekirdek Uzantıları'nı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-361">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="5abf5-362">Yasal koşulları kabul etmek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-362">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="5abf5-363">**Ekle uzantı** bıçağında **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-363">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="5abf5-364">Bilgilendirimiolan bir açılır ileti, uzantıların başarıyla ne zaman yüklenir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-364">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="5abf5-365">Stdout günlüğe kaydetme etkin değilse, aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-365">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="5abf5-366">Azure portalında, **GELIŞTIRME ARAÇLARı** alanındaki **Gelişmiş Araçlar** bıçak larını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-366">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="5abf5-367">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-367">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-368">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-368">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-369">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-369">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-370">Klasörleri yol **sitesi** > **wwwroot'a** açın ve listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-370">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="5abf5-371">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-371">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-372">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-372">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="5abf5-373">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-373">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="5abf5-374">Tanısal günlüğü etkinleştirmeye devam edin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-374">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="5abf5-375">Azure portalında **Tanılama günlükleri** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-375">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="5abf5-376">**Uygulama Günlüğe Kaydetme (Filesystem)** ve **Ayrıntılı hata iletileri**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-376">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="5abf5-377">Bıçağın üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-377">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="5abf5-378">Başarısız İstek Olay Arabelleği (FREB) günlüğe kaydetme olarak da bilinen başarısız istek izlemesini eklemek için, **Başarısız istek izleme**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-378">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="5abf5-379">Portaldaki **Teşhis günlükleri** bıçağının hemen altında listelenen **Log akış** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-379">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="5abf5-380">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-380">Make a request to the app.</span></span>
1. <span data-ttu-id="5abf5-381">Günlük akışı verileri içinde, hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-381">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="5abf5-382">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı bettiğinizden emin olun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-382">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="5abf5-383">Başarısız istek izleme günlüklerini (FREB günlükleri) görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-383">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="5abf5-384">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-384">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="5abf5-385">Kenar çubuğunun **DESTEK ARAÇLARI** alanından **Başarısız İstek İzleme Günlükleri'ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-385">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="5abf5-386">Azure Uygulama Hizmeti konusundaki web uygulamaları için etkinleştir meslüpleri oturum açma [bölümünün başarısız istek izlemeleri bölümüne](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure'daki Web Apps Uygulamaları için Uygulama performansı SSS'lerine bakın: Daha](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) fazla bilgi için başarısız istek izlemesini nasıl açarım?</span><span class="sxs-lookup"><span data-stu-id="5abf5-386">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="5abf5-387">Daha fazla bilgi için azure [Uygulama Hizmeti'ndeki web uygulamaları için tanılama günlüğe kaydetme'ye](/azure/app-service/web-sites-enable-diagnostic-log)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-387">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-388">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-388">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-389">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-389">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="5abf5-390">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-390">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-391">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-391">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="5abf5-392">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-392">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="5abf5-393">Uygulama Etkinlik Günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-393">Application Event Log (IIS)</span></span>

<span data-ttu-id="5abf5-394">Uygulama Etkinlik Günlüğüne Erişin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-394">Access the Application Event Log:</span></span>

1. <span data-ttu-id="5abf5-395">Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-395">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="5abf5-396">**Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-396">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="5abf5-397">Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-397">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="5abf5-398">Başarısız uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-398">Search for errors associated with the failing app.</span></span> <span data-ttu-id="5abf5-399">Hatalar *Kaynak* sütunda *IIS AspNetCore Modülü* veya *IIS Express AspNetCore Modülü* değerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-399">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="5abf5-400">Uygulamayı komut istemiyle çalıştırma</span><span class="sxs-lookup"><span data-stu-id="5abf5-400">Run the app at a command prompt</span></span>

<span data-ttu-id="5abf5-401">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="5abf5-401">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="5abf5-402">Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-402">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="5abf5-403">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="5abf5-403">Framework-dependent deployment</span></span>

<span data-ttu-id="5abf5-404">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-404">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="5abf5-405">Komut isteminde dağıtım klasörüne gidin ve uygulamanın montajını *dotnet.exe*ile çalıştırarak uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-405">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="5abf5-406">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `dotnet .\<assembly_name>.dll`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-406">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="5abf5-407">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-407">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="5abf5-408">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-408">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="5abf5-409">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-409">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="5abf5-410">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-410">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="5abf5-411">Bağımsız dağıtım</span><span class="sxs-lookup"><span data-stu-id="5abf5-411">Self-contained deployment</span></span>

<span data-ttu-id="5abf5-412">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-412">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="5abf5-413">Komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir uygulamasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-413">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="5abf5-414">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `<assembly_name>.exe`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="5abf5-415">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="5abf5-416">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="5abf5-417">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="5abf5-418">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="5abf5-419">ASP.NET Çekirdek Modülü stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-419">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="5abf5-420">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-420">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5abf5-421">Barındırma sisteminde sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-421">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="5abf5-422">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-422">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="5abf5-423">MSBuild'in dağıtımdaki *günlükler* klasörünü otomatik olarak oluşturmasına nasıl etkinleştirilen talimatlar için [Dizin yapısı](xref:host-and-deploy/directory-structure) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-423">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="5abf5-424">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-424">Edit the *web.config* file.</span></span> <span data-ttu-id="5abf5-425">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** yolunu *günlükler* klasörüne (örneğin) `.\logs\stdout`işaret etmek için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-425">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="5abf5-426">`stdout`yoldaki günlük dosya adı önekidir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-426">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="5abf5-427">Günlük oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-427">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="5abf5-428">Dosya `stdout` adı öneki olarak kullanarak, tipik bir günlük dosyası *stdout_20180205184032_5412.log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-428">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="5abf5-429">Uygulama havuzunuzun kimliğinin *günlükler* klasörüne yazma izinleri olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-429">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="5abf5-430">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-430">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-431">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-431">Make a request to the app.</span></span>
1. <span data-ttu-id="5abf5-432">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-432">Navigate to the *logs* folder.</span></span> <span data-ttu-id="5abf5-433">En son stdout günlüğünü bulun ve açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-433">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="5abf5-434">Hatalar için günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-434">Study the log for errors.</span></span>

<span data-ttu-id="5abf5-435">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-435">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="5abf5-436">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-436">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-437">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="5abf5-437">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="5abf5-438">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-438">Save the file.</span></span>

<span data-ttu-id="5abf5-439">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="5abf5-439">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-440">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-440">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-441">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-441">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="5abf5-442">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-442">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-443">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-443">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="5abf5-444">ASP.NET Çekirdek Modülü hata ayıklama günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-444">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="5abf5-445">Temel Modül hata ayıklama günlüğünü ASP.NET etkinleştirmek için uygulamanın *web.config* dosyasına aşağıdaki işleyici ayarlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-445">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="5abf5-446">Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-446">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="5abf5-447">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="5abf5-447">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="5abf5-448">Geliştirici Özel Durum Sayfasını Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="5abf5-448">Enable the Developer Exception Page</span></span>

<span data-ttu-id="5abf5-449">Ortamı `ASPNETCORE_ENVIRONMENT` geliştirme ortamında çalıştırmak için [web.config'e ortam değişkeni eklenebilir.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="5abf5-449">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="5abf5-450">Ortam, ana bilgisayar `UseEnvironment` oluşturucutarafından uygulama başlatmada geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında Geliştirici Özel Durum [Sayfasının](xref:fundamentals/error-handling) görünmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-450">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="5abf5-451">Ortam değişkenini `ASPNETCORE_ENVIRONMENT` ayarlamak yalnızca Internet'e maruz olmayan hazırlama ve sınama sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-451">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="5abf5-452">Sorun giderme den sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-452">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="5abf5-453">*web.config'de*ortam değişkenlerini ayarlama hakkında bilgi için [bkz.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="5abf5-453">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="5abf5-454">Bir uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="5abf5-454">Obtain data from an app</span></span>

<span data-ttu-id="5abf5-455">Bir uygulama isteklere yanıt verebiliyorsa, terminal sıralı ara yazılımları kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-455">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="5abf5-456">Daha fazla bilgi ve <xref:test/troubleshoot#obtain-data-from-an-app>örnek kod için bkz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-456">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="5abf5-457">Yavaş veya asılı uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-457">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="5abf5-458">*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-458">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="5abf5-459">Uygulama çöküyor veya bir özel durumla karşılaşıyor</span><span class="sxs-lookup"><span data-stu-id="5abf5-459">App crashes or encounters an exception</span></span>

<span data-ttu-id="5abf5-460">Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:</span><span class="sxs-lookup"><span data-stu-id="5abf5-460">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="5abf5-461">Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun</span><span class="sxs-lookup"><span data-stu-id="5abf5-461">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="5abf5-462">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-462">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="5abf5-463">[EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-463">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="5abf5-464">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-464">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="5abf5-465">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-465">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="5abf5-466">Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-466">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="5abf5-467">Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-467">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="5abf5-468">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-468">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="5abf5-469">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-469">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="5abf5-470">Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-470">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="5abf5-471">PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-471">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-472">Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).</span><span class="sxs-lookup"><span data-stu-id="5abf5-472">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="5abf5-473">Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır</span><span class="sxs-lookup"><span data-stu-id="5abf5-473">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="5abf5-474">Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)</span><span class="sxs-lookup"><span data-stu-id="5abf5-474">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="5abf5-475">Dökümü analiz edin</span><span class="sxs-lookup"><span data-stu-id="5abf5-475">Analyze the dump</span></span>

<span data-ttu-id="5abf5-476">Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-476">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="5abf5-477">Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)</span><span class="sxs-lookup"><span data-stu-id="5abf5-477">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="5abf5-478">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="5abf5-478">Clear package caches</span></span>

<span data-ttu-id="5abf5-479">İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-479">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="5abf5-480">Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-480">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="5abf5-481">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-481">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="5abf5-482">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-482">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="5abf5-483">[Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-483">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="5abf5-484">Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-484">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="5abf5-485">*nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-485">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="5abf5-486">Projeyi geri yükleyin ve yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-486">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="5abf5-487">Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-487">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5abf5-488">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5abf5-488">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="5abf5-489">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="5abf5-489">Azure documentation</span></span>

* [<span data-ttu-id="5abf5-490">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="5abf5-490">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="5abf5-491">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme bölümünün uzaktan hata ayıklama web uygulamaları bölümü</span><span class="sxs-lookup"><span data-stu-id="5abf5-491">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="5abf5-492">Azure Uygulama Hizmeti tanılama genel bakış</span><span class="sxs-lookup"><span data-stu-id="5abf5-492">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="5abf5-493">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="5abf5-493">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="5abf5-494">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-494">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="5abf5-495">Azure web uygulamalarınızda HTTP hatalarını "502 kötü ağ geçidi" ve "503 hizmeti kullanılamıyor" hatalarını giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-495">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="5abf5-496">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-496">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="5abf5-497">Azure'daki Web Apps için Uygulama performansı SSS'leri</span><span class="sxs-lookup"><span data-stu-id="5abf5-497">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="5abf5-498">Azure Web App sandbox (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="5abf5-498">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="5abf5-499">Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="5abf5-499">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="5abf5-500">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="5abf5-500">Visual Studio documentation</span></span>

* [<span data-ttu-id="5abf5-501">Visual Studio 2017'de Azure'da IIS'de Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5abf5-501">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="5abf5-502">Visual Studio 2017'de Uzaktan IIS Bilgisayarında Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5abf5-502">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="5abf5-503">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="5abf5-503">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="5abf5-504">Visual Studio Code dokümantasyonu</span><span class="sxs-lookup"><span data-stu-id="5abf5-504">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="5abf5-505">Visual Studio Code ile Hata Ayıklama</span><span class="sxs-lookup"><span data-stu-id="5abf5-505">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5abf5-506">Bu makalede, bir uygulama Azure Uygulama Hizmeti veya IIS'ye dağıtıldığında hataların nasıl tanılanacağına ilişkin yaygın uygulama başlangıç hataları ve yönergeler hakkında bilgiler verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-506">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="5abf5-507">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="5abf5-507">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="5abf5-508">Ortak başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-508">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="5abf5-509">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-509">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="5abf5-510">Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-510">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="5abf5-511">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-511">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="5abf5-512">IIS'ye dağıtılan veya IIS Express'te yerel olarak çalışan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-512">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="5abf5-513">Kılavuz, hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-513">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="5abf5-514">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="5abf5-514">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="5abf5-515">Büyük yükseltmeler yaparken veya paket sürümlerini değiştirirken tutarsız paketler bir uygulamayı kırdığında ne yapması gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-515">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="5abf5-516">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5abf5-516">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="5abf5-517">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="5abf5-517">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="5abf5-518">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="5abf5-518">App startup errors</span></span>

<span data-ttu-id="5abf5-519">Visual Studio'da, ASP.NET Core projesi hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırma için varsayılandır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-519">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="5abf5-520">A *502.5 - İşlem Hatası* veya *500.30 -* Yerel hata ayıklama zaman ortaya çıkan Başlat Hatası bu konuda ki tavsiye kullanılarak teşhis edilebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-520">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="5abf5-521">403.14 Yasak</span><span class="sxs-lookup"><span data-stu-id="5abf5-521">403.14 Forbidden</span></span>

<span data-ttu-id="5abf5-522">Uygulama başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-522">The app fails to start.</span></span> <span data-ttu-id="5abf5-523">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-523">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="5abf5-524">Hata genellikle aşağıdaki senaryolardan herhangi birini içeren barındırma sisteminde ki bozuk dağıtımdan kaynaklanır:</span><span class="sxs-lookup"><span data-stu-id="5abf5-524">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="5abf5-525">Uygulama barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-525">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="5abf5-526">Dağıtım işlemi, uygulamanın tüm dosya ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="5abf5-526">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="5abf5-527">*web.config* dosyası dağıtımda eksik veya *web.config* dosyası içeriği yanlış biçimlendirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-527">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="5abf5-528">Aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-528">Perform the following steps:</span></span>

1. <span data-ttu-id="5abf5-529">Barındırma sistemindeki dağıtım klasöründeki tüm dosya ve klasörleri silin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-529">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="5abf5-530">Visual Studio, PowerShell veya manuel dağıtım gibi normal dağıtım yönteminizi kullanarak uygulamanın *yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-530">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="5abf5-531">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-531">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="5abf5-532">Azure Uygulama Hizmeti'nde barındırma yaparken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-532">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="5abf5-533">Uygulama IIS tarafından barındırıldığında, uygulamanın **IIS Yöneticisi'nin**Temel **Ayarları'nda**gösterilen IIS **Fiziksel yoluna** dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-533">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="5abf5-534">Barındırma sistemindeki dağıtımı projenin *yayımlama* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtılmış olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-534">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="5abf5-535">Yayınlanan bir ASP.NET Core uygulamasının düzeni hakkında <xref:host-and-deploy/directory-structure>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-535">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="5abf5-536">*web.config* dosyasında daha fazla <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-536">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="5abf5-537">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="5abf5-537">500 Internal Server Error</span></span>

<span data-ttu-id="5abf5-538">Uygulama başlar, ancak bir hata sunucunun isteği yerine getirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="5abf5-538">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="5abf5-539">Bu hata, başlangıç sırasında veya yanıt oluştururken uygulamanın kodu içinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-539">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="5abf5-540">Yanıt hiçbir içerik içermeyebilir veya yanıt tarayıcıda *500 Dahili Sunucu Hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-540">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="5abf5-541">Uygulama Olay Günlüğü genellikle uygulamanın normal olarak başladığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-541">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="5abf5-542">Sunucunun bakış açısından, bu doğru.</span><span class="sxs-lookup"><span data-stu-id="5abf5-542">From the server's perspective, that's correct.</span></span> <span data-ttu-id="5abf5-543">Uygulama başladı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-543">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="5abf5-544">Uygulamayı sunucudaki bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Çekirdek Modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-544">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="5abf5-545">500.0 İşlem Içi İşleyici Yük Arızası</span><span class="sxs-lookup"><span data-stu-id="5abf5-545">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="5abf5-546">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-546">The worker process fails.</span></span> <span data-ttu-id="5abf5-547">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-547">The app doesn't start.</span></span>

<span data-ttu-id="5abf5-548">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR'yi ve işlem içi istek işleyicisini *(aspnetcorev2_inprocess.dll)* bulamaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-548">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="5abf5-549">Şuna bakın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-549">Check that:</span></span>

* <span data-ttu-id="5abf5-550">Uygulama ya [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet paketi veya [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)hedefliyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-550">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="5abf5-551">ASP.NET Core'un, uygulama hedeflerinin hedef makineye yüklenmiş olduğu paylaşılan çerçevesi nin sürümü.</span><span class="sxs-lookup"><span data-stu-id="5abf5-551">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="5abf5-552">500.0 İşlem Dışı İşleyici Yük Arızası</span><span class="sxs-lookup"><span data-stu-id="5abf5-552">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="5abf5-553">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-553">The worker process fails.</span></span> <span data-ttu-id="5abf5-554">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-554">The app doesn't start.</span></span>

<span data-ttu-id="5abf5-555">[ASP.NET Çekirdek Modülü,](xref:host-and-deploy/aspnet-core-module) işlem dışı barındırma isteği işleyicisini bulamaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-555">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="5abf5-556">*aspnetcorev2_outofprocess.dll'nin* *aspnetcorev2.dll'nin*yanındaki bir alt klasörde bulunduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-556">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="5abf5-557">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="5abf5-557">502.5 Process Failure</span></span>

<span data-ttu-id="5abf5-558">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-558">The worker process fails.</span></span> <span data-ttu-id="5abf5-559">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-559">The app doesn't start.</span></span>

<span data-ttu-id="5abf5-560">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) alt işlemi başlatmaya çalışır, ancak başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-560">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="5abf5-561">İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-561">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="5abf5-562">Yaygın bir hata koşulu, uygulamanın ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmış olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-562">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="5abf5-563">hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-563">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="5abf5-564">*Paylaşılan çerçeve,* makineye yüklenen *.dll* ve meta package gibi `Microsoft.AspNetCore.App`bir meta paketle başvurulan derlemeler kümesidir .</span><span class="sxs-lookup"><span data-stu-id="5abf5-564">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="5abf5-565">Metapackage başvurusu en az gerekli sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-565">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="5abf5-566">Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-566">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="5abf5-567">*502.5 İşlem Hatası* hatası sayfası, bir barındırma veya uygulama yanlış yapılandırması alt işlemin başarısız lığa neden olduğunda döndürülür:</span><span class="sxs-lookup"><span data-stu-id="5abf5-567">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="5abf5-568">Uygulama başlatılamamış (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="5abf5-568">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="5abf5-569">Uygulamanın derlemesi *(.dll)* yüklenemediği için uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="5abf5-569">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="5abf5-570">Bu hata, yayınlanan uygulama ile w3wp/iisexpress işlemi arasında bir bitness uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-570">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="5abf5-571">Uygulama havuzunun 32 bit ayarı doğru olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-571">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="5abf5-572">IIS Manager'ın **Uygulama Havuzları'ndaki**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-572">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="5abf5-573">**Eylemler** panelinde **Uygulama Havuzu** altında Gelişmiş **Ayarlar'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-573">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="5abf5-574">**32 Bit Uygulamaları Etkinleştir'i**Ayarlayın :</span><span class="sxs-lookup"><span data-stu-id="5abf5-574">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="5abf5-575">32 bit (x86) bir uygulama dağıtıyorsanız, `True`değeri .</span><span class="sxs-lookup"><span data-stu-id="5abf5-575">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="5abf5-576">64 bit (x64) bir uygulama dağıtıyorsanız, `False`değeri .</span><span class="sxs-lookup"><span data-stu-id="5abf5-576">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="5abf5-577">Proje dosyasındaki BIR `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bitliği arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-577">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="5abf5-578">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="5abf5-578">Connection reset</span></span>

<span data-ttu-id="5abf5-579">Üstbilgi gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Dahili Sunucu Hatası** göndermesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-579">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="5abf5-580">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirme sırasında bir hata oluşur oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-580">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="5abf5-581">Bu hata türü istemcide *bağlantı sıfırlama* hatası olarak görünür.</span><span class="sxs-lookup"><span data-stu-id="5abf5-581">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="5abf5-582">[Uygulama günlüğe kaydetme,](xref:fundamentals/logging/index) bu tür hataları gidermede yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-582">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="5abf5-583">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="5abf5-583">Default startup limits</span></span>

<span data-ttu-id="5abf5-584">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) 120 saniyelik varsayılan *başlangıçTimeLimit* ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-584">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="5abf5-585">Varsayılan değerde bırakıldığında, bir uygulamanın bir işlem hatası günlüğe kaydetmesi iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-585">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="5abf5-586">Modülü yapılandırma hakkında daha fazla bilgi için [aspNetCore öğesinin Öznitelikleri'ne](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-586">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="5abf5-587">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-587">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="5abf5-588">Uygulama Etkinlik Günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-588">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="5abf5-589">Uygulama Olay Günlüğü'ne erişmek için Azure portalındaki **Tanılama ve sorunları çözme** sorununu kullanın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-589">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="5abf5-590">Azure portalında Uygulamayı Uygulama **Hizmetleri'nde**açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-590">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="5abf5-591">**Sorunları tanılama ve çözme** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-591">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="5abf5-592">**Tanılama Araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-592">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="5abf5-593">**Destek Araçları** **altında, Uygulama Etkinlikleri** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-593">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="5abf5-594">**Kaynak** sütundaki *IIS AspNetCoreModule* veya *IIS AspNetCoreModule V2* girişi tarafından sağlanan en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-594">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="5abf5-595">**Tanılama ve sorunları çözmenin** alternatifi, Uygulama Olay Günlüğü dosyasını doğrudan [Kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-595">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="5abf5-596">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-596">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5abf5-597">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-597">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-598">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-598">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-599">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-599">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-600">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-600">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="5abf5-601">*eventlog.xml* dosyasının yanındaki kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-601">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="5abf5-602">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-602">Examine the log.</span></span> <span data-ttu-id="5abf5-603">En son olayları görmek için günlüğün altına gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-603">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="5abf5-604">Uygulamayı Kudu konsolunda çalıştırın</span><span class="sxs-lookup"><span data-stu-id="5abf5-604">Run the app in the Kudu console</span></span>

<span data-ttu-id="5abf5-605">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="5abf5-605">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="5abf5-606">Hatayı bulmak için uygulamayı [Kudu](https://github.com/projectkudu/kudu/wiki) Uzaktan Yürütme Konsolu'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5abf5-606">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="5abf5-607">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-607">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5abf5-608">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-608">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-609">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-609">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-610">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-610">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="5abf5-611">32 bit (x86) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="5abf5-611">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="5abf5-612">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="5abf5-612">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="5abf5-613">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-613">Run the app:</span></span>
   * <span data-ttu-id="5abf5-614">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-614">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="5abf5-615">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-615">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="5abf5-616">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-616">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="5abf5-617">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="5abf5-617">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="5abf5-618">*Core {VERSION} (x86) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="5abf5-618">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="5abf5-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="5abf5-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="5abf5-620">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5abf5-620">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="5abf5-621">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-621">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="5abf5-622">64 bit (x64) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="5abf5-622">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="5abf5-623">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="5abf5-623">**Current release**</span></span>

* <span data-ttu-id="5abf5-624">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-624">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="5abf5-625">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5abf5-625">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="5abf5-626">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-626">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="5abf5-627">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="5abf5-627">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="5abf5-628">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-628">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="5abf5-629">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="5abf5-629">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="5abf5-630">*Core {VERSION} (x64) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="5abf5-630">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="5abf5-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="5abf5-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="5abf5-632">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5abf5-632">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="5abf5-633">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-633">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="5abf5-634">ASP.NET Çekirdek Modül stdout günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-634">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="5abf5-635">ASP.NET Çekirdek Modülü stdout günlüğü genellikle Uygulama Olay Günlüğü'nde bulunmayan yararlı hata iletileri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="5abf5-635">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="5abf5-636">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-636">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5abf5-637">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-637">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="5abf5-638">**PROBLEM KATEGORİsİ SEÇ'in** **altında, Web Uygulaması Aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-638">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="5abf5-639">**Önerilen Çözümler** > Altında **Stdout Log Redirection etkinleştirin**, **Web.Config'i yeniden etkinleştirmek için Kudu Konsolu'nu açın**düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-639">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="5abf5-640">Kudu **Tanı Konsolu,** yol **sitesi** > **wwwroot**klasörleri açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-640">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="5abf5-641">Listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-641">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="5abf5-642">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-642">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-643">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-643">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="5abf5-644">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-644">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-645">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-645">Make a request to the app.</span></span>
1. <span data-ttu-id="5abf5-646">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="5abf5-646">Return to the Azure portal.</span></span> <span data-ttu-id="5abf5-647">**GELİşTİrME ARAÇLARI** alanında **Gelişmiş Araçlar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-647">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="5abf5-648">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-648">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-649">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-649">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-650">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-650">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-651">Günlük **Dosyaları** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-651">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="5abf5-652">**Değiştirilen** sütunu inceleyin ve en son değişiklik tarihiyle birlikte stdout günlüğünü deletmek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-652">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="5abf5-653">Günlük dosyası açıldığında, hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-653">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="5abf5-654">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-654">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="5abf5-655">Kudu **Tanı Konsolu,** *web.config* dosyasını ortaya çıkarmak için yol **sitesi** > **wwwroot** dönün.</span><span class="sxs-lookup"><span data-stu-id="5abf5-655">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="5abf5-656">Kalem simgesini seçerek **web.config** dosyasını yeniden açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-656">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="5abf5-657">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="5abf5-657">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="5abf5-658">Dosyayı kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-658">Select **Save** to save the file.</span></span>

<span data-ttu-id="5abf5-659">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="5abf5-659">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-660">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-660">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-661">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-661">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="5abf5-662">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlük kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-662">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="5abf5-663">Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-663">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-664">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-664">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="5abf5-665">ASP.NET Çekirdek Modül hata ayıklama günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-665">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="5abf5-666">ASP.NET Çekirdek Modülü hata ayıklama günlüğü, ASP.NET Çekirdek Modülünden ek, daha derin günlüğe kaydetme sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-666">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="5abf5-667">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-667">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5abf5-668">Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-668">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="5abf5-669">Gelişmiş bir tanı günlüğü için uygulamayı yapılandırmak için [Gelişmiş tanı günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-669">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="5abf5-670">Uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-670">Redeploy the app.</span></span>
   * <span data-ttu-id="5abf5-671">Kudu `<handlerSettings>` konsolu kullanarak canlı uygulamanın *web.config* dosyasına [Gelişmiş tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilenleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-671">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="5abf5-672">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-672">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5abf5-673">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-673">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-674">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-674">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="5abf5-675">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-675">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="5abf5-676">Klasörleri yol **sitesine** > aç**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="5abf5-676">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="5abf5-677">Kalem düğmesini seçerek *web.config* dosyasını düzenleme.</span><span class="sxs-lookup"><span data-stu-id="5abf5-677">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="5abf5-678">Gelişmiş `<handlerSettings>` [tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi bölümü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-678">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="5abf5-679">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-679">Select the **Save** button.</span></span>
1. <span data-ttu-id="5abf5-680">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5abf5-681">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-682">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-682">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-683">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-684">Klasörleri yol **sitesine** > aç**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="5abf5-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="5abf5-685">*aspnetcore-debug.log* dosyası için bir yol sağlamadıysanız, dosya listede görünür.</span><span class="sxs-lookup"><span data-stu-id="5abf5-685">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="5abf5-686">Bir yol sağladıysanız, günlük dosyasının konumuna gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-686">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="5abf5-687">Dosya adının yanındaki kalem düğmesiyle günlük dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-687">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="5abf5-688">Sorun giderme tamamlandığında hata ayıklama günlemasını devre dışı kındırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-688">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="5abf5-689">Gelişmiş hata ayıklama günlüğünü devre dışı kalmak için aşağıdakilerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-689">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="5abf5-690">`<handlerSettings>` *Web.config* dosyasını yerel olarak kaldırın ve uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-690">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="5abf5-691">*Web.config* dosyasını ve bölümü kaldırmak için Kudu konsoluna `<handlerSettings>` bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-691">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="5abf5-692">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-692">Save the file.</span></span>

<span data-ttu-id="5abf5-693">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="5abf5-693">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-694">Hata ayıklama günlüğünün devre dışı edilmemesi uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-694">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-695">Günlük dosyası boyutunda bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-695">There's no limit on log file size.</span></span> <span data-ttu-id="5abf5-696">Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğe kaydetmeyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-696">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="5abf5-697">Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-697">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-698">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-698">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="5abf5-699">Yavaş veya asma uygulaması (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-699">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="5abf5-700">Bir uygulama yavaş yanıt verdiğinde veya bir isteğe bağlı kaldığında aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-700">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="5abf5-701">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-701">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="5abf5-702">Azure Web Uygulamasında Aralıklı Özel Durum sorunları veya performans sorunları için Döküm'ü yakalamak için Kilitlenme Tanılayıcı Site Uzantısı'nı kullanma</span><span class="sxs-lookup"><span data-stu-id="5abf5-702">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="5abf5-703">İzleme bıçakları</span><span class="sxs-lookup"><span data-stu-id="5abf5-703">Monitoring blades</span></span>

<span data-ttu-id="5abf5-704">İzleme bıçakları, konunun daha önce açıklanan yöntemlerine alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-704">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="5abf5-705">Bu bıçaklar 500 seri hataları teşhis etmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-705">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="5abf5-706">ASP.NET Çekirdek Uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-706">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="5abf5-707">Uzantılar yüklenmiyorsa, bunları el ile yükleyin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-707">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="5abf5-708">**GELİşİm ARAÇLARI** bıçak bölümünde **Uzantılar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-708">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="5abf5-709">**ASP.NET Çekirdek Uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-709">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="5abf5-710">Uzantılar yüklenmiyorsa **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-710">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="5abf5-711">Listeden **ASP.NET Çekirdek Uzantıları'nı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-711">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="5abf5-712">Yasal koşulları kabul etmek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-712">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="5abf5-713">**Ekle uzantı** bıçağında **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-713">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="5abf5-714">Bilgilendirimiolan bir açılır ileti, uzantıların başarıyla ne zaman yüklenir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-714">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="5abf5-715">Stdout günlüğe kaydetme etkin değilse, aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-715">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="5abf5-716">Azure portalında, **GELIŞTIRME ARAÇLARı** alanındaki **Gelişmiş Araçlar** bıçak larını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-716">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="5abf5-717">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-717">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-718">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-718">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-719">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-719">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-720">Klasörleri yol **sitesi** > **wwwroot'a** açın ve listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-720">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="5abf5-721">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-721">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-722">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-722">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="5abf5-723">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-723">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="5abf5-724">Tanısal günlüğü etkinleştirmeye devam edin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-724">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="5abf5-725">Azure portalında **Tanılama günlükleri** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-725">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="5abf5-726">**Uygulama Günlüğe Kaydetme (Filesystem)** ve **Ayrıntılı hata iletileri**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-726">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="5abf5-727">Bıçağın üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-727">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="5abf5-728">Başarısız İstek Olay Arabelleği (FREB) günlüğe kaydetme olarak da bilinen başarısız istek izlemesini eklemek için, **Başarısız istek izleme**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-728">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="5abf5-729">Portaldaki **Teşhis günlükleri** bıçağının hemen altında listelenen **Log akış** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-729">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="5abf5-730">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-730">Make a request to the app.</span></span>
1. <span data-ttu-id="5abf5-731">Günlük akışı verileri içinde, hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-731">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="5abf5-732">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı bettiğinizden emin olun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-732">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="5abf5-733">Başarısız istek izleme günlüklerini (FREB günlükleri) görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-733">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="5abf5-734">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-734">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="5abf5-735">Kenar çubuğunun **DESTEK ARAÇLARI** alanından **Başarısız İstek İzleme Günlükleri'ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-735">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="5abf5-736">Azure Uygulama Hizmeti konusundaki web uygulamaları için etkinleştir meslüpleri oturum açma [bölümünün başarısız istek izlemeleri bölümüne](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure'daki Web Apps Uygulamaları için Uygulama performansı SSS'lerine bakın: Daha](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) fazla bilgi için başarısız istek izlemesini nasıl açarım?</span><span class="sxs-lookup"><span data-stu-id="5abf5-736">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="5abf5-737">Daha fazla bilgi için azure [Uygulama Hizmeti'ndeki web uygulamaları için tanılama günlüğe kaydetme'ye](/azure/app-service/web-sites-enable-diagnostic-log)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-737">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-738">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-738">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-739">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-739">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="5abf5-740">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-740">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-741">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-741">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="5abf5-742">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-742">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="5abf5-743">Uygulama Etkinlik Günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-743">Application Event Log (IIS)</span></span>

<span data-ttu-id="5abf5-744">Uygulama Etkinlik Günlüğüne Erişin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-744">Access the Application Event Log:</span></span>

1. <span data-ttu-id="5abf5-745">Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-745">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="5abf5-746">**Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-746">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="5abf5-747">Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-747">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="5abf5-748">Başarısız uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-748">Search for errors associated with the failing app.</span></span> <span data-ttu-id="5abf5-749">Hatalar *Kaynak* sütunda *IIS AspNetCore Modülü* veya *IIS Express AspNetCore Modülü* değerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-749">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="5abf5-750">Uygulamayı komut istemiyle çalıştırma</span><span class="sxs-lookup"><span data-stu-id="5abf5-750">Run the app at a command prompt</span></span>

<span data-ttu-id="5abf5-751">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="5abf5-751">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="5abf5-752">Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-752">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="5abf5-753">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="5abf5-753">Framework-dependent deployment</span></span>

<span data-ttu-id="5abf5-754">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-754">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="5abf5-755">Komut isteminde dağıtım klasörüne gidin ve uygulamanın montajını *dotnet.exe*ile çalıştırarak uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-755">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="5abf5-756">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `dotnet .\<assembly_name>.dll`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-756">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="5abf5-757">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-757">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="5abf5-758">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-758">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="5abf5-759">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-759">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="5abf5-760">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-760">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="5abf5-761">Bağımsız dağıtım</span><span class="sxs-lookup"><span data-stu-id="5abf5-761">Self-contained deployment</span></span>

<span data-ttu-id="5abf5-762">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-762">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="5abf5-763">Komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir uygulamasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-763">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="5abf5-764">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `<assembly_name>.exe`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="5abf5-765">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="5abf5-766">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="5abf5-767">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="5abf5-768">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="5abf5-769">ASP.NET Çekirdek Modülü stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-769">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="5abf5-770">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-770">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5abf5-771">Barındırma sisteminde sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-771">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="5abf5-772">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-772">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="5abf5-773">MSBuild'in dağıtımdaki *günlükler* klasörünü otomatik olarak oluşturmasına nasıl etkinleştirilen talimatlar için [Dizin yapısı](xref:host-and-deploy/directory-structure) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-773">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="5abf5-774">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-774">Edit the *web.config* file.</span></span> <span data-ttu-id="5abf5-775">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** yolunu *günlükler* klasörüne (örneğin) `.\logs\stdout`işaret etmek için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-775">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="5abf5-776">`stdout`yoldaki günlük dosya adı önekidir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-776">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="5abf5-777">Günlük oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-777">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="5abf5-778">Dosya `stdout` adı öneki olarak kullanarak, tipik bir günlük dosyası *stdout_20180205184032_5412.log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-778">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="5abf5-779">Uygulama havuzunuzun kimliğinin *günlükler* klasörüne yazma izinleri olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-779">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="5abf5-780">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-780">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-781">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-781">Make a request to the app.</span></span>
1. <span data-ttu-id="5abf5-782">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-782">Navigate to the *logs* folder.</span></span> <span data-ttu-id="5abf5-783">En son stdout günlüğünü bulun ve açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-783">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="5abf5-784">Hatalar için günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-784">Study the log for errors.</span></span>

<span data-ttu-id="5abf5-785">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-785">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="5abf5-786">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-786">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-787">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="5abf5-787">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="5abf5-788">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-788">Save the file.</span></span>

<span data-ttu-id="5abf5-789">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="5abf5-789">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-790">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-790">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-791">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-791">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="5abf5-792">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-792">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-793">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-793">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="5abf5-794">ASP.NET Çekirdek Modülü hata ayıklama günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-794">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="5abf5-795">Temel Modül hata ayıklama günlüğünü ASP.NET etkinleştirmek için uygulamanın *web.config* dosyasına aşağıdaki işleyici ayarlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-795">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="5abf5-796">Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-796">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="5abf5-797">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="5abf5-797">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="5abf5-798">Geliştirici Özel Durum Sayfasını Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="5abf5-798">Enable the Developer Exception Page</span></span>

<span data-ttu-id="5abf5-799">Ortamı `ASPNETCORE_ENVIRONMENT` geliştirme ortamında çalıştırmak için [web.config'e ortam değişkeni eklenebilir.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="5abf5-799">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="5abf5-800">Ortam, ana bilgisayar `UseEnvironment` oluşturucutarafından uygulama başlatmada geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında Geliştirici Özel Durum [Sayfasının](xref:fundamentals/error-handling) görünmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-800">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="5abf5-801">Ortam değişkenini `ASPNETCORE_ENVIRONMENT` ayarlamak yalnızca Internet'e maruz olmayan hazırlama ve sınama sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-801">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="5abf5-802">Sorun giderme den sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-802">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="5abf5-803">*web.config'de*ortam değişkenlerini ayarlama hakkında bilgi için [bkz.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="5abf5-803">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="5abf5-804">Bir uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="5abf5-804">Obtain data from an app</span></span>

<span data-ttu-id="5abf5-805">Bir uygulama isteklere yanıt verebiliyorsa, terminal sıralı ara yazılımları kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-805">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="5abf5-806">Daha fazla bilgi ve <xref:test/troubleshoot#obtain-data-from-an-app>örnek kod için bkz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-806">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="5abf5-807">Yavaş veya asılı uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-807">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="5abf5-808">*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-808">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="5abf5-809">Uygulama çöküyor veya bir özel durumla karşılaşıyor</span><span class="sxs-lookup"><span data-stu-id="5abf5-809">App crashes or encounters an exception</span></span>

<span data-ttu-id="5abf5-810">Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:</span><span class="sxs-lookup"><span data-stu-id="5abf5-810">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="5abf5-811">Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun</span><span class="sxs-lookup"><span data-stu-id="5abf5-811">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="5abf5-812">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-812">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="5abf5-813">[EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-813">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="5abf5-814">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-814">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="5abf5-815">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-815">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="5abf5-816">Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-816">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="5abf5-817">Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-817">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="5abf5-818">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-818">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="5abf5-819">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-819">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="5abf5-820">Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-820">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="5abf5-821">PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-821">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-822">Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).</span><span class="sxs-lookup"><span data-stu-id="5abf5-822">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="5abf5-823">Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır</span><span class="sxs-lookup"><span data-stu-id="5abf5-823">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="5abf5-824">Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)</span><span class="sxs-lookup"><span data-stu-id="5abf5-824">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="5abf5-825">Dökümü analiz edin</span><span class="sxs-lookup"><span data-stu-id="5abf5-825">Analyze the dump</span></span>

<span data-ttu-id="5abf5-826">Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-826">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="5abf5-827">Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)</span><span class="sxs-lookup"><span data-stu-id="5abf5-827">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="5abf5-828">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="5abf5-828">Clear package caches</span></span>

<span data-ttu-id="5abf5-829">İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-829">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="5abf5-830">Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-830">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="5abf5-831">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-831">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="5abf5-832">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-832">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="5abf5-833">[Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-833">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="5abf5-834">Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-834">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="5abf5-835">*nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-835">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="5abf5-836">Projeyi geri yükleyin ve yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-836">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="5abf5-837">Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-837">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5abf5-838">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5abf5-838">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="5abf5-839">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="5abf5-839">Azure documentation</span></span>

* [<span data-ttu-id="5abf5-840">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="5abf5-840">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="5abf5-841">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme bölümünün uzaktan hata ayıklama web uygulamaları bölümü</span><span class="sxs-lookup"><span data-stu-id="5abf5-841">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="5abf5-842">Azure Uygulama Hizmeti tanılama genel bakış</span><span class="sxs-lookup"><span data-stu-id="5abf5-842">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="5abf5-843">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="5abf5-843">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="5abf5-844">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-844">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="5abf5-845">Azure web uygulamalarınızda HTTP hatalarını "502 kötü ağ geçidi" ve "503 hizmeti kullanılamıyor" hatalarını giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-845">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="5abf5-846">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-846">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="5abf5-847">Azure'daki Web Apps için Uygulama performansı SSS'leri</span><span class="sxs-lookup"><span data-stu-id="5abf5-847">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="5abf5-848">Azure Web App sandbox (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="5abf5-848">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="5abf5-849">Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="5abf5-849">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="5abf5-850">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="5abf5-850">Visual Studio documentation</span></span>

* [<span data-ttu-id="5abf5-851">Visual Studio 2017'de Azure'da IIS'de Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5abf5-851">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="5abf5-852">Visual Studio 2017'de Uzaktan IIS Bilgisayarında Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5abf5-852">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="5abf5-853">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="5abf5-853">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="5abf5-854">Visual Studio Code dokümantasyonu</span><span class="sxs-lookup"><span data-stu-id="5abf5-854">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="5abf5-855">Visual Studio Code ile Hata Ayıklama</span><span class="sxs-lookup"><span data-stu-id="5abf5-855">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5abf5-856">Bu makalede, bir uygulama Azure Uygulama Hizmeti veya IIS'ye dağıtıldığında hataların nasıl tanılanacağına ilişkin yaygın uygulama başlangıç hataları ve yönergeler hakkında bilgiler verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-856">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="5abf5-857">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="5abf5-857">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="5abf5-858">Ortak başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-858">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="5abf5-859">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-859">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="5abf5-860">Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-860">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="5abf5-861">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-861">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="5abf5-862">IIS'ye dağıtılan veya IIS Express'te yerel olarak çalışan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-862">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="5abf5-863">Kılavuz, hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-863">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="5abf5-864">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="5abf5-864">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="5abf5-865">Büyük yükseltmeler yaparken veya paket sürümlerini değiştirirken tutarsız paketler bir uygulamayı kırdığında ne yapması gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-865">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="5abf5-866">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5abf5-866">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="5abf5-867">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="5abf5-867">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="5abf5-868">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="5abf5-868">App startup errors</span></span>

<span data-ttu-id="5abf5-869">Visual Studio'da, ASP.NET Core projesi hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırma için varsayılandır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-869">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="5abf5-870">Yerel hata ayıklama zaman ortaya çıkan bir *502.5 İşlem Hatası* bu konuda ki tavsiye kullanılarak teşhis edilebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-870">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="5abf5-871">403.14 Yasak</span><span class="sxs-lookup"><span data-stu-id="5abf5-871">403.14 Forbidden</span></span>

<span data-ttu-id="5abf5-872">Uygulama başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-872">The app fails to start.</span></span> <span data-ttu-id="5abf5-873">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-873">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="5abf5-874">Hata genellikle aşağıdaki senaryolardan herhangi birini içeren barındırma sisteminde ki bozuk dağıtımdan kaynaklanır:</span><span class="sxs-lookup"><span data-stu-id="5abf5-874">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="5abf5-875">Uygulama barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-875">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="5abf5-876">Dağıtım işlemi, uygulamanın tüm dosya ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="5abf5-876">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="5abf5-877">*web.config* dosyası dağıtımda eksik veya *web.config* dosyası içeriği yanlış biçimlendirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-877">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="5abf5-878">Aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-878">Perform the following steps:</span></span>

1. <span data-ttu-id="5abf5-879">Barındırma sistemindeki dağıtım klasöründeki tüm dosya ve klasörleri silin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-879">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="5abf5-880">Visual Studio, PowerShell veya manuel dağıtım gibi normal dağıtım yönteminizi kullanarak uygulamanın *yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-880">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="5abf5-881">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-881">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="5abf5-882">Azure Uygulama Hizmeti'nde barındırma yaparken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-882">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="5abf5-883">Uygulama IIS tarafından barındırıldığında, uygulamanın **IIS Yöneticisi'nin**Temel **Ayarları'nda**gösterilen IIS **Fiziksel yoluna** dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-883">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="5abf5-884">Barındırma sistemindeki dağıtımı projenin *yayımlama* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtılmış olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-884">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="5abf5-885">Yayınlanan bir ASP.NET Core uygulamasının düzeni hakkında <xref:host-and-deploy/directory-structure>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-885">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="5abf5-886">*web.config* dosyasında daha fazla <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-886">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="5abf5-887">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="5abf5-887">500 Internal Server Error</span></span>

<span data-ttu-id="5abf5-888">Uygulama başlar, ancak bir hata sunucunun isteği yerine getirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="5abf5-888">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="5abf5-889">Bu hata, başlangıç sırasında veya yanıt oluştururken uygulamanın kodu içinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-889">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="5abf5-890">Yanıt hiçbir içerik içermeyebilir veya yanıt tarayıcıda *500 Dahili Sunucu Hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-890">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="5abf5-891">Uygulama Olay Günlüğü genellikle uygulamanın normal olarak başladığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-891">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="5abf5-892">Sunucunun bakış açısından, bu doğru.</span><span class="sxs-lookup"><span data-stu-id="5abf5-892">From the server's perspective, that's correct.</span></span> <span data-ttu-id="5abf5-893">Uygulama başladı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-893">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="5abf5-894">Uygulamayı sunucudaki bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Çekirdek Modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-894">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="5abf5-895">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="5abf5-895">502.5 Process Failure</span></span>

<span data-ttu-id="5abf5-896">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-896">The worker process fails.</span></span> <span data-ttu-id="5abf5-897">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-897">The app doesn't start.</span></span>

<span data-ttu-id="5abf5-898">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) alt işlemi başlatmaya çalışır, ancak başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-898">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="5abf5-899">İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-899">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="5abf5-900">Yaygın bir hata koşulu, uygulamanın ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmış olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-900">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="5abf5-901">hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-901">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="5abf5-902">*Paylaşılan çerçeve,* makineye yüklenen *.dll* ve meta package gibi `Microsoft.AspNetCore.App`bir meta paketle başvurulan derlemeler kümesidir .</span><span class="sxs-lookup"><span data-stu-id="5abf5-902">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="5abf5-903">Metapackage başvurusu en az gerekli sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-903">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="5abf5-904">Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-904">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="5abf5-905">*502.5 İşlem Hatası* hatası sayfası, bir barındırma veya uygulama yanlış yapılandırması alt işlemin başarısız lığa neden olduğunda döndürülür:</span><span class="sxs-lookup"><span data-stu-id="5abf5-905">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="5abf5-906">Uygulama başlatılamamış (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="5abf5-906">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="5abf5-907">Uygulamanın derlemesi *(.dll)* yüklenemediği için uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="5abf5-907">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="5abf5-908">Bu hata, yayınlanan uygulama ile w3wp/iisexpress işlemi arasında bir bitness uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-908">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="5abf5-909">Uygulama havuzunun 32 bit ayarı doğru olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-909">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="5abf5-910">IIS Manager'ın **Uygulama Havuzları'ndaki**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-910">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="5abf5-911">**Eylemler** panelinde **Uygulama Havuzu** altında Gelişmiş **Ayarlar'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-911">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="5abf5-912">**32 Bit Uygulamaları Etkinleştir'i**Ayarlayın :</span><span class="sxs-lookup"><span data-stu-id="5abf5-912">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="5abf5-913">32 bit (x86) bir uygulama dağıtıyorsanız, `True`değeri .</span><span class="sxs-lookup"><span data-stu-id="5abf5-913">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="5abf5-914">64 bit (x64) bir uygulama dağıtıyorsanız, `False`değeri .</span><span class="sxs-lookup"><span data-stu-id="5abf5-914">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="5abf5-915">Proje dosyasındaki BIR `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bitliği arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-915">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="5abf5-916">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="5abf5-916">Connection reset</span></span>

<span data-ttu-id="5abf5-917">Üstbilgi gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Dahili Sunucu Hatası** göndermesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-917">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="5abf5-918">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirme sırasında bir hata oluşur oluşur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-918">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="5abf5-919">Bu hata türü istemcide *bağlantı sıfırlama* hatası olarak görünür.</span><span class="sxs-lookup"><span data-stu-id="5abf5-919">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="5abf5-920">[Uygulama günlüğe kaydetme,](xref:fundamentals/logging/index) bu tür hataları gidermede yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-920">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="5abf5-921">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="5abf5-921">Default startup limits</span></span>

<span data-ttu-id="5abf5-922">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) 120 saniyelik varsayılan *başlangıçTimeLimit* ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-922">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="5abf5-923">Varsayılan değerde bırakıldığında, bir uygulamanın bir işlem hatası günlüğe kaydetmesi iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-923">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="5abf5-924">Modülü yapılandırma hakkında daha fazla bilgi için [aspNetCore öğesinin Öznitelikleri'ne](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-924">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="5abf5-925">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-925">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="5abf5-926">Uygulama Etkinlik Günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-926">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="5abf5-927">Uygulama Olay Günlüğü'ne erişmek için Azure portalındaki **Tanılama ve sorunları çözme** sorununu kullanın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-927">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="5abf5-928">Azure portalında Uygulamayı Uygulama **Hizmetleri'nde**açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-928">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="5abf5-929">**Sorunları tanılama ve çözme** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-929">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="5abf5-930">**Tanılama Araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-930">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="5abf5-931">**Destek Araçları** **altında, Uygulama Etkinlikleri** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-931">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="5abf5-932">**Kaynak** sütundaki *IIS AspNetCoreModule* veya *IIS AspNetCoreModule V2* girişi tarafından sağlanan en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-932">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="5abf5-933">**Tanılama ve sorunları çözmenin** alternatifi, Uygulama Olay Günlüğü dosyasını doğrudan [Kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-933">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="5abf5-934">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-934">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5abf5-935">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-935">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-936">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-936">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-937">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-937">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-938">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-938">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="5abf5-939">*eventlog.xml* dosyasının yanındaki kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-939">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="5abf5-940">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-940">Examine the log.</span></span> <span data-ttu-id="5abf5-941">En son olayları görmek için günlüğün altına gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-941">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="5abf5-942">Uygulamayı Kudu konsolunda çalıştırın</span><span class="sxs-lookup"><span data-stu-id="5abf5-942">Run the app in the Kudu console</span></span>

<span data-ttu-id="5abf5-943">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="5abf5-943">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="5abf5-944">Hatayı bulmak için uygulamayı [Kudu](https://github.com/projectkudu/kudu/wiki) Uzaktan Yürütme Konsolu'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5abf5-944">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="5abf5-945">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-945">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="5abf5-946">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-946">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-947">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-947">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-948">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-948">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="5abf5-949">32 bit (x86) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="5abf5-949">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="5abf5-950">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="5abf5-950">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="5abf5-951">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-951">Run the app:</span></span>
   * <span data-ttu-id="5abf5-952">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-952">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="5abf5-953">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-953">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="5abf5-954">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-954">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="5abf5-955">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="5abf5-955">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="5abf5-956">*Core {VERSION} (x86) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="5abf5-956">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="5abf5-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="5abf5-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="5abf5-958">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5abf5-958">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="5abf5-959">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-959">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="5abf5-960">64 bit (x64) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="5abf5-960">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="5abf5-961">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="5abf5-961">**Current release**</span></span>

* <span data-ttu-id="5abf5-962">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-962">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="5abf5-963">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5abf5-963">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="5abf5-964">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-964">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="5abf5-965">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="5abf5-965">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="5abf5-966">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-966">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="5abf5-967">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="5abf5-967">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="5abf5-968">*Core {VERSION} (x64) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="5abf5-968">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="5abf5-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="5abf5-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="5abf5-970">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="5abf5-970">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="5abf5-971">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-971">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="5abf5-972">ASP.NET Çekirdek Modül stdout günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-972">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="5abf5-973">ASP.NET Çekirdek Modülü stdout günlüğü genellikle Uygulama Olay Günlüğü'nde bulunmayan yararlı hata iletileri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="5abf5-973">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="5abf5-974">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-974">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5abf5-975">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-975">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="5abf5-976">**PROBLEM KATEGORİsİ SEÇ'in** **altında, Web Uygulaması Aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-976">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="5abf5-977">**Önerilen Çözümler** > Altında **Stdout Log Redirection etkinleştirin**, **Web.Config'i yeniden etkinleştirmek için Kudu Konsolu'nu açın**düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-977">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="5abf5-978">Kudu **Tanı Konsolu,** yol **sitesi** > **wwwroot**klasörleri açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-978">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="5abf5-979">Listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-979">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="5abf5-980">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-980">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-981">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-981">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="5abf5-982">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-982">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-983">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-983">Make a request to the app.</span></span>
1. <span data-ttu-id="5abf5-984">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="5abf5-984">Return to the Azure portal.</span></span> <span data-ttu-id="5abf5-985">**GELİşTİrME ARAÇLARI** alanında **Gelişmiş Araçlar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-985">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="5abf5-986">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-986">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-987">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-987">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-988">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-988">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-989">Günlük **Dosyaları** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-989">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="5abf5-990">**Değiştirilen** sütunu inceleyin ve en son değişiklik tarihiyle birlikte stdout günlüğünü deletmek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-990">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="5abf5-991">Günlük dosyası açıldığında, hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-991">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="5abf5-992">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-992">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="5abf5-993">Kudu **Tanı Konsolu,** *web.config* dosyasını ortaya çıkarmak için yol **sitesi** > **wwwroot** dönün.</span><span class="sxs-lookup"><span data-stu-id="5abf5-993">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="5abf5-994">Kalem simgesini seçerek **web.config** dosyasını yeniden açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-994">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="5abf5-995">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="5abf5-995">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="5abf5-996">Dosyayı kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-996">Select **Save** to save the file.</span></span>

<span data-ttu-id="5abf5-997">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="5abf5-997">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-998">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-998">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-999">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-999">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="5abf5-1000">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlük kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1000">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="5abf5-1001">Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1001">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-1002">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1002">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="5abf5-1003">Yavaş veya asma uygulaması (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="5abf5-1003">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="5abf5-1004">Bir uygulama yavaş yanıt verdiğinde veya bir isteğe bağlı kaldığında aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1004">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="5abf5-1005">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-1005">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="5abf5-1006">Azure Web Uygulamasında Aralıklı Özel Durum sorunları veya performans sorunları için Döküm'ü yakalamak için Kilitlenme Tanılayıcı Site Uzantısı'nı kullanma</span><span class="sxs-lookup"><span data-stu-id="5abf5-1006">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="5abf5-1007">İzleme bıçakları</span><span class="sxs-lookup"><span data-stu-id="5abf5-1007">Monitoring blades</span></span>

<span data-ttu-id="5abf5-1008">İzleme bıçakları, konunun daha önce açıklanan yöntemlerine alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1008">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="5abf5-1009">Bu bıçaklar 500 seri hataları teşhis etmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1009">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="5abf5-1010">ASP.NET Çekirdek Uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1010">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="5abf5-1011">Uzantılar yüklenmiyorsa, bunları el ile yükleyin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1011">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="5abf5-1012">**GELİşİm ARAÇLARI** bıçak bölümünde **Uzantılar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1012">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="5abf5-1013">**ASP.NET Çekirdek Uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1013">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="5abf5-1014">Uzantılar yüklenmiyorsa **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1014">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="5abf5-1015">Listeden **ASP.NET Çekirdek Uzantıları'nı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1015">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="5abf5-1016">Yasal koşulları kabul etmek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1016">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="5abf5-1017">**Ekle uzantı** bıçağında **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1017">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="5abf5-1018">Bilgilendirimiolan bir açılır ileti, uzantıların başarıyla ne zaman yüklenir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1018">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="5abf5-1019">Stdout günlüğe kaydetme etkin değilse, aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1019">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="5abf5-1020">Azure portalında, **GELIŞTIRME ARAÇLARı** alanındaki **Gelişmiş Araçlar** bıçak larını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1020">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="5abf5-1021">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1021">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="5abf5-1022">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1022">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="5abf5-1023">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1023">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="5abf5-1024">Klasörleri yol **sitesi** > **wwwroot'a** açın ve listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1024">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="5abf5-1025">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1025">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-1026">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-1026">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="5abf5-1027">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1027">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="5abf5-1028">Tanısal günlüğü etkinleştirmeye devam edin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1028">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="5abf5-1029">Azure portalında **Tanılama günlükleri** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1029">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="5abf5-1030">**Uygulama Günlüğe Kaydetme (Filesystem)** ve **Ayrıntılı hata iletileri**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1030">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="5abf5-1031">Bıçağın üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1031">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="5abf5-1032">Başarısız İstek Olay Arabelleği (FREB) günlüğe kaydetme olarak da bilinen başarısız istek izlemesini eklemek için, **Başarısız istek izleme**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1032">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="5abf5-1033">Portaldaki **Teşhis günlükleri** bıçağının hemen altında listelenen **Log akış** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1033">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="5abf5-1034">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1034">Make a request to the app.</span></span>
1. <span data-ttu-id="5abf5-1035">Günlük akışı verileri içinde, hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1035">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="5abf5-1036">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı bettiğinizden emin olun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1036">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="5abf5-1037">Başarısız istek izleme günlüklerini (FREB günlükleri) görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1037">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="5abf5-1038">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1038">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="5abf5-1039">Kenar çubuğunun **DESTEK ARAÇLARI** alanından **Başarısız İstek İzleme Günlükleri'ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1039">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="5abf5-1040">Azure Uygulama Hizmeti konusundaki web uygulamaları için etkinleştir meslüpleri oturum açma [bölümünün başarısız istek izlemeleri bölümüne](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure'daki Web Apps Uygulamaları için Uygulama performansı SSS'lerine bakın: Daha](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) fazla bilgi için başarısız istek izlemesini nasıl açarım?</span><span class="sxs-lookup"><span data-stu-id="5abf5-1040">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="5abf5-1041">Daha fazla bilgi için azure [Uygulama Hizmeti'ndeki web uygulamaları için tanılama günlüğe kaydetme'ye](/azure/app-service/web-sites-enable-diagnostic-log)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1041">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-1042">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1042">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-1043">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1043">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="5abf5-1044">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1044">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-1045">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1045">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="5abf5-1046">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-1046">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="5abf5-1047">Uygulama Etkinlik Günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-1047">Application Event Log (IIS)</span></span>

<span data-ttu-id="5abf5-1048">Uygulama Etkinlik Günlüğüne Erişin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1048">Access the Application Event Log:</span></span>

1. <span data-ttu-id="5abf5-1049">Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1049">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="5abf5-1050">**Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1050">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="5abf5-1051">Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1051">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="5abf5-1052">Başarısız uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1052">Search for errors associated with the failing app.</span></span> <span data-ttu-id="5abf5-1053">Hatalar *Kaynak* sütunda *IIS AspNetCore Modülü* veya *IIS Express AspNetCore Modülü* değerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1053">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="5abf5-1054">Uygulamayı komut istemiyle çalıştırma</span><span class="sxs-lookup"><span data-stu-id="5abf5-1054">Run the app at a command prompt</span></span>

<span data-ttu-id="5abf5-1055">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1055">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="5abf5-1056">Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1056">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="5abf5-1057">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="5abf5-1057">Framework-dependent deployment</span></span>

<span data-ttu-id="5abf5-1058">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1058">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="5abf5-1059">Komut isteminde dağıtım klasörüne gidin ve uygulamanın montajını *dotnet.exe*ile çalıştırarak uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1059">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="5abf5-1060">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `dotnet .\<assembly_name>.dll`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-1060">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="5abf5-1061">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1061">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="5abf5-1062">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1062">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="5abf5-1063">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1063">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="5abf5-1064">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1064">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="5abf5-1065">Bağımsız dağıtım</span><span class="sxs-lookup"><span data-stu-id="5abf5-1065">Self-contained deployment</span></span>

<span data-ttu-id="5abf5-1066">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1066">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="5abf5-1067">Komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir uygulamasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1067">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="5abf5-1068">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `<assembly_name>.exe`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="5abf5-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="5abf5-1069">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="5abf5-1070">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="5abf5-1071">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="5abf5-1072">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="5abf5-1073">ASP.NET Çekirdek Modülü stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-1073">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="5abf5-1074">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1074">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="5abf5-1075">Barındırma sisteminde sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1075">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="5abf5-1076">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1076">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="5abf5-1077">MSBuild'in dağıtımdaki *günlükler* klasörünü otomatik olarak oluşturmasına nasıl etkinleştirilen talimatlar için [Dizin yapısı](xref:host-and-deploy/directory-structure) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1077">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="5abf5-1078">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1078">Edit the *web.config* file.</span></span> <span data-ttu-id="5abf5-1079">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** yolunu *günlükler* klasörüne (örneğin) `.\logs\stdout`işaret etmek için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1079">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="5abf5-1080">`stdout`yoldaki günlük dosya adı önekidir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1080">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="5abf5-1081">Günlük oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1081">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="5abf5-1082">Dosya `stdout` adı öneki olarak kullanarak, tipik bir günlük dosyası *stdout_20180205184032_5412.log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1082">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="5abf5-1083">Uygulama havuzunuzun kimliğinin *günlükler* klasörüne yazma izinleri olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1083">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="5abf5-1084">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1084">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-1085">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1085">Make a request to the app.</span></span>
1. <span data-ttu-id="5abf5-1086">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1086">Navigate to the *logs* folder.</span></span> <span data-ttu-id="5abf5-1087">En son stdout günlüğünü bulun ve açın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1087">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="5abf5-1088">Hatalar için günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1088">Study the log for errors.</span></span>

<span data-ttu-id="5abf5-1089">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1089">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="5abf5-1090">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1090">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="5abf5-1091">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1091">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="5abf5-1092">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1092">Save the file.</span></span>

<span data-ttu-id="5abf5-1093">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1093">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-1094">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1094">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="5abf5-1095">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1095">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="5abf5-1096">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1096">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5abf5-1097">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1097">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="5abf5-1098">Geliştirici Özel Durum Sayfasını Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="5abf5-1098">Enable the Developer Exception Page</span></span>

<span data-ttu-id="5abf5-1099">Ortamı `ASPNETCORE_ENVIRONMENT` geliştirme ortamında çalıştırmak için [web.config'e ortam değişkeni eklenebilir.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="5abf5-1099">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="5abf5-1100">Ortam, ana bilgisayar `UseEnvironment` oluşturucutarafından uygulama başlatmada geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında Geliştirici Özel Durum [Sayfasının](xref:fundamentals/error-handling) görünmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1100">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="5abf5-1101">Ortam değişkenini `ASPNETCORE_ENVIRONMENT` ayarlamak yalnızca Internet'e maruz olmayan hazırlama ve sınama sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1101">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="5abf5-1102">Sorun giderme den sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1102">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="5abf5-1103">*web.config'de*ortam değişkenlerini ayarlama hakkında bilgi için [bkz.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="5abf5-1103">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="5abf5-1104">Bir uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="5abf5-1104">Obtain data from an app</span></span>

<span data-ttu-id="5abf5-1105">Bir uygulama isteklere yanıt verebiliyorsa, terminal sıralı ara yazılımları kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1105">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="5abf5-1106">Daha fazla bilgi ve <xref:test/troubleshoot#obtain-data-from-an-app>örnek kod için bkz.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1106">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="5abf5-1107">Yavaş veya asılı uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="5abf5-1107">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="5abf5-1108">*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1108">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="5abf5-1109">Uygulama çöküyor veya bir özel durumla karşılaşıyor</span><span class="sxs-lookup"><span data-stu-id="5abf5-1109">App crashes or encounters an exception</span></span>

<span data-ttu-id="5abf5-1110">Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1110">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="5abf5-1111">Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun</span><span class="sxs-lookup"><span data-stu-id="5abf5-1111">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="5abf5-1112">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1112">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="5abf5-1113">[EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1113">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="5abf5-1114">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1114">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="5abf5-1115">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1115">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="5abf5-1116">Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1116">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="5abf5-1117">Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1117">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="5abf5-1118">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1118">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="5abf5-1119">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1119">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="5abf5-1120">Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1120">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="5abf5-1121">PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1121">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="5abf5-1122">Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).</span><span class="sxs-lookup"><span data-stu-id="5abf5-1122">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="5abf5-1123">Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır</span><span class="sxs-lookup"><span data-stu-id="5abf5-1123">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="5abf5-1124">Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)</span><span class="sxs-lookup"><span data-stu-id="5abf5-1124">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="5abf5-1125">Dökümü analiz edin</span><span class="sxs-lookup"><span data-stu-id="5abf5-1125">Analyze the dump</span></span>

<span data-ttu-id="5abf5-1126">Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1126">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="5abf5-1127">Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)</span><span class="sxs-lookup"><span data-stu-id="5abf5-1127">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="5abf5-1128">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="5abf5-1128">Clear package caches</span></span>

<span data-ttu-id="5abf5-1129">İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1129">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="5abf5-1130">Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1130">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="5abf5-1131">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:</span><span class="sxs-lookup"><span data-stu-id="5abf5-1131">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="5abf5-1132">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1132">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="5abf5-1133">[Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1133">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="5abf5-1134">Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1134">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="5abf5-1135">*nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1135">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="5abf5-1136">Projeyi geri yükleyin ve yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1136">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="5abf5-1137">Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="5abf5-1137">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5abf5-1138">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5abf5-1138">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="5abf5-1139">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="5abf5-1139">Azure documentation</span></span>

* [<span data-ttu-id="5abf5-1140">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="5abf5-1140">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="5abf5-1141">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme bölümünün uzaktan hata ayıklama web uygulamaları bölümü</span><span class="sxs-lookup"><span data-stu-id="5abf5-1141">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="5abf5-1142">Azure Uygulama Hizmeti tanılama genel bakış</span><span class="sxs-lookup"><span data-stu-id="5abf5-1142">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="5abf5-1143">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="5abf5-1143">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="5abf5-1144">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-1144">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="5abf5-1145">Azure web uygulamalarınızda HTTP hatalarını "502 kötü ağ geçidi" ve "503 hizmeti kullanılamıyor" hatalarını giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-1145">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="5abf5-1146">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="5abf5-1146">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="5abf5-1147">Azure'daki Web Apps için Uygulama performansı SSS'leri</span><span class="sxs-lookup"><span data-stu-id="5abf5-1147">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="5abf5-1148">Azure Web App sandbox (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="5abf5-1148">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="5abf5-1149">Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="5abf5-1149">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="5abf5-1150">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="5abf5-1150">Visual Studio documentation</span></span>

* [<span data-ttu-id="5abf5-1151">Visual Studio 2017'de Azure'da IIS'de Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5abf5-1151">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="5abf5-1152">Visual Studio 2017'de Uzaktan IIS Bilgisayarında Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5abf5-1152">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="5abf5-1153">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="5abf5-1153">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="5abf5-1154">Visual Studio Code dokümantasyonu</span><span class="sxs-lookup"><span data-stu-id="5abf5-1154">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="5abf5-1155">Visual Studio Code ile Hata Ayıklama</span><span class="sxs-lookup"><span data-stu-id="5abf5-1155">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
