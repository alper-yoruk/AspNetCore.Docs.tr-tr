---
title: Visual Studio ile Azure 'da ASP.NET Core uygulaması yayımlama
author: rick-anderson
description: ASP.NET Core uygulamasının Visual Studio kullanarak Azure App Service nasıl yayımlanacağını öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 634fb821ef0478dbcf57fdbb991a2e8bbb9402f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777091"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Visual Studio ile Azure 'da ASP.NET Core uygulaması yayımlama

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


MacOS 'ta çalışıyorsanız [Mac için Visual Studio kullanarak Azure App Service Için Web uygulaması yayımlama](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) konusuna bakın.

App Service dağıtım sorununu gidermek için, bkz <xref:test/troubleshoot-azure-iis>..

## <a name="set-up"></a>Ayarlama

* Hesabınız yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın. 

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

Visual Studio başlangıç sayfasında **dosya > yeni > proje...** öğesini seçin.

![Dosya menüsü](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

**Yeni proje** iletişim kutusunu doldurun:

* Sol bölmede **.NET Core**' u seçin.
* Orta bölmede **ASP.NET Core Web uygulaması**' nı seçin.
* **Tamam**’ı seçin.

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj.png)

**Yeni ASP.NET Core Web uygulaması** iletişim kutusunda:

* **Web uygulaması**' nı seçin.
* **Kimlik doğrulamasını Değiştir**' i seçin.

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

**Kimlik doğrulamasını Değiştir** iletişim kutusu görüntülenir. 

* **Bireysel kullanıcı hesapları**' nı seçin.
* **Yeni ASP.NET Core Web uygulamasına**geri dönmek için **Tamam** ' ı seçin ve ardından yeniden **Tamam** ' ı seçin.

