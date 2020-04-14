---
title: Uygulamayı Uygulama Hizmetine dağıtın - ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps'lerin ilk adımı olan Azure Uygulama Hizmetine bir ASP.NET Core uygulaması dağıtın.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: d7ee3e42d320d35c2aaff6e097203c45289ec5b1
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228133"
---
# <a name="deploy-an-app-to-app-service"></a>Uygulama Hizmetine uygulama dağıtma

[Azure Uygulama Hizmeti,](/azure/app-service/) Azure'un web barındırma platformudur. Bir web uygulamasını Azure Uygulama Hizmeti'ne dağıtmak el ile veya otomatik bir işlemle yapılabilir. Kılavuzun bu bölümünde, komut satırı kullanılarak el ile veya komut dosyası yla tetiklenebilen veya Visual Studio kullanılarak el ile tetiklenen dağıtım yöntemleri anlatılır.

Bu bölümde, aşağıdaki görevleri yerine getirirsiniz:

* Örnek uygulamayı indirin ve oluşturun.
* Azure Bulut Kabuğu'nu kullanarak bir Azure Uygulama Hizmeti Web Uygulaması oluşturun.
* Örnek uygulamayı Git'i kullanarak Azure'a dağıtın.
* Visual Studio'yu kullanarak uygulamaya bir değişiklik dağıtın.
* Web uygulamasına bir evreleme yuvası ekleyin.
* Evreleme yuvasına bir güncelleştirme dağıtın.
* Hazırlama ve üretim yuvalarını değiştirin.

## <a name="download-and-test-the-app"></a>Uygulamayı indirin ve test edin

Bu kılavuzda kullanılan uygulama önceden oluşturulmuş bir ASP.NET Core uygulaması, [Basit Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/). RSS/Atom akışı almak ve `Microsoft.SyndicationFeed.ReaderWriter` haber öğelerini bir listede görüntülemek için API'yi kullanan bir Razor Pages uygulamasıdır.

Kodu gözden geçirmekten çekinmeyin, ancak bu uygulamanın özel bir şey olmadığını anlamak önemlidir. Bu sadece açıklayıcı amaçlar için basit bir ASP.NET Core uygulaması.

Komut kabuğundan kodu indirin, projeyi oluşturun ve aşağıdaki gibi çalıştırın.

> *Not: Linux/macOS kullanıcıları yollar için uygun değişiklikler yapmalıdır, örneğin, geri eğik çizgi yerine ileri eğik çizgi ()`/`kullanarak .`\`*

1. Kodu yerel makinenizdeki bir klasöre kopyalayın.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Çalışma klasörünüzü oluşturulan *basit özet okuyucu* klasörüne değiştirin.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Paketleri geri yükleyin ve çözümü oluşturun.

    ```dotnetcli
    dotnet build
    ```

4. Uygulamayı çalıştırın.

    ```dotnetcli
    dotnet run
    ```

    ![Dotnet çalıştır komutu başarılı](./media/deploying-to-app-service/dotnet-run.png)

5. Bir tarayıcıyı açın ve `http://localhost:5000` dizinine gidin. Uygulama, bir sendikasyon akışı URL'si yazmanıza veya yapıştırmanıza ve haber öğelerinin listesini görüntülemenize olanak tanır.

     ![RSS akışının içeriğini görüntüleyen uygulama](./media/deploying-to-app-service/app-in-browser.png)

6. Uygulamanın doğru çalıştığından emin olduktan sonra, komut kabuğundaki **Ctrl**+**C** tuşuna basarak uygulamayı kapatın.

## <a name="create-the-azure-app-service-web-app"></a>Azure App Service Web Uygulamasını Oluşturun

Uygulamayı dağıtmak için bir Uygulama Hizmeti Web [Uygulaması](/azure/app-service/app-service-web-overview)oluşturmanız gerekir. Web Uygulaması oluşturulduktan sonra, Git'i kullanarak yerel makinenizden dağıtacaksınız.

