---
title: Azure Uygulama Hizmeti ve IIS'de ASP.NET Core sorun giderme
author: rick-anderson
description: Azure Uygulama Hizmeti ve Internet Bilgi Hizmetleri (IIS) dağıtımlarıyla ilgili sorunları ASP.NET Temel uygulamalarla ilgili sorunları nasıl tanılayabileceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: f994cd1274bda9082a7cd8b637968b2769db1671
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661706"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="f63ef-103">Azure Uygulama Hizmeti ve IIS'de ASP.NET Core sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="f63ef-104">Yazar: [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="f63ef-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f63ef-105">Bu makalede, bir uygulama Azure Uygulama Hizmeti veya IIS'ye dağıtıldığında hataların nasıl tanılanacağına ilişkin yaygın uygulama başlangıç hataları ve yönergeler hakkında bilgiler verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="f63ef-106">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="f63ef-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="f63ef-107">Ortak başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="f63ef-108">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="f63ef-109">Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="f63ef-110">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="f63ef-111">IIS'ye dağıtılan veya IIS Express'te yerel olarak çalışan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="f63ef-112">Kılavuz, hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="f63ef-113">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="f63ef-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="f63ef-114">Büyük yükseltmeler yaparken veya paket sürümlerini değiştirirken tutarsız paketler bir uygulamayı kırdığında ne yapması gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="f63ef-115">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f63ef-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="f63ef-116">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="f63ef-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="f63ef-117">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="f63ef-117">App startup errors</span></span>

<span data-ttu-id="f63ef-118">Visual Studio'da, ASP.NET Core projesi hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırma için varsayılandır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="f63ef-119">A *502.5 - İşlem Hatası* veya *500.30 -* Yerel hata ayıklama zaman ortaya çıkan Başlat Hatası bu konuda ki tavsiye kullanılarak teşhis edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="f63ef-120">403.14 Yasak</span><span class="sxs-lookup"><span data-stu-id="f63ef-120">403.14 Forbidden</span></span>

<span data-ttu-id="f63ef-121">Uygulama başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-121">The app fails to start.</span></span> <span data-ttu-id="f63ef-122">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="f63ef-123">Hata genellikle aşağıdaki senaryolardan herhangi birini içeren barındırma sisteminde ki bozuk dağıtımdan kaynaklanır:</span><span class="sxs-lookup"><span data-stu-id="f63ef-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="f63ef-124">Uygulama barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="f63ef-125">Dağıtım işlemi, uygulamanın tüm dosya ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="f63ef-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="f63ef-126">*web.config* dosyası dağıtımda eksik veya *web.config* dosyası içeriği yanlış biçimlendirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="f63ef-127">Aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-127">Perform the following steps:</span></span>

1. <span data-ttu-id="f63ef-128">Barındırma sistemindeki dağıtım klasöründeki tüm dosya ve klasörleri silin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="f63ef-129">Visual Studio, PowerShell veya manuel dağıtım gibi normal dağıtım yönteminizi kullanarak uygulamanın *yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="f63ef-130">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="f63ef-131">Azure Uygulama Hizmeti'nde barındırma yaparken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="f63ef-132">Uygulama IIS tarafından barındırıldığında, uygulamanın **IIS Yöneticisi'nin**Temel **Ayarları'nda**gösterilen IIS **Fiziksel yoluna** dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="f63ef-133">Barındırma sistemindeki dağıtımı projenin *yayımlama* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtılmış olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="f63ef-134">Yayınlanan bir ASP.NET Core uygulamasının düzeni hakkında <xref:host-and-deploy/directory-structure>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="f63ef-135">*web.config* dosyasında daha fazla <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="f63ef-136">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="f63ef-136">500 Internal Server Error</span></span>

<span data-ttu-id="f63ef-137">Uygulama başlar, ancak bir hata sunucunun isteği yerine getirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="f63ef-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="f63ef-138">Bu hata, başlangıç sırasında veya yanıt oluştururken uygulamanın kodu içinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="f63ef-139">Yanıt hiçbir içerik içermeyebilir veya yanıt tarayıcıda *500 Dahili Sunucu Hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="f63ef-140">Uygulama Olay Günlüğü genellikle uygulamanın normal olarak başladığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="f63ef-141">Sunucunun bakış açısından, bu doğru.</span><span class="sxs-lookup"><span data-stu-id="f63ef-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="f63ef-142">Uygulama başladı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="f63ef-143">Uygulamayı sunucudaki bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Çekirdek Modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="f63ef-144">500.0 İşlem Içi İşleyici Yük Arızası</span><span class="sxs-lookup"><span data-stu-id="f63ef-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="f63ef-145">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-145">The worker process fails.</span></span> <span data-ttu-id="f63ef-146">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-146">The app doesn't start.</span></span>

<span data-ttu-id="f63ef-147">[Core Module bileşenleriASP.NET](xref:host-and-deploy/aspnet-core-module) yüklemede bilinmeyen bir hata oluştu.</span><span class="sxs-lookup"><span data-stu-id="f63ef-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="f63ef-148">Aşağıdaki eylemlerden birini uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-148">Take one of the following actions:</span></span>

* <span data-ttu-id="f63ef-149">[Microsoft Desteği'ne](https://support.microsoft.com/oas/default.aspx?prid=15832) başvurun **(Geliştirici Araçları'nı** seçin ve **ardından ASP.NET Core' u**seçin).</span><span class="sxs-lookup"><span data-stu-id="f63ef-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="f63ef-150">Stack Taşma hakkında bir soru sorun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="f63ef-151">[GitHub depomuzda](https://github.com/dotnet/AspNetCore)bir sorun dosyala.</span><span class="sxs-lookup"><span data-stu-id="f63ef-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="f63ef-152">500.30 İşlem Içi Başlatma Hatası</span><span class="sxs-lookup"><span data-stu-id="f63ef-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="f63ef-153">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-153">The worker process fails.</span></span> <span data-ttu-id="f63ef-154">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-154">The app doesn't start.</span></span>

<span data-ttu-id="f63ef-155">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR'yi başlatmaya çalışır, ancak başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="f63ef-156">İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="f63ef-157">Yaygın arıza koşulları:</span><span class="sxs-lookup"><span data-stu-id="f63ef-157">Common failure conditions:</span></span>

* <span data-ttu-id="f63ef-158">Uygulama, ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="f63ef-159">hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="f63ef-160">Azure Key Vault'u kullanarak, Anahtar Kasası'na izin verilmemesi.</span><span class="sxs-lookup"><span data-stu-id="f63ef-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="f63ef-161">Doğru izinlerin verildiğinden emin olmak için hedeflenen Anahtar Kasası'ndaki erişim ilkelerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="f63ef-162">500.31 ANCM Yerel Bağımlılıkları Bulamayı Başaramadı</span><span class="sxs-lookup"><span data-stu-id="f63ef-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="f63ef-163">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-163">The worker process fails.</span></span> <span data-ttu-id="f63ef-164">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-164">The app doesn't start.</span></span>

<span data-ttu-id="f63ef-165">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) .NET Core çalışma süresini başlatmaya çalışır, ancak başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="f63ef-166">Bu başlangıç hatasının en yaygın `Microsoft.NETCore.App` nedeni, çalışma `Microsoft.AspNetCore.App` zamanının yüklenmemesidir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="f63ef-167">Uygulama Core 3.0 ASP.NET hedeflemek üzere dağıtılırsa ve bu sürüm makinede yoksa, bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="f63ef-168">Örnek bir hata iletisi aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="f63ef-169">Hata iletisi, yüklenen tüm .NET Core sürümlerini ve uygulama tarafından istenen sürümü listeler.</span><span class="sxs-lookup"><span data-stu-id="f63ef-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="f63ef-170">Bu hatayı düzeltmek için aşağıdakileri de:</span><span class="sxs-lookup"><span data-stu-id="f63ef-170">To fix this error, either:</span></span>

* <span data-ttu-id="f63ef-171">Makineye .NET Core'un uygun sürümünü yükleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="f63ef-172">Uygulamayı makinede bulunan .NET Core sürümünü hedef almak için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="f63ef-173">Uygulamayı [bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)olarak yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="f63ef-174">Geliştirme aşamasında çalışırken `ASPNETCORE_ENVIRONMENT` (ortam değişkeni `Development`ayarlanır), belirli bir hata HTTP yanıtına yazılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="f63ef-175">İşlem başlatma hatasının nedeni, Uygulama Olayı Günlüğü'nde de bulunur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="f63ef-176">500.32 ANCM dll Yüklemek için başarısız oldu</span><span class="sxs-lookup"><span data-stu-id="f63ef-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="f63ef-177">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-177">The worker process fails.</span></span> <span data-ttu-id="f63ef-178">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-178">The app doesn't start.</span></span>

<span data-ttu-id="f63ef-179">Bu hatanın en yaygın nedeni, uygulamanın uyumsuz bir işlemci mimarisi için yayımlanmış olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="f63ef-180">Alt işlem 32 bit uygulama olarak çalışıyorsa ve uygulama 64 bit hedef olarak yayımlanmışsa, bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="f63ef-181">Bu hatayı düzeltmek için aşağıdakileri de:</span><span class="sxs-lookup"><span data-stu-id="f63ef-181">To fix this error, either:</span></span>

