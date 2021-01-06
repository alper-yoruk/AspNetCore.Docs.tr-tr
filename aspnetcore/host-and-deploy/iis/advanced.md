---
title: Gelişmiş yapılandırma
author: rick-anderson
description: ASP.NET Core modülü ve Internet Information Services (IIS) ile gelişmiş yapılandırma.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058625"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="e029a-103">ASP.NET Core modülünün ve IIS 'nin gelişmiş yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e029a-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="e029a-104">Bu makalede ASP.NET Core modülü ve IIS için gelişmiş yapılandırma seçenekleri ve senaryoları ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e029a-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="e029a-105">Yığın boyutunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="e029a-105">Modify the stack size</span></span>

<span data-ttu-id="e029a-106">*Yalnızca işlem içi barındırma modeli kullanılırken geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="e029a-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="e029a-107">Yönetilen yığın boyutunu `stackSize` dosyadaki bayt cinsinden ayarını kullanarak yapılandırın `web.config` .</span><span class="sxs-lookup"><span data-stu-id="e029a-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="e029a-108">Varsayılan boyut 1.048.576 bayttır (1 MB).</span><span class="sxs-lookup"><span data-stu-id="e029a-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="e029a-109">Aşağıdaki örnek, yığın boyutunu 2 MB (2.097.152 bayt) olarak değiştirir:</span><span class="sxs-lookup"><span data-stu-id="e029a-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="e029a-110">Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır</span><span class="sxs-lookup"><span data-stu-id="e029a-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="e029a-111">*Yalnızca işlem dışı barındırma için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="e029a-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="e029a-112">ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="e029a-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="e029a-113">Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.</span><span class="sxs-lookup"><span data-stu-id="e029a-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="e029a-114">Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e029a-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="e029a-115">Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="e029a-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="e029a-116">Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e029a-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="e029a-117">IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler.</span><span class="sxs-lookup"><span data-stu-id="e029a-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="e029a-118">Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="e029a-119">Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="e029a-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="e029a-120">Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.</span><span class="sxs-lookup"><span data-stu-id="e029a-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="e029a-121">IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="e029a-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="e029a-122">ASP.NET Core modülü yükleyicisi hesap ayrıcalıklarıyla çalışır `TrustedInstaller` .</span><span class="sxs-lookup"><span data-stu-id="e029a-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="e029a-123">Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki dosyadaki modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar `applicationHost.config` .</span><span class="sxs-lookup"><span data-stu-id="e029a-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="e029a-124">IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde ayarlanan parametre ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın `OPT_NO_SHARED_CONFIG_CHECK` `1` :</span><span class="sxs-lookup"><span data-stu-id="e029a-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="e029a-125">Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="e029a-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="e029a-126">IIS paylaşılan yapılandırmasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="e029a-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="e029a-127">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e029a-127">Run the installer.</span></span>
1. <span data-ttu-id="e029a-128">Güncelleştirilmiş `applicationHost.config` dosyayı dosya paylaşımında dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="e029a-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="e029a-129">IIS paylaşılan yapılandırmasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="e029a-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="e029a-130">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="e029a-130">Data protection</span></span>