1. [Azure Cloud Shell](https://shell.azure.com/bash)'de oturum açın. Not: İlk kez oturum açtığınızda, Cloud Shell yapılandırma dosyaları için bir depolama hesabı oluşturmasını ister. Varsayılanları kabul edin veya benzersiz bir ad sağlayın.

2. Aşağıdaki adımlar için Bulut Kabuğu'nu kullanın.

    a. Web uygulamanızın adını depolamak için bir değişken bildirin. Varsayılan URL'de kullanılabilmesi için ad benzersiz olmalıdır. Adı `$RANDOM` oluşturmak için Bash işlevini kullanmak benzersizliği garanti `webappname99999`eder ve biçimde sonuçlanır.

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Bir kaynak grubu oluşturun. Kaynak grupları, grup olarak yönetilecek Azure kaynaklarını toplamak için bir araç sağlar.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    Komut `az` Azure [CLI'yi](/cli/azure/)çağırır. CLI yerel olarak çalıştırılabilir, ancak Bulut Kabuğu'nda kullanmak zamandan ve yapılandırmadan tasarruf sağlar.

    c. S1 katmanında bir Uygulama Hizmeti planı oluşturun. Uygulama Hizmeti planı, aynı fiyatlandırma katmanını paylaşan web uygulamalarının gruplandırılmasıdır. S1 katmanı ücretsiz değildir, ancak evreleme yuvaları özelliği için gereklidir.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Aynı kaynak grubunda Uygulama Hizmeti planını kullanarak web uygulaması kaynağını oluşturun.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Dağıtım kimlik bilgilerini ayarlayın. Bu dağıtım kimlik bilgileri aboneliğinizdeki tüm web uygulamaları için geçerlidir. Kullanıcı adında özel karakterler kullanmayın.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Web uygulamasını yerel Git'den gelen dağıtımları kabul etmek ve *Git dağıtım URL'sini*görüntülemek için yapılandırın. **Başvuru için bu URL'yi daha sonra not edin.**

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Web *uygulaması URL'sini*görüntüleyin. Boş web uygulamasını görmek için bu URL'ye göz atın. **Başvuru için bu URL'yi daha sonra not edin.**

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Yerel makinenizde komut kabuğu nu kullanarak, web uygulamasının proje klasörüne gidin (örneğin, `.\simple-feed-reader\SimpleFeedReader`). Git'i dağıtım URL'sine itmek için ayarlamak için aşağıdaki komutları uygulayın:

    a. Uzak URL'yi yerel depoya ekleyin.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Yerel *ana* dalı *azure-prod* uzaktan kumandanın *ana* dalına itin.

    ```console
    git push azure-prod master
    ```

    Daha önce oluşturduğunuz dağıtım kimlik bilgileri için istenirsiniz. Komut kabuğundaki çıkışı gözlemleyin. Azure, ASP.NET Core uygulamasını uzaktan oluşturur.

4. Bir tarayıcıda, *Web uygulaması URL'sine* gidin ve uygulamanın oluşturulup dağıtıldığını unutmayın. Yerel Git deposuna ek değişiklikler `git commit`yapılabilir. Bu değişiklikler önceki `git push` komutla Azure'a itilir.

## <a name="deployment-with-visual-studio"></a>Visual Studio ile Dağıtım

> *Not: Bu bölüm yalnızca Windows için geçerlidir. Linux ve macOS kullanıcıları aşağıdaki adım 2'de açıklanan değişikliği yapmalıdır. Dosyayı kaydedin ve değişikliği yerel depoya `git commit`' la birlikte kaydedin. Son olarak, değişikliği `git push`ilk bölümde olduğu gibi , ile itin.*

Uygulama komut kabuğundan zaten dağıtıldı. Uygulamaya bir güncelleştirme dağıtmak için Visual Studio'nun tümleşik araçlarını kullanalım. Sahne arkasında, Visual Studio komut satırı araç olarak aynı şeyi gerçekleştirir, ancak Visual Studio tanıdık UI içinde.

1. *SimpleFeedReader.sln'i* Visual Studio'da açın.
2. Çözüm Gezgini'nde, *Sayfalar\Index.cshtml'i*açın. 'ye `<h2>Simple Feed Reader - V2</h2>`değiştirin. `<h2>Simple Feed Reader</h2>`
3. Uygulamayı oluşturmak için **Ctrl**+**Shift**+**B** tuşuna basın.
4. Çözüm Gezgini'nde projeye sağ tıklayın ve **Yayımla'yı**tıklatın.

    ![Sağ tıklatma, Yayımla](./media/deploying-to-app-service/publish.png)
5. Visual Studio yeni bir Uygulama Hizmeti kaynağı oluşturabilir, ancak bu güncelleştirme varolan dağıtım üzerinden yayınlanacaktır. **Yayımlama hedef** iletişim kutusunda, soldaki listeden **App Service'i** seçin ve ardından **Varolan'ı seçin'i**seçin. **Yayımla**’ta tıklayın.
6. Uygulama **Hizmeti** iletişim kutusunda, Azure aboneliğinizi oluşturmak için kullanılan Microsoft veya Kuruluş hesabının sağ üstte görüntülendiğini doğrulayın. Değilse, açılır yere tıklayın ve ekleyin.
7. Doğru Azure **Aboneliği'nin** seçildiğini doğrulayın. **Görünüm**için **Kaynak Grubu'nü**seçin. **AzureTutorial** kaynak grubunu genişletin ve ardından varolan web uygulamasını seçin. **Tamam**'a tıklayın.

    ![Uygulama Hizmeti Yayımla iletişim kutusunu gösteren ekran görüntüsü](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio uygulamayı oluşturur ve Azure'a dağır. Web uygulaması URL'sine göz atın. Öğe değişikliğinin `<h2>` canlı olduğunu doğrulayın.

![Başlığı değiştirilen uygulama](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Dağıtım yuvaları

Dağıtım yuvaları, üretimde çalışan uygulamayı etkilemeden değişikliklerin evrelemesine destek verir. Uygulamanın aşamalı sürümü bir kalite güvence ekibi tarafından doğrulandıktan sonra, üretim ve hazırlama yuvaları değiştirilebilir. Evreleme uygulaması bu şekilde üretime yükseltilir. Aşağıdaki adımlar bir evreleme yuvası oluşturmak, bazı değişiklikler dağıtmak ve doğrulamadan sonra üretim ile evreleme yuvası takas.

1. Azure Bulut [Kabuğu'nda](https://shell.azure.com/bash)oturum açın , daha önce oturum açmamışsa.
2. Evreleme yuvasını oluşturun.

    a. Ad *evreleme*ile bir dağıtım yuvası oluşturun.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Hazırlama yuvasını yerel Git'den dağıtım kullanacak ve **hazırlama** dağıtım URL'sini alacak şekilde yapılandırın. **Başvuru için bu URL'yi daha sonra not edin.**

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Hazırlama yuvasının URL'sini görüntüleyin. Boş evreleme yuvasını görmek için URL'ye göz atın. **Başvuru için bu URL'yi daha sonra not edin.**

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. Metin düzenleyicisinde veya Visual Studio'da, öğenin dosyayı `<h2>` okuyup `<h2>Simple Feed Reader - V3</h2>` kaydedilemesi için *Pages/Index.cshtml'i* yeniden değiştirin.

4. Visual Studio'nun *Takım Gezgini* sekmesindeki **Değişiklikler** sayfasını kullanarak veya yerel makinenin komut kabuğunu kullanarak aşağıdakileri girerek dosyayı yerel Git deposuna işleyin:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Yerel makinenin komut kabuğunu kullanarak, hazırlama dağıtım URL'sini Git uzaktan kumandası olarak ekleyin ve taahhüt edilen değişiklikleri itin:

    a. Yerel Git deposuna evreleme için uzak URL ekleyin.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Yerel *ana* dalı *azure evreleme* uzaktan kumandasının *ana* dalına itin.

    ```console
    git push azure-staging master
    ```

    Azure uygulamayı oluştururken ve dağıtırken bekleyin.

6. V3'ün evreleme yuvasına dağıtıldığını doğrulamak için iki tarayıcı penceresi açın. Bir pencerede, orijinal web uygulaması URL'sine gidin. Diğer pencerede, evreleme web uygulaması URL'sine gidin. Üretim URL'si uygulamanın V2'sini hizmet vermektedir. Evreleme URL'si uygulamanın V3'üne hizmet eder.

    ![Tarayıcı pencerelerini karşılaştıran ekran görüntüsü](./media/deploying-to-app-service/ready-to-swap.png)

7. Cloud Shell'de, doğrulanmış/ısınmış evreleme yuvasını üretime dönüştürün.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Takasın iki tarayıcı penceresini yenileyerek gerçekleştiğini doğrulayın.

    ![Takastan sonra tarayıcı pencerelerini karşılaştırma](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Özet

Bu bölümde, aşağıdaki görevler tamamlandı:

* Örnek uygulamayı indirip oluşturup oluşturun.
* Azure Bulut Kabuğu'nu kullanarak bir Azure Uygulama Hizmeti Web Uygulaması oluşturdu.
* Örnek uygulamayı Git'i kullanarak Azure'a dağıttı.
* Visual Studio'yu kullanarak uygulamaya bir değişiklik dağıtıldı.
* Web uygulamasına bir evreleme yuvası eklendi.
* Evreleme yuvasına bir güncelleştirme dağıtıldı.
* Evreleme ve üretim yuvalarını değiştirdim.

Sonraki bölümde, Azure Ardışık Hatları ile DevOps ardışık bir boru hattı oluşturmayı öğreneceksiniz.

## <a name="additional-reading"></a>Ek okuma

* [Web Apps'a genel bakış](/azure/app-service/app-service-web-overview)
* [Azure App Service’te .NET Core ve SQL Veritabanı web uygulaması oluşturma](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Azure Uygulama Hizmeti için dağıtım kimlik bilgilerini yapılandırma](/azure/app-service/app-service-deployment-credentials)
* [Azure App Service’te hazırlık ortamları ayarlama](/azure/app-service/web-sites-staged-publishing)
