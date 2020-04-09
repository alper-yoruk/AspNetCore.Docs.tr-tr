---
title: ASP.NET Core ile Visual Studio ve Git kullanarak Azure’a sürekli dağıtım
author: rick-anderson
description: Visual Studio'yu kullanarak bir ASP.NET Core web uygulaması oluşturmayı ve sürekli dağıtım için Git'i kullanarak Azure Uygulama Hizmeti'ne nasıl dağıttığı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3b344505739bb4292ed1683c73ff314b6e4e01e9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660855"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>ASP.NET Core ile Visual Studio ve Git kullanarak Azure’a sürekli dağıtım

Yazar: [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Bu öğretici, Visual Studio'ASP.NET bir web uygulamasının nasıl oluşturulup Visual Studio'dan Azure Uygulama Hizmeti'ne sürekli dağıtım kullanarak nasıl dağıtılanın gerektiğini gösterir.

Ayrıca Azure DevOps Hizmetlerini kullanarak [Azure Uygulama Hizmeti](/azure/app-service/app-service-web-overview) için sürekli teslimat (CD) iş akışını nasıl yapılandırabileceğinizi gösteren Azure [Ardışık Hatları ile ilk ardışık alanınızı oluşturun.](/azure/devops/pipelines/get-started-yaml) Azure Ardışık Hatları (Azure DevOps Hizmetleri hizmeti), Azure Uygulama Hizmeti'nde barındırılan uygulamaların güncelleştirmelerini yayımlamak için sağlam bir dağıtım ardışık birimi oluşturmayı kolaylaştırır. Ardışık düzen, Azure portalından, testleri oluşturmak, çalıştırmak, bir hazırlama yuvasına dağıtmak ve sonra üretime dağıtmak için yapılandırılabilir.

> [!NOTE]
> Bu öğreticiyi tamamlamak için bir Microsoft Azure hesabı gereklidir. Hesap edinmek için [MSDN abone avantajlarını etkinleştirin](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) veya [ücretsiz deneme sürümüne kaydolun.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="prerequisites"></a>Ön koşullar

Bu öğretici, aşağıdaki yazılımın yüklü olduğunu varsayar:

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* Windows için [Git](https://git-scm.com/downloads)

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core web uygulaması oluşturma

1. Visual Studio’yu çalıştırın.

1. **Dosya** menüsünden **Yeni** > **Proje'yi**seçin.

1. ASP.NET **Çekirdek Web Uygulaması** proje şablonu seçin. **Yüklü** > **Şablonlar** > **Görsel C#** > **.NET Core**altında görünür. Projeyi `SampleWebAppDemo`adlandırın. Yeni **Git deposu Oluştur** seçeneğini seçin ve **Tamam'ı**tıklatın.

   ![Yeni Proje iletişim kutusu](azure-continuous-deployment/_static/01-new-project.png)

1. Yeni **ASP.NET Çekirdek Projesi** iletişim kutusunda, ASP.NET Çekirdek **Boş** şablonuna tıklayın ve **ardından Tamam'ı**tıklatın.

   ![Yeni ASP.NET Çekirdek Projesi iletişim kutusu](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> .NET Core'un en son sürümü 2.0'dır.

### <a name="running-the-web-app-locally"></a>Web uygulamasını yerel olarak çalıştırma

1. Visual Studio uygulamayı oluşturmayı bitirdikten sonra **Hata** > **Ayıklama Başlat'ı**seçerek uygulamayı çalıştırın. Alternatif olarak **F5**tuşuna basın.

   Visual Studio ve yeni uygulamayı başlatması zaman alabilir. Tamamlandıktan sonra, tarayıcı çalışan uygulamayı gösterir.

   !['Hello World!' uygulamasını gösteren tarayıcı penceresi](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Çalışan Web uygulamasını inceledikten sonra tarayıcıyı kapatın ve uygulamayı durdurmak için Visual Studio'nun araç çubuğundaki "Hata Ayıklamayı Durdur" simgesini seçin.

## <a name="create-a-web-app-in-the-azure-portal"></a>Azure Portalı'nda bir web uygulaması oluşturma

Aşağıdaki adımlar Azure Portalı'nda bir web uygulaması oluşturur:

1. [Azure Portalı'na](https://portal.azure.com)giriş yapın.

1. Portal arabiriminin sol üst kısmında **Yenİ'yi** seçin.

1. **Web + Mobil** > **Web Uygulamasını**seçin.

   ![Microsoft Azure Portalı: Yeni düğme: Web + Mobile under Marketplace: Öne Çıkan Uygulamalar altında Web Uygulaması düğmesi](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. Web **App** blade'ine, Uygulama **Hizmet Adı**için benzersiz bir değer girin.

   ![Web App bıçak](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > **Uygulama Hizmeti Adı** benzersiz olmalıdır. Ad sağlandığında portal bu kuralı uygular. Farklı bir değer sağlıyorsanız, bu öğreticide **SampleWebAppDemo'nun** her oluşumu için bu değeri değiştirin.

   **Ayrıca Web App** blade'de, varolan bir Uygulama Hizmet **Planı/Konumu'nu** seçin veya yeni bir uygulama oluşturun. Yeni bir plan oluşturuyorsanız, fiyatlandırma katmanını, konumu ve diğer seçenekleri seçin. Uygulama Hizmeti planları hakkında daha fazla bilgi için Azure [Uygulama Hizmeti planlarına derinlemesine genel bakış](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)bakın.

1. **Oluştur'u**seçin. Azure, web uygulamasını sağlayacak ve başlatacaktır.

   ![Azure Portalı: Örnek Web App Demo 01 Essentials bıçak](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Yeni web uygulaması için Git yayımlamayı etkinleştirme

Git, bir Azure App Service web uygulamasını dağıtmak için kullanılabilecek dağıtılmış bir sürüm kontrol sistemidir. Web uygulaması kodu yerel bir Git deposunda depolanır ve kod uzak bir depoya itilerek Azure'a dağıtılır.

1. [Azure Portalına](https://portal.azure.com)giriş yapın.

1. Azure aboneliğiyle ilişkili uygulama hizmetlerinin listesini görüntülemek için **Uygulama Hizmetleri'ni** seçin.

1. Bu öğreticinin önceki bölümünde oluşturulan web uygulamasını seçin.

1. **Dağıtım** bıçağında, **Dağıtım seçeneklerini** > seçin Kaynak > **Yerel Git Deposu'nu****seçin.**

   ![Ayarlar bıçak: Dağıtım kaynak bıçak: Kaynak bıçak seçin](azure-continuous-deployment/_static/deployment-options.png)

1. **Tamam'ı**seçin.

1. Bir web uygulamasını veya başka bir Uygulama Hizmeti uygulamasını yayımlamaiçin dağıtım kimlik bilgileri daha önce ayarlanmadıysa, bunları şimdi ayarlayın:

   * **Ayarlar** > **Dağıtım kimlik bilgilerini**seçin. **Dağıtım kimlik bilgilerini ayarla** görüntülenir.
   * Bir kullanıcı adı ve parola oluşturun. Parolayı Git'i ayarlarken daha sonra kullanmak üzere kaydedin.
   * **Kaydet'i**seçin.

1. Web **App** blade'inde **Ayarlar** > **Özellikleri'ni**seçin. Dağıtılabilmek için dağıtılabilmek için uzak Git deposunun **URL'si GIT**URL'si altında gösterilir.

1. Öğreticide daha sonra kullanmak üzere **GIT URL** değerini kopyalayın.

   ![Azure Portal: uygulama Özellikleri bıçak](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Web uygulamasını Azure App Service’te yayımlama

Bu bölümde, Visual Studio'yu kullanarak yerel bir Git deposu oluşturun ve web uygulamasını dağıtmak için bu depodan Azure'a itin. Söz konusu adımlar şunlardır:

* Yerel deponun Azure'a dağıtılaabilmesi için GIT URL değerini kullanarak uzak depo ayarını ekleyin.
* Proje değişiklikleri gerçekleştirin.
* Proje değişikliklerini yerel depodan Azure'daki uzak depoya taşıyın.

1. **Solution Explorer'da** **Çözüm 'SampleWebAppDemo'** seçeneğini sağ tıklatın ve **Commit'i**seçin. **Takım Gezgini** görüntülenir.

   ![Takım Gezgini Bağlan sekmesi](azure-continuous-deployment/_static/10-team-explorer.png)

1. **Takım Gezgini'nde,** **Ayarlar** > Deposu Ayarları'> **Ana Sayfa** (ev simgesi) seçeneğini**belirleyin.**

1. **Depo Ayarları**’nın **Uzak öğeler** bölümünde **Ekle**’yi seçin. **Uzak Öğe Ekle** iletişim kutusu görüntülenir.

1. Uzaktan kumandanın **adını** **Azure-SampleApp**olarak ayarlayın.

1. Bu öğreticide Azure'dan kopyalanan **Git URL'sine** **Getir** değerini ayarlayın. Bu URL ile biten **.git**olduğunu unutmayın.

   ![Uzak iletişim kutusunu edin](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Alternatif olarak, **Komut Penceresi'ni**açarak, proje dizinine değiştirerek ve komutu girerek Komut **Penceresinden** uzak depoyu belirtin. Örnek:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1.  >  **Ayarlar****Genel Ayarları**> **Ana Sayfa** (ana sayfa simgesi) seçeneğini belirleyin. Adın ve e-posta adresinin ayarlandığını onaylayın. Gerekirse **Güncelleştir'i** seçin.

1. **Değişiklikler** görünümüne dönmek için **Ana Sayfa** > **Değişiklikleri'ni** seçin.

1. **İlk Itme #1** gibi bir ileti girin ve **Commit'i**seçin. Bu eylem yerel bir *commit* oluşturur.

   ![Takım Gezgini Bağlan sekmesi](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Alternatif olarak, **Komut Penceresi'ni**açarak, proje dizinine değiştirerek ve git komutlarını girerek Komut **Penceresinden** değişiklikler gerçekleştirin. Örnek:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. **Ana Sayfa** > **Eşitleme** > **Eylemleri** > **Açık Komut İstem'ini**seçin. Komut istemi proje dizinine açılır.

1. Komut penceresine aşağıdaki komutu girin:

   `git push -u Azure-SampleApp master`

1. Azure'da daha önce oluşturulan Azure **dağıtım kimlik bilgileri** parolasını girin.

   Bu komut, yerel proje dosyalarını Azure'a itme işlemini başlatır. Yukarıdaki komuttan gelen çıktı, dağıtımın başarılı olduğu iletisiyle sona erer.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Projede işbirliği gerekiyorsa, Azure'a geçmeden önce [GitHub'a](https://github.com) itmeyi düşünün.
 
### <a name="verify-the-active-deployment"></a>Etkin Dağıtımı Doğrula

Web uygulamasının yerel ortamdan Azure'a aktarılmasının başarılı olduğunu doğrulayın.

Azure [Portalı'nda](https://portal.azure.com)web uygulamasını seçin. **Dağıtım** > **Dağıtım seçeneklerini**seçin.

![Azure Portalı: Ayarlar bıçak: Başarılı dağıtım gösteren dağıtım lar](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Azure’da uygulamayı çalıştırma

Artık web uygulaması Azure'a dağıtılanın, uygulamayı çalıştırın.

Bu iki şekilde gerçekleştirilebilir:

* Azure Portalı'nda, web uygulaması için web uygulama bıçaklarını bulun. Uygulamayı varsayılan tarayıcıda görüntülemek için **Gözat'ı** seçin.
* Bir tarayıcı açın ve web uygulamasının URL'sini girin. Örnek: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Web uygulamasını güncelleştirin ve yeniden yayımlayın

Yerel kodda değişiklik yaptıktan sonra yeniden yayımlayın:

1. Visual Studio **Çözüm Explorer'da** *Startup.cs* dosyasını açın.

1. `Configure` Yöntemde, yöntemi `Response.WriteAsync` aşağıdaki gibi görünecek şekilde değiştirin:

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Değişiklikleri Startup.cs *kaydedin.*

1. **Solution Explorer'da**, **'SampleWebAppDemo' seçeneğini** sağ tıklatın ve **Commit'i**seçin. **Takım Gezgini** görüntülenir.

1. Bir commit iletisi `Update #2`girin, örneğin.

1. Proje değişikliklerini işlemek için **Commit** düğmesine basın.

1. **Ev** > **Eşitleme** > **Eylemleri** > **Push'u**seçin.

> [!NOTE]
> Alternatif olarak, **komut penceresini**açarak, proje dizinine değiştirerek ve git komutunu girerek değişiklikleri Komut **Penceresinden** itin. Örnek:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Azure'da güncelleştirilmiş web uygulamasını görüntüleyin

Azure Portalı'ndaki web uygulama bıçağından **Gözat'ı** seçerek veya bir tarayıcı açıp web uygulamasının URL'sini girerek güncelleştirilmiş web uygulamasını görüntüleyin. Örnek: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure Ardışık Hatları ile ilk ardışık sisteminizi oluşturun](/azure/devops/pipelines/get-started-yaml)
* [Kudu Projesi](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
