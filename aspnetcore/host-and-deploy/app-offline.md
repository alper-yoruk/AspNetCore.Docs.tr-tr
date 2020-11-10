---
title: Uygulama çevrimdışı dosyası (app_offline.htm)
author: rick-anderson
description: Uygulama çevrimdışı dosyasının ( `app_offline.htm` ) ASP.NET Core modülüyle nasıl çalıştığını öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 29f3fc5ecd18196d914a46629bc9eb50b183bf61
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431036"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="64d36-103">Uygulama çevrimdışı dosyası ( `app_offline.htm` )</span><span class="sxs-lookup"><span data-stu-id="64d36-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="64d36-104">Uygulama çevrimdışı dosyası ( `app_offline.htm` ), bir uygulamayı kapatmak için ASP.NET Core modülü tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64d36-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="64d36-105">Uygulamanın kök dizininde ada sahip bir dosya `app_offline.htm` algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="64d36-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="64d36-106">Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="64d36-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="64d36-107">`app_offline.htm`Dosya mevcut olsa da, ASP.NET Core modülü dosya içeriğini geri göndererek isteklere yanıt verir `app_offline.htm` .</span><span class="sxs-lookup"><span data-stu-id="64d36-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="64d36-108">`app_offline.htm`Dosya kaldırıldığında, sonraki istek uygulamayı başlatır.</span><span class="sxs-lookup"><span data-stu-id="64d36-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="64d36-109">İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir.</span><span class="sxs-lookup"><span data-stu-id="64d36-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="64d36-110">Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.</span><span class="sxs-lookup"><span data-stu-id="64d36-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="64d36-111">Kilitli dağıtım dosyaları</span><span class="sxs-lookup"><span data-stu-id="64d36-111">Locked deployment files</span></span>

<span data-ttu-id="64d36-112">Dağıtım klasöründeki dosyalar, uygulama çalışırken kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="64d36-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="64d36-113">Dağıtım sırasında kilitli dosyaların üzerine yazılamaz.</span><span class="sxs-lookup"><span data-stu-id="64d36-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="64d36-114">`app_offline.htm` , kilitli dosyaları serbest bırakmaya yönelik birincil mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="64d36-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="64d36-115">`app_offline.htm` , uygulamayı düzgün bir şekilde durdurmak ve başlatmak için Web Dağıtımı tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64d36-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="64d36-116">`app_offline.htm` uygulamayı başlatmak ve durdurmak için el ile kullanılabilir (PowerShell 5 veya üzerini gerektirir):</span><span class="sxs-lookup"><span data-stu-id="64d36-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'


New-Item -Path $pathToApp -Name "app_offline.htm" -ItemType "file"

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp\app_offline.htm
```

<span data-ttu-id="64d36-117">Önceki PowerShell betiğinden:</span><span class="sxs-lookup"><span data-stu-id="64d36-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="64d36-118">Yer tutucu, `{PATH TO APP}` uygulamanın yoludur.</span><span class="sxs-lookup"><span data-stu-id="64d36-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="64d36-119">`New-Item`Komut, uygulama havuzunu sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="64d36-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="64d36-120">`Remove-Item`Komut, uygulama havuzunu başlatır.</span><span class="sxs-lookup"><span data-stu-id="64d36-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="64d36-121">`New-Item`Komutu ile komutu arasındaki komutlar, `Remove-Item` uygulamayı dağıtmak için geliştirici tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="64d36-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="64d36-122">Ayrıca, sunucudaki IIS Yöneticisi 'ndeki uygulama havuzunu el ile durdurarak dosyaların kilidi açılabilir.</span><span class="sxs-lookup"><span data-stu-id="64d36-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="64d36-123">`app_offine.htm`Uygulama havuzunu durdurup yeniden başlatmak IÇIN IIS Yöneticisi 'ni kullanırken dosyayı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="64d36-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
