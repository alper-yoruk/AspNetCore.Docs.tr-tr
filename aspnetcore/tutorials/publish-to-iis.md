---
title: IIS 'de ASP.NET Core uygulaması yayımlama
author: rick-anderson
description: ASP.NET Core uygulamasının bir IIS sunucusunda nasıl barındırılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: 0f70b5f12b9097f8710c9641404b3e085968fc3f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753159"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>IIS 'de ASP.NET Core uygulaması yayımlama

Bu öğreticide bir IIS sunucusunda ASP.NET Core uygulamasının nasıl barındırılacağını gösterilmektedir.

Bu öğreticide aşağıdaki konular ele alınmaktadır:

> [!div class="checklist"]
> * .NET Core barındırma paketi 'ni Windows Server 'a yükler.
> * IIS Yöneticisi 'nde bir IIS sitesi oluşturun.
> * ASP.NET Core uygulamasını dağıtın.

## <a name="prerequisites"></a>Ön koşullar

* Geliştirme makinesinde yüklü [.NET Core SDK](/dotnet/core/sdk) .
* **Web sunucusu (IIS)** sunucu rolüyle yapılandırılmış Windows Server. Sunucunuz Web sitelerini IIS ile barındırmak üzere yapılandırılmamışsa, makalenin *IIS yapılandırması* bölümündeki yönergeleri izleyin <xref:host-and-deploy/iis/index#iis-configuration> ve ardından Bu öğreticiye geri dönün.

