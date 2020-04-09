---
title: Visual Studio ile Azure'da ASP.NET Core uygulaması yayınlama
author: rick-anderson
description: Visual Studio'ASP.NET bir ASP.NET Core uygulamasını Azure Uygulama Hizmeti'nde nasıl yayınlayacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7fc3644df3dcb957f2537538aaa9506c6b38a480
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662206"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Visual Studio ile Azure'da ASP.NET Core uygulaması yayınlama

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Bkz. macOS üzerinde çalışıyorsanız [Mac için Visual Studio'u kullanarak Azure Uygulama Hizmeti'ne web uygulaması yayımla.](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019)

Bir Uygulama Hizmeti dağıtım sorununu <xref:test/troubleshoot-azure-iis>gidermek için bkz.

## <a name="set-up"></a>Ayarla

* Hesabınız yoksa ücretsiz bir [Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın. 

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

Visual Studio Başlangıç Sayfasında **Dosya > Yeni > Projesi'ni seçin...**

![Dosya menüsü](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Yeni **Proje** iletişim kutusunu tamamlayın:

* Sol bölmede **.NET Core'u**seçin.
* Orta **bölmede, Core Web Uygulaması ASP.NET**seçin.
* **Tamam'ı**seçin.

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Yeni **ASP.NET Çekirdek Web Uygulaması** iletişim kutusunda:

* **Web Uygulamasını**Seçin.
* **Kimlik Doğrulamasını Değiştir'i**seçin.

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

**Kimlik Doğrulamasını Değiştir** iletişim kutusu görüntülenir. 

* **Bireysel Kullanıcı Hesapları**seçin.
* Yeni ASP.NET Çekirdek **Web Uygulamasına**dönmek için **Tamam'ı** seçin, ardından tekrar **Tamam'ı** seçin.

![Yeni ASP.NET Core Web kimlik doğrulama iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio çözümü oluşturur.

## <a name="run-the-app"></a>Uygulamayı çalıştırma

* Projeyi çalıştırmak için CTRL+F5 tuşuna basın.
* **Hakkında** ve **İletişim** bağlantılarını test edin.

![Web uygulaması localhost microsoft edge açık](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Bir kullanıcıyı kaydetme

* **Kaydol'u** seçin ve yeni bir kullanıcı kaydettirin. Hayali bir e-posta adresi kullanabilirsiniz. Gönderdiğiniz zaman, sayfa aşağıdaki hatayı görüntüler:

    *"İç Sunucu Hatası: Bir veritabanı işlemi isteği işlerken başarısız oldu. SQL özel durumu: Veritabanını açamıyor. Uygulama DB bağlamı için varolan geçişleri uygulamak bu sorunu çözebilir."*
* **Geçişuygula'yı** seçin ve sayfa güncellendikten sonra sayfayı yenileyin.

![İç Sunucu Hatası: Bir veritabanı işlemi isteği işlerken başarısız oldu. SQL özel durumu: Veritabanını açamıyor. Uygulama DB bağlamı için varolan geçişleri uygulamak bu sorunu çözebilir.](publish-to-azure-webapp-using-vs/_static/mig.png)

Uygulama, yeni kullanıcıyı kaydetmek için kullanılan e-postayı ve **Oturum Dışı** bağlantısını görüntüler.

![Web uygulaması Microsoft Edge'de açılır. Kayıt bağlantısı merhaba metni ile email@domain.comdeğiştirilir!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Uygulamayı Azure’da dağıtma

Solution Explorer'da projeye sağ tıklayın ve **Yayımla'yı seçin...**.

![Bağlamsal menü açık ve Publish bağlantısı vurgulanır](publish-to-azure-webapp-using-vs/_static/pub.png)

**Yayımla** iletişim kutusunda:

* **Microsoft Azure Uygulama Hizmeti'ni**seçin.
* Vites simgesini seçin ve ardından **Profil Oluştur'u**seçin.
* **Profil Oluştur**'u seçin.

![İletişim kutusu yayımlama](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Azure kaynakları oluşturma

**Uygulama Hizmeti Oluştur** iletişim kutusu görüntülenir:

* Aboneliğinizi girin.
* **Uygulama Adı,** **Kaynak Grubu**ve Uygulama **Hizmet Planı** giriş alanları doldurulur. Bu adları saklayabilir veya değiştirebilirsiniz.

![Uygulama Hizmeti iletişim kutusu](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Yeni bir veritabanı oluşturmak için **Hizmetler** sekmesini seçin.

* Yeni bir **+** SQL Veritabanı oluşturmak için yeşil simgeyi seçin

![Yeni SQL Veritabanı](publish-to-azure-webapp-using-vs/_static/sql.png)

* Yeni bir veritabanı oluşturmak için **SQL Veritabanını Yapılandır'da** **Yeni...** seçeneğini belirleyin.

![Yeni SQL Veritabanı ve sunucu](publish-to-azure-webapp-using-vs/_static/conf.png)

**SQL Server'ı Yapılandır** ı iletişim kutusu görüntülenir.

* Yönetici kullanıcı adı ve parola girin ve ardından **Tamam'ı**seçin. Varsayılan **Sunucu Adını**tutabilirsiniz. 

> [!NOTE]
> Yönetici kullanıcı adı olarak "yönetici"ye izin verilmez.

![SQL Server iletişim kutusunu yapılandırma](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* **Tamam'ı**seçin.

Visual **Studio, Uygulama Hizmeti Oluştur** iletişim kutusuna geri döner.

* Uygulama Hizmeti **Oluştur** iletişim kutusunda **Oluştur'u** seçin.

![SQL Veritabanı iletişim kutusunu yapılandırma](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio, Azure'da Web uygulamasını ve SQL Server'ı oluşturur. Bu adım birkaç dakika sürebilir. Oluşturulan kaynaklar hakkında bilgi için [ek kaynaklara](#additional-resources)bakın.

Dağıtım tamamlandığında **Ayarlar'ı**seçin:

![SQL Server iletişim kutusunu yapılandırma](publish-to-azure-webapp-using-vs/_static/set.png)

**Yayımla** iletişim kutusunun **Ayarlar** sayfasında:

* **Veritabanlarını** genişletin ve **çalışma zamanında bu bağlantı dizelerini kullanın**denetleyin.
* **Varlık Çerçeve Geçişlerini** Genişletin ve bu geçişi yayımlamaya uygulayın' ı **denetleyin.**

* **Kaydet'i**seçin. Visual Studio **Yayımla** iletişim kutusuna döner. 

![Yayımlama iletişim kutusu: Ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pubs.png)

**Yayımla**’ta tıklayın. Visual Studio uygulamanızı Azure'da yayınlar. Dağıtım tamamlandığında, uygulama bir tarayıcıda açılır.

### <a name="test-your-app-in-azure"></a>Uygulamanızı Azure’da test edin

* **Hakkında** ve **İletişim** bağlantılarını test edin

* Yeni bir kullanıcı kaydetme

![Azure Uygulama Hizmetinde Microsoft Edge'de web uygulaması açıldı](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Uygulamayı güncelleştirin

* *Sayfaları/About.cshtml* Razor sayfasını edin ve içeriğini değiştirin. Örneğin, paragrafı "Hello ASP.NET Core!" diyecek şekilde değiştirebilirsiniz:

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Projeye sağ tıklayın ve **Tekrar Yayımla...'ı** seçin.

![Bağlamsal menü açık ve Publish bağlantısı vurgulanır](publish-to-azure-webapp-using-vs/_static/pub.png)

* Uygulama yayınlandıktan sonra yaptığınız değişikliklerin Azure'da kullanılabildiğinizi doğrulayın.

![Görevin tamamolduğunu doğrula](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Temizleme

Uygulamayı test etmeyi bitirdikten sonra [Azure portalına](https://portal.azure.com/) gidin ve uygulamayı silin.

* **Kaynak gruplarını**seçin, ardından oluşturduğunuz kaynak grubunu seçin.

![Azure Portalı: Kenar çubuğu menüsünde Kaynak Grupları](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Kaynak **grupları** sayfasında **Sil'i**seçin.

![Azure Portalı: Kaynak Grupları sayfası](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Kaynak grubunun adını girin ve **Sil'i**seçin. Uygulamanız ve bu eğitimde oluşturulan diğer tüm kaynaklar artık Azure'dan silinir.

### <a name="next-steps"></a>Sonraki adımlar

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Ek kaynaklar

* Visual Studio Code için [bkz.](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Azure kaynak grupları](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Veritabanı](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
