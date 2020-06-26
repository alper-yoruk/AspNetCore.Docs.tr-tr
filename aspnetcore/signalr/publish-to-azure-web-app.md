---
title: SignalRAzure App Service için ASP.NET Core uygulaması yayımlama
author: bradygaster
description: Azure App Service için ASP.NET Core uygulamasının nasıl yayımlanacağını öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: d3f48b3171012b03fcaf7665c2089b27d37bbeca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408844"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a><span data-ttu-id="a2741-103">SignalRAzure App Service için ASP.NET Core uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="a2741-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="a2741-104">, [Brady Gaster](https://twitter.com/bradygaster) tarafından</span><span class="sxs-lookup"><span data-stu-id="a2741-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="a2741-105">[Azure App Service](/azure/app-service/app-service-web-overview) , Web uygulamalarını barındırmak için ASP.NET Core dahil olmak üzere bir [Microsoft bulut bilgi işlem](https://azure.microsoft.com/) platformu hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="a2741-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="a2741-106">Bu makale, SignalR Visual Studio 'dan bir ASP.NET Core uygulaması yayımlamaya başvurur.</span><span class="sxs-lookup"><span data-stu-id="a2741-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="a2741-107">Daha fazla bilgi için bkz. [ SignalR Azure hizmeti](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="a2741-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="a2741-108">Uygulamayı yayımlama</span><span class="sxs-lookup"><span data-stu-id="a2741-108">Publish the app</span></span>

<span data-ttu-id="a2741-109">Bu makalede, Visual Studio 'daki araçları kullanarak yayımlama ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="a2741-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="a2741-110">Visual Studio Code kullanıcılar, Azure 'da uygulama yayımlamak için [Azure CLI](/cli/azure) komutlarını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="a2741-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="a2741-111">Daha fazla bilgi için bkz. [komut satırı araçlarıyla Azure 'da ASP.NET Core uygulama yayımlama](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="a2741-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="a2741-112">**Çözüm Gezgini** projede projeye sağ tıklayın ve **Yayımla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="a2741-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="a2741-113">**Bir yayımlama hedefi seç** iletişim kutusunda **App Service** ve **Yeni oluştur** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="a2741-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="a2741-114">**Yayımla** düğmesi açılan listesinden **Profil oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a2741-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="a2741-115">**App Service oluştur** iletişim kutusunda aşağıdaki tabloda açıklanan bilgileri girin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a2741-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="a2741-116">Öğe</span><span class="sxs-lookup"><span data-stu-id="a2741-116">Item</span></span>               | <span data-ttu-id="a2741-117">Açıklama</span><span class="sxs-lookup"><span data-stu-id="a2741-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="a2741-118">**Adı**</span><span class="sxs-lookup"><span data-stu-id="a2741-118">**Name**</span></span>           | <span data-ttu-id="a2741-119">Uygulamanın benzersiz adı.</span><span class="sxs-lookup"><span data-stu-id="a2741-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="a2741-120">**Abonelik**</span><span class="sxs-lookup"><span data-stu-id="a2741-120">**Subscription**</span></span>   | <span data-ttu-id="a2741-121">Uygulamanın kullandığı Azure aboneliği.</span><span class="sxs-lookup"><span data-stu-id="a2741-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="a2741-122">**Kaynak grubu**</span><span class="sxs-lookup"><span data-stu-id="a2741-122">**Resource Group**</span></span> | <span data-ttu-id="a2741-123">Uygulamanın ait olduğu ilgili kaynaklar grubu.</span><span class="sxs-lookup"><span data-stu-id="a2741-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="a2741-124">**Barındırma Planı**</span><span class="sxs-lookup"><span data-stu-id="a2741-124">**Hosting Plan**</span></span>   | <span data-ttu-id="a2741-125">Web uygulaması için fiyatlandırma planı.</span><span class="sxs-lookup"><span data-stu-id="a2741-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="a2741-126">**Bağımlılıklar**ekleme açılan listesinden **Azure SignalR hizmetini** seçin  >  **Add** :</span><span class="sxs-lookup"><span data-stu-id="a2741-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   <span data-ttu-id="a2741-127">![Ekle açılan listesinde Azure hizmeti seçimini gösteren bağımlılıklar alanı SignalR](publish-to-azure-web-app/_static/signalr-service-dependency.png)</span><span class="sxs-lookup"><span data-stu-id="a2741-127">![Dependencies area showing the selection of Azure SignalR Service in the Add drop-down list](publish-to-azure-web-app/_static/signalr-service-dependency.png)</span></span>

1. <span data-ttu-id="a2741-128">**Azure SignalR hizmeti** iletişim kutusunda **Yeni bir Azure SignalR hizmet örneği oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="a2741-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="a2741-129">Bir **ad**, **kaynak grubu**ve **konum**belirtin.</span><span class="sxs-lookup"><span data-stu-id="a2741-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="a2741-130">**Azure SignalR hizmeti** iletişim kutusuna dönün ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a2741-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="a2741-131">Visual Studio aşağıdaki görevleri tamamlar:</span><span class="sxs-lookup"><span data-stu-id="a2741-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="a2741-132">Yayımlama ayarlarını içeren bir yayımlama profili oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a2741-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="a2741-133">Belirtilen ayrıntılarla bir *Azure Web uygulaması* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a2741-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="a2741-134">Uygulamayı yayımlar.</span><span class="sxs-lookup"><span data-stu-id="a2741-134">Publishes the app.</span></span>
* <span data-ttu-id="a2741-135">Web uygulamasını yükleyen bir tarayıcı başlatır.</span><span class="sxs-lookup"><span data-stu-id="a2741-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="a2741-136">Uygulamanın URL 'sinin biçimi `{APP SERVICE NAME}.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="a2741-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="a2741-137">Örneğin, adlı bir uygulamanın `SignalRChatApp` URL 'si vardır `https://signalrchatapp.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="a2741-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="a2741-138">Bir Preview .NET Core sürümünü hedefleyen bir uygulama dağıtırken HTTP *502,2-Hatalı ağ geçidi* hatası oluşursa, bu sorunu çözmek için [Azure App Service ASP.NET Core önizleme sürümünü dağıtma](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="a2741-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="a2741-139">Uygulamayı Azure App Service yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a2741-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="a2741-140">*Bu bölüm yalnızca Azure hizmetini kullanmayan uygulamalar için geçerlidir SignalR .*</span><span class="sxs-lookup"><span data-stu-id="a2741-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="a2741-141">Uygulama Azure SignalR hizmetini kullanıyorsa, App Service uygulama Isteği yönlendirme (ARR) benzeşimi ve bu bölümde açıklanan Web Yuvaları yapılandırmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="a2741-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="a2741-142">İstemciler Web yuvalarını SignalR doğrudan uygulamaya değil Azure hizmetine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="a2741-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="a2741-143">Azure hizmeti olmadan barındırılan uygulamalar için SignalR şunları etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="a2741-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="a2741-144">İstekleri bir kullanıcıdan tekrar aynı App Service örneğine yönlendirmek için [ARR benzeşimi](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) .</span><span class="sxs-lookup"><span data-stu-id="a2741-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="a2741-145">Varsayılan ayar **Açık '** dır.</span><span class="sxs-lookup"><span data-stu-id="a2741-145">The default setting is **On**.</span></span>
* <span data-ttu-id="a2741-146">Web Sockets taşımanın çalışmasına izin veren [Web Yuvaları](xref:fundamentals/websockets) .</span><span class="sxs-lookup"><span data-stu-id="a2741-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="a2741-147">Varsayılan ayar **kapalıdır**.</span><span class="sxs-lookup"><span data-stu-id="a2741-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="a2741-148">Azure portal, **uygulama hizmetleri**' nde Web uygulamasına gidin.</span><span class="sxs-lookup"><span data-stu-id="a2741-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="a2741-149">**Yapılandırma**  >  **genel ayarlarını**açın.</span><span class="sxs-lookup"><span data-stu-id="a2741-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="a2741-150">**Web yuvalarını** **Açık**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="a2741-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="a2741-151">**ARR benzeşiminin** **Açık**olarak ayarlandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="a2741-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="a2741-152">App Service planı limitleri</span><span class="sxs-lookup"><span data-stu-id="a2741-152">App Service Plan limits</span></span>

<span data-ttu-id="a2741-153">Web Yuvaları ve diğer aktarımlar, seçilen App Service planına göre sınırlandırılır.</span><span class="sxs-lookup"><span data-stu-id="a2741-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="a2741-154">Daha fazla bilgi için [Azure abonelik ve hizmet sınırları, Kotalar ve kısıtlamalar](/azure/azure-subscription-service-limits#app-service-limits) makalesinin *Azure Cloud Services sınırları* ve *App Service sınırları* bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="a2741-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a2741-155">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a2741-155">Additional resources</span></span>

* <span data-ttu-id="a2741-156">[Azure hizmeti nedir SignalR ?](/azure/azure-signalr/signalr-overview)</span><span class="sxs-lookup"><span data-stu-id="a2741-156">[What is Azure SignalR Service?](/azure/azure-signalr/signalr-overview)</span></span>
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="a2741-157">Komut satırı araçlarıyla Azure 'da ASP.NET Core uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="a2741-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="a2741-158">Azure 'da ASP.NET Core Preview uygulamaları barındırma ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="a2741-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
