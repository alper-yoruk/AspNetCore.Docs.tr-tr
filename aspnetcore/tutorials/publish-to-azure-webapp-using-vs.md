---
title: Visual Studio ile Azure 'da ASP.NET Core uygulaması yayımlama
author: rick-anderson
description: ASP.NET Core uygulamasının Visual Studio kullanarak Azure App Service nasıl yayımlanacağını öğrenin.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 1fced12700fcd5910c1484ebb9190c7652b2646e
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130710"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Visual Studio ile Azure 'da ASP.NET Core uygulaması yayımlama

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


MacOS 'ta çalışıyorsanız [Mac için Visual Studio kullanarak Azure App Service Için Web uygulaması yayımlama](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) konusuna bakın.

App Service dağıtım sorununu gidermek için, bkz <xref:test/troubleshoot-azure-iis> ..

## <a name="set-up"></a>Kurulum

* Hesabınız yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın. 

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

Visual Studio başlangıç sayfasında **dosya > yeni > proje...** öğesini seçin.

![Dosya menüsü](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

**Yeni proje** iletişim kutusunu doldurun:

* **ASP.NET Core Web uygulaması**' nı seçin.
* **İleri**’yi seçin.

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj.png)

**Yeni ASP.NET Core Web uygulaması** iletişim kutusunda:

* **Web uygulaması**' nı seçin.
* Kimlik doğrulaması altında **Değiştir** ' i seçin.