![Yeni ASP.NET Core Web kimlik doğrulaması iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio çözümü oluşturur.

## <a name="run-the-app"></a>Uygulamayı çalıştırma

* Projeyi çalıştırmak için CTRL + F5 tuşlarına basın.
* **Hakkında** ve **iletişim** bağlantılarını test edin.

![Web uygulaması, localhost üzerinde Microsoft Edge 'de açık](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Kullanıcı kaydetme

* **Kaydet** ve yeni bir Kullanıcı Kaydet ' i seçin. Kurgusal bir e-posta adresi kullanabilirsiniz. Gönderdiğinizde, sayfada şu hata görüntülenir:

    *"İç sunucu hatası: istek işlenirken bir veritabanı işlemi başarısız oldu. SQL özel durumu: veritabanı açılamıyor. Uygulama DB bağlamı için mevcut geçişleri uygulamak, bu sorunu çözebilir. "*
* **Geçişleri Uygula** ' yı seçin ve sayfa güncelleştirildiğinde sayfayı yenileyin.

![İç sunucu hatası: istek işlenirken bir veritabanı işlemi başarısız oldu. SQL özel durumu: veritabanı açılamıyor. Uygulama DB bağlamı için mevcut geçişleri uygulamak bu sorunu çözebilir.](publish-to-azure-webapp-using-vs/_static/mig.png)

Uygulama, Yeni Kullanıcı ve bir **Oturum çıkış** bağlantısı kaydetmek için kullanılan e-postayı görüntüler.

![Web uygulaması Microsoft Edge 'de açılır. Kayıt bağlantısı, Merhaba email@domain.commetinle değiştirilmiştir!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Uygulamayı Azure’da dağıtma

Çözüm Gezgini projeye sağ tıklayıp **Yayımla...**' yı seçin.

![Yayımla bağlantısı vurgulanmış bağlamsal menü açık](publish-to-azure-webapp-using-vs/_static/pub.png)

**Yayımla** iletişim kutusunda:

* **Microsoft Azure App Service**seçin.
* Dişli simgesini seçin ve ardından **Profil oluştur**' u seçin.
* **Profil Oluştur**'u seçin.

![Yayımla iletişim kutusu](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Azure kaynakları oluşturma

**App Service oluştur** iletişim kutusu görüntülenir:

* Aboneliğinizi girin.
* **Uygulama adı**, **kaynak grubu**ve **App Service planı** giriş alanları doldurulur. Bu adları koruyabilir veya değiştirebilirsiniz.

![App Service iletişim kutusu](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Yeni bir veritabanı oluşturmak için **Hizmetler** sekmesini seçin.

* Yeni bir SQL **+** veritabanı oluşturmak için yeşil simgesini seçin

![Yeni SQL Veritabanı](publish-to-azure-webapp-using-vs/_static/sql.png)

* Yeni bir veritabanı oluşturmak için **SQL veritabanını Yapılandır** Iletişim kutusunda **yeni...** seçeneğini belirleyin.

![Yeni SQL veritabanı ve sunucu](publish-to-azure-webapp-using-vs/_static/conf.png)

**SQL Server Yapılandır** iletişim kutusu görüntülenir.

* Bir Yönetici Kullanıcı adı ve parola girin ve **Tamam**' ı seçin. Varsayılan **sunucu adını**koruyabilirsiniz. 

> [!NOTE]
> Yönetici Kullanıcı adı olarak "admin" kullanımına izin verilmez.

![SQL Server iletişim kutusunu Yapılandır](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* **Tamam**’ı seçin.

Visual Studio, **oluşturma App Service** iletişim kutusuna geri döner.

* Oluştur **App Service** Iletişim kutusunda **Oluştur** ' u seçin.

![SQL veritabanını Yapılandır iletişim kutusu](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio, Azure üzerinde Web uygulaması ve SQL Server oluşturur. Bu adım birkaç dakika sürebilir. Oluşturulan kaynaklar hakkında daha fazla bilgi için bkz. [ek kaynaklar](#additional-resources).

Dağıtım tamamlandığında **Ayarlar**' ı seçin:

![SQL Server iletişim kutusunu Yapılandır](publish-to-azure-webapp-using-vs/_static/set.png)

**Yayımla** Iletişim kutusunun **Ayarlar** sayfasında:

* **Veritabanları** ' nı genişletin ve **çalışma zamanında bu bağlantı dizesini kullan**' ı işaretleyin.
* **Entity Framework geçişleri** genişletin ve **Bu geçişi yayınla Uygula**' yı işaretleyin.

* **Kaydet**’i seçin. Visual Studio **Yayımla** iletişim kutusuna geri döner. 

![Yayımla iletişim kutusu: ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pubs.png)

**Yayımla**’ta tıklayın. Visual Studio, uygulamanızı Azure 'da yayımlar. Dağıtım tamamlandığında, uygulama bir tarayıcıda açılır.

### <a name="test-your-app-in-azure"></a>Uygulamanızı Azure’da test edin

* **Hakkında** ve **iletişim** bağlantılarını test edin

* Yeni bir Kullanıcı Kaydet

![Azure App Service üzerinde Microsoft Edge 'de açılan Web uygulaması](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Uygulamayı güncelleştirme

* *Pages/about. cshtml* Razor sayfasını düzenleyin ve içeriğini değiştirin. Örneğin, paragrafı "Merhaba ASP.NET Core!" olacak şekilde değiştirebilirsiniz:

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Projeye sağ tıklayın ve **Yayımla...** ' yı seçin.

![Yayımla bağlantısı vurgulanmış bağlamsal menü açık](publish-to-azure-webapp-using-vs/_static/pub.png)

* Uygulama yayımlandıktan sonra, yaptığınız değişikliklerin Azure 'da kullanılabilir olduğunu doğrulayın.

![Görevin tamamlandığını doğrulama](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Temizleme

Uygulamayı test etmeyi bitirdiğinizde [Azure Portal](https://portal.azure.com/) gidin ve uygulamayı silin.

* **Kaynak grupları**' nı ve ardından oluşturduğunuz kaynak grubunu seçin.

![Azure portalı: kenar çubuğu menüsündeki kaynak grupları](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* **Kaynak grupları** sayfasında **Sil**' i seçin.

![Azure portalı: kaynak grupları sayfası](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Kaynak grubunun adını girip **Sil**' i seçin. Uygulamanız ve bu öğreticide oluşturulan diğer tüm kaynaklar artık Azure 'dan silinir.

### <a name="next-steps"></a>Sonraki adımlar

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Ek kaynaklar

* Visual Studio Code için bkz. [Yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Azure kaynak grupları](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Veritabanı](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
