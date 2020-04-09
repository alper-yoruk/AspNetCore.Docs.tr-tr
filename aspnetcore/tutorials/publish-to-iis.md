---
title: IIS'de bir ASP.NET Core uygulaması yayınlama
author: rick-anderson
description: Bir ASP.NET Core uygulamasını bir IIS sunucusunda nasıl barındırılacIz öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 47f78ba78741a8e0175ce801c0c0e51f091273a8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511398"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>IIS'de bir ASP.NET Core uygulaması yayınlama

Bu öğretici, bir IIS sunucusunda ASP.NET Core uygulamasının nasıl barındırılabildiğini gösterir.

Bu öğretici aşağıdaki konuları kapsar:

> [!div class="checklist"]
> * .NET Core Hosting Paketini Windows Server'a yükleyin.
> * IIS Manager'da bir IIS sitesi oluşturun.
> * Bir ASP.NET Core uygulaması dağıtın.

## <a name="prerequisites"></a>Ön koşullar

* [.NET Core SDK](/dotnet/core/sdk) geliştirme makinesine yüklendi.
* Windows **Server, Web Server (IIS)** sunucu rolüyle yapılandırıldı. Sunucunuz IIS'li web sitelerini barındıracak şekilde yapılandırılmamışsa, <xref:host-and-deploy/iis/index#iis-configuration> makalenin *IIS yapılandırma* bölümündeki kılavuzu izleyin ve ardından bu öğreticiye geri dönün.