![Yeni ASP.NET Core Web iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

**Kimlik doğrulamasını Değiştir** iletişim kutusu görüntülenir. 

* **Bireysel kullanıcı hesapları**' nı seçin.
* **Yeni ASP.NET Core Web uygulamasına**geri dönmek için **Tamam** ' ı seçin ve ardından **Oluştur**' u seçin.

![Yeni ASP.NET Core Web kimlik doğrulaması iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio çözümü oluşturur.

## <a name="run-the-app"></a>Uygulamayı çalıştırma

* Projeyi çalıştırmak için CTRL + F5 tuşlarına basın.
* **Gizlilik** bağlantısını test edin.

![Web uygulaması, localhost üzerinde Microsoft Edge 'de açık](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Kullanıcı kaydetme

* **Kaydet** ve yeni bir Kullanıcı Kaydet ' i seçin. Kurgusal bir e-posta adresi kullanabilirsiniz. Gönderdiğinizde, sayfada şu hata görüntülenir:

    *"İstek işlenirken bir veritabanı işlemi başarısız oldu. Uygulama DB bağlamı için mevcut geçişleri uygulamak, bu sorunu çözebilir. "*
* **Geçişleri Uygula** ' yı seçin ve sayfa güncelleştirildiğinde sayfayı yenileyin.

![İstek işlenirken bir veritabanı işlemi başarısız oldu. Uygulama DB bağlamı için mevcut geçişleri uygulamak bu sorunu çözebilir.](publish-to-azure-webapp-using-vs/_static/mig.png)

Uygulama, yeni kullanıcıyı ve bir **oturum kapatma** bağlantısını kaydetmek için kullanılan e-postayı görüntüler.

![Web uygulaması Microsoft Edge 'de açılır. Kayıt bağlantısı, Merhaba metinle değiştirilmiştir user1@example.com !](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Uygulamayı Azure’da dağıtma

Çözüm Gezgini projeye sağ tıklayıp **Yayımla...**' yı seçin.

![Yayımla bağlantısı vurgulanmış bağlamsal menü açık](publish-to-azure-webapp-using-vs/_static/pub.png)

**Yayımla** iletişim kutusunda:

* **Azure**' u seçin.
* **İleri**’yi seçin.

![Yayımla iletişim kutusu](publish-to-azure-webapp-using-vs/_static/maas1.png)

**Yayımla** iletişim kutusunda:

* **Azure App Service (Linux)** öğesini seçin.
* **İleri**’yi seçin.

![Yayımla Iletişim kutusu: Azure hizmeti seçin](publish-to-azure-webapp-using-vs/_static/maas2.png)

**Yayımla** iletişim kutusunda **yeni Azure App Service oluştur ' u seçin...**

![Yayımla iletişim kutusu: Azure hizmet örneği seçin](publish-to-azure-webapp-using-vs/_static/maas3.png)

**App Service oluştur** iletişim kutusu görüntülenir:

* **Uygulama adı**, **kaynak grubu**ve **App Service planı** giriş alanları doldurulur. Bu adları koruyabilir veya değiştirebilirsiniz.
* **Oluştur**’u seçin.

![App Service Oluştur iletişim kutusu](publish-to-azure-webapp-using-vs/_static/newrg1.png)

Oluşturma işlemi tamamlandıktan sonra iletişim kutusu otomatik olarak kapatılır ve **Yayımla** iletişim kutusu odağı yeniden alır:

* Yeni oluşturulan örnek otomatik olarak seçilir.
* **Son**’u seçin.

![Yayımla iletişim kutusu: App Service örneği seçin](publish-to-azure-webapp-using-vs/_static/select_as.png)

Daha sonra **profil Yayımlama Özeti** sayfasını görürsünüz. Visual Studio, bu uygulamanın SQL Server bir veritabanı gerektirdiğini algıladı ve bunu yapılandırmanızı istiyor. **Yapılandır**'ı seçin.

![Yayımlama profili Özet sayfası: SQL Server bağımlılığı yapılandırma](publish-to-azure-webapp-using-vs/_static/sql.png)

**Bağımlılığı Yapılandır** iletişim kutusu görünür:

* **Azure SQL veritabanı**' nı seçin.
* **İleri**’yi seçin.

![SQL Server bağımlılığı iletişim kutusunu Yapılandır](publish-to-azure-webapp-using-vs/_static/sql1.png)

**Azure SQL veritabanı 'Nı Yapılandır** ILETIŞIM kutusunda **SQL veritabanı oluştur** ' u seçin.

![Azure SQL veritabanı 'nı yapılandırma iletişim kutusu](publish-to-azure-webapp-using-vs/_static/sql2.png)

**Azure SQL veritabanı oluştur** görüntülenir:

* **Veritabanı adı**, **kaynak grubu**, **veritabanı sunucusu** ve **App Service planı** giriş alanları doldurulur. Bu değerleri tutabilir veya değiştirebilirsiniz.
* Seçili **veritabanı sunucusu** için **veritabanı yöneticisi Kullanıcı adı** ve **veritabanı yönetici parolasını** girin (kullandığınız hesabın, yeni Azure SQL veritabanı oluşturmak için gerekli izinlere sahip olması gerekir)
* **Oluştur**’u seçin.

![Yeni Azure SQL veritabanı iletişim kutusu](publish-to-azure-webapp-using-vs/_static/sql_create.png)

Oluşturma işlemi tamamlandıktan sonra iletişim kutusu otomatik olarak kapatılır ve **Azure SQL veritabanı 'Nı Yapılandır** iletişim kutusu odağı yeniden alır:

* Yeni oluşturulan örnek otomatik olarak seçilir.
* **İleri**’yi seçin.

![Azure SQL veritabanı 'nı yapılandırma iletişim kutusu](publish-to-azure-webapp-using-vs/_static/sql_select.png)

**Azure SQL veritabanı yapılandırma** iletişim kutusunun sonraki adımında:

* **Veritabanı bağlantısı Kullanıcı adı** ve **veritabanı bağlantısı parolası** alanlarını girin. Bunlar, uygulamanızın çalışma zamanında veritabanına bağlanmak için kullanacağı ayrıntılardır. En iyi yöntem, önceki adımda kullanılan yönetici Kullanıcı adı & parolasıyla aynı ayrıntıların kullanılmasını önmaktır.
* **Son**’u seçin.

![Azure SQL veritabanı iletişim kutusunu, bağlantı dizesi ayrıntılarını yapılandırma](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

**Profil Özeti Yayımla** sayfasında **Ayarlar**' ı seçin:

![Yayımlama profili Özet sayfası: Ayarları Düzenle](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

**Yayımla** Iletişim kutusunun **Ayarlar** sayfasında:

* **Veritabanları** ' nı genişletin ve **çalışma zamanında bu bağlantı dizesini kullan**' ı işaretleyin.
* **Entity Framework geçişleri** genişletin ve **Bu geçişi yayınla Uygula**' yı işaretleyin.

* **Kaydet**’i seçin. Visual Studio **Yayımla** iletişim kutusuna geri döner. 

![Yayımla iletişim kutusu: ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

**Yayımla**’ya tıklayın. Visual Studio, uygulamanızı Azure 'da yayımlar. Dağıtım tamamlandığında, uygulama bir tarayıcıda açılır.

![Yayımla iletişim kutusu: ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a>Uygulamayı güncelleştirme

* *Pages/Index. cshtml* Razor sayfasını düzenleyin ve içeriğini değiştirin. Örneğin, paragrafı "Merhaba ASP.NET Core!" olacak şekilde değiştirebilirsiniz:

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* **Profili Yayımla Özeti** sayfasından **Yayımla** ' yı seçin.

![Yayımlama profili Özet sayfası](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

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
