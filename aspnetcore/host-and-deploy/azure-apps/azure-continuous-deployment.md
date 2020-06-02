---
title: ASP.NET Core ile Visual Studio ve Git kullanarak Azure’a sürekli dağıtım
author: rick-anderson
description: Visual Studio kullanarak ASP.NET Core bir Web uygulaması oluşturmayı ve sürekli dağıtım için git 'i kullanarak Azure App Service nasıl dağıtacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 0b9f5f15403c7d18d68a41baa2a59582cf039f44
ms.sourcegitcommit: 4a9321db7ca4e69074fa08a678dcc91e16215b1e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82850571"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>ASP.NET Core ile Visual Studio ve Git kullanarak Azure’a sürekli dağıtım

By [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Bu öğreticide, Visual Studio kullanarak ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı ve Visual Studio 'dan sürekli dağıtım kullanarak Azure App Service nasıl dağıtılacağı gösterilmektedir.

Ayrıca, Azure DevOps Services kullanarak [Azure App Service](/azure/app-service/app-service-web-overview) için sürekli teslım (CD) iş akışını yapılandırmayı gösteren [Azure Pipelines ilk Işlem hattınızı oluşturun](/azure/devops/pipelines/get-started-yaml). Azure Pipelines (bir Azure DevOps Services hizmeti), Azure App Service 'de barındırılan uygulamalar için güncelleştirmeleri yayımlamak üzere güçlü bir dağıtım işlem hattı ayarlamayı basitleştirir. İşlem hattı, Azure portal derlemek, testler çalıştırmak, bir hazırlama yuvasına dağıtmak ve sonra üretime dağıtmak için yapılandırılabilir.

> [!NOTE]
> Bu öğreticiyi tamamlayabilmeniz için bir Microsoft Azure hesabı gereklidir. Bir hesap almak için [MSDN abone avantajlarını etkinleştirin](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) veya [ücretsiz deneme için kaydolun](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticide aşağıdaki yazılımların yüklü olduğu varsayılmaktadır:

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* Windows için [Git](https://git-scm.com/downloads)

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core web uygulaması oluşturma

1. Visual Studio’yu çalıştırın.

1. **Dosya** menüsünden **Yeni**  >  **Proje**' yi seçin.

1. **ASP.NET Core Web uygulaması** proje şablonunu seçin. **Yüklü**  >  **Şablonlar**  >  **Visual C#**  >  **.NET Core**altında görüntülenir. Projeyi adlandırın `SampleWebAppDemo` . **Yeni git deposu oluştur** seçeneğini belirleyip **Tamam**' a tıklayın.

   ![Yeni Proje iletişim kutusu](azure-continuous-deployment/_static/01-new-project.png)

1. **Yeni ASP.NET Core projesi** Iletişim kutusunda **boş** şablon ASP.NET Core seçin ve ardından **Tamam**' a tıklayın.

   ![Yeni ASP.NET Core projesi iletişim kutusu](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> .NET Core 'un en son sürümü 2,0 ' dir.

### <a name="running-the-web-app-locally"></a>Web uygulamasını yerel olarak çalıştırma

1. Visual Studio uygulamayı oluşturmayı tamamladıktan sonra **Hata Ayıkla**  >  **başlatma hata ayıklamayı**seçerek uygulamayı çalıştırın. Alternatif olarak **F5**tuşuna basın.

   Visual Studio 'Yu ve yeni uygulamayı başlatmak zaman alabilir. Tamamlandıktan sonra tarayıcı, çalışan uygulamayı gösterir.

   ![' Merhaba Dünya! ' görüntüleyen uygulamanın çalıştığını gösteren tarayıcı penceresi](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Çalışan Web uygulamasını inceledikten sonra, tarayıcıyı kapatın ve Visual Studio araç çubuğunda "hata ayıklamayı Durdur" simgesini seçerek uygulamayı durdurun.

## <a name="create-a-web-app-in-the-azure-portal"></a>Azure portalında bir Web uygulaması oluşturma

Aşağıdaki adımlar Azure portalında bir Web uygulaması oluşturur:

1. [Azure portalında](https://portal.azure.com)oturum açın.

1. Portal arabiriminin sol üst kısmındaki **Yeni** ' yi seçin.

1. **Web ve mobil**  >  **Web uygulaması**' nı seçin.

   ![Microsoft Azure Portal: yeni düğme: market altında Web ve Mobil: öne çıkan uygulamalar altında Web uygulaması düğmesi](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. **Web uygulaması** dikey penceresinde, **App Service adı**için benzersiz bir değer girin.

   ![Web uygulaması dikey penceresi](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > **App Service ad** adı benzersiz olmalıdır. Ad sağlandığında Portal bu kuralı uygular. Farklı bir değer sağlıyorsanız, bu öğreticide her **Samplewebappdemo** oluşumu için bu değeri değiştirin.

   Ayrıca, **Web uygulaması** dikey penceresinde, mevcut bir **App Service planı/konumu** seçin veya yeni bir tane oluşturun. Yeni bir plan oluşturuyorsanız, fiyatlandırma katmanını, konumunu ve diğer seçenekleri seçin. App Service planları hakkında daha fazla bilgi için bkz. [Azure App Service planlar ayrıntılı genel bakış](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. **Oluştur**’u seçin. Azure, Web uygulamasını sağlayacak ve başlatacak.

   ![Azure portalı: örnek Web uygulaması tanıtımı 01 Essentials dikey penceresi](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Yeni Web uygulaması için git yayımlamayı etkinleştir

Git, bir Azure App Service Web uygulaması dağıtmak için kullanılabilen bir dağıtılmış sürüm denetim sistemidir. Web uygulaması kodu yerel bir git deposunda depolanır ve kod, uzak bir depoya ileterek Azure 'a dağıtılır.

1. [Azure portalında](https://portal.azure.com)oturum açın.

1. Azure aboneliğiyle ilişkili uygulama hizmetlerinin listesini görüntülemek için **uygulama hizmetleri** ' ni seçin.

1. Bu öğreticinin önceki bölümünde oluşturulan Web uygulamasını seçin.

1. **Dağıtım** dikey penceresinde **dağıtım seçenekleri**  >  **kaynak**  >  **yerel Git deposu**' nu seçin.

   ![Ayarlar dikey penceresi: dağıtım kaynağı dikey penceresi: kaynak dikey penceresini seçin](azure-continuous-deployment/_static/deployment-options.png)

1. **Tamam**’ı seçin.

1. Bir Web uygulamasını yayınlamak için dağıtım kimlik bilgileri App Service veya daha önce ayarlanmamışsa, bu uygulamaları şimdi ayarlayın:

   * **Ayarlar**  >  **dağıtım kimlik bilgileri**' ni seçin. **Dağıtım kimlik bilgilerini ayarla** dikey penceresi görüntülenir.
   * Bir kullanıcı adı ve parola oluşturun. Git ayarlanırken daha sonra kullanmak üzere parolayı kaydedin.
   * **Kaydet**’i seçin.

1. **Web uygulaması** dikey penceresinde **Ayarlar**  >  **Özellikler**' i seçin. Dağıtım yapılacak uzak git deposunun URL 'SI **GIT URL 'si**altında gösterilir.

1. Öğreticide daha sonra kullanmak için **GIT URL 'si** değerini kopyalayın.

   ![Azure portalı: uygulama özellikleri dikey penceresi](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Web uygulamasını Azure App Service’te yayımlama

Bu bölümde, Visual Studio 'Yu kullanarak yerel bir git deposu oluşturun ve Web uygulamasını dağıtmak için bu depodan Azure 'a gönderin. Söz konusu adımlar şunları içerir:

* Yerel depo Azure 'a dağıtılabilmesi için GIT URL 'SI değerini kullanarak uzak depo ayarını ekleyin.
* Proje değişikliklerini Yürüt.
* Yerel depodan Azure 'daki uzak depoya proje değişiklikleri gönderin.

1. **Çözüm Gezgini** **' Samplewebappdemo ' çözümüne** sağ tıklayın ve **Yürüt**' ü seçin. **Takım Gezgini** görüntülenir.

   ![Takım Gezgini Bağlan sekmesi](azure-continuous-deployment/_static/10-team-explorer.png)

1. **Takım Gezgini** **, >** **ayarları**  >  **Depo ayarları**' nı seçin.

1. **Depo Ayarları**’nın **Uzak öğeler** bölümünde **Ekle**’yi seçin. **Uzak Öğe Ekle** iletişim kutusu görüntülenir.

1. Uzak **adını** **Azure-SampleApp**olarak ayarlayın.

1. **Fetch** için değeri bu öğreticide daha önce Azure 'Dan KOPYALANMıŞ **Git URL** 'sine ayarlayın. Bunun, **. git**Ile biten URL olduğunu unutmayın.

   ![Uzak iletişim kutusunu Düzenle](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Alternatif olarak, **komut penceresinden komut** **penceresini açıp, proje**dizinine giderek ve komutu girerek, uzak depoyu belirtin. Örnek:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. **Home** **Ayarlar**  >  **genel ayarları**> giriş (giriş simgesi) seçeneğini belirleyin. Adın ve e-posta adresinin ayarlandığını onaylayın. Gerekirse **Güncelleştir** ' i seçin.

1. **Home**  >  **Değişiklikler** görünümüne geri dönmek için ana**değişiklikler** ' i seçin.

1. **Ilk gönderme #1** gibi bir teslim iletisi girin ve **Kaydet**' i seçin. Bu eylem yerel olarak bir *işleme* oluşturur.

   ![Takım Gezgini Bağlan sekmesi](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Alternatif olarak **, komut penceresini açıp, proje**dizini olarak değiştirerek ve git komutlarını girerek **komut penceresinden** değişiklikleri işleyin. Örnek:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. **Ana**  >  **eşitleme**  >  **eylemlerini**seçin  >  **komut istemi 'ni açın**. Komut istemi proje dizini için açılır.

1. Komut penceresine aşağıdaki komutu girin:

   `git push -u Azure-SampleApp master`

1. Azure 'da daha önce oluşturulan Azure **dağıtım kimlik bilgileri** parolasını girin.

   Bu komut, yerel proje dosyalarını Azure 'a iletme işlemini başlatır. Yukarıdaki komutun çıktısı, dağıtımın başarılı olduğunu belirten bir iletiyle biter.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Projede işbirliği gerekiyorsa, Azure 'a göndermeden önce [GitHub](https://github.com) 'a göndermeyi düşünün.
 
### <a name="verify-the-active-deployment"></a>Etkin dağıtımı doğrulama

Yerel ortamdan Azure 'a Web uygulaması aktarımının başarılı olduğunu doğrulayın.

[Azure portalında](https://portal.azure.com)Web uygulamasını seçin. **Dağıtım**  >  **dağıtım seçeneklerini**belirleyin.

![Azure portalı: ayarlar dikey penceresi: başarılı dağıtımı gösteren dağıtımlar dikey penceresi](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Azure’da uygulamayı çalıştırma

Web uygulaması Azure 'a dağıtıldığına göre, uygulamayı çalıştırın.

Bu, iki şekilde gerçekleştirilebilir:

* Azure portalında, Web uygulaması için Web uygulaması dikey penceresini bulun. Uygulamayı varsayılan tarayıcıda görüntülemek için **Gözatma** ' yı seçin.
* Bir tarayıcı açın ve Web uygulamasının URL 'sini girin. Örnek: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Web uygulamasını güncelleştirme ve yeniden yayımlama

Yerel kodda değişiklikler yaptıktan sonra, yeniden yayımlayın:

1. Visual Studio 'nun **Çözüm Gezgini** , *Startup.cs* dosyasını açın.

1. Yönteminde, `Configure` `Response.WriteAsync` yöntemi aşağıdaki şekilde görünecek şekilde değiştirin:

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Değişiklikleri *Startup.cs*'ye kaydedin.

1. **Çözüm Gezgini** **' Samplewebappdemo ' çözümüne** sağ tıklayın ve **Yürüt**' ü seçin. **Takım Gezgini** görüntülenir.

1. Gibi bir kayıt iletisi girin `Update #2` .

1. Proje değişikliklerini yürütmek için **Yürüt** düğmesine basın.

1. **Ana**  >  **eşitleme**  >  **eylemleri**  >  **Gönder**' i seçin.

> [!NOTE]
> Alternatif olarak **, komut penceresini açıp, proje**dizinine değiştirerek ve bir git komutu girerek değişiklikleri **komut penceresinden** gönderin. Örnek:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Azure 'da güncelleştirilmiş Web uygulamasını görüntüleme

Azure portalındaki Web uygulaması dikey penceresinde veya **Browse** bir tarayıcı açıp Web uygulamasının URL 'sini girerek güncelleştirilmiş Web uygulamasını görüntüleyin. Örnek: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure Pipelines ile ilk işlem hattınızı oluşturma](/azure/devops/pipelines/get-started-yaml)
* [Kudu Projesi](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