> [!WARNING]
> **IIS yapılandırması ve Web sitesi güvenliği, bu öğretici kapsamında olmayan kavramları içerir.** IIS 'de üretim uygulamalarını barındırmadan önce, [MICROSOFT IIS BELGELERINDEKI](https://www.iis.net/) IIS KıLAVUZUNA ve [IIS ile barındırma hakkında ASP.NET Core makalesine](xref:host-and-deploy/iis/index) başvurun.
>
> Bu öğreticide kapsanmayan IIS barındırması için önemli senaryolar şunlardır:
>
> * [ASP.NET Core veri koruması için bir kayıt defteri kovanı oluşturma](xref:host-and-deploy/iis/advanced#data-protection)
> * [Uygulama havuzunun Access Control listesi (ACL) yapılandırması](xref:host-and-deploy/iis/advanced#application-pool-identity)
> * IIS dağıtım kavramlarına odaklanmak için bu öğretici, IIS 'de HTTPS güvenliği olmayan bir uygulama dağıtır. HTTPS protokolü için etkinleştirilmiş bir uygulamayı barındırma hakkında daha fazla bilgi için, bu makalenin [ek kaynaklar](#additional-resources) bölümündeki güvenlik konularına bakın. ASP.NET Core uygulamalar barındırılmasına yönelik daha fazla rehberlik makalesinde sunulmaktadır <xref:host-and-deploy/iis/index> .

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core barındırma paketi 'ni yükler

*.NET Core barındırma paketi* 'ni IIS sunucusuna yükler. Paket, .NET Core çalışma zamanı, .NET Core kitaplığı ve [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)de yüklüyor. Modül ASP.NET Core uygulamaların IIS 'nin arkasında çalışmasına izin verir.

Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:

[Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Yükleyiciyi IIS sunucusunda çalıştırın.

1. Sunucuyu yeniden başlatın veya `net stop was /y` `net start w3svc` bir komut kabuğundan bunu yürütün.

## <a name="create-the-iis-site"></a>IIS sitesini oluşturma

1. IIS sunucusunda, uygulamanın yayımlanan klasörlerini ve dosyalarını içeren bir klasör oluşturun. Aşağıdaki adımda, klasörün yolu, uygulamanın fiziksel yolu olarak IIS 'ye sağlanır. Uygulamanın dağıtım klasörü ve dosya düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..

1. IIS Yöneticisi 'nde, **Bağlantılar** panelinde sunucunun düğümünü açın. **Siteler** klasörüne sağ tıklayın. Bağlamsal menüden **Web sitesi Ekle** ' yi seçin.

1. Bir **site adı** belirtin ve **fiziksel yolu** , oluşturduğunuz uygulamanın dağıtım klasörüne ayarlayın. **Bağlama** yapılandırmasını sağlayın ve **Tamam**' ı seçerek Web sitesini oluşturun.

   > [!WARNING]
   > Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır  . Üst düzey joker karakter bağlamaları, uygulamanızı güvenlik açıklarına açabilir. Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir. Joker karakterler yerine açık ana bilgisayar adları kullanın. Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskine sahip değildir `*.com` . Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. İşlem modeli kimliğinin uygun izinlere sahip olduğunu doğrulayın.

   Uygulama havuzunun varsayılan kimliği (**işlem modeli**  >  **Identity** ) `ApplicationPoolIdentity` ' den başka bir kimliğe değiştirilirse, yeni kimliğin uygulamanın klasörüne, veritabanına ve diğer gerekli kaynaklara erişmek için gerekli izinlere sahip olduğunu doğrulayın. Örneğin, uygulama havuzu, uygulamanın dosyaları okuduğu ve yazdığı klasörlere okuma ve yazma erişimi gerektirir.

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>ASP.NET Core Razor Pages uygulaması oluşturma

<xref:getting-started>Bir sayfalar uygulaması oluşturmak için öğreticiyi izleyin Razor .

## <a name="publish-and-deploy-the-app"></a>Uygulamayı yayımlama ve dağıtma

*Uygulama yayımlama* , bir sunucu tarafından barındırılabilecek derlenmiş bir uygulama oluşturmak anlamına gelir. *Uygulama dağıtma* , yayımlanan uygulamayı bir barındırma sistemine taşımak anlamına gelir. Yayımla adımı [.NET Core SDK](/dotnet/core/sdk)tarafından işlenir, ancak dağıtım adımı çeşitli yaklaşımlar tarafından işlenebilir. Bu öğretici, şu konumda *klasör* dağıtım yaklaşımını benimsemektedir:
 
* Uygulama bir klasöre yayımlanır.
* Klasörün içeriği IIS sitesinin klasörüne taşınır (IIS Yöneticisi 'nde sitenin **fiziksel yolu** ).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Çözüm Gezgini** projede projeye sağ tıklayın ve **Yayımla**' yı seçin.
1. **Bir yayımlama hedefi seç** iletişim kutusunda, **klasörü** Yayımla seçeneğini belirleyin.
1. **Klasör veya dosya paylaşma** yolunu ayarlayın.
   * Geliştirme makinesinde bir ağ paylaşımında bulunan IIS sitesi için bir klasör oluşturduysanız, paylaşımın yolunu belirtin. Geçerli kullanıcının paylaşıma yayımlamak için yazma erişimi olmalıdır.
   * IIS sunucusunda IIS site klasörüne doğrudan dağıtadıysanız, çıkarılabilir medyada bir klasöre yayımlayın ve yayımlanan uygulamayı sunucuda, sitenin **fiziksel yolu** olan sunucudaki IIS site klasörüne fiziksel olarak taşıyın. Klasörün içeriğini, sunucudaki IIS `bin/Release/{TARGET FRAMEWORK}/publish` Yöneticisi 'Ndeki **fiziksel yolu** olan sunucudaki IIS site klasörüne taşıyın.
1. **Yayımla** düğmesini seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. Bir komut kabuğunda, uygulamayı sürüm yapılandırması 'nda [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutuyla yayımlayın:

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Klasörün içeriğini, sunucudaki IIS `bin/Release/{TARGET FRAMEWORK}/publish` Yöneticisi 'Ndeki **fiziksel yolu** olan sunucudaki IIS site klasörüne taşıyın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözümdeki** projeye sağ tıklayın ve Yayımla klasörünü **Yayımla ' yı seçin**  >  .
1. **Klasör seçin** yolunu ayarlayın.
   * Geliştirme makinesinde bir ağ paylaşımında bulunan IIS sitesi için bir klasör oluşturduysanız, paylaşımın yolunu belirtin. Geçerli kullanıcının paylaşıma yayımlamak için yazma erişimi olmalıdır.
   * IIS sunucusunda IIS site klasörüne doğrudan dağıtım yapadıysanız, kaldırılabilir medyada bir klasöre yayımlayın ve yayımlanan uygulamayı sunucuda, sitenin **fiziksel yolu** olan sunucudaki IIS site klasörüne fiziksel olarak taşıyın. Klasörün içeriğini, sunucudaki IIS `bin/Release/{TARGET FRAMEWORK}/publish` Yöneticisi 'Ndeki **fiziksel yolu** olan sunucudaki IIS site klasörüne taşıyın.
1. **Yayımla** düğmesini seçin.

---

## <a name="browse-the-website"></a>Web sitesine gidin

Uygulamanın ilk isteği aldıktan sonra tarayıcıda erişilebilir olması gerekir. Site için IIS Yöneticisi 'nde oluşturduğunuz uç nokta bağlamasındaki uygulamaya bir istek oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * .NET Core barındırma paketi 'ni Windows Server 'a yükler.
> * IIS Yöneticisi 'nde bir IIS sitesi oluşturun.
> * ASP.NET Core uygulamasını dağıtın.

IIS 'de ASP.NET Core uygulamaları barındırma hakkında daha fazla bilgi için bkz. IIS genel bakış makalesi:

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Ek kaynaklar

### <a name="articles-in-the-aspnet-core-documentation-set"></a>ASP.NET Core belge kümesi makaleleri

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>ASP.NET Core Uygulama dağıtımıyla ilgili makaleler

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Mac için Visual Studio kullanarak bir Web uygulamasını bir klasöre yayımlama](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>IIS HTTPS yapılandırması makaleleri

* [IIS Yöneticisi 'nde SSL 'yi yapılandırma](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [IIS 'de SSL ayarlama](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>IIS ve Windows Server makaleleri

* [Resmi Microsoft IIS sitesi](https://www.iis.net/)
* [Windows Server teknik içerik kitaplığı](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a>IIS yöneticileri için dağıtım kaynakları

* [IIS belgeleri](/iis)
* [IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core uygulama dağıtımı](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

