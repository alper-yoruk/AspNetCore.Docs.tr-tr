---
title: SignalRAzure App Service için ASP.NET Core uygulaması yayımlama
author: bradygaster
description: Azure App Service için ASP.NET Core uygulamasının nasıl yayımlanacağını öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/02/2020
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 8e6d36fe0b38486f94078b8f9cf12b852da7e0d9
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234522"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a>SignalRAzure App Service için ASP.NET Core uygulaması yayımlama

, [Brady Gaster](https://twitter.com/bradygaster) tarafından

[Azure App Service](/azure/app-service/app-service-web-overview) , Web uygulamalarını barındırmak için ASP.NET Core dahil olmak üzere bir [Microsoft bulut bilgi işlem](https://azure.microsoft.com/) platformu hizmetidir.

> [!NOTE]
> Bu makale, SignalR Visual Studio 'dan bir ASP.NET Core uygulaması yayımlamaya başvurur. Daha fazla bilgi için bkz. [ SignalR Azure hizmeti](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Uygulamayı yayımlama

Bu makalede, Visual Studio 'daki araçları kullanarak yayımlama ele alınmaktadır. Visual Studio Code kullanıcılar, Azure 'da uygulama yayımlamak için [Azure CLI](/cli/azure) komutlarını kullanabilir. Daha fazla bilgi için bkz. [komut satırı araçlarıyla Azure 'da ASP.NET Core uygulama yayımlama](/azure/app-service/app-service-web-get-started-dotnet).

1. **Çözüm Gezgini** projede projeye sağ tıklayın ve **Yayımla** ' yı seçin.

1. **Bir yayımlama hedefi seç** iletişim kutusunda **App Service** ve **Yeni oluştur** ' un seçili olduğunu doğrulayın.

1. **Yayımla** düğmesi açılan listesinden **Profil oluştur** ' u seçin.

   **App Service oluştur** iletişim kutusunda aşağıdaki tabloda açıklanan bilgileri girin ve **Oluştur** ' u seçin.

   | Öğe               | Açıklama |
   | ------------------ | ----------- |
   | **Ad**           | Uygulamanın benzersiz adı. |
   | **Abonelik**   | Uygulamanın kullandığı Azure aboneliği. |
   | **Kaynak Grubu** | Uygulamanın ait olduğu ilgili kaynaklar grubu. |
   | **Barındırma Planı**   | Web uygulaması için fiyatlandırma planı. |

1. **Hizmet bağımlılıkları** bölümünde **Azure SignalR hizmeti** ' ni seçin. Düğmeyi seçin **+** :

   ![Ekle açılan listesinde Azure::: No-Loc (SignalR)::: Service seçimini gösteren bağımlılıklar alanı](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. **Azure SignalR hizmeti** iletişim kutusunda **Yeni bir Azure SignalR hizmet örneği oluştur** ' u seçin.

1. Bir **ad** , **kaynak grubu** ve **konum** belirtin. **Azure SignalR hizmeti** iletişim kutusuna dönün ve **Ekle** ' yi seçin.

Visual Studio aşağıdaki görevleri tamamlar:

* Yayımlama ayarlarını içeren bir yayımlama profili oluşturur.
* Belirtilen ayrıntılarla bir *Azure Web uygulaması* oluşturur.
* Uygulamayı yayımlar.
* Web uygulamasını yükleyen bir tarayıcı başlatır.

Uygulamanın URL 'sinin biçimi `{APP SERVICE NAME}.azurewebsites.net` . Örneğin, adlı bir uygulamanın `SignalRChatApp` URL 'si vardır `https://signalrchatapp.azurewebsites.net` .

Bir Preview .NET Core sürümünü hedefleyen bir uygulama dağıtırken HTTP *502,2-Hatalı ağ geçidi* hatası oluşursa, bu sorunu çözmek için [Azure App Service ASP.NET Core önizleme sürümünü dağıtma](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) bölümüne bakın.

## <a name="configure-the-app-in-azure-app-service"></a>Uygulamayı Azure App Service yapılandırma

> [!NOTE]
> *Bu bölüm yalnızca Azure hizmetini kullanmayan uygulamalar için geçerlidir SignalR .*
>
> Uygulama Azure SignalR hizmetini kullanıyorsa, App Service uygulama Isteği yönlendirme (ARR) benzeşimi ve bu bölümde açıklanan Web Yuvaları yapılandırmasını gerektirmez. İstemciler Web yuvalarını SignalR doğrudan uygulamaya değil Azure hizmetine bağlanır.

Azure hizmeti olmadan barındırılan uygulamalar için SignalR şunları etkinleştirin:

* [ARR benzeşimi] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(ARR- cookie ) -for-Azure-web-apps.html) bir kullanıcıdan gelen istekleri yeniden aynı App Service örneğine yönlendirmek için. Varsayılan ayar **Açık '** dır.
* Web Sockets taşımanın çalışmasına izin veren [Web Yuvaları](xref:fundamentals/websockets) . Varsayılan ayar **kapalıdır** .

1. Azure portal, **uygulama hizmetleri** ' nde Web uygulamasına gidin.
1. **Yapılandırma**  >  **genel ayarlarını** açın.
1. **Web yuvalarını** **Açık** olarak ayarlayın.
1. **ARR benzeşiminin** **Açık** olarak ayarlandığını doğrulayın.

## <a name="app-service-plan-limits"></a>App Service planı limitleri

Web Yuvaları ve diğer aktarımlar, seçilen App Service planına göre sınırlandırılır. Daha fazla bilgi için [Azure abonelik ve hizmet sınırları, Kotalar ve kısıtlamalar](/azure/azure-subscription-service-limits#app-service-limits) makalesinin *Azure Cloud Services sınırları* ve *App Service sınırları* bölümlerine bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure hizmeti nedir SignalR ?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Komut satırı araçlarıyla Azure 'da ASP.NET Core uygulaması yayımlama](/azure/app-service/app-service-web-get-started-dotnet)
* [Azure 'da ASP.NET Core Preview uygulamaları barındırma ve dağıtma](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