<span data-ttu-id="e029a-131">[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulamasında kullanılan ara yazılımlar dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e029a-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="e029a-132">Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturmak için veri koruma bir dağıtım betiği veya Kullanıcı kodu ile yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e029a-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="e029a-133">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="e029a-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="e029a-134">Veri koruma anahtarı halkası, uygulama yeniden başlatıldığında bellekte depolanıyorsa:</span><span class="sxs-lookup"><span data-stu-id="e029a-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="e029a-135">Tüm cookie tabanlı kimlik doğrulama belirteçleri geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="e029a-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="e029a-136">Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e029a-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="e029a-137">Anahtar halkası ile korunan tüm veriler artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="e029a-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="e029a-138">Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)öğesini içerebilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="e029a-139">Anahtar halkasını sürdürmek için IIS altındaki veri korumasını yapılandırmak için aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="e029a-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="e029a-140">**Veri koruma kayıt defteri anahtarları oluşturma**</span><span class="sxs-lookup"><span data-stu-id="e029a-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="e029a-141">ASP.NET Core uygulamalar tarafından kullanılan veri koruma anahtarları, uygulamaların dış kayıt defterinde saklanır.</span><span class="sxs-lookup"><span data-stu-id="e029a-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="e029a-142">Belirli bir uygulamanın anahtarlarını kalıcı hale getirmek için, uygulama havuzu için kayıt defteri anahtarları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e029a-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="e029a-143">Tek başına, Web grubu olmayan IIS yüklemeleri için, [veri koruma Provision-AutoGenKeys.ps1 PowerShell betiği](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) , bir ASP.NET Core uygulamasıyla kullanılan her uygulama havuzu için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="e029a-144">Bu betik, yalnızca uygulamanın uygulama havuzunun çalışan işlem hesabına erişilebilen HKLM Kayıt defterinde bir kayıt defteri anahtarı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e029a-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="e029a-145">Anahtarlar, makine genelindeki bir anahtarla DPAPI kullanılarak şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="e029a-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="e029a-146">Web grubu senaryolarında bir uygulama, veri koruma anahtar halkasını depolamak için bir UNC yolu kullanacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="e029a-147">Varsayılan olarak anahtarlar şifrelenmez.</span><span class="sxs-lookup"><span data-stu-id="e029a-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="e029a-148">Ağ paylaşımının dosya izinlerinin, uygulamanın çalıştığı Windows hesabıyla sınırlı olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="e029a-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="e029a-149">Bir x509 sertifikası, bekleyen anahtarları korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="e029a-150">Kullanıcıların sertifikaları karşıya yüklemesine izin vermek için bir mekanizma düşünün.</span><span class="sxs-lookup"><span data-stu-id="e029a-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="e029a-151">Sertifikaları kullanıcının güvenilen sertifika deposuna yerleştirin ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduklarından emin olun.</span><span class="sxs-lookup"><span data-stu-id="e029a-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="e029a-152">Daha fazla bilgi için bkz. <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="e029a-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="e029a-153">**Kullanıcı profilini yüklemek için IIS uygulama havuzunu yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="e029a-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="e029a-154">Bu ayar, uygulama havuzunun **Gelişmiş ayarları** altındaki **işlem modeli** bölümünde bulunur.</span><span class="sxs-lookup"><span data-stu-id="e029a-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="e029a-155">**Kullanıcı profilini yükle** ' ye ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="e029a-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="e029a-156">Olarak ayarlandığında `True` anahtarlar Kullanıcı profili dizininde depolanır ve Kullanıcı hesabına özgü bir ANAHTARLA DPAPI kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="e029a-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="e029a-157">Anahtarlar `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` klasörde kalıcıdır.</span><span class="sxs-lookup"><span data-stu-id="e029a-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="e029a-158">Uygulama havuzunun [ `setProfileEnvironment` özniteliğinin](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e029a-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="e029a-159">Varsayılan değeri `setProfileEnvironment` `true` .</span><span class="sxs-lookup"><span data-stu-id="e029a-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="e029a-160">Bazı senaryolarda (örneğin, Windows işletim sistemi), `setProfileEnvironment` olarak ayarlanır `false` .</span><span class="sxs-lookup"><span data-stu-id="e029a-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="e029a-161">Anahtarlar beklenen şekilde Kullanıcı profili dizininde depolanmıyorsa:</span><span class="sxs-lookup"><span data-stu-id="e029a-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="e029a-162">`%windir%/system32/inetsrv/config` klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="e029a-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="e029a-163">`applicationHost.config` dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="e029a-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="e029a-164">Öğesini bulun `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .</span><span class="sxs-lookup"><span data-stu-id="e029a-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="e029a-165">`setProfileEnvironment`Özniteliğinin mevcut olmadığını, değeri varsayılan olarak değerini, `true` veya özniteliğin değerini olarak olarak ayarlandığını doğrulayın `true` .</span><span class="sxs-lookup"><span data-stu-id="e029a-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="e029a-166">**Dosya sistemini anahtar halka deposu olarak kullanma**</span><span class="sxs-lookup"><span data-stu-id="e029a-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="e029a-167">[Dosya sistemini anahtar halka deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="e029a-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="e029a-168">Anahtar halkasını korumak ve sertifikanın güvenilen bir sertifika olduğundan emin olmak için bir x509 sertifikası kullanın.</span><span class="sxs-lookup"><span data-stu-id="e029a-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="e029a-169">Sertifika kendinden imzalı ise, sertifikayı güvenilen kök depoya yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="e029a-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="e029a-170">IIS 'yi bir Web grubunda kullanırken:</span><span class="sxs-lookup"><span data-stu-id="e029a-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="e029a-171">Tüm makinelerin erişebileceği bir dosya paylaşma kullanın.</span><span class="sxs-lookup"><span data-stu-id="e029a-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="e029a-172">Her makineye bir x509 sertifikası dağıtın.</span><span class="sxs-lookup"><span data-stu-id="e029a-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="e029a-173">[Kodda veri korumasını](xref:security/data-protection/configuration/overview)yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="e029a-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="e029a-174">**Veri koruma için makineye özel bir ilke ayarlama**</span><span class="sxs-lookup"><span data-stu-id="e029a-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="e029a-175">Veri koruma sistemi, veri koruma API 'Lerini kullanan tüm uygulamalar için varsayılan [makine genelinde bir ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="e029a-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="e029a-176">Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="e029a-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="e029a-177">IIS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e029a-177">IIS configuration</span></span>

<span data-ttu-id="e029a-178">**Windows Server işletim sistemleri**</span><span class="sxs-lookup"><span data-stu-id="e029a-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="e029a-179">**Web sunucusu (IIS)** sunucu rolünü etkinleştirin ve rol hizmetleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e029a-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="e029a-180">**Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi** bağlantısındaki bağlantıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="e029a-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="e029a-181">**Sunucu rolleri** adımında, **Web sunucusu (IIS)** kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="e029a-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Sunucu rollerini seçin adımında Web sunucusu IIS rolü seçilidir.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="e029a-183">**Özellikler** adımından sonra, Web sunucusu (IIS) için **rol hizmetleri** adımı yüklenir.</span><span class="sxs-lookup"><span data-stu-id="e029a-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="e029a-184">İstenen IIS rol hizmetlerini seçin veya varsayılan rol hizmetlerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="e029a-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Rol hizmetlerini seçin adımında varsayılan rol hizmetleri seçilidir.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="e029a-186">**Windows kimlik doğrulaması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="e029a-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e029a-187">Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **güvenliği**.</span><span class="sxs-lookup"><span data-stu-id="e029a-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="e029a-188">**Windows kimlik doğrulama** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e029a-189">Daha fazla bilgi için bkz. [Windows `<windowsAuthentication>` kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="e029a-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e029a-190">**WebSockets (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="e029a-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e029a-191">WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="e029a-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e029a-192">WebSockets etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **uygulama geliştirme**.</span><span class="sxs-lookup"><span data-stu-id="e029a-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="e029a-193">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e029a-194">Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="e029a-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e029a-195">Web sunucusu rolü ve hizmetlerini yüklemek için **onay** adımına ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="e029a-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="e029a-196">**Web sunucusu (IIS)** rolü yüklendikten sonra sunucu/IIS yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e029a-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="e029a-197">**Windows masaüstü işletim sistemleri**</span><span class="sxs-lookup"><span data-stu-id="e029a-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="e029a-198">**IIS Yönetim Konsolu** ve **World Wide Web hizmetlerini** etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="e029a-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e029a-199">**Denetim Masası**  >  **programları**  >  **Programlar ve Özellikler**  >  **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="e029a-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="e029a-200">**Internet Information Services** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="e029a-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="e029a-201">**Web yönetimi araçları** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="e029a-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="e029a-202">**IIS Yönetim Konsolu** kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="e029a-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="e029a-203">**World Wide Web Hizmetleri** kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="e029a-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e029a-204">**World Wide Web Hizmetleri** için varsayılan özellikleri kabul edın veya IIS özelliklerini özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e029a-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="e029a-205">**Windows kimlik doğrulaması (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="e029a-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e029a-206">Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **World Wide Web Hizmetleri**  >  **güvenliği**.</span><span class="sxs-lookup"><span data-stu-id="e029a-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="e029a-207">**Windows kimlik doğrulama** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e029a-208">Daha fazla bilgi için bkz. [Windows `<windowsAuthentication>` kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="e029a-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e029a-209">**WebSockets (Isteğe bağlı)**</span><span class="sxs-lookup"><span data-stu-id="e029a-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e029a-210">WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="e029a-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e029a-211">WebSockets etkinleştirmek için şu düğümleri genişletin: **World Wide Web Services**  >  **uygulaması geliştirme özellikleri**.</span><span class="sxs-lookup"><span data-stu-id="e029a-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="e029a-212">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e029a-213">Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="e029a-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e029a-214">IIS yüklemesi için yeniden başlatma gerekiyorsa, sistemi yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="e029a-214">If the IIS installation requires a restart, restart the system.</span></span>

![IIS Yönetim Konsolu ve World Wide Web Hizmetleri Windows Özellikleri ' nde seçilidir.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="e029a-216">Sanal Dizinler</span><span class="sxs-lookup"><span data-stu-id="e029a-216">Virtual Directories</span></span>

<span data-ttu-id="e029a-217">[IIS sanal dizinleri](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ASP.NET Core uygulamalarla desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e029a-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="e029a-218">Bir uygulama, bir [alt uygulama](#sub-applications)olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="e029a-219">Alt uygulamalar</span><span class="sxs-lookup"><span data-stu-id="e029a-219">Sub-applications</span></span>

<span data-ttu-id="e029a-220">ASP.NET Core bir uygulama, bir [IIS alt uygulaması (alt uygulama)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="e029a-221">Alt uygulamanın yolu, kök uygulamanın URL 'sinin bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="e029a-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="e029a-222">Alt uygulama içindeki statik varlık bağlantıları, tilde işareti ( `~/` ) gösterimini kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e029a-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="e029a-223">Tilde işareti gösterimi, alt uygulamanın pathbase 'i işlenmiş göreli bağlantıya eklemek için bir [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) tetikler.</span><span class="sxs-lookup"><span data-stu-id="e029a-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="e029a-224">Üzerindeki bir alt uygulama için `/subapp_path` ile bağlantılı bir görüntü `src="~/image.png"` olarak işlenir `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="e029a-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="e029a-225">Kök uygulamanın statik dosya ara yazılımı statik dosya isteğini işlemez.</span><span class="sxs-lookup"><span data-stu-id="e029a-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="e029a-226">İstek, alt uygulamanın statik dosya ara yazılımı tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="e029a-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="e029a-227">Statik bir varlığın `src` özniteliği mutlak bir yola ayarlandıysa (örneğin, `src="/image.png"` ), bağlantı alt uygulamanın pathbase olmadan işlenir.</span><span class="sxs-lookup"><span data-stu-id="e029a-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="e029a-228">Kök uygulamanın statik dosya ara yazılımı, statik varlık kök uygulamadan kullanılabilir olmadığı takdirde *404-bulunamayan* bir Yanıt ile sonuçlanır. Bu, kök uygulamanın [Web kökünden](xref:fundamentals/index#web-root)varlık sunmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="e029a-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="e029a-229">Bir ASP.NET Core uygulamasını başka bir ASP.NET Core uygulaması altında alt uygulama olarak barındırmak için:</span><span class="sxs-lookup"><span data-stu-id="e029a-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="e029a-230">Alt uygulama için bir uygulama havuzu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e029a-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="e029a-231">.NET Core için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı masaüstü CLR (.NET CLR) değil, çalışan işlemde barındırmak için önyüklenirken **.NET CLR sürümünü** **yönetilen kod olmadan** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="e029a-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="e029a-232">Kök siteyi, kök sitenin altındaki bir klasörde bulunan alt uygulamayla IIS Yöneticisi 'ne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e029a-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="e029a-233">IIS Yöneticisi 'ndeki alt uygulama klasörüne sağ tıklayın ve **uygulamaya Dönüştür**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="e029a-234">**Uygulama Ekle** iletişim kutusunda, alt uygulama için oluşturduğunuz uygulama havuzunu atamak üzere **uygulama havuzunun** **Seç** düğmesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e029a-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="e029a-235">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-235">Select **OK**.</span></span>

<span data-ttu-id="e029a-236">Ayrı bir uygulama havuzunun alt uygulamaya atanması, işlem içi barındırma modelinin kullanıldığı bir gereksinimdir.</span><span class="sxs-lookup"><span data-stu-id="e029a-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="e029a-237">İşlem içi barındırma modeli ve ASP.NET Core modülünü yapılandırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="e029a-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="e029a-238">Uygulama havuzları</span><span class="sxs-lookup"><span data-stu-id="e029a-238">Application Pools</span></span>

<span data-ttu-id="e029a-239">Uygulama havuzu yalıtımı, barındırma modeliyle belirlenir:</span><span class="sxs-lookup"><span data-stu-id="e029a-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="e029a-240">İşlem içi barındırma: uygulamaların ayrı uygulama havuzlarında çalışması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e029a-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="e029a-241">İşlem dışı barındırma: her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmayı öneririz.</span><span class="sxs-lookup"><span data-stu-id="e029a-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="e029a-242">IIS **Web sitesi ekleme** iletişim kutusu varsayılan olarak uygulama başına tek bir uygulama havuzu olur.</span><span class="sxs-lookup"><span data-stu-id="e029a-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="e029a-243">Bir **site adı** sağlandığında, metin otomatik olarak **uygulama havuzu** metin kutusuna aktarılır.</span><span class="sxs-lookup"><span data-stu-id="e029a-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="e029a-244">Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e029a-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="e029a-245">Uygulama havuzu Identity</span><span class="sxs-lookup"><span data-stu-id="e029a-245">Application Pool Identity</span></span>

<span data-ttu-id="e029a-246">Bir uygulama havuzu kimliği hesabı, bir uygulamanın etki alanı veya yerel hesap oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="e029a-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="e029a-247">IIS 8,0 veya sonraki sürümlerde, IIS Yönetici çalışan Işlemi (WAS), yeni uygulama havuzunun adıyla bir sanal hesap oluşturur ve varsayılan olarak bu hesap altında uygulama havuzunun çalışan işlemlerini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="e029a-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="e029a-248">Uygulama havuzu için **Gelişmiş ayarlar** altındaki IIS Yönetim Konsolu ' nda, **Identity** öğesinin kullanılmak üzere ayarlandığından emin olun `ApplicationPoolIdentity` :</span><span class="sxs-lookup"><span data-stu-id="e029a-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![Uygulama havuzu Gelişmiş ayarları iletişim kutusu](index/_static/apppool-identity.png)

<span data-ttu-id="e029a-250">IIS Yönetim işlemi, Windows güvenlik sisteminde uygulama havuzunun adıyla güvenli bir tanımlayıcı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e029a-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="e029a-251">Bu kimlik kullanılarak kaynakların güvenliği sağlanmış olabilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="e029a-252">Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi konsolunda gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="e029a-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="e029a-253">IIS çalışan işlemi uygulamaya yükseltilmiş erişim gerektiriyorsa, uygulamayı içeren dizinin Access Control listesini (ACL) değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e029a-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="e029a-254">Windows Gezgini 'ni açın ve dizine gidin.</span><span class="sxs-lookup"><span data-stu-id="e029a-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="e029a-255">Dizine sağ tıklayıp **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="e029a-256">**Güvenlik** sekmesinde, **Düzenle** düğmesini ve ardından **Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="e029a-257">**Konumlar** düğmesini seçin ve sistemin seçili olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="e029a-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="e029a-258">`IIS AppPool\{APP POOL NAME}`Yer tutucunun `{APP POOL NAME}` uygulama havuzu adı olduğu, burada **Seçilecek nesne adlarını girin** alanına biçim girin.</span><span class="sxs-lookup"><span data-stu-id="e029a-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="e029a-259">**Adları denetle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-259">Select the **Check Names** button.</span></span> <span data-ttu-id="e029a-260">*DefaultAppPool* için, kullanarak adları kontrol edin `IIS AppPool\DefaultAppPool` .</span><span class="sxs-lookup"><span data-stu-id="e029a-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="e029a-261">**Adları denetle** düğmesi seçildiğinde, `DefaultAppPool` nesne adları alanında bir değeri gösterilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="e029a-262">Uygulama havuzu adının doğrudan nesne adları alanına girilmesi mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="e029a-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="e029a-263">`IIS AppPool\{APP POOL NAME}` `{APP POOL NAME}` Nesne adı denetlenirken yer tutucunun uygulama havuzu adı olduğu biçimi kullanın.</span><span class="sxs-lookup"><span data-stu-id="e029a-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "ad denetle" seçmeden önce "DefaultAppPool" uygulama havuzu adı, nesne adları alanında "IIS AppPool" öğesine eklenir \" .](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="e029a-265">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-265">Select **OK**.</span></span>

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "adları denetle" seçeneğini belirledikten sonra, nesne adları alanında "DefaultAppPool" nesne adı gösterilir.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="e029a-267">Okuma &amp; yürütme izinleri varsayılan olarak verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="e029a-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="e029a-268">Gerektiğinde ek izinler sağlayın.</span><span class="sxs-lookup"><span data-stu-id="e029a-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="e029a-269">Erişim, **ıacl 'ler** Aracı kullanılarak bir komut isteminde de verilebilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="e029a-270">Örnek olarak *DefaultAppPool* kullanarak, aşağıdaki komut kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e029a-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="e029a-271">Daha fazla bilgi için [ıcacl 'ler](/windows-server/administration/windows-commands/icacls) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="e029a-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e029a-272">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="e029a-272">HTTP/2 support</span></span>

<span data-ttu-id="e029a-273">[Http/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki IIS dağıtım senaryolarında ASP.NET Core desteklenir:</span><span class="sxs-lookup"><span data-stu-id="e029a-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="e029a-274">İşlem içi</span><span class="sxs-lookup"><span data-stu-id="e029a-274">In-process</span></span>
  * <span data-ttu-id="e029a-275">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="e029a-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e029a-276">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="e029a-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="e029a-277">İşlem dışı</span><span class="sxs-lookup"><span data-stu-id="e029a-277">Out-of-process</span></span>
  * <span data-ttu-id="e029a-278">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="e029a-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e029a-279">Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak [Kestrel sunucusuyla](xref:fundamentals/servers/kestrel) ters proxy bağlantısı http/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="e029a-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="e029a-280">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="e029a-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e029a-281">Bir HTTP/2 bağlantısı oluşturulduğunda, işlem içi dağıtım için [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="e029a-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="e029a-282">Bir HTTP/2 bağlantısı oluşturulduğunda, işlem dışı bir dağıtım için [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="e029a-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="e029a-283">İşlem içi ve işlem dışı barındırma modelleri hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="e029a-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e029a-284">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="e029a-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e029a-285">HTTP/2 bağlantısı kurulmadıysa bağlantılar HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="e029a-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="e029a-286">IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için bkz. [IIS 'de http/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="e029a-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="e029a-287">CORS ön denetim istekleri</span><span class="sxs-lookup"><span data-stu-id="e029a-287">CORS preflight requests</span></span>

<span data-ttu-id="e029a-288">*Bu bölüm, yalnızca .NET Framework hedefleyen ASP.NET Core uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="e029a-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="e029a-289">.NET Framework hedefleyen ASP.NET Core bir uygulama için, Seçenekler istekleri uygulamaya varsayılan olarak IIS 'de aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="e029a-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="e029a-290">' De uygulama IIS işleyicilerini seçenek isteklerini geçecek şekilde yapılandırma hakkında bilgi edinmek için `web.config` bkz. [ASP.NET Web API 2 ' de çapraz kaynak isteklerini ETKINLEŞTIRME: CORS 'Nin nasıl çalıştığı](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="e029a-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="e029a-291">Uygulama başlatma modülü ve boşta kalma zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="e029a-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="e029a-292">ASP.NET Core modülü sürüm 2 tarafından IIS 'de barındırıldığında:</span><span class="sxs-lookup"><span data-stu-id="e029a-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="e029a-293">[Uygulama başlatma modülü](#application-initialization-module): uygulamanın çalışan işlem yeniden başlatması veya sunucu yeniden başlatması üzerinde otomatik olarak başlayacak şekilde, [uygulamanın barındırılan veya](xref:host-and-deploy/iis/in-process-hosting) [işlem dışı](xref:host-and-deploy/iis/out-of-process-hosting) bir şekilde yapılandırılmış olması için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="e029a-294">[Boşta kalma zaman aşımı](#idle-timeout): uygulamanın barındırılan, [işlem](xref:host-and-deploy/iis/in-process-hosting) yapılmayan dönemler sırasında zaman aşımına uğramaması için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="e029a-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="e029a-295">Uygulama başlatma modülü</span><span class="sxs-lookup"><span data-stu-id="e029a-295">Application Initialization Module</span></span>

<span data-ttu-id="e029a-296">*İşlem içi ve işlem dışı barındırılan uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="e029a-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="e029a-297">[IIS uygulaması başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) , uygulama havuzu başlatıldığında veya geri DÖNÜŞTÜRÜLDÜĞÜNDE uygulamaya http isteği gönderen bir IIS özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="e029a-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="e029a-298">İstek, uygulamayı başlatmak üzere tetikler.</span><span class="sxs-lookup"><span data-stu-id="e029a-298">The request triggers the app to start.</span></span> <span data-ttu-id="e029a-299">Varsayılan olarak IIS, uygulamayı başlatmak için uygulamanın kök URL 'SI () için bir istek yayınlar `/` (yapılandırma hakkında daha fazla bilgi için [ek kaynaklara](#application-initialization-module-and-idle-timeout-additional-resources) bakın).</span><span class="sxs-lookup"><span data-stu-id="e029a-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="e029a-300">IIS uygulama başlatma rolü özelliğinin etkin olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="e029a-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="e029a-301">IIS 'yi yerel olarak kullanırken Windows 7 veya üzeri masaüstü sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="e029a-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="e029a-302">**Denetim Masası**  >  **programları**  >  **Programlar ve Özellikler**  >  **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="e029a-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="e029a-303">**Internet Information Services**  >  **World Wide Web Services**  >  **uygulama geliştirme özelliklerini** açın.</span><span class="sxs-lookup"><span data-stu-id="e029a-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="e029a-304">**Uygulama başlatma** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="e029a-305">Windows Server 2008 R2 veya sonraki sürümlerde:</span><span class="sxs-lookup"><span data-stu-id="e029a-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="e029a-306">**Rol ve Özellik Ekleme Sihirbazı 'nı** açın.</span><span class="sxs-lookup"><span data-stu-id="e029a-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="e029a-307">**Rol hizmetlerini Seç** panelinde, **uygulama geliştirme** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="e029a-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="e029a-308">**Uygulama başlatma** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="e029a-309">Site için uygulama başlatma modülünü etkinleştirmek üzere aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e029a-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="e029a-310">IIS Yöneticisi 'Ni kullanma:</span><span class="sxs-lookup"><span data-stu-id="e029a-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="e029a-311">**Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="e029a-312">Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="e029a-313">Varsayılan **Başlangıç modu** `OnDemand` .</span><span class="sxs-lookup"><span data-stu-id="e029a-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="e029a-314">**Başlangıç modunu** olarak ayarlayın `AlwaysRunning` .</span><span class="sxs-lookup"><span data-stu-id="e029a-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="e029a-315">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-315">Select **OK**.</span></span>
  1. <span data-ttu-id="e029a-316">**Bağlantılar** panelinde **siteler** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="e029a-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="e029a-317">Uygulamaya sağ tıklayın ve **Web sitesi**  >  **Gelişmiş ayarlarını** Yönet ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="e029a-318">Varsayılan **önyükleme etkin** ayarı `False` .</span><span class="sxs-lookup"><span data-stu-id="e029a-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="e029a-319">**Önyüklemeyi etkin** olarak ayarlayın `True` .</span><span class="sxs-lookup"><span data-stu-id="e029a-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="e029a-320">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-320">Select **OK**.</span></span>

* <span data-ttu-id="e029a-321">Kullanarak `web.config` , `<applicationInitialization>` öğesini `doAppInitAfterRestart` olarak ayarlanmış öğesini `true` `<system.webServer>` uygulamanın dosyasındaki öğelerine ekleyin `web.config` :</span><span class="sxs-lookup"><span data-stu-id="e029a-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="e029a-322">Boşta Kalma Süresi Zaman Aşımı</span><span class="sxs-lookup"><span data-stu-id="e029a-322">Idle Timeout</span></span>

<span data-ttu-id="e029a-323">*Yalnızca işlem sırasında barındırılan uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="e029a-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="e029a-324">Uygulamanın çalışmasını engellemek için, IIS Yöneticisi 'Ni kullanarak uygulama havuzunun boşta kalma zaman aşımını ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="e029a-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="e029a-325">**Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="e029a-326">Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="e029a-327">Varsayılan **boşta kalma zaman aşımı (dakika)** `20` dakikadır.</span><span class="sxs-lookup"><span data-stu-id="e029a-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="e029a-328">**Boşta kalma süresi (dakika)** `0` değerini (sıfır) olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="e029a-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="e029a-329">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e029a-329">Select **OK**.</span></span>
1. <span data-ttu-id="e029a-330">Çalışan işlemini geri dönüştür.</span><span class="sxs-lookup"><span data-stu-id="e029a-330">Recycle the worker process.</span></span>

<span data-ttu-id="e029a-331">[İşlem dışı](xref:host-and-deploy/iis/out-of-process-hosting) barındırılan uygulamaların zaman aşımına uğramasını engellemek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e029a-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="e029a-332">Uygulamayı çalışır durumda tutmak için bir dış hizmetten ping yapın.</span><span class="sxs-lookup"><span data-stu-id="e029a-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="e029a-333">Uygulama yalnızca arka plan hizmetleri barındırıyorsa, IIS barındırmaktan kaçının ve [ASP.NET Core uygulamasını barındırmak için bir Windows hizmeti](xref:host-and-deploy/windows-service)kullanın.</span><span class="sxs-lookup"><span data-stu-id="e029a-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="e029a-334">Uygulama başlatma modülü ve boşta kalma zaman aşımı ek kaynakları</span><span class="sxs-lookup"><span data-stu-id="e029a-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="e029a-335">IIS 8,0 uygulama başlatma</span><span class="sxs-lookup"><span data-stu-id="e029a-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="e029a-336">[Uygulama başlatma `<applicationInitialization>` ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="e029a-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="e029a-337">[Uygulama havuzu `<processModel>` Için Işlem modeli ayarları ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="e029a-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="e029a-338">Modül, şema ve yapılandırma dosyası konumları</span><span class="sxs-lookup"><span data-stu-id="e029a-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="e029a-339">Modül</span><span class="sxs-lookup"><span data-stu-id="e029a-339">Module</span></span>

<span data-ttu-id="e029a-340">**IIS (X86/AMD64)**:</span><span class="sxs-lookup"><span data-stu-id="e029a-340">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="e029a-341">**IIS Express (X86/AMD64)**:</span><span class="sxs-lookup"><span data-stu-id="e029a-341">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="e029a-342">Şema</span><span class="sxs-lookup"><span data-stu-id="e029a-342">Schema</span></span>

<span data-ttu-id="e029a-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="e029a-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="e029a-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="e029a-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="e029a-345">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e029a-345">Configuration</span></span>

<span data-ttu-id="e029a-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="e029a-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="e029a-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="e029a-347">**IIS Express**</span></span>

* <span data-ttu-id="e029a-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="e029a-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="e029a-349">*iisexpress.exe* CLı `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="e029a-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="e029a-350">Dosyalar dosyada arayarak bulunabilir `aspnetcore` `applicationHost.config` .</span><span class="sxs-lookup"><span data-stu-id="e029a-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