> [!WARNING]
> **IIS yapılandırması ve web sitesi güvenliği, bu öğretici kapsamında olmayan kavramları içerir.** IIS'de üretim uygulamalarını barındırmadan önce [Microsoft IIS belgelerindeki IIS](https://www.iis.net/) kılavuzuna ve [IIS ile barındırma yla ilgili ASP.NET Core makalesine](xref:host-and-deploy/iis/index) başvurun.
>
> Bu öğretici kapsamında olmayan IIS barındırma için önemli senaryolar şunlardır:
>
> * [ASP.NET Çekirdek Veri Koruması için kayıt defteri kovanının oluşturulması](xref:host-and-deploy/iis/index#data-protection)
> * [Uygulama havuzunun Erişim Kontrol Listesinin (ACL) yapılandırması](xref:host-and-deploy/iis/index#application-pool-identity)
> * IIS dağıtım kavramlarına odaklanmak için bu öğretici, IIS'de yapılandırılan HTTPS güvenliği olmayan bir uygulama dağıtır. HTTPS protokolü için etkin leştirilmiş bir uygulamayı barındırma hakkında daha fazla bilgi için, bu makalenin [Ek kaynaklar](#additional-resources) bölümündeki güvenlik konularına bakın. <xref:host-and-deploy/iis/index> Makalede, ASP.NET Core uygulamaları barındırma küçlükiçin daha fazla kılavuz sunulmuştur.

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core Hosting Paketini yükleyin

*.NET Core Hosting Paketini* IIS sunucusuna yükleyin. Pakette .NET Core Runtime, .NET Core Library ve [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)yüklenir. Modül, ASP.NET Core uygulamalarının IIS'nin arkasında çalışmasını sağlar.

Aşağıdaki bağlantıyı kullanarak yükleyiciindir:

[Geçerli .NET Çekirdek Hosting Paketi yükleyici (doğrudan indir)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Yükleyicisi IIS sunucusunda çalıştırın.

1. Sunucuyu yeniden başlatın veya net stop çalıştırın **/y** bir komut kabuğunda **net start w3svc** izledi.

## <a name="create-the-iis-site"></a>IIS sitesini oluşturma

1. IIS sunucusunda, uygulamanın yayımlanmış klasörlerini ve dosyalarını içerecek bir klasör oluşturun. Aşağıdaki adımda, klasörün yolu uygulamanın fiziksel yolu olarak IIS'ye sağlanır.

1. IIS Manager'da, **Bağlantılar** panelinde sunucu düğümlerini açın. **Siteler** klasörüne sağ tıklayın. Bağlamsal menüden **Web Sitesi Ekle'yi** seçin.

1. Bir **Site adı** sağlayın ve **Fiziksel yolu** oluşturduğunuz uygulamanın dağıtım klasörüne ayarlayın. **Bağlama** yapılandırmasını sağlayın ve **Tamam'ı**seçerek web sitesini oluşturun.

## <a name="create-an-aspnet-core-razor-pages-app"></a>ASP.NET Core Razor Pages uygulaması oluşturma

Razor <xref:getting-started> Pages uygulaması oluşturmak için öğreticiyi izleyin.

## <a name="publish-and-deploy-the-app"></a>Uygulamayı yayımlama ve dağıtma

*Bir uygulama yayımlamak,* bir sunucu tarafından barındırılabilen derlenmiş bir uygulama üretmek anlamına gelir. *Bir uygulamayı dağıtmak,* yayınlanan uygulamayı barındırma sistemine taşımak anlamına gelir. Yayımlama adımı [.NET Core SDK](/dotnet/core/sdk)tarafından işlenirken, dağıtım adımı çeşitli yaklaşımlarla işlenebilir. Bu öğretici, *klasör* dağıtım yaklaşımını benimser:

* Uygulama bir klasörde yayınlanır.
* Klasörün içeriği IIS sitesinin klasörüne (IIS Manager'daki siteye **fiziksel yol)** taşınır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Solution Explorer'da** projeye sağ tıklayın ve **Yayımla'yı**seçin.
1. **Yayımlama hedef** iletişim kutusunda Klasör **yayımlama** seçeneğini belirleyin.
1. Klasör **veya Dosya Paylaşımı** yolunu ayarlayın.
   * Geliştirme makinesinde ağ paylaşımı olarak kullanılabilen IIS sitesi için bir klasör oluşturduysanız, paylaşıma giden yolu sağlayın. Geçerli kullanıcı nın paylaşıma yayımlamak için yazma erişimine sahip olması gerekir.
   * IIS sunucusundaki IIS site klasörüne doğrudan dağıtamıyorsanız, kaldırılabilir ortamla ilgili bir klasöre yayımlayın ve yayınlanan uygulamayı fiziksel olarak sitenin IIS Yöneticisi'ndeki **Fiziksel yolu** olan sunucudaki IIS site klasörüne taşıyın. *Depo gözü/Yayın/{TARGET FRAMEWORK}/publish* klasörünün içeriğini, sitenin IIS Yöneticisi'ndeki Fiziksel **yolu** olan sunucudaki IIS site klasörüne taşıyın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. Komut kabuğunda, [uygulamayı dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutuyla Serbest Bırak yapılandırmasında yayımlayın:

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. *Depo gözü/Yayın/{TARGET FRAMEWORK}/publish* klasörünün içeriğini, sitenin IIS Yöneticisi'ndeki Fiziksel **yolu** olan sunucudaki IIS site klasörüne taşıyın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözüm'deki** projeye sağ tıklayın ve**Klasöre Yayımla'yı** **Publish** > seçin.
1. Klasör yolunu **seç'i** ayarlayın.
   * Geliştirme makinesinde ağ paylaşımı olarak kullanılabilen IIS sitesi için bir klasör oluşturduysanız, paylaşıma giden yolu sağlayın. Geçerli kullanıcı nın paylaşıma yayımlamak için yazma erişimine sahip olması gerekir.
   * IIS sunucusundaki IIS site klasörüne doğrudan dağıtamıyorsanız, kaldırılabilir ortamla ilgili bir klasöre yayımlayın ve yayınlanan uygulamayı fiziksel olarak sitenin IIS Yöneticisi'ndeki **Fiziksel yolu** olan sunucudaki IIS site klasörüne taşıyın. *Depo gözü/Yayın/{TARGET FRAMEWORK}/publish* klasörünün içeriğini, sitenin IIS Yöneticisi'ndeki Fiziksel **yolu** olan sunucudaki IIS site klasörüne taşıyın.

---

## <a name="browse-the-website"></a>Web sitesine göz atın

Uygulama, ilk isteği aldıktan sonra bir tarayıcıda erişilebilir. Site için IIS Manager'da oluşturduğunuz bitiş noktası bağlama noktasında uygulamaya bir istekte bulunun.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * .NET Core Hosting Paketini Windows Server'a yükleyin.
> * IIS Manager'da bir IIS sitesi oluşturun.
> * Bir ASP.NET Core uygulaması dağıtın.

IIS'de ASP.NET Core uygulamalarını barındırma hakkında daha fazla bilgi edinmek için IIS Genel Bakış makalesine bakın:

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Ek kaynaklar

### <a name="articles-in-the-aspnet-core-documentation-set"></a>ASP.NET Çekirdek dokümantasyon setindeki makaleler

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>ASP.NET Core uygulama dağıtımıyla ilgili makaleler

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Mac için Visual Studio'u kullanarak bir klasöre Web uygulaması yayımlama](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>IIS HTTPS yapılandırması ile ilgili makaleler

* [IIS Yöneticisi'nde SSL yapılandırma](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [IIS'de SSL Nasıl Ayarlanır?](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>IIS ve Windows Server ile ilgili makaleler

* [Resmi Microsoft IIS Sitesi](https://www.iis.net/)
* [Windows Server teknik içerik kitaplığı](/windows-server/windows-server)