* <span data-ttu-id="f63ef-182">Alt işlemle aynı işlemci mimarisi için uygulamayı yeniden yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="f63ef-183">Uygulamayı [çerçeveye bağımlı dağıtım](/dotnet/core/deploying/#framework-dependent-executables-fde)olarak yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="f63ef-184">500.33 ANCM İstek Işleyici Yük Arızası</span><span class="sxs-lookup"><span data-stu-id="f63ef-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="f63ef-185">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-185">The worker process fails.</span></span> <span data-ttu-id="f63ef-186">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-186">The app doesn't start.</span></span>

<span data-ttu-id="f63ef-187">Uygulama çerçeveye `Microsoft.AspNetCore.App` atıfta bulunmuyordu.</span><span class="sxs-lookup"><span data-stu-id="f63ef-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="f63ef-188">Yalnızca çerçeveyi `Microsoft.AspNetCore.App` hedefleyen uygulamalar ASP.NET [Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)tarafından barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="f63ef-189">Bu hatayı düzeltmek için, uygulamanın `Microsoft.AspNetCore.App` çerçeveyi hedeflediğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="f63ef-190">Uygulama `.runtimeconfig.json` tarafından hedeflenen çerçeveyi doğrulamak için kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="f63ef-191">500.34 ANCM Karma Hosting Modelleri Desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="f63ef-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="f63ef-192">Alt işlem, aynı işlemde hem bir işlem içi uygulama hem de işlem dışı bir uygulamayı çalıştıramaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="f63ef-193">Bu hatayı düzeltmek için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="f63ef-194">500.35 AYNı İşlemde ANCM Çoklu İşlem Uygulamaları</span><span class="sxs-lookup"><span data-stu-id="f63ef-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="f63ef-195">Alt işlem aynı işlemde birden çok işlem içi uygulama çalıştıramaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="f63ef-196">Bu hatayı düzeltmek için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="f63ef-197">500.36 ANCM İşlem Dışı İşleyici Yük Arızası</span><span class="sxs-lookup"><span data-stu-id="f63ef-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="f63ef-198">İşlem dışı istek işleyicisi, *aspnetcorev2_outofprocess.dll,* *aspnetcorev2.dll* dosyasının yanında değildir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="f63ef-199">Bu, [ASP.NET Çekirdek Modülü'nün](xref:host-and-deploy/aspnet-core-module)bozuk bir yüklemesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="f63ef-200">Bu hatayı gidermek için [.NET Core Hosting Paketinin](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS için) veya Visual Studio (IIS Express için) kurulumunu onarın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="f63ef-201">500.37 ANCM Başlangıç Süresi İçinde Başlayamamış</span><span class="sxs-lookup"><span data-stu-id="f63ef-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="f63ef-202">ANCM, önlenen başlangıç süresi içinde başlayamadı.</span><span class="sxs-lookup"><span data-stu-id="f63ef-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="f63ef-203">Varsayılan olarak, zaman ası 120 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="f63ef-204">Bu hata, aynı makinede çok sayıda uygulama başlatırken oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="f63ef-205">Başlangıç sırasında sunucuda CPU/Bellek kullanım artışlarını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="f63ef-206">Birden çok uygulamanın başlangıç işlemini sendemeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="f63ef-207">500.38 ANCM Uygulama DLL Bulunamadı</span><span class="sxs-lookup"><span data-stu-id="f63ef-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="f63ef-208">ANCM yürütülebilir yanında olması gereken uygulama DLL, bulmak için başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="f63ef-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="f63ef-209">Bu hata, işlem içi barındırma modelini kullanarak [tek dosyalı çalıştırılabilir](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) olarak paketlenmiş bir uygulamayı barındırırken oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="f63ef-210">İşlem içi model, ANCM'nin .NET Core uygulamasını mevcut IIS işlemine yüklemesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="f63ef-211">Bu senaryo tek dosyalı dağıtım modeli tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="f63ef-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="f63ef-212">Bu hatayı düzeltmek için uygulamanın proje dosyasında aşağıdaki **yaklaşımlardan birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="f63ef-213">MSBuild özelliğini ' ye `PublishSingleFile` `false`ayarlayarak tek dosyayayımlamayı devre dışı</span><span class="sxs-lookup"><span data-stu-id="f63ef-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="f63ef-214">`AspNetCoreHostingModel` MSBuild özelliğini ' ye ayarlayarak işlem dışı `OutOfProcess`barındırma modeline geçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="f63ef-215">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="f63ef-215">502.5 Process Failure</span></span>

<span data-ttu-id="f63ef-216">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-216">The worker process fails.</span></span> <span data-ttu-id="f63ef-217">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-217">The app doesn't start.</span></span>

<span data-ttu-id="f63ef-218">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) alt işlemi başlatmaya çalışır, ancak başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="f63ef-219">İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="f63ef-220">Yaygın bir hata koşulu, uygulamanın ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmış olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="f63ef-221">hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="f63ef-222">*Paylaşılan çerçeve,* makineye yüklenen *.dll* ve meta package gibi `Microsoft.AspNetCore.App`bir meta paketle başvurulan derlemeler kümesidir .</span><span class="sxs-lookup"><span data-stu-id="f63ef-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="f63ef-223">Metapackage başvurusu en az gerekli sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="f63ef-224">Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="f63ef-225">*502.5 İşlem Hatası* hatası sayfası, bir barındırma veya uygulama yanlış yapılandırması alt işlemin başarısız lığa neden olduğunda döndürülür:</span><span class="sxs-lookup"><span data-stu-id="f63ef-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="f63ef-226">Uygulama başlatılamamış (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="f63ef-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="f63ef-227">Uygulamanın derlemesi *(.dll)* yüklenemediği için uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="f63ef-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="f63ef-228">Bu hata, yayınlanan uygulama ile w3wp/iisexpress işlemi arasında bir bitness uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="f63ef-229">Uygulama havuzunun 32 bit ayarı doğru olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="f63ef-230">IIS Manager'ın **Uygulama Havuzları'ndaki**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="f63ef-231">**Eylemler** panelinde **Uygulama Havuzu** altında Gelişmiş **Ayarlar'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="f63ef-232">**32 Bit Uygulamaları Etkinleştir'i**Ayarlayın :</span><span class="sxs-lookup"><span data-stu-id="f63ef-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="f63ef-233">32 bit (x86) bir uygulama dağıtıyorsanız, `True`değeri .</span><span class="sxs-lookup"><span data-stu-id="f63ef-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="f63ef-234">64 bit (x64) bir uygulama dağıtıyorsanız, `False`değeri .</span><span class="sxs-lookup"><span data-stu-id="f63ef-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="f63ef-235">Proje dosyasındaki BIR `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bitliği arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="f63ef-236">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="f63ef-236">Connection reset</span></span>

<span data-ttu-id="f63ef-237">Üstbilgi gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Dahili Sunucu Hatası** göndermesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="f63ef-238">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirme sırasında bir hata oluşur oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="f63ef-239">Bu hata türü istemcide *bağlantı sıfırlama* hatası olarak görünür.</span><span class="sxs-lookup"><span data-stu-id="f63ef-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="f63ef-240">[Uygulama günlüğe kaydetme,](xref:fundamentals/logging/index) bu tür hataları gidermede yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="f63ef-241">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="f63ef-241">Default startup limits</span></span>

<span data-ttu-id="f63ef-242">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) 120 saniyelik varsayılan *başlangıçTimeLimit* ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="f63ef-243">Varsayılan değerde bırakıldığında, bir uygulamanın bir işlem hatası günlüğe kaydetmesi iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="f63ef-244">Modülü yapılandırma hakkında daha fazla bilgi için [aspNetCore öğesinin Öznitelikleri'ne](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="f63ef-245">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="f63ef-246">Uygulama Etkinlik Günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="f63ef-247">Uygulama Olay Günlüğü'ne erişmek için Azure portalındaki **Tanılama ve sorunları çözme** sorununu kullanın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="f63ef-248">Azure portalında Uygulamayı Uygulama **Hizmetleri'nde**açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="f63ef-249">**Sorunları tanılama ve çözme** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="f63ef-250">**Tanılama Araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="f63ef-251">**Destek Araçları** **altında, Uygulama Etkinlikleri** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="f63ef-252">**Kaynak** sütundaki *IIS AspNetCoreModule* veya *IIS AspNetCoreModule V2* girişi tarafından sağlanan en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="f63ef-253">**Tanılama ve sorunları çözmenin** alternatifi, Uygulama Olay Günlüğü dosyasını doğrudan [Kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="f63ef-254">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="f63ef-255">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-256">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-257">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-258">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="f63ef-259">*eventlog.xml* dosyasının yanındaki kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="f63ef-260">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-260">Examine the log.</span></span> <span data-ttu-id="f63ef-261">En son olayları görmek için günlüğün altına gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="f63ef-262">Uygulamayı Kudu konsolunda çalıştırın</span><span class="sxs-lookup"><span data-stu-id="f63ef-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="f63ef-263">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="f63ef-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="f63ef-264">Hatayı bulmak için uygulamayı [Kudu](https://github.com/projectkudu/kudu/wiki) Uzaktan Yürütme Konsolu'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f63ef-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="f63ef-265">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="f63ef-266">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-267">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-268">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="f63ef-269">32 bit (x86) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f63ef-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="f63ef-270">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="f63ef-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="f63ef-271">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-271">Run the app:</span></span>
   * <span data-ttu-id="f63ef-272">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="f63ef-273">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="f63ef-274">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="f63ef-275">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="f63ef-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="f63ef-276">*Core {VERSION} (x86) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="f63ef-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="f63ef-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="f63ef-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="f63ef-278">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="f63ef-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="f63ef-279">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="f63ef-280">64 bit (x64) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f63ef-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="f63ef-281">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="f63ef-281">**Current release**</span></span>

* <span data-ttu-id="f63ef-282">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="f63ef-283">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="f63ef-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="f63ef-284">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="f63ef-285">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="f63ef-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="f63ef-286">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="f63ef-287">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="f63ef-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="f63ef-288">*Core {VERSION} (x64) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="f63ef-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="f63ef-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="f63ef-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="f63ef-290">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="f63ef-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="f63ef-291">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="f63ef-292">ASP.NET Çekirdek Modül stdout günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="f63ef-293">ASP.NET Çekirdek Modülü stdout günlüğü genellikle Uygulama Olay Günlüğü'nde bulunmayan yararlı hata iletileri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="f63ef-293">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="f63ef-294">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-294">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="f63ef-295">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-295">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="f63ef-296">**PROBLEM KATEGORİsİ SEÇ'in** **altında, Web Uygulaması Aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-296">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="f63ef-297">**Önerilen Çözümler** > Altında **Stdout Log Redirection etkinleştirin**, **Web.Config'i yeniden etkinleştirmek için Kudu Konsolu'nu açın**düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-297">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="f63ef-298">Kudu **Tanı Konsolu,** yol **sitesi** > **wwwroot**klasörleri açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-298">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="f63ef-299">Listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-299">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="f63ef-300">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-300">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-301">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-301">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="f63ef-302">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-302">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-303">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-303">Make a request to the app.</span></span>
1. <span data-ttu-id="f63ef-304">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="f63ef-304">Return to the Azure portal.</span></span> <span data-ttu-id="f63ef-305">**GELİşTİrME ARAÇLARI** alanında **Gelişmiş Araçlar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-305">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="f63ef-306">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-306">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-307">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-307">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-308">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-308">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-309">Günlük **Dosyaları** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-309">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="f63ef-310">**Değiştirilen** sütunu inceleyin ve en son değişiklik tarihiyle birlikte stdout günlüğünü deletmek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-310">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="f63ef-311">Günlük dosyası açıldığında, hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-311">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="f63ef-312">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-312">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="f63ef-313">Kudu **Tanı Konsolu,** *web.config* dosyasını ortaya çıkarmak için yol **sitesi** > **wwwroot** dönün.</span><span class="sxs-lookup"><span data-stu-id="f63ef-313">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="f63ef-314">Kalem simgesini seçerek **web.config** dosyasını yeniden açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-314">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="f63ef-315">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="f63ef-315">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="f63ef-316">Dosyayı kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-316">Select **Save** to save the file.</span></span>

<span data-ttu-id="f63ef-317">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f63ef-317">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-318">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-318">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-319">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-319">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="f63ef-320">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlük kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-320">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="f63ef-321">Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-321">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-322">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-322">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="f63ef-323">ASP.NET Çekirdek Modül hata ayıklama günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-323">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="f63ef-324">ASP.NET Çekirdek Modülü hata ayıklama günlüğü, ASP.NET Çekirdek Modülünden ek, daha derin günlüğe kaydetme sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-324">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="f63ef-325">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-325">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="f63ef-326">Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-326">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="f63ef-327">Gelişmiş bir tanı günlüğü için uygulamayı yapılandırmak için [Gelişmiş tanı günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-327">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="f63ef-328">Uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-328">Redeploy the app.</span></span>
   * <span data-ttu-id="f63ef-329">Kudu `<handlerSettings>` konsolu kullanarak canlı uygulamanın *web.config* dosyasına [Gelişmiş tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilenleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-329">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="f63ef-330">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="f63ef-331">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-332">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-332">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="f63ef-333">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="f63ef-334">Klasörleri yol **sitesine** > aç**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="f63ef-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="f63ef-335">Kalem düğmesini seçerek *web.config* dosyasını düzenleme.</span><span class="sxs-lookup"><span data-stu-id="f63ef-335">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="f63ef-336">Gelişmiş `<handlerSettings>` [tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi bölümü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-336">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="f63ef-337">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-337">Select the **Save** button.</span></span>
1. <span data-ttu-id="f63ef-338">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-338">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="f63ef-339">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-339">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-340">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-340">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-341">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-341">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-342">Klasörleri yol **sitesine** > aç**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="f63ef-342">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="f63ef-343">*aspnetcore-debug.log* dosyası için bir yol sağlamadıysanız, dosya listede görünür.</span><span class="sxs-lookup"><span data-stu-id="f63ef-343">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="f63ef-344">Bir yol sağladıysanız, günlük dosyasının konumuna gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-344">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="f63ef-345">Dosya adının yanındaki kalem düğmesiyle günlük dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-345">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="f63ef-346">Sorun giderme tamamlandığında hata ayıklama günlemasını devre dışı kındırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-346">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="f63ef-347">Gelişmiş hata ayıklama günlüğünü devre dışı kalmak için aşağıdakilerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-347">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="f63ef-348">`<handlerSettings>` *Web.config* dosyasını yerel olarak kaldırın ve uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-348">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="f63ef-349">*Web.config* dosyasını ve bölümü kaldırmak için Kudu konsoluna `<handlerSettings>` bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-349">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="f63ef-350">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-350">Save the file.</span></span>

<span data-ttu-id="f63ef-351">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="f63ef-351">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-352">Hata ayıklama günlüğünün devre dışı edilmemesi uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-352">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-353">Günlük dosyası boyutunda bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-353">There's no limit on log file size.</span></span> <span data-ttu-id="f63ef-354">Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğe kaydetmeyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-354">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="f63ef-355">Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-355">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-356">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-356">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="f63ef-357">Yavaş veya asma uygulaması (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-357">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="f63ef-358">Bir uygulama yavaş yanıt verdiğinde veya bir isteğe bağlı kaldığında aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-358">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="f63ef-359">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-359">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="f63ef-360">Azure Web Uygulamasında Aralıklı Özel Durum sorunları veya performans sorunları için Döküm'ü yakalamak için Kilitlenme Tanılayıcı Site Uzantısı'nı kullanma</span><span class="sxs-lookup"><span data-stu-id="f63ef-360">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="f63ef-361">İzleme bıçakları</span><span class="sxs-lookup"><span data-stu-id="f63ef-361">Monitoring blades</span></span>

<span data-ttu-id="f63ef-362">İzleme bıçakları, konunun daha önce açıklanan yöntemlerine alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-362">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="f63ef-363">Bu bıçaklar 500 seri hataları teşhis etmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-363">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="f63ef-364">ASP.NET Çekirdek Uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-364">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="f63ef-365">Uzantılar yüklenmiyorsa, bunları el ile yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-365">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="f63ef-366">**GELİşİm ARAÇLARI** bıçak bölümünde **Uzantılar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-366">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="f63ef-367">**ASP.NET Çekirdek Uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-367">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="f63ef-368">Uzantılar yüklenmiyorsa **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-368">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="f63ef-369">Listeden **ASP.NET Çekirdek Uzantıları'nı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-369">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="f63ef-370">Yasal koşulları kabul etmek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-370">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="f63ef-371">**Ekle uzantı** bıçağında **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-371">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="f63ef-372">Bilgilendirimiolan bir açılır ileti, uzantıların başarıyla ne zaman yüklenir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-372">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="f63ef-373">Stdout günlüğe kaydetme etkin değilse, aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-373">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="f63ef-374">Azure portalında, **GELIŞTIRME ARAÇLARı** alanındaki **Gelişmiş Araçlar** bıçak larını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-374">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="f63ef-375">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-375">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-376">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-376">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-377">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-377">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-378">Klasörleri yol **sitesi** > **wwwroot'a** açın ve listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-378">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="f63ef-379">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-379">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-380">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-380">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="f63ef-381">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-381">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="f63ef-382">Tanısal günlüğü etkinleştirmeye devam edin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-382">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="f63ef-383">Azure portalında **Tanılama günlükleri** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-383">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="f63ef-384">**Uygulama Günlüğe Kaydetme (Filesystem)** ve **Ayrıntılı hata iletileri**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-384">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="f63ef-385">Bıçağın üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-385">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="f63ef-386">Başarısız İstek Olay Arabelleği (FREB) günlüğe kaydetme olarak da bilinen başarısız istek izlemesini eklemek için, **Başarısız istek izleme**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-386">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="f63ef-387">Portaldaki **Teşhis günlükleri** bıçağının hemen altında listelenen **Log akış** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-387">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="f63ef-388">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-388">Make a request to the app.</span></span>
1. <span data-ttu-id="f63ef-389">Günlük akışı verileri içinde, hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-389">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="f63ef-390">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı bettiğinizden emin olun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-390">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="f63ef-391">Başarısız istek izleme günlüklerini (FREB günlükleri) görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-391">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="f63ef-392">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-392">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="f63ef-393">Kenar çubuğunun **DESTEK ARAÇLARI** alanından **Başarısız İstek İzleme Günlükleri'ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-393">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="f63ef-394">Azure Uygulama Hizmeti konusundaki web uygulamaları için etkinleştir meslüpleri oturum açma [bölümünün başarısız istek izlemeleri bölümüne](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure'daki Web Apps Uygulamaları için Uygulama performansı SSS'lerine bakın: Daha](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) fazla bilgi için başarısız istek izlemesini nasıl açarım?</span><span class="sxs-lookup"><span data-stu-id="f63ef-394">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="f63ef-395">Daha fazla bilgi için azure [Uygulama Hizmeti'ndeki web uygulamaları için tanılama günlüğe kaydetme'ye](/azure/app-service/web-sites-enable-diagnostic-log)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-395">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-396">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-396">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-397">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-397">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="f63ef-398">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-398">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-399">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-399">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="f63ef-400">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-400">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="f63ef-401">Uygulama Etkinlik Günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-401">Application Event Log (IIS)</span></span>

<span data-ttu-id="f63ef-402">Uygulama Etkinlik Günlüğüne Erişin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-402">Access the Application Event Log:</span></span>

1. <span data-ttu-id="f63ef-403">Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-403">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="f63ef-404">**Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-404">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="f63ef-405">Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-405">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="f63ef-406">Başarısız uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-406">Search for errors associated with the failing app.</span></span> <span data-ttu-id="f63ef-407">Hatalar *Kaynak* sütunda *IIS AspNetCore Modülü* veya *IIS Express AspNetCore Modülü* değerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-407">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="f63ef-408">Uygulamayı komut istemiyle çalıştırma</span><span class="sxs-lookup"><span data-stu-id="f63ef-408">Run the app at a command prompt</span></span>

<span data-ttu-id="f63ef-409">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="f63ef-409">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="f63ef-410">Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-410">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="f63ef-411">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="f63ef-411">Framework-dependent deployment</span></span>

<span data-ttu-id="f63ef-412">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-412">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="f63ef-413">Komut isteminde dağıtım klasörüne gidin ve uygulamanın montajını *dotnet.exe*ile çalıştırarak uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-413">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="f63ef-414">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `dotnet .\<assembly_name>.dll`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="f63ef-415">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="f63ef-416">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="f63ef-417">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="f63ef-418">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="f63ef-419">Bağımsız dağıtım</span><span class="sxs-lookup"><span data-stu-id="f63ef-419">Self-contained deployment</span></span>

<span data-ttu-id="f63ef-420">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-420">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="f63ef-421">Komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir uygulamasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-421">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="f63ef-422">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `<assembly_name>.exe`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-422">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="f63ef-423">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-423">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="f63ef-424">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-424">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="f63ef-425">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-425">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="f63ef-426">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-426">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="f63ef-427">ASP.NET Çekirdek Modülü stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-427">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="f63ef-428">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-428">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="f63ef-429">Barındırma sisteminde sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-429">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="f63ef-430">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-430">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="f63ef-431">MSBuild'in dağıtımdaki *günlükler* klasörünü otomatik olarak oluşturmasına nasıl etkinleştirilen talimatlar için [Dizin yapısı](xref:host-and-deploy/directory-structure) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-431">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="f63ef-432">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-432">Edit the *web.config* file.</span></span> <span data-ttu-id="f63ef-433">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** yolunu *günlükler* klasörüne (örneğin) `.\logs\stdout`işaret etmek için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-433">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="f63ef-434">`stdout`yoldaki günlük dosya adı önekidir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-434">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="f63ef-435">Günlük oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-435">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="f63ef-436">Dosya `stdout` adı öneki olarak kullanarak, tipik bir günlük dosyası *stdout_20180205184032_5412.log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-436">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="f63ef-437">Uygulama havuzunuzun kimliğinin *günlükler* klasörüne yazma izinleri olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-437">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="f63ef-438">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-438">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-439">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-439">Make a request to the app.</span></span>
1. <span data-ttu-id="f63ef-440">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-440">Navigate to the *logs* folder.</span></span> <span data-ttu-id="f63ef-441">En son stdout günlüğünü bulun ve açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-441">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="f63ef-442">Hatalar için günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-442">Study the log for errors.</span></span>

<span data-ttu-id="f63ef-443">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-443">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="f63ef-444">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-444">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-445">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="f63ef-445">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="f63ef-446">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-446">Save the file.</span></span>

<span data-ttu-id="f63ef-447">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f63ef-447">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-448">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-448">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-449">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-449">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="f63ef-450">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-450">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-451">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-451">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="f63ef-452">ASP.NET Çekirdek Modülü hata ayıklama günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-452">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="f63ef-453">Temel Modül hata ayıklama günlüğünü ASP.NET etkinleştirmek için uygulamanın *web.config* dosyasına aşağıdaki işleyici ayarlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-453">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="f63ef-454">Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-454">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="f63ef-455">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="f63ef-455">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="f63ef-456">Geliştirici Özel Durum Sayfasını Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f63ef-456">Enable the Developer Exception Page</span></span>

<span data-ttu-id="f63ef-457">Ortamı `ASPNETCORE_ENVIRONMENT` geliştirme ortamında çalıştırmak için [web.config'e ortam değişkeni eklenebilir.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="f63ef-457">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="f63ef-458">Ortam, ana bilgisayar `UseEnvironment` oluşturucutarafından uygulama başlatmada geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında Geliştirici Özel Durum [Sayfasının](xref:fundamentals/error-handling) görünmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-458">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="f63ef-459">Ortam değişkenini `ASPNETCORE_ENVIRONMENT` ayarlamak yalnızca Internet'e maruz olmayan hazırlama ve sınama sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-459">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="f63ef-460">Sorun giderme den sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-460">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="f63ef-461">*web.config'de*ortam değişkenlerini ayarlama hakkında bilgi için [bkz.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="f63ef-461">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="f63ef-462">Bir uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="f63ef-462">Obtain data from an app</span></span>

<span data-ttu-id="f63ef-463">Bir uygulama isteklere yanıt verebiliyorsa, terminal sıralı ara yazılımları kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-463">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="f63ef-464">Daha fazla bilgi ve <xref:test/troubleshoot#obtain-data-from-an-app>örnek kod için bkz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-464">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="f63ef-465">Yavaş veya asılı uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-465">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="f63ef-466">*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-466">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="f63ef-467">Uygulama çöküyor veya bir özel durumla karşılaşıyor</span><span class="sxs-lookup"><span data-stu-id="f63ef-467">App crashes or encounters an exception</span></span>

<span data-ttu-id="f63ef-468">Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:</span><span class="sxs-lookup"><span data-stu-id="f63ef-468">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="f63ef-469">Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun</span><span class="sxs-lookup"><span data-stu-id="f63ef-469">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="f63ef-470">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-470">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="f63ef-471">[EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-471">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="f63ef-472">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-472">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="f63ef-473">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-473">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="f63ef-474">Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-474">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="f63ef-475">Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-475">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="f63ef-476">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-476">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="f63ef-477">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-477">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="f63ef-478">Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-478">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="f63ef-479">PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-479">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-480">Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).</span><span class="sxs-lookup"><span data-stu-id="f63ef-480">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="f63ef-481">Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır</span><span class="sxs-lookup"><span data-stu-id="f63ef-481">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="f63ef-482">Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)</span><span class="sxs-lookup"><span data-stu-id="f63ef-482">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="f63ef-483">Dökümü analiz edin</span><span class="sxs-lookup"><span data-stu-id="f63ef-483">Analyze the dump</span></span>

<span data-ttu-id="f63ef-484">Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-484">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="f63ef-485">Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)</span><span class="sxs-lookup"><span data-stu-id="f63ef-485">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="f63ef-486">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="f63ef-486">Clear package caches</span></span>

<span data-ttu-id="f63ef-487">İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-487">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="f63ef-488">Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-488">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="f63ef-489">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-489">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="f63ef-490">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-490">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="f63ef-491">[Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-491">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="f63ef-492">Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-492">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="f63ef-493">*nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-493">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="f63ef-494">Projeyi geri yükleyin ve yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-494">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="f63ef-495">Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-495">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f63ef-496">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f63ef-496">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="f63ef-497">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="f63ef-497">Azure documentation</span></span>

* [<span data-ttu-id="f63ef-498">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="f63ef-498">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="f63ef-499">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme bölümünün uzaktan hata ayıklama web uygulamaları bölümü</span><span class="sxs-lookup"><span data-stu-id="f63ef-499">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="f63ef-500">Azure Uygulama Hizmeti tanılama genel bakış</span><span class="sxs-lookup"><span data-stu-id="f63ef-500">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="f63ef-501">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="f63ef-501">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="f63ef-502">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-502">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="f63ef-503">Azure web uygulamalarınızda HTTP hatalarını "502 kötü ağ geçidi" ve "503 hizmeti kullanılamıyor" hatalarını giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-503">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="f63ef-504">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-504">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="f63ef-505">Azure'daki Web Apps için Uygulama performansı SSS'leri</span><span class="sxs-lookup"><span data-stu-id="f63ef-505">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="f63ef-506">Azure Web App sandbox (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="f63ef-506">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="f63ef-507">Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="f63ef-507">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="f63ef-508">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="f63ef-508">Visual Studio documentation</span></span>

* [<span data-ttu-id="f63ef-509">Visual Studio 2017'de Azure'da IIS'de Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f63ef-509">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="f63ef-510">Visual Studio 2017'de Uzaktan IIS Bilgisayarında Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f63ef-510">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="f63ef-511">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="f63ef-511">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="f63ef-512">Visual Studio Code dokümantasyonu</span><span class="sxs-lookup"><span data-stu-id="f63ef-512">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="f63ef-513">Visual Studio Code ile Hata Ayıklama</span><span class="sxs-lookup"><span data-stu-id="f63ef-513">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f63ef-514">Bu makalede, bir uygulama Azure Uygulama Hizmeti veya IIS'ye dağıtıldığında hataların nasıl tanılanacağına ilişkin yaygın uygulama başlangıç hataları ve yönergeler hakkında bilgiler verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-514">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="f63ef-515">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="f63ef-515">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="f63ef-516">Ortak başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-516">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="f63ef-517">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-517">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="f63ef-518">Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-518">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="f63ef-519">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-519">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="f63ef-520">IIS'ye dağıtılan veya IIS Express'te yerel olarak çalışan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-520">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="f63ef-521">Kılavuz, hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-521">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="f63ef-522">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="f63ef-522">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="f63ef-523">Büyük yükseltmeler yaparken veya paket sürümlerini değiştirirken tutarsız paketler bir uygulamayı kırdığında ne yapması gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-523">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="f63ef-524">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f63ef-524">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="f63ef-525">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="f63ef-525">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="f63ef-526">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="f63ef-526">App startup errors</span></span>

<span data-ttu-id="f63ef-527">Visual Studio'da, ASP.NET Core projesi hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırma için varsayılandır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-527">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="f63ef-528">A *502.5 - İşlem Hatası* veya *500.30 -* Yerel hata ayıklama zaman ortaya çıkan Başlat Hatası bu konuda ki tavsiye kullanılarak teşhis edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-528">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="f63ef-529">403.14 Yasak</span><span class="sxs-lookup"><span data-stu-id="f63ef-529">403.14 Forbidden</span></span>

<span data-ttu-id="f63ef-530">Uygulama başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-530">The app fails to start.</span></span> <span data-ttu-id="f63ef-531">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-531">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="f63ef-532">Hata genellikle aşağıdaki senaryolardan herhangi birini içeren barındırma sisteminde ki bozuk dağıtımdan kaynaklanır:</span><span class="sxs-lookup"><span data-stu-id="f63ef-532">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="f63ef-533">Uygulama barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-533">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="f63ef-534">Dağıtım işlemi, uygulamanın tüm dosya ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="f63ef-534">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="f63ef-535">*web.config* dosyası dağıtımda eksik veya *web.config* dosyası içeriği yanlış biçimlendirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-535">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="f63ef-536">Aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-536">Perform the following steps:</span></span>

1. <span data-ttu-id="f63ef-537">Barındırma sistemindeki dağıtım klasöründeki tüm dosya ve klasörleri silin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-537">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="f63ef-538">Visual Studio, PowerShell veya manuel dağıtım gibi normal dağıtım yönteminizi kullanarak uygulamanın *yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-538">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="f63ef-539">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-539">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="f63ef-540">Azure Uygulama Hizmeti'nde barındırma yaparken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-540">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="f63ef-541">Uygulama IIS tarafından barındırıldığında, uygulamanın **IIS Yöneticisi'nin**Temel **Ayarları'nda**gösterilen IIS **Fiziksel yoluna** dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-541">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="f63ef-542">Barındırma sistemindeki dağıtımı projenin *yayımlama* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtılmış olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-542">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="f63ef-543">Yayınlanan bir ASP.NET Core uygulamasının düzeni hakkında <xref:host-and-deploy/directory-structure>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-543">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="f63ef-544">*web.config* dosyasında daha fazla <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-544">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="f63ef-545">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="f63ef-545">500 Internal Server Error</span></span>

<span data-ttu-id="f63ef-546">Uygulama başlar, ancak bir hata sunucunun isteği yerine getirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="f63ef-546">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="f63ef-547">Bu hata, başlangıç sırasında veya yanıt oluştururken uygulamanın kodu içinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-547">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="f63ef-548">Yanıt hiçbir içerik içermeyebilir veya yanıt tarayıcıda *500 Dahili Sunucu Hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-548">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="f63ef-549">Uygulama Olay Günlüğü genellikle uygulamanın normal olarak başladığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-549">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="f63ef-550">Sunucunun bakış açısından, bu doğru.</span><span class="sxs-lookup"><span data-stu-id="f63ef-550">From the server's perspective, that's correct.</span></span> <span data-ttu-id="f63ef-551">Uygulama başladı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-551">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="f63ef-552">Uygulamayı sunucudaki bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Çekirdek Modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-552">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="f63ef-553">500.0 İşlem Içi İşleyici Yük Arızası</span><span class="sxs-lookup"><span data-stu-id="f63ef-553">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="f63ef-554">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-554">The worker process fails.</span></span> <span data-ttu-id="f63ef-555">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-555">The app doesn't start.</span></span>

<span data-ttu-id="f63ef-556">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR'yi ve işlem içi istek işleyicisini *(aspnetcorev2_inprocess.dll)* bulamaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-556">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="f63ef-557">Şuna bakın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-557">Check that:</span></span>

* <span data-ttu-id="f63ef-558">Uygulama ya [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet paketi veya [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)hedefliyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-558">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="f63ef-559">ASP.NET Core'un, uygulama hedeflerinin hedef makineye yüklenmiş olduğu paylaşılan çerçevesi nin sürümü.</span><span class="sxs-lookup"><span data-stu-id="f63ef-559">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="f63ef-560">500.0 İşlem Dışı İşleyici Yük Arızası</span><span class="sxs-lookup"><span data-stu-id="f63ef-560">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="f63ef-561">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-561">The worker process fails.</span></span> <span data-ttu-id="f63ef-562">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-562">The app doesn't start.</span></span>

<span data-ttu-id="f63ef-563">[ASP.NET Çekirdek Modülü,](xref:host-and-deploy/aspnet-core-module) işlem dışı barındırma isteği işleyicisini bulamaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-563">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="f63ef-564">*aspnetcorev2_outofprocess.dll'nin* *aspnetcorev2.dll'nin*yanındaki bir alt klasörde bulunduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-564">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="f63ef-565">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="f63ef-565">502.5 Process Failure</span></span>

<span data-ttu-id="f63ef-566">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-566">The worker process fails.</span></span> <span data-ttu-id="f63ef-567">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-567">The app doesn't start.</span></span>

<span data-ttu-id="f63ef-568">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) alt işlemi başlatmaya çalışır, ancak başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-568">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="f63ef-569">İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-569">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="f63ef-570">Yaygın bir hata koşulu, uygulamanın ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmış olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-570">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="f63ef-571">hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-571">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="f63ef-572">*Paylaşılan çerçeve,* makineye yüklenen *.dll* ve meta package gibi `Microsoft.AspNetCore.App`bir meta paketle başvurulan derlemeler kümesidir .</span><span class="sxs-lookup"><span data-stu-id="f63ef-572">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="f63ef-573">Metapackage başvurusu en az gerekli sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-573">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="f63ef-574">Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-574">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="f63ef-575">*502.5 İşlem Hatası* hatası sayfası, bir barındırma veya uygulama yanlış yapılandırması alt işlemin başarısız lığa neden olduğunda döndürülür:</span><span class="sxs-lookup"><span data-stu-id="f63ef-575">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="f63ef-576">Uygulama başlatılamamış (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="f63ef-576">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="f63ef-577">Uygulamanın derlemesi *(.dll)* yüklenemediği için uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="f63ef-577">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="f63ef-578">Bu hata, yayınlanan uygulama ile w3wp/iisexpress işlemi arasında bir bitness uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-578">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="f63ef-579">Uygulama havuzunun 32 bit ayarı doğru olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-579">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="f63ef-580">IIS Manager'ın **Uygulama Havuzları'ndaki**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-580">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="f63ef-581">**Eylemler** panelinde **Uygulama Havuzu** altında Gelişmiş **Ayarlar'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-581">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="f63ef-582">**32 Bit Uygulamaları Etkinleştir'i**Ayarlayın :</span><span class="sxs-lookup"><span data-stu-id="f63ef-582">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="f63ef-583">32 bit (x86) bir uygulama dağıtıyorsanız, `True`değeri .</span><span class="sxs-lookup"><span data-stu-id="f63ef-583">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="f63ef-584">64 bit (x64) bir uygulama dağıtıyorsanız, `False`değeri .</span><span class="sxs-lookup"><span data-stu-id="f63ef-584">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="f63ef-585">Proje dosyasındaki BIR `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bitliği arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-585">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="f63ef-586">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="f63ef-586">Connection reset</span></span>

<span data-ttu-id="f63ef-587">Üstbilgi gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Dahili Sunucu Hatası** göndermesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-587">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="f63ef-588">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirme sırasında bir hata oluşur oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-588">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="f63ef-589">Bu hata türü istemcide *bağlantı sıfırlama* hatası olarak görünür.</span><span class="sxs-lookup"><span data-stu-id="f63ef-589">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="f63ef-590">[Uygulama günlüğe kaydetme,](xref:fundamentals/logging/index) bu tür hataları gidermede yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-590">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="f63ef-591">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="f63ef-591">Default startup limits</span></span>

<span data-ttu-id="f63ef-592">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) 120 saniyelik varsayılan *başlangıçTimeLimit* ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-592">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="f63ef-593">Varsayılan değerde bırakıldığında, bir uygulamanın bir işlem hatası günlüğe kaydetmesi iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-593">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="f63ef-594">Modülü yapılandırma hakkında daha fazla bilgi için [aspNetCore öğesinin Öznitelikleri'ne](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-594">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="f63ef-595">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-595">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="f63ef-596">Uygulama Etkinlik Günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-596">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="f63ef-597">Uygulama Olay Günlüğü'ne erişmek için Azure portalındaki **Tanılama ve sorunları çözme** sorununu kullanın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-597">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="f63ef-598">Azure portalında Uygulamayı Uygulama **Hizmetleri'nde**açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-598">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="f63ef-599">**Sorunları tanılama ve çözme** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-599">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="f63ef-600">**Tanılama Araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-600">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="f63ef-601">**Destek Araçları** **altında, Uygulama Etkinlikleri** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-601">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="f63ef-602">**Kaynak** sütundaki *IIS AspNetCoreModule* veya *IIS AspNetCoreModule V2* girişi tarafından sağlanan en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-602">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="f63ef-603">**Tanılama ve sorunları çözmenin** alternatifi, Uygulama Olay Günlüğü dosyasını doğrudan [Kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-603">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="f63ef-604">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-604">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="f63ef-605">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-605">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-606">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-606">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-607">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-607">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-608">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-608">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="f63ef-609">*eventlog.xml* dosyasının yanındaki kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-609">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="f63ef-610">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-610">Examine the log.</span></span> <span data-ttu-id="f63ef-611">En son olayları görmek için günlüğün altına gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-611">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="f63ef-612">Uygulamayı Kudu konsolunda çalıştırın</span><span class="sxs-lookup"><span data-stu-id="f63ef-612">Run the app in the Kudu console</span></span>

<span data-ttu-id="f63ef-613">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="f63ef-613">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="f63ef-614">Hatayı bulmak için uygulamayı [Kudu](https://github.com/projectkudu/kudu/wiki) Uzaktan Yürütme Konsolu'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f63ef-614">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="f63ef-615">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-615">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="f63ef-616">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-616">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-617">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-617">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-618">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-618">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="f63ef-619">32 bit (x86) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f63ef-619">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="f63ef-620">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="f63ef-620">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="f63ef-621">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-621">Run the app:</span></span>
   * <span data-ttu-id="f63ef-622">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-622">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="f63ef-623">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-623">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="f63ef-624">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-624">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="f63ef-625">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="f63ef-625">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="f63ef-626">*Core {VERSION} (x86) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="f63ef-626">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="f63ef-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="f63ef-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="f63ef-628">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="f63ef-628">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="f63ef-629">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-629">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="f63ef-630">64 bit (x64) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f63ef-630">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="f63ef-631">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="f63ef-631">**Current release**</span></span>

* <span data-ttu-id="f63ef-632">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-632">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="f63ef-633">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="f63ef-633">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="f63ef-634">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-634">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="f63ef-635">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="f63ef-635">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="f63ef-636">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-636">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="f63ef-637">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="f63ef-637">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="f63ef-638">*Core {VERSION} (x64) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="f63ef-638">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="f63ef-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="f63ef-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="f63ef-640">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="f63ef-640">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="f63ef-641">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-641">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="f63ef-642">ASP.NET Çekirdek Modül stdout günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-642">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="f63ef-643">ASP.NET Çekirdek Modülü stdout günlüğü genellikle Uygulama Olay Günlüğü'nde bulunmayan yararlı hata iletileri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="f63ef-643">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="f63ef-644">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-644">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="f63ef-645">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-645">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="f63ef-646">**PROBLEM KATEGORİsİ SEÇ'in** **altında, Web Uygulaması Aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-646">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="f63ef-647">**Önerilen Çözümler** > Altında **Stdout Log Redirection etkinleştirin**, **Web.Config'i yeniden etkinleştirmek için Kudu Konsolu'nu açın**düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-647">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="f63ef-648">Kudu **Tanı Konsolu,** yol **sitesi** > **wwwroot**klasörleri açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-648">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="f63ef-649">Listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-649">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="f63ef-650">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-650">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-651">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-651">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="f63ef-652">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-652">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-653">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-653">Make a request to the app.</span></span>
1. <span data-ttu-id="f63ef-654">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="f63ef-654">Return to the Azure portal.</span></span> <span data-ttu-id="f63ef-655">**GELİşTİrME ARAÇLARI** alanında **Gelişmiş Araçlar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-655">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="f63ef-656">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-656">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-657">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-657">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-658">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-658">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-659">Günlük **Dosyaları** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-659">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="f63ef-660">**Değiştirilen** sütunu inceleyin ve en son değişiklik tarihiyle birlikte stdout günlüğünü deletmek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-660">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="f63ef-661">Günlük dosyası açıldığında, hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-661">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="f63ef-662">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-662">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="f63ef-663">Kudu **Tanı Konsolu,** *web.config* dosyasını ortaya çıkarmak için yol **sitesi** > **wwwroot** dönün.</span><span class="sxs-lookup"><span data-stu-id="f63ef-663">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="f63ef-664">Kalem simgesini seçerek **web.config** dosyasını yeniden açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-664">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="f63ef-665">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="f63ef-665">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="f63ef-666">Dosyayı kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-666">Select **Save** to save the file.</span></span>

<span data-ttu-id="f63ef-667">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f63ef-667">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-668">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-668">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-669">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-669">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="f63ef-670">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlük kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-670">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="f63ef-671">Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-671">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-672">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-672">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="f63ef-673">ASP.NET Çekirdek Modül hata ayıklama günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-673">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="f63ef-674">ASP.NET Çekirdek Modülü hata ayıklama günlüğü, ASP.NET Çekirdek Modülünden ek, daha derin günlüğe kaydetme sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-674">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="f63ef-675">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-675">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="f63ef-676">Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-676">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="f63ef-677">Gelişmiş bir tanı günlüğü için uygulamayı yapılandırmak için [Gelişmiş tanı günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-677">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="f63ef-678">Uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-678">Redeploy the app.</span></span>
   * <span data-ttu-id="f63ef-679">Kudu `<handlerSettings>` konsolu kullanarak canlı uygulamanın *web.config* dosyasına [Gelişmiş tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilenleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-679">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="f63ef-680">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="f63ef-681">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-682">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-682">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="f63ef-683">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="f63ef-684">Klasörleri yol **sitesine** > aç**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="f63ef-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="f63ef-685">Kalem düğmesini seçerek *web.config* dosyasını düzenleme.</span><span class="sxs-lookup"><span data-stu-id="f63ef-685">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="f63ef-686">Gelişmiş `<handlerSettings>` [tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi bölümü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-686">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="f63ef-687">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-687">Select the **Save** button.</span></span>
1. <span data-ttu-id="f63ef-688">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-688">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="f63ef-689">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-689">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-690">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-690">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-691">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-691">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-692">Klasörleri yol **sitesine** > aç**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="f63ef-692">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="f63ef-693">*aspnetcore-debug.log* dosyası için bir yol sağlamadıysanız, dosya listede görünür.</span><span class="sxs-lookup"><span data-stu-id="f63ef-693">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="f63ef-694">Bir yol sağladıysanız, günlük dosyasının konumuna gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-694">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="f63ef-695">Dosya adının yanındaki kalem düğmesiyle günlük dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-695">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="f63ef-696">Sorun giderme tamamlandığında hata ayıklama günlemasını devre dışı kındırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-696">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="f63ef-697">Gelişmiş hata ayıklama günlüğünü devre dışı kalmak için aşağıdakilerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-697">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="f63ef-698">`<handlerSettings>` *Web.config* dosyasını yerel olarak kaldırın ve uygulamayı yeniden dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-698">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="f63ef-699">*Web.config* dosyasını ve bölümü kaldırmak için Kudu konsoluna `<handlerSettings>` bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-699">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="f63ef-700">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-700">Save the file.</span></span>

<span data-ttu-id="f63ef-701">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="f63ef-701">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-702">Hata ayıklama günlüğünün devre dışı edilmemesi uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-702">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-703">Günlük dosyası boyutunda bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-703">There's no limit on log file size.</span></span> <span data-ttu-id="f63ef-704">Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğe kaydetmeyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-704">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="f63ef-705">Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-705">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-706">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-706">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="f63ef-707">Yavaş veya asma uygulaması (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-707">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="f63ef-708">Bir uygulama yavaş yanıt verdiğinde veya bir isteğe bağlı kaldığında aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-708">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="f63ef-709">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-709">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="f63ef-710">Azure Web Uygulamasında Aralıklı Özel Durum sorunları veya performans sorunları için Döküm'ü yakalamak için Kilitlenme Tanılayıcı Site Uzantısı'nı kullanma</span><span class="sxs-lookup"><span data-stu-id="f63ef-710">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="f63ef-711">İzleme bıçakları</span><span class="sxs-lookup"><span data-stu-id="f63ef-711">Monitoring blades</span></span>

<span data-ttu-id="f63ef-712">İzleme bıçakları, konunun daha önce açıklanan yöntemlerine alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-712">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="f63ef-713">Bu bıçaklar 500 seri hataları teşhis etmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-713">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="f63ef-714">ASP.NET Çekirdek Uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-714">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="f63ef-715">Uzantılar yüklenmiyorsa, bunları el ile yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-715">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="f63ef-716">**GELİşİm ARAÇLARI** bıçak bölümünde **Uzantılar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-716">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="f63ef-717">**ASP.NET Çekirdek Uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-717">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="f63ef-718">Uzantılar yüklenmiyorsa **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-718">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="f63ef-719">Listeden **ASP.NET Çekirdek Uzantıları'nı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-719">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="f63ef-720">Yasal koşulları kabul etmek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-720">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="f63ef-721">**Ekle uzantı** bıçağında **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-721">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="f63ef-722">Bilgilendirimiolan bir açılır ileti, uzantıların başarıyla ne zaman yüklenir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-722">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="f63ef-723">Stdout günlüğe kaydetme etkin değilse, aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-723">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="f63ef-724">Azure portalında, **GELIŞTIRME ARAÇLARı** alanındaki **Gelişmiş Araçlar** bıçak larını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-724">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="f63ef-725">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-725">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-726">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-726">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-727">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-727">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-728">Klasörleri yol **sitesi** > **wwwroot'a** açın ve listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-728">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="f63ef-729">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-729">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-730">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-730">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="f63ef-731">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-731">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="f63ef-732">Tanısal günlüğü etkinleştirmeye devam edin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-732">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="f63ef-733">Azure portalında **Tanılama günlükleri** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-733">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="f63ef-734">**Uygulama Günlüğe Kaydetme (Filesystem)** ve **Ayrıntılı hata iletileri**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-734">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="f63ef-735">Bıçağın üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-735">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="f63ef-736">Başarısız İstek Olay Arabelleği (FREB) günlüğe kaydetme olarak da bilinen başarısız istek izlemesini eklemek için, **Başarısız istek izleme**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-736">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="f63ef-737">Portaldaki **Teşhis günlükleri** bıçağının hemen altında listelenen **Log akış** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-737">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="f63ef-738">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-738">Make a request to the app.</span></span>
1. <span data-ttu-id="f63ef-739">Günlük akışı verileri içinde, hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-739">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="f63ef-740">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı bettiğinizden emin olun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-740">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="f63ef-741">Başarısız istek izleme günlüklerini (FREB günlükleri) görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-741">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="f63ef-742">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-742">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="f63ef-743">Kenar çubuğunun **DESTEK ARAÇLARI** alanından **Başarısız İstek İzleme Günlükleri'ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-743">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="f63ef-744">Azure Uygulama Hizmeti konusundaki web uygulamaları için etkinleştir meslüpleri oturum açma [bölümünün başarısız istek izlemeleri bölümüne](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure'daki Web Apps Uygulamaları için Uygulama performansı SSS'lerine bakın: Daha](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) fazla bilgi için başarısız istek izlemesini nasıl açarım?</span><span class="sxs-lookup"><span data-stu-id="f63ef-744">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="f63ef-745">Daha fazla bilgi için azure [Uygulama Hizmeti'ndeki web uygulamaları için tanılama günlüğe kaydetme'ye](/azure/app-service/web-sites-enable-diagnostic-log)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-745">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-746">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-746">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-747">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-747">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="f63ef-748">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-748">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-749">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-749">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="f63ef-750">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-750">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="f63ef-751">Uygulama Etkinlik Günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-751">Application Event Log (IIS)</span></span>

<span data-ttu-id="f63ef-752">Uygulama Etkinlik Günlüğüne Erişin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-752">Access the Application Event Log:</span></span>

1. <span data-ttu-id="f63ef-753">Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-753">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="f63ef-754">**Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-754">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="f63ef-755">Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-755">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="f63ef-756">Başarısız uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-756">Search for errors associated with the failing app.</span></span> <span data-ttu-id="f63ef-757">Hatalar *Kaynak* sütunda *IIS AspNetCore Modülü* veya *IIS Express AspNetCore Modülü* değerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-757">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="f63ef-758">Uygulamayı komut istemiyle çalıştırma</span><span class="sxs-lookup"><span data-stu-id="f63ef-758">Run the app at a command prompt</span></span>

<span data-ttu-id="f63ef-759">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="f63ef-759">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="f63ef-760">Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-760">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="f63ef-761">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="f63ef-761">Framework-dependent deployment</span></span>

<span data-ttu-id="f63ef-762">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-762">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="f63ef-763">Komut isteminde dağıtım klasörüne gidin ve uygulamanın montajını *dotnet.exe*ile çalıştırarak uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-763">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="f63ef-764">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `dotnet .\<assembly_name>.dll`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="f63ef-765">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="f63ef-766">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="f63ef-767">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="f63ef-768">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="f63ef-769">Bağımsız dağıtım</span><span class="sxs-lookup"><span data-stu-id="f63ef-769">Self-contained deployment</span></span>

<span data-ttu-id="f63ef-770">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-770">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="f63ef-771">Komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir uygulamasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-771">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="f63ef-772">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `<assembly_name>.exe`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-772">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="f63ef-773">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-773">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="f63ef-774">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-774">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="f63ef-775">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-775">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="f63ef-776">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-776">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="f63ef-777">ASP.NET Çekirdek Modülü stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-777">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="f63ef-778">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-778">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="f63ef-779">Barındırma sisteminde sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-779">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="f63ef-780">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-780">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="f63ef-781">MSBuild'in dağıtımdaki *günlükler* klasörünü otomatik olarak oluşturmasına nasıl etkinleştirilen talimatlar için [Dizin yapısı](xref:host-and-deploy/directory-structure) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-781">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="f63ef-782">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-782">Edit the *web.config* file.</span></span> <span data-ttu-id="f63ef-783">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** yolunu *günlükler* klasörüne (örneğin) `.\logs\stdout`işaret etmek için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-783">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="f63ef-784">`stdout`yoldaki günlük dosya adı önekidir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-784">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="f63ef-785">Günlük oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-785">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="f63ef-786">Dosya `stdout` adı öneki olarak kullanarak, tipik bir günlük dosyası *stdout_20180205184032_5412.log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-786">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="f63ef-787">Uygulama havuzunuzun kimliğinin *günlükler* klasörüne yazma izinleri olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-787">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="f63ef-788">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-788">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-789">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-789">Make a request to the app.</span></span>
1. <span data-ttu-id="f63ef-790">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-790">Navigate to the *logs* folder.</span></span> <span data-ttu-id="f63ef-791">En son stdout günlüğünü bulun ve açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-791">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="f63ef-792">Hatalar için günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-792">Study the log for errors.</span></span>

<span data-ttu-id="f63ef-793">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-793">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="f63ef-794">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-794">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-795">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="f63ef-795">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="f63ef-796">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-796">Save the file.</span></span>

<span data-ttu-id="f63ef-797">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f63ef-797">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-798">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-798">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-799">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-799">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="f63ef-800">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-800">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-801">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-801">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="f63ef-802">ASP.NET Çekirdek Modülü hata ayıklama günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-802">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="f63ef-803">Temel Modül hata ayıklama günlüğünü ASP.NET etkinleştirmek için uygulamanın *web.config* dosyasına aşağıdaki işleyici ayarlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-803">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="f63ef-804">Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-804">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="f63ef-805">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="f63ef-805">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="f63ef-806">Geliştirici Özel Durum Sayfasını Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f63ef-806">Enable the Developer Exception Page</span></span>

<span data-ttu-id="f63ef-807">Ortamı `ASPNETCORE_ENVIRONMENT` geliştirme ortamında çalıştırmak için [web.config'e ortam değişkeni eklenebilir.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="f63ef-807">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="f63ef-808">Ortam, ana bilgisayar `UseEnvironment` oluşturucutarafından uygulama başlatmada geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında Geliştirici Özel Durum [Sayfasının](xref:fundamentals/error-handling) görünmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-808">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="f63ef-809">Ortam değişkenini `ASPNETCORE_ENVIRONMENT` ayarlamak yalnızca Internet'e maruz olmayan hazırlama ve sınama sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-809">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="f63ef-810">Sorun giderme den sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-810">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="f63ef-811">*web.config'de*ortam değişkenlerini ayarlama hakkında bilgi için [bkz.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="f63ef-811">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="f63ef-812">Bir uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="f63ef-812">Obtain data from an app</span></span>

<span data-ttu-id="f63ef-813">Bir uygulama isteklere yanıt verebiliyorsa, terminal sıralı ara yazılımları kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-813">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="f63ef-814">Daha fazla bilgi ve <xref:test/troubleshoot#obtain-data-from-an-app>örnek kod için bkz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-814">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="f63ef-815">Yavaş veya asılı uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-815">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="f63ef-816">*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-816">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="f63ef-817">Uygulama çöküyor veya bir özel durumla karşılaşıyor</span><span class="sxs-lookup"><span data-stu-id="f63ef-817">App crashes or encounters an exception</span></span>

<span data-ttu-id="f63ef-818">Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:</span><span class="sxs-lookup"><span data-stu-id="f63ef-818">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="f63ef-819">Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun</span><span class="sxs-lookup"><span data-stu-id="f63ef-819">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="f63ef-820">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-820">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="f63ef-821">[EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-821">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="f63ef-822">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-822">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="f63ef-823">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-823">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="f63ef-824">Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-824">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="f63ef-825">Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-825">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="f63ef-826">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-826">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="f63ef-827">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-827">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="f63ef-828">Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-828">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="f63ef-829">PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-829">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-830">Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).</span><span class="sxs-lookup"><span data-stu-id="f63ef-830">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="f63ef-831">Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır</span><span class="sxs-lookup"><span data-stu-id="f63ef-831">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="f63ef-832">Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)</span><span class="sxs-lookup"><span data-stu-id="f63ef-832">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="f63ef-833">Dökümü analiz edin</span><span class="sxs-lookup"><span data-stu-id="f63ef-833">Analyze the dump</span></span>

<span data-ttu-id="f63ef-834">Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-834">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="f63ef-835">Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)</span><span class="sxs-lookup"><span data-stu-id="f63ef-835">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="f63ef-836">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="f63ef-836">Clear package caches</span></span>

<span data-ttu-id="f63ef-837">İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-837">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="f63ef-838">Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-838">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="f63ef-839">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-839">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="f63ef-840">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-840">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="f63ef-841">[Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-841">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="f63ef-842">Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-842">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="f63ef-843">*nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-843">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="f63ef-844">Projeyi geri yükleyin ve yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-844">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="f63ef-845">Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-845">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f63ef-846">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f63ef-846">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="f63ef-847">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="f63ef-847">Azure documentation</span></span>

* [<span data-ttu-id="f63ef-848">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="f63ef-848">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="f63ef-849">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme bölümünün uzaktan hata ayıklama web uygulamaları bölümü</span><span class="sxs-lookup"><span data-stu-id="f63ef-849">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="f63ef-850">Azure Uygulama Hizmeti tanılama genel bakış</span><span class="sxs-lookup"><span data-stu-id="f63ef-850">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="f63ef-851">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="f63ef-851">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="f63ef-852">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-852">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="f63ef-853">Azure web uygulamalarınızda HTTP hatalarını "502 kötü ağ geçidi" ve "503 hizmeti kullanılamıyor" hatalarını giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-853">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="f63ef-854">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-854">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="f63ef-855">Azure'daki Web Apps için Uygulama performansı SSS'leri</span><span class="sxs-lookup"><span data-stu-id="f63ef-855">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="f63ef-856">Azure Web App sandbox (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="f63ef-856">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="f63ef-857">Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="f63ef-857">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="f63ef-858">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="f63ef-858">Visual Studio documentation</span></span>

* [<span data-ttu-id="f63ef-859">Visual Studio 2017'de Azure'da IIS'de Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f63ef-859">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="f63ef-860">Visual Studio 2017'de Uzaktan IIS Bilgisayarında Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f63ef-860">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="f63ef-861">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="f63ef-861">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="f63ef-862">Visual Studio Code dokümantasyonu</span><span class="sxs-lookup"><span data-stu-id="f63ef-862">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="f63ef-863">Visual Studio Code ile Hata Ayıklama</span><span class="sxs-lookup"><span data-stu-id="f63ef-863">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f63ef-864">Bu makalede, bir uygulama Azure Uygulama Hizmeti veya IIS'ye dağıtıldığında hataların nasıl tanılanacağına ilişkin yaygın uygulama başlangıç hataları ve yönergeler hakkında bilgiler verilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-864">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="f63ef-865">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="f63ef-865">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="f63ef-866">Ortak başlangıç HTTP durum kodu senaryolarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-866">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="f63ef-867">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-867">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="f63ef-868">Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-868">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="f63ef-869">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-869">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="f63ef-870">IIS'ye dağıtılan veya IIS Express'te yerel olarak çalışan uygulamalar için sorun giderme önerileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-870">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="f63ef-871">Kılavuz, hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-871">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="f63ef-872">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="f63ef-872">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="f63ef-873">Büyük yükseltmeler yaparken veya paket sürümlerini değiştirirken tutarsız paketler bir uygulamayı kırdığında ne yapması gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-873">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="f63ef-874">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f63ef-874">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="f63ef-875">Ek sorun giderme konularını listeler.</span><span class="sxs-lookup"><span data-stu-id="f63ef-875">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="f63ef-876">Uygulama başlatma hataları</span><span class="sxs-lookup"><span data-stu-id="f63ef-876">App startup errors</span></span>

<span data-ttu-id="f63ef-877">Visual Studio'da, ASP.NET Core projesi hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırma için varsayılandır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-877">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="f63ef-878">Yerel hata ayıklama zaman ortaya çıkan bir *502.5 İşlem Hatası* bu konuda ki tavsiye kullanılarak teşhis edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-878">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="f63ef-879">403.14 Yasak</span><span class="sxs-lookup"><span data-stu-id="f63ef-879">403.14 Forbidden</span></span>

<span data-ttu-id="f63ef-880">Uygulama başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-880">The app fails to start.</span></span> <span data-ttu-id="f63ef-881">Aşağıdaki hata günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-881">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="f63ef-882">Hata genellikle aşağıdaki senaryolardan herhangi birini içeren barındırma sisteminde ki bozuk dağıtımdan kaynaklanır:</span><span class="sxs-lookup"><span data-stu-id="f63ef-882">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="f63ef-883">Uygulama barındırma sisteminde yanlış klasöre dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-883">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="f63ef-884">Dağıtım işlemi, uygulamanın tüm dosya ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.</span><span class="sxs-lookup"><span data-stu-id="f63ef-884">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="f63ef-885">*web.config* dosyası dağıtımda eksik veya *web.config* dosyası içeriği yanlış biçimlendirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-885">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="f63ef-886">Aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-886">Perform the following steps:</span></span>

1. <span data-ttu-id="f63ef-887">Barındırma sistemindeki dağıtım klasöründeki tüm dosya ve klasörleri silin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-887">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="f63ef-888">Visual Studio, PowerShell veya manuel dağıtım gibi normal dağıtım yönteminizi kullanarak uygulamanın *yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-888">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="f63ef-889">*web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-889">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="f63ef-890">Azure Uygulama Hizmeti'nde barındırma yaparken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-890">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="f63ef-891">Uygulama IIS tarafından barındırıldığında, uygulamanın **IIS Yöneticisi'nin**Temel **Ayarları'nda**gösterilen IIS **Fiziksel yoluna** dağıtıldığından onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-891">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="f63ef-892">Barındırma sistemindeki dağıtımı projenin *yayımlama* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtılmış olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-892">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="f63ef-893">Yayınlanan bir ASP.NET Core uygulamasının düzeni hakkında <xref:host-and-deploy/directory-structure>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-893">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="f63ef-894">*web.config* dosyasında daha fazla <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-894">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="f63ef-895">500 İç Sunucu Hatası</span><span class="sxs-lookup"><span data-stu-id="f63ef-895">500 Internal Server Error</span></span>

<span data-ttu-id="f63ef-896">Uygulama başlar, ancak bir hata sunucunun isteği yerine getirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="f63ef-896">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="f63ef-897">Bu hata, başlangıç sırasında veya yanıt oluştururken uygulamanın kodu içinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-897">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="f63ef-898">Yanıt hiçbir içerik içermeyebilir veya yanıt tarayıcıda *500 Dahili Sunucu Hatası* olarak görünebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-898">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="f63ef-899">Uygulama Olay Günlüğü genellikle uygulamanın normal olarak başladığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-899">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="f63ef-900">Sunucunun bakış açısından, bu doğru.</span><span class="sxs-lookup"><span data-stu-id="f63ef-900">From the server's perspective, that's correct.</span></span> <span data-ttu-id="f63ef-901">Uygulama başladı, ancak geçerli bir yanıt oluşturamıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-901">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="f63ef-902">Uygulamayı sunucudaki bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Çekirdek Modülü stdout günlüğünü etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-902">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="f63ef-903">502.5 İşlem Hatası</span><span class="sxs-lookup"><span data-stu-id="f63ef-903">502.5 Process Failure</span></span>

<span data-ttu-id="f63ef-904">Alt işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-904">The worker process fails.</span></span> <span data-ttu-id="f63ef-905">Uygulama başlatılmıyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-905">The app doesn't start.</span></span>

<span data-ttu-id="f63ef-906">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) alt işlemi başlatmaya çalışır, ancak başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-906">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="f63ef-907">İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-907">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="f63ef-908">Yaygın bir hata koşulu, uygulamanın ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmış olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-908">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="f63ef-909">hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-909">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="f63ef-910">*Paylaşılan çerçeve,* makineye yüklenen *.dll* ve meta package gibi `Microsoft.AspNetCore.App`bir meta paketle başvurulan derlemeler kümesidir .</span><span class="sxs-lookup"><span data-stu-id="f63ef-910">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="f63ef-911">Metapackage başvurusu en az gerekli sürümü belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-911">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="f63ef-912">Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-912">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="f63ef-913">*502.5 İşlem Hatası* hatası sayfası, bir barındırma veya uygulama yanlış yapılandırması alt işlemin başarısız lığa neden olduğunda döndürülür:</span><span class="sxs-lookup"><span data-stu-id="f63ef-913">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="f63ef-914">Uygulama başlatılamamış (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="f63ef-914">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="f63ef-915">Uygulamanın derlemesi *(.dll)* yüklenemediği için uygulama başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="f63ef-915">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="f63ef-916">Bu hata, yayınlanan uygulama ile w3wp/iisexpress işlemi arasında bir bitness uyuşmazlığı olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-916">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="f63ef-917">Uygulama havuzunun 32 bit ayarı doğru olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-917">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="f63ef-918">IIS Manager'ın **Uygulama Havuzları'ndaki**uygulama havuzunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-918">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="f63ef-919">**Eylemler** panelinde **Uygulama Havuzu** altında Gelişmiş **Ayarlar'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-919">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="f63ef-920">**32 Bit Uygulamaları Etkinleştir'i**Ayarlayın :</span><span class="sxs-lookup"><span data-stu-id="f63ef-920">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="f63ef-921">32 bit (x86) bir uygulama dağıtıyorsanız, `True`değeri .</span><span class="sxs-lookup"><span data-stu-id="f63ef-921">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="f63ef-922">64 bit (x64) bir uygulama dağıtıyorsanız, `False`değeri .</span><span class="sxs-lookup"><span data-stu-id="f63ef-922">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="f63ef-923">Proje dosyasındaki BIR `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bitliği arasında bir çakışma olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-923">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="f63ef-924">Bağlantı sıfırlama</span><span class="sxs-lookup"><span data-stu-id="f63ef-924">Connection reset</span></span>

<span data-ttu-id="f63ef-925">Üstbilgi gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Dahili Sunucu Hatası** göndermesi için çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-925">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="f63ef-926">Bu genellikle bir yanıt için karmaşık nesnelerin serileştirme sırasında bir hata oluşur oluşur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-926">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="f63ef-927">Bu hata türü istemcide *bağlantı sıfırlama* hatası olarak görünür.</span><span class="sxs-lookup"><span data-stu-id="f63ef-927">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="f63ef-928">[Uygulama günlüğe kaydetme,](xref:fundamentals/logging/index) bu tür hataları gidermede yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-928">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="f63ef-929">Varsayılan başlangıç sınırları</span><span class="sxs-lookup"><span data-stu-id="f63ef-929">Default startup limits</span></span>

<span data-ttu-id="f63ef-930">[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) 120 saniyelik varsayılan *başlangıçTimeLimit* ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-930">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="f63ef-931">Varsayılan değerde bırakıldığında, bir uygulamanın bir işlem hatası günlüğe kaydetmesi iki dakika kadar sürebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-931">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="f63ef-932">Modülü yapılandırma hakkında daha fazla bilgi için [aspNetCore öğesinin Öznitelikleri'ne](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-932">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="f63ef-933">Azure Uygulama Hizmetinde Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-933">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="f63ef-934">Uygulama Etkinlik Günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-934">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="f63ef-935">Uygulama Olay Günlüğü'ne erişmek için Azure portalındaki **Tanılama ve sorunları çözme** sorununu kullanın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-935">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="f63ef-936">Azure portalında Uygulamayı Uygulama **Hizmetleri'nde**açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-936">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="f63ef-937">**Sorunları tanılama ve çözme** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-937">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="f63ef-938">**Tanılama Araçları** başlığını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-938">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="f63ef-939">**Destek Araçları** **altında, Uygulama Etkinlikleri** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-939">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="f63ef-940">**Kaynak** sütundaki *IIS AspNetCoreModule* veya *IIS AspNetCoreModule V2* girişi tarafından sağlanan en son hatayı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-940">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="f63ef-941">**Tanılama ve sorunları çözmenin** alternatifi, Uygulama Olay Günlüğü dosyasını doğrudan [Kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-941">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="f63ef-942">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-942">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="f63ef-943">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-943">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-944">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-944">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-945">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-945">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-946">**LogFiles** klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-946">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="f63ef-947">*eventlog.xml* dosyasının yanındaki kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-947">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="f63ef-948">Günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-948">Examine the log.</span></span> <span data-ttu-id="f63ef-949">En son olayları görmek için günlüğün altına gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-949">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="f63ef-950">Uygulamayı Kudu konsolunda çalıştırın</span><span class="sxs-lookup"><span data-stu-id="f63ef-950">Run the app in the Kudu console</span></span>

<span data-ttu-id="f63ef-951">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="f63ef-951">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="f63ef-952">Hatayı bulmak için uygulamayı [Kudu](https://github.com/projectkudu/kudu/wiki) Uzaktan Yürütme Konsolu'nda çalıştırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f63ef-952">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="f63ef-953">**Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-953">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="f63ef-954">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-954">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-955">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-955">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-956">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-956">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="f63ef-957">32 bit (x86) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f63ef-957">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="f63ef-958">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="f63ef-958">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="f63ef-959">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-959">Run the app:</span></span>
   * <span data-ttu-id="f63ef-960">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-960">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="f63ef-961">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-961">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="f63ef-962">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-962">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="f63ef-963">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="f63ef-963">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="f63ef-964">*Core {VERSION} (x86) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="f63ef-964">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="f63ef-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="f63ef-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="f63ef-966">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="f63ef-966">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="f63ef-967">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-967">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="f63ef-968">64 bit (x64) uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f63ef-968">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="f63ef-969">**Geçerli sürüm**</span><span class="sxs-lookup"><span data-stu-id="f63ef-969">**Current release**</span></span>

* <span data-ttu-id="f63ef-970">Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-970">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="f63ef-971">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="f63ef-971">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="f63ef-972">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-972">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="f63ef-973">Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="f63ef-973">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="f63ef-974">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-974">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="f63ef-975">**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**</span><span class="sxs-lookup"><span data-stu-id="f63ef-975">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="f63ef-976">*Core {VERSION} (x64) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*</span><span class="sxs-lookup"><span data-stu-id="f63ef-976">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="f63ef-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)</span><span class="sxs-lookup"><span data-stu-id="f63ef-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="f63ef-978">Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="f63ef-978">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="f63ef-979">Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-979">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="f63ef-980">ASP.NET Çekirdek Modül stdout günlüğü (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-980">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="f63ef-981">ASP.NET Çekirdek Modülü stdout günlüğü genellikle Uygulama Olay Günlüğü'nde bulunmayan yararlı hata iletileri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="f63ef-981">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="f63ef-982">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-982">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="f63ef-983">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-983">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="f63ef-984">**PROBLEM KATEGORİsİ SEÇ'in** **altında, Web Uygulaması Aşağı** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-984">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="f63ef-985">**Önerilen Çözümler** > Altında **Stdout Log Redirection etkinleştirin**, **Web.Config'i yeniden etkinleştirmek için Kudu Konsolu'nu açın**düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-985">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="f63ef-986">Kudu **Tanı Konsolu,** yol **sitesi** > **wwwroot**klasörleri açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-986">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="f63ef-987">Listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-987">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="f63ef-988">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-988">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-989">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-989">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="f63ef-990">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-990">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-991">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-991">Make a request to the app.</span></span>
1. <span data-ttu-id="f63ef-992">Azure portalına dönün.</span><span class="sxs-lookup"><span data-stu-id="f63ef-992">Return to the Azure portal.</span></span> <span data-ttu-id="f63ef-993">**GELİşTİrME ARAÇLARI** alanında **Gelişmiş Araçlar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-993">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="f63ef-994">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-994">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-995">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-995">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-996">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-996">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-997">Günlük **Dosyaları** klasörünü seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-997">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="f63ef-998">**Değiştirilen** sütunu inceleyin ve en son değişiklik tarihiyle birlikte stdout günlüğünü deletmek için kalem simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-998">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="f63ef-999">Günlük dosyası açıldığında, hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-999">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="f63ef-1000">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1000">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="f63ef-1001">Kudu **Tanı Konsolu,** *web.config* dosyasını ortaya çıkarmak için yol **sitesi** > **wwwroot** dönün.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1001">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="f63ef-1002">Kalem simgesini seçerek **web.config** dosyasını yeniden açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1002">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="f63ef-1003">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1003">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="f63ef-1004">Dosyayı kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1004">Select **Save** to save the file.</span></span>

<span data-ttu-id="f63ef-1005">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1005">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-1006">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1006">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-1007">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1007">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="f63ef-1008">Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlük kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1008">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="f63ef-1009">Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1009">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-1010">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1010">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="f63ef-1011">Yavaş veya asma uygulaması (Azure Uygulama Hizmeti)</span><span class="sxs-lookup"><span data-stu-id="f63ef-1011">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="f63ef-1012">Bir uygulama yavaş yanıt verdiğinde veya bir isteğe bağlı kaldığında aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1012">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="f63ef-1013">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-1013">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="f63ef-1014">Azure Web Uygulamasında Aralıklı Özel Durum sorunları veya performans sorunları için Döküm'ü yakalamak için Kilitlenme Tanılayıcı Site Uzantısı'nı kullanma</span><span class="sxs-lookup"><span data-stu-id="f63ef-1014">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="f63ef-1015">İzleme bıçakları</span><span class="sxs-lookup"><span data-stu-id="f63ef-1015">Monitoring blades</span></span>

<span data-ttu-id="f63ef-1016">İzleme bıçakları, konunun daha önce açıklanan yöntemlerine alternatif bir sorun giderme deneyimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1016">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="f63ef-1017">Bu bıçaklar 500 seri hataları teşhis etmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1017">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="f63ef-1018">ASP.NET Çekirdek Uzantılarının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1018">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="f63ef-1019">Uzantılar yüklenmiyorsa, bunları el ile yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1019">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="f63ef-1020">**GELİşİm ARAÇLARI** bıçak bölümünde **Uzantılar** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1020">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="f63ef-1021">**ASP.NET Çekirdek Uzantıları** listede görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1021">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="f63ef-1022">Uzantılar yüklenmiyorsa **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1022">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="f63ef-1023">Listeden **ASP.NET Çekirdek Uzantıları'nı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1023">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="f63ef-1024">Yasal koşulları kabul etmek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1024">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="f63ef-1025">**Ekle uzantı** bıçağında **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1025">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="f63ef-1026">Bilgilendirimiolan bir açılır ileti, uzantıların başarıyla ne zaman yüklenir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1026">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="f63ef-1027">Stdout günlüğe kaydetme etkin değilse, aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1027">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="f63ef-1028">Azure portalında, **GELIŞTIRME ARAÇLARı** alanındaki **Gelişmiş Araçlar** bıçak larını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1028">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="f63ef-1029">\*\*Git&rarr; \*\* düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1029">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="f63ef-1030">Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1030">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="f63ef-1031">Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1031">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="f63ef-1032">Klasörleri yol **sitesi** > **wwwroot'a** açın ve listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1032">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="f63ef-1033">*web.config* dosyasının yanındaki kalem simgesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1033">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-1034">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-1034">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="f63ef-1035">Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1035">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="f63ef-1036">Tanısal günlüğü etkinleştirmeye devam edin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1036">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="f63ef-1037">Azure portalında **Tanılama günlükleri** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1037">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="f63ef-1038">**Uygulama Günlüğe Kaydetme (Filesystem)** ve **Ayrıntılı hata iletileri**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1038">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="f63ef-1039">Bıçağın üst kısmındaki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1039">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="f63ef-1040">Başarısız İstek Olay Arabelleği (FREB) günlüğe kaydetme olarak da bilinen başarısız istek izlemesini eklemek için, **Başarısız istek izleme**için **Açık** anahtarını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1040">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="f63ef-1041">Portaldaki **Teşhis günlükleri** bıçağının hemen altında listelenen **Log akış** bıçağını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1041">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="f63ef-1042">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1042">Make a request to the app.</span></span>
1. <span data-ttu-id="f63ef-1043">Günlük akışı verileri içinde, hatanın nedeni belirtilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1043">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="f63ef-1044">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı bettiğinizden emin olun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1044">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="f63ef-1045">Başarısız istek izleme günlüklerini (FREB günlükleri) görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1045">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="f63ef-1046">Azure portalında **Tanıla ve sorunları çöz'** e gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1046">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="f63ef-1047">Kenar çubuğunun **DESTEK ARAÇLARI** alanından **Başarısız İstek İzleme Günlükleri'ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1047">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="f63ef-1048">Azure Uygulama Hizmeti konusundaki web uygulamaları için etkinleştir meslüpleri oturum açma [bölümünün başarısız istek izlemeleri bölümüne](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure'daki Web Apps Uygulamaları için Uygulama performansı SSS'lerine bakın: Daha](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) fazla bilgi için başarısız istek izlemesini nasıl açarım?</span><span class="sxs-lookup"><span data-stu-id="f63ef-1048">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="f63ef-1049">Daha fazla bilgi için azure [Uygulama Hizmeti'ndeki web uygulamaları için tanılama günlüğe kaydetme'ye](/azure/app-service/web-sites-enable-diagnostic-log)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1049">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-1050">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1050">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-1051">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1051">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="f63ef-1052">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1052">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-1053">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1053">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="f63ef-1054">IIS üzerinde sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-1054">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="f63ef-1055">Uygulama Etkinlik Günlüğü (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-1055">Application Event Log (IIS)</span></span>

<span data-ttu-id="f63ef-1056">Uygulama Etkinlik Günlüğüne Erişin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1056">Access the Application Event Log:</span></span>

1. <span data-ttu-id="f63ef-1057">Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1057">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="f63ef-1058">**Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1058">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="f63ef-1059">Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1059">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="f63ef-1060">Başarısız uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1060">Search for errors associated with the failing app.</span></span> <span data-ttu-id="f63ef-1061">Hatalar *Kaynak* sütunda *IIS AspNetCore Modülü* veya *IIS Express AspNetCore Modülü* değerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1061">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="f63ef-1062">Uygulamayı komut istemiyle çalıştırma</span><span class="sxs-lookup"><span data-stu-id="f63ef-1062">Run the app at a command prompt</span></span>

<span data-ttu-id="f63ef-1063">Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1063">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="f63ef-1064">Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1064">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="f63ef-1065">Çerçeveye bağımlı dağıtım</span><span class="sxs-lookup"><span data-stu-id="f63ef-1065">Framework-dependent deployment</span></span>

<span data-ttu-id="f63ef-1066">Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1066">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="f63ef-1067">Komut isteminde dağıtım klasörüne gidin ve uygulamanın montajını *dotnet.exe*ile çalıştırarak uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1067">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="f63ef-1068">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `dotnet .\<assembly_name>.dll`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="f63ef-1069">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="f63ef-1070">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="f63ef-1071">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="f63ef-1072">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="f63ef-1073">Bağımsız dağıtım</span><span class="sxs-lookup"><span data-stu-id="f63ef-1073">Self-contained deployment</span></span>

<span data-ttu-id="f63ef-1074">Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1074">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="f63ef-1075">Komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir uygulamasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1075">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="f63ef-1076">Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `<assembly_name>.exe`yerine değiştirin: .</span><span class="sxs-lookup"><span data-stu-id="f63ef-1076">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="f63ef-1077">Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1077">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="f63ef-1078">Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1078">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="f63ef-1079">Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1079">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="f63ef-1080">Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1080">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="f63ef-1081">ASP.NET Çekirdek Modülü stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-1081">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="f63ef-1082">Stdout günlüklerini etkinleştirmek ve görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1082">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="f63ef-1083">Barındırma sisteminde sitenin dağıtım klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1083">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="f63ef-1084">*Günlükler* klasörü yoksa, klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1084">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="f63ef-1085">MSBuild'in dağıtımdaki *günlükler* klasörünü otomatik olarak oluşturmasına nasıl etkinleştirilen talimatlar için [Dizin yapısı](xref:host-and-deploy/directory-structure) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1085">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="f63ef-1086">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1086">Edit the *web.config* file.</span></span> <span data-ttu-id="f63ef-1087">**StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** yolunu *günlükler* klasörüne (örneğin) `.\logs\stdout`işaret etmek için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1087">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="f63ef-1088">`stdout`yoldaki günlük dosya adı önekidir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1088">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="f63ef-1089">Günlük oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1089">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="f63ef-1090">Dosya `stdout` adı öneki olarak kullanarak, tipik bir günlük dosyası *stdout_20180205184032_5412.log*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1090">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="f63ef-1091">Uygulama havuzunuzun kimliğinin *günlükler* klasörüne yazma izinleri olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1091">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="f63ef-1092">Güncelleştirilmiş *web.config* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1092">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-1093">Uygulamaya bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1093">Make a request to the app.</span></span>
1. <span data-ttu-id="f63ef-1094">*Günlükler* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1094">Navigate to the *logs* folder.</span></span> <span data-ttu-id="f63ef-1095">En son stdout günlüğünü bulun ve açın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1095">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="f63ef-1096">Hatalar için günlüğü inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1096">Study the log for errors.</span></span>

<span data-ttu-id="f63ef-1097">Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1097">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="f63ef-1098">*Web.config* dosyasını edin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1098">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="f63ef-1099">Set **stdoutLogEnabled** için `false`.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1099">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="f63ef-1100">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1100">Save the file.</span></span>

<span data-ttu-id="f63ef-1101">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1101">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-1102">Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1102">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="f63ef-1103">Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1103">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="f63ef-1104">ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1104">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f63ef-1105">Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1105">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="f63ef-1106">Geliştirici Özel Durum Sayfasını Etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f63ef-1106">Enable the Developer Exception Page</span></span>

<span data-ttu-id="f63ef-1107">Ortamı `ASPNETCORE_ENVIRONMENT` geliştirme ortamında çalıştırmak için [web.config'e ortam değişkeni eklenebilir.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="f63ef-1107">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="f63ef-1108">Ortam, ana bilgisayar `UseEnvironment` oluşturucutarafından uygulama başlatmada geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında Geliştirici Özel Durum [Sayfasının](xref:fundamentals/error-handling) görünmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1108">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="f63ef-1109">Ortam değişkenini `ASPNETCORE_ENVIRONMENT` ayarlamak yalnızca Internet'e maruz olmayan hazırlama ve sınama sunucularında kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1109">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="f63ef-1110">Sorun giderme den sonra *web.config* dosyasından ortam değişkenini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1110">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="f63ef-1111">*web.config'de*ortam değişkenlerini ayarlama hakkında bilgi için [bkz.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)</span><span class="sxs-lookup"><span data-stu-id="f63ef-1111">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="f63ef-1112">Bir uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="f63ef-1112">Obtain data from an app</span></span>

<span data-ttu-id="f63ef-1113">Bir uygulama isteklere yanıt verebiliyorsa, terminal sıralı ara yazılımları kullanarak uygulamadan istek, bağlantı ve ek veri alın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1113">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="f63ef-1114">Daha fazla bilgi ve <xref:test/troubleshoot#obtain-data-from-an-app>örnek kod için bkz.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1114">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="f63ef-1115">Yavaş veya asılı uygulama (IIS)</span><span class="sxs-lookup"><span data-stu-id="f63ef-1115">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="f63ef-1116">*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1116">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="f63ef-1117">Uygulama çöküyor veya bir özel durumla karşılaşıyor</span><span class="sxs-lookup"><span data-stu-id="f63ef-1117">App crashes or encounters an exception</span></span>

<span data-ttu-id="f63ef-1118">Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1118">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="f63ef-1119">Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun</span><span class="sxs-lookup"><span data-stu-id="f63ef-1119">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="f63ef-1120">Uygulama havuzunun klasöre yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1120">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="f63ef-1121">[EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1121">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="f63ef-1122">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1122">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="f63ef-1123">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1123">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="f63ef-1124">Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1124">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="f63ef-1125">Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1125">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="f63ef-1126">Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1126">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="f63ef-1127">Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1127">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="f63ef-1128">Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1128">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="f63ef-1129">PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1129">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="f63ef-1130">Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).</span><span class="sxs-lookup"><span data-stu-id="f63ef-1130">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="f63ef-1131">Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır</span><span class="sxs-lookup"><span data-stu-id="f63ef-1131">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="f63ef-1132">Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)</span><span class="sxs-lookup"><span data-stu-id="f63ef-1132">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="f63ef-1133">Dökümü analiz edin</span><span class="sxs-lookup"><span data-stu-id="f63ef-1133">Analyze the dump</span></span>

<span data-ttu-id="f63ef-1134">Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1134">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="f63ef-1135">Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)</span><span class="sxs-lookup"><span data-stu-id="f63ef-1135">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="f63ef-1136">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="f63ef-1136">Clear package caches</span></span>

<span data-ttu-id="f63ef-1137">İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1137">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="f63ef-1138">Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1138">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="f63ef-1139">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:</span><span class="sxs-lookup"><span data-stu-id="f63ef-1139">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="f63ef-1140">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1140">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="f63ef-1141">[Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1141">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="f63ef-1142">Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1142">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="f63ef-1143">*nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1143">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="f63ef-1144">Projeyi geri yükleyin ve yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1144">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="f63ef-1145">Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="f63ef-1145">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f63ef-1146">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f63ef-1146">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="f63ef-1147">Azure belgeleri</span><span class="sxs-lookup"><span data-stu-id="f63ef-1147">Azure documentation</span></span>

* [<span data-ttu-id="f63ef-1148">ASP.NET Core için Application Insights</span><span class="sxs-lookup"><span data-stu-id="f63ef-1148">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="f63ef-1149">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme bölümünün uzaktan hata ayıklama web uygulamaları bölümü</span><span class="sxs-lookup"><span data-stu-id="f63ef-1149">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="f63ef-1150">Azure Uygulama Hizmeti tanılama genel bakış</span><span class="sxs-lookup"><span data-stu-id="f63ef-1150">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="f63ef-1151">Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme</span><span class="sxs-lookup"><span data-stu-id="f63ef-1151">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="f63ef-1152">Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-1152">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="f63ef-1153">Azure web uygulamalarınızda HTTP hatalarını "502 kötü ağ geçidi" ve "503 hizmeti kullanılamıyor" hatalarını giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-1153">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="f63ef-1154">Azure App Service web uygulamasında yavaş performans sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="f63ef-1154">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="f63ef-1155">Azure'daki Web Apps için Uygulama performansı SSS'leri</span><span class="sxs-lookup"><span data-stu-id="f63ef-1155">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="f63ef-1156">Azure Web App sandbox (App Service çalışma zamanı yürütme sınırlamaları)</span><span class="sxs-lookup"><span data-stu-id="f63ef-1156">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="f63ef-1157">Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)</span><span class="sxs-lookup"><span data-stu-id="f63ef-1157">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="f63ef-1158">Visual Studio belgeleri</span><span class="sxs-lookup"><span data-stu-id="f63ef-1158">Visual Studio documentation</span></span>

* [<span data-ttu-id="f63ef-1159">Visual Studio 2017'de Azure'da IIS'de Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f63ef-1159">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="f63ef-1160">Visual Studio 2017'de Uzaktan IIS Bilgisayarında Uzaktan Hata Ayıklama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f63ef-1160">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="f63ef-1161">Visual Studio kullanarak hata ayıklamayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="f63ef-1161">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="f63ef-1162">Visual Studio Code dokümantasyonu</span><span class="sxs-lookup"><span data-stu-id="f63ef-1162">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="f63ef-1163">Visual Studio Code ile Hata Ayıklama</span><span class="sxs-lookup"><span data-stu-id="f63ef-1163">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
