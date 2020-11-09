---
title: SignalRAzure App Service için ASP.NET Core uygulaması yayımlama
author: bradygaster
description: 'Azure App Service için ASP.NET Core uygulamasının nasıl yayımlanacağını öğrenin SignalR .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/02/2020
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 8e6d36fe0b38486f94078b8f9cf12b852da7e0d9
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234522"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="6b344-103">SignalRAzure App Service için ASP.NET Core uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="6b344-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="6b344-104">, [Brady Gaster](https://twitter.com/bradygaster) tarafından</span><span class="sxs-lookup"><span data-stu-id="6b344-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="6b344-105">[Azure App Service](/azure/app-service/app-service-web-overview) , Web uygulamalarını barındırmak için ASP.NET Core dahil olmak üzere bir [Microsoft bulut bilgi işlem](https://azure.microsoft.com/) platformu hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="6b344-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="6b344-106">Bu makale, SignalR Visual Studio 'dan bir ASP.NET Core uygulaması yayımlamaya başvurur.</span><span class="sxs-lookup"><span data-stu-id="6b344-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="6b344-107">Daha fazla bilgi için bkz. [ SignalR Azure hizmeti](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="6b344-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="6b344-108">Uygulamayı yayımlama</span><span class="sxs-lookup"><span data-stu-id="6b344-108">Publish the app</span></span>

<span data-ttu-id="6b344-109">Bu makalede, Visual Studio 'daki araçları kullanarak yayımlama ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="6b344-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="6b344-110">Visual Studio Code kullanıcılar, Azure 'da uygulama yayımlamak için [Azure CLI](/cli/azure) komutlarını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="6b344-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="6b344-111">Daha fazla bilgi için bkz. [komut satırı araçlarıyla Azure 'da ASP.NET Core uygulama yayımlama](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="6b344-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="6b344-112">**Çözüm Gezgini** projede projeye sağ tıklayın ve **Yayımla** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="6b344-112">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>

1. <span data-ttu-id="6b344-113">**Bir yayımlama hedefi seç** iletişim kutusunda **App Service** ve **Yeni oluştur** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6b344-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="6b344-114">**Yayımla** düğmesi açılan listesinden **Profil oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6b344-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="6b344-115">**App Service oluştur** iletişim kutusunda aşağıdaki tabloda açıklanan bilgileri girin ve **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6b344-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create** .</span></span>

   | <span data-ttu-id="6b344-116">Öğe</span><span class="sxs-lookup"><span data-stu-id="6b344-116">Item</span></span>               | <span data-ttu-id="6b344-117">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6b344-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="6b344-118">**Ad**</span><span class="sxs-lookup"><span data-stu-id="6b344-118">**Name**</span></span>           | <span data-ttu-id="6b344-119">Uygulamanın benzersiz adı.</span><span class="sxs-lookup"><span data-stu-id="6b344-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="6b344-120">**Abonelik**</span><span class="sxs-lookup"><span data-stu-id="6b344-120">**Subscription**</span></span>   | <span data-ttu-id="6b344-121">Uygulamanın kullandığı Azure aboneliği.</span><span class="sxs-lookup"><span data-stu-id="6b344-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="6b344-122">**Kaynak Grubu**</span><span class="sxs-lookup"><span data-stu-id="6b344-122">**Resource Group**</span></span> | <span data-ttu-id="6b344-123">Uygulamanın ait olduğu ilgili kaynaklar grubu.</span><span class="sxs-lookup"><span data-stu-id="6b344-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="6b344-124">**Barındırma Planı**</span><span class="sxs-lookup"><span data-stu-id="6b344-124">**Hosting Plan**</span></span>   | <span data-ttu-id="6b344-125">Web uygulaması için fiyatlandırma planı.</span><span class="sxs-lookup"><span data-stu-id="6b344-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="6b344-126">**Hizmet bağımlılıkları** bölümünde **Azure SignalR hizmeti** ' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="6b344-126">Select **Azure SignalR Service** in the **Service Dependencies** section.</span></span> <span data-ttu-id="6b344-127">Düğmeyi seçin **+** :</span><span class="sxs-lookup"><span data-stu-id="6b344-127">Select the **+** button:</span></span>

   ![Ekle açılan listesinde Azure::: No-Loc (SignalR)::: Service seçimini gösteren bağımlılıklar alanı](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="6b344-129">**Azure SignalR hizmeti** iletişim kutusunda **Yeni bir Azure SignalR hizmet örneği oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6b344-129">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance** .</span></span>

1. <span data-ttu-id="6b344-130">Bir **ad** , **kaynak grubu** ve **konum** belirtin.</span><span class="sxs-lookup"><span data-stu-id="6b344-130">Provide a **Name** , **Resource Group** , and **Location** .</span></span> <span data-ttu-id="6b344-131">**Azure SignalR hizmeti** iletişim kutusuna dönün ve **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6b344-131">Return to the **Azure SignalR Service** dialog and select **Add** .</span></span>

<span data-ttu-id="6b344-132">Visual Studio aşağıdaki görevleri tamamlar:</span><span class="sxs-lookup"><span data-stu-id="6b344-132">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="6b344-133">Yayımlama ayarlarını içeren bir yayımlama profili oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6b344-133">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="6b344-134">Belirtilen ayrıntılarla bir *Azure Web uygulaması* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6b344-134">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="6b344-135">Uygulamayı yayımlar.</span><span class="sxs-lookup"><span data-stu-id="6b344-135">Publishes the app.</span></span>
* <span data-ttu-id="6b344-136">Web uygulamasını yükleyen bir tarayıcı başlatır.</span><span class="sxs-lookup"><span data-stu-id="6b344-136">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="6b344-137">Uygulamanın URL 'sinin biçimi `{APP SERVICE NAME}.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="6b344-137">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="6b344-138">Örneğin, adlı bir uygulamanın `SignalRChatApp` URL 'si vardır `https://signalrchatapp.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="6b344-138">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="6b344-139">Bir Preview .NET Core sürümünü hedefleyen bir uygulama dağıtırken HTTP *502,2-Hatalı ağ geçidi* hatası oluşursa, bu sorunu çözmek için [Azure App Service ASP.NET Core önizleme sürümünü dağıtma](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="6b344-139">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="6b344-140">Uygulamayı Azure App Service yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6b344-140">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="6b344-141">*Bu bölüm yalnızca Azure hizmetini kullanmayan uygulamalar için geçerlidir SignalR .*</span><span class="sxs-lookup"><span data-stu-id="6b344-141">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="6b344-142">Uygulama Azure SignalR hizmetini kullanıyorsa, App Service uygulama Isteği yönlendirme (ARR) benzeşimi ve bu bölümde açıklanan Web Yuvaları yapılandırmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="6b344-142">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="6b344-143">İstemciler Web yuvalarını SignalR doğrudan uygulamaya değil Azure hizmetine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="6b344-143">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="6b344-144">Azure hizmeti olmadan barındırılan uygulamalar için SignalR şunları etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="6b344-144">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="6b344-145">[ARR benzeşimi] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(ARR- cookie ) -for-Azure-web-apps.html) bir kullanıcıdan gelen istekleri yeniden aynı App Service örneğine yönlendirmek için.</span><span class="sxs-lookup"><span data-stu-id="6b344-145">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="6b344-146">Varsayılan ayar **Açık '** dır.</span><span class="sxs-lookup"><span data-stu-id="6b344-146">The default setting is **On** .</span></span>
* <span data-ttu-id="6b344-147">Web Sockets taşımanın çalışmasına izin veren [Web Yuvaları](xref:fundamentals/websockets) .</span><span class="sxs-lookup"><span data-stu-id="6b344-147">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="6b344-148">Varsayılan ayar **kapalıdır** .</span><span class="sxs-lookup"><span data-stu-id="6b344-148">The default setting is **Off** .</span></span>

1. <span data-ttu-id="6b344-149">Azure portal, **uygulama hizmetleri** ' nde Web uygulamasına gidin.</span><span class="sxs-lookup"><span data-stu-id="6b344-149">In the Azure portal, navigate to the web app in **App Services** .</span></span>
1. <span data-ttu-id="6b344-150">**Yapılandırma**  >  **genel ayarlarını** açın.</span><span class="sxs-lookup"><span data-stu-id="6b344-150">Open **Configuration** > **General settings** .</span></span>
1. <span data-ttu-id="6b344-151">**Web yuvalarını** **Açık** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6b344-151">Set **Web sockets** to **On** .</span></span>
1. <span data-ttu-id="6b344-152">**ARR benzeşiminin** **Açık** olarak ayarlandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6b344-152">Verify that **ARR affinity** is set to **On** .</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="6b344-153">App Service planı limitleri</span><span class="sxs-lookup"><span data-stu-id="6b344-153">App Service Plan limits</span></span>

<span data-ttu-id="6b344-154">Web Yuvaları ve diğer aktarımlar, seçilen App Service planına göre sınırlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6b344-154">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="6b344-155">Daha fazla bilgi için [Azure abonelik ve hizmet sınırları, Kotalar ve kısıtlamalar](/azure/azure-subscription-service-limits#app-service-limits) makalesinin *Azure Cloud Services sınırları* ve *App Service sınırları* bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="6b344-155">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6b344-156">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6b344-156">Additional resources</span></span>

* [<span data-ttu-id="6b344-157">Azure hizmeti nedir SignalR ?</span><span class="sxs-lookup"><span data-stu-id="6b344-157">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="6b344-158">Komut satırı araçlarıyla Azure 'da ASP.NET Core uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="6b344-158">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="6b344-159">Azure 'da ASP.NET Core Preview uygulamaları barındırma ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="6b344-159">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
