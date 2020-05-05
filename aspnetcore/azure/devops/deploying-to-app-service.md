---
title: ASP.NET Core ve Azure ile App Service DevOps 'a uygulama dağıtma
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps için ilk adımı Azure App Service için bir ASP.NET Core uygulaması dağıtın.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: 811b6d047e344fa98ce14f436d3cd8f03c786aff
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767037"
---
# <a name="deploy-an-app-to-app-service"></a>App Service için uygulama dağıtma

[Azure App Service](/azure/app-service/) Azure 'un web barındırma platformudur. Bir Web uygulamasını Azure App Service dağıtmak, el ile veya otomatik bir işlem tarafından yapılabilir. Kılavuzun bu bölümü, komut satırını kullanarak el ile veya komut dosyası tarafından tetiklenebilecek veya Visual Studio kullanarak el ile tetiklenen dağıtım yöntemlerini açıklar.

Bu bölümde, aşağıdaki görevleri yerine getirirsiniz:

* Örnek uygulamayı indirin ve derleyin.
* Azure Cloud Shell kullanarak Azure App Service bir Web uygulaması oluşturun.
* Git kullanarak örnek uygulamayı Azure 'a dağıtın.
* Visual Studio 'Yu kullanarak uygulamaya bir değişiklik dağıtın.
* Web uygulamasına bir hazırlama yuvası ekleyin.
* Hazırlama yuvasına bir güncelleştirme dağıtın.
* Hazırlama ve üretim yuvalarını değiştirin.

## <a name="download-and-test-the-app"></a>Uygulamayı indirme ve test etme

Bu kılavuzda kullanılan uygulama, önceden oluşturulmuş bir ASP.NET Core uygulaması, [basit bir akış okuyucusu](https://github.com/Azure-Samples/simple-feed-reader/). Bu, `Microsoft.SyndicationFeed.ReaderWriter` bir Razor RSS/Atom akışı almak ve haber öğelerini bir listede göstermek için API kullanan bir sayfalar uygulamasıdır.

Kodu gözden geçirmeniz ücretsizdir, ancak bu uygulamayla ilgili özel bir şey olmadığını anlamak önemlidir. Bu, tanım amaçları için yalnızca basit bir ASP.NET Core uygulamasıdır.

Bir komut kabuğundan kodu indirin, projeyi derleyin ve aşağıdaki gibi çalıştırın.

> *Note: Linux/macOS kullanıcıları, ters eğik çizgi (`/``\`) yerine eğik çizgi () kullanarak yollar için uygun değişiklikleri yapmalıdır.*

1. Kodu yerel makinenizde bir klasöre kopyalayın.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Çalışma klasörünüzü, oluşturulan *basit akış okuyucusu* klasörü ile değiştirin.

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

    ![DotNet Run komutu başarılı](./media/deploying-to-app-service/dotnet-run.png)

5. Bir tarayıcıyı açın ve `http://localhost:5000` dizinine gidin. Uygulama, bir dağıtım akışı URL 'SI yazabilir veya yapıştırmanıza ve haber öğelerinin bir listesini görüntülemenize olanak sağlar.

     ![RSS akışı içeriğini görüntüleyen uygulama](./media/deploying-to-app-service/app-in-browser.png)

6. Uygulamanın doğru şekilde çalışmaya başladıktan sonra komut kabuğu 'nda **CTRL**+**C** tuşuna basarak kapatın.

## <a name="create-the-azure-app-service-web-app"></a>Azure App Service Web uygulaması oluşturma

Uygulamayı dağıtmak için bir App Service [Web uygulaması](/azure/app-service/app-service-web-overview)oluşturmanız gerekir. Web uygulaması oluşturulduktan sonra Git kullanarak yerel makinenizden buna dağıtırsınız.

1. [Azure Cloud Shell](https://shell.azure.com/bash)'de oturum açın. Note: ilk kez oturum açtığınızda, Cloud Shell yapılandırma dosyaları için bir depolama hesabı oluşturmak isteyip istemediğinizi sorar. Varsayılanları kabul edin veya benzersiz bir ad sağlayın.

2. Aşağıdaki adımlar için Cloud Shell kullanın.

    a. Web uygulamanızın adını depolamak için bir değişken bildirin. Ad, varsayılan URL 'de kullanılmak üzere benzersiz olmalıdır. Adı oluşturmak `$RANDOM` için bash işlevinin kullanılması, benzersizliği garanti eder ve biçimde `webappname99999`sonuçlanır.

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Bir kaynak grubu oluşturun. Kaynak grupları, Azure kaynaklarını bir grup olarak yönetilecek şekilde toplamanız için bir yol sağlar.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    `az` Komut, [Azure CLI](/cli/azure/)'yı çağırır. CLı yerel olarak çalıştırılabilir, ancak Cloud Shell kullanılması zaman ve yapılandırmayı kaydeder.

    c. S1 katmanında bir App Service planı oluşturun. App Service planı, aynı fiyatlandırma katmanını paylaşan Web Apps 'in bir gruplandırmasıdır. S1 katmanı ücretsizdir, ancak hazırlama yuvaları özelliği için gereklidir.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Aynı kaynak grubundaki App Service planını kullanarak Web uygulaması kaynağını oluşturun.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Dağıtım kimlik bilgilerini ayarlayın. Bu dağıtım kimlik bilgileri, aboneliğinizdeki tüm Web uygulamaları için geçerlidir. Kullanıcı adında özel karakterler kullanmayın.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Web uygulamasını yerel git 'ten dağıtımları kabul edecek şekilde yapılandırın ve *Git DAĞıTıM URL*'sini görüntüleyin. **Başvuru için bu URL 'yi daha sonra dikkate alın**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. *Web uygulaması URL 'sini*görüntüleyin. Boş Web uygulamasını görmek için bu URL 'ye gidin. **Başvuru için bu URL 'yi daha sonra dikkate alın**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Yerel makinenizde bir komut kabuğu kullanarak, Web uygulamasının proje klasörüne (örneğin, `.\simple-feed-reader\SimpleFeedReader`) gidin. Dağıtım URL 'sine göndermek için git 'i ayarlamak üzere aşağıdaki komutları yürütün:

    a. Uzak URL 'YI yerel depoya ekleyin.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Yerel *ana* dalı *Azure-prod* uzak öğesinin *ana* dalına gönderin.

    ```console
    git push azure-prod master
    ```

    Daha önce oluşturduğunuz dağıtım kimlik bilgileri istenir. Komut kabuğu 'ndaki çıktıyı gözlemleyin. Azure, ASP.NET Core uygulamasını uzaktan oluşturur.

4. Bir tarayıcıda, *Web UYGULAMASı URL* 'sine gidin ve uygulamanın oluşturulup dağıtıldığını aklınızda edin. İle `git commit`yerel git deposuna ek değişiklikler uygulanabilir. Bu değişiklikler, önceki `git push` komutla Azure 'a gönderilir.

## <a name="deployment-with-visual-studio"></a>Visual Studio ile dağıtım

> *Note: Bu bölüm yalnızca Windows için geçerlidir. Linux ve macOS kullanıcıları aşağıdaki adım 2 ' de açıklanan değişikliği yapması gerekir. Dosyayı kaydedin ve yerel depoya ile `git commit`değişikliği yürütün. Son olarak, değişikliği ilk bölümde `git push`olduğu gibi, ile gönderin.*

Uygulama, komut kabuğundan zaten dağıtıldı. Uygulamaya bir güncelleştirme dağıtmak için Visual Studio 'nun tümleşik araçlarını kullanalım. Visual Studio, arka planda komut satırı araçlarıyla aynı şeyi gerçekleştirir, ancak Visual Studio 'nun tanıdık kullanıcı arabirimi içinde.

1. Visual Studio 'da *Simplefeedreader. sln* ' i açın.
2. Çözüm Gezgini, *Pages\ındex.cshtml*dosyasını açın. Olarak `<h2>Simple Feed Reader</h2>` `<h2>Simple Feed Reader - V2</h2>`değiştirin.
3. Uygulamayı derlemek için **CTRL**+**SHIFT**+**B** tuşlarına basın.
4. Çözüm Gezgini, projeye sağ tıklayın ve **Yayımla**' ya tıklayın.

    ![Sağ tıklama, yayımlamayı gösteren ekran görüntüsü](./media/deploying-to-app-service/publish.png)
5. Visual Studio yeni bir App Service kaynağı oluşturabilir, ancak bu güncelleştirme mevcut dağıtım üzerinden yayımlanacak. **Bir yayımlama hedefi seç** iletişim kutusunda, sol taraftaki listeden **App Service** ' yi seçin ve ardından **Varolanı Seç**' i seçin. **Yayımla**’ta tıklayın.
6. **App Service** iletişim kutusunda, Azure aboneliğinizi oluşturmak Için kullanılan Microsoft veya kuruluş hesabının sağ üst köşede görüntülendiğini doğrulayın. Aksi takdirde, açılan eklentiye tıklayın ve ekleyin.
7. Doğru Azure **aboneliğinin** seçili olduğunu onaylayın. **Görünüm**Için **kaynak grubu**' nu seçin. **AzureTutorial** kaynak grubunu genişletin ve ardından mevcut Web uygulamasını seçin. **Tamam**'a tıklayın.

    ![Yayımla App Service iletişim kutusunu gösteren ekran görüntüsü](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio, uygulamayı oluşturur ve Azure 'a dağıtır. Web uygulaması URL 'sine gidin. `<h2>` Öğe değişikliğini canlı olduğunu doğrulayın.

![Değiştirilen başlığa sahip uygulama](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Dağıtım yuvaları

Dağıtım yuvaları, üretimde çalışan uygulamayı etkilemeden değişikliklerin hazırlanmasını destekler. Uygulamanın hazırlanmış sürümü bir kalite güvencesi ekibi tarafından doğrulandıktan sonra, üretim ve hazırlama yuvaları takas edilebilir. Hazırlama aşamasındaki uygulama üretime bu şekilde yükseltilir. Aşağıdaki adımlar bir hazırlama yuvası oluşturur, bu üzerinde bazı değişiklikler dağıtır ve doğrulama işleminden sonra hazırlama yuvasını üretim ile değiştirir.

1. Henüz oturum açmadıysanız [Azure Cloud Shell](https://shell.azure.com/bash)oturum açın.
2. Hazırlama yuvasını oluşturun.

    a. *Hazırlama*adına sahip bir dağıtım yuvası oluşturun.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Hazırlama yuvasını yerel git 'ten dağıtımı kullanacak şekilde yapılandırın ve **hazırlama** dağıtımı URL 'sini alın. **Başvuru için bu URL 'yi daha sonra dikkate alın**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Hazırlama yuvasının URL 'sini görüntüleyin. Boş hazırlama yuvasını görmek için URL 'ye gidin. **Başvuru için bu URL 'yi daha sonra dikkate alın**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. Bir metin düzenleyicisinde veya Visual Studio 'da, *sayfa/Index. cshtml* `<h2>` öğesini tekrar değiştirerek öğenin dosyayı okuyup `<h2>Simple Feed Reader - V3</h2>` kaydetmesini sağlayın.

4. Dosyayı, Visual Studio 'nun *Takım Gezgini* sekmesindeki **değişiklikler** sayfasını kullanarak veya yerel makinenin komut kabuğunu kullanarak aşağıdakini girerek Yerel git deposuna kaydedin:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Yerel makinenin komut kabuğunu kullanarak, hazırlama dağıtımı URL 'sini git uzak olarak ekleyin ve kaydedilen değişiklikleri gönderin:

    a. Hazırlama için uzak URL 'YI yerel git deposuna ekleyin.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Yerel *ana* dalı *Azure hazırlama* uzak uygulamasının *ana* dalına gönderin.

    ```console
    git push azure-staging master
    ```

    Azure uygulamayı oluşturup dağıttığında bekleyin.

6. V3 'in hazırlama yuvasına dağıtıldığını doğrulamak için iki tarayıcı penceresi açın. Bir pencerede, özgün Web uygulaması URL 'sine gidin. Diğer pencerede, hazırlama Web uygulaması URL 'sine gidin. Üretim URL 'si uygulamanın v2 'ye hizmet eder. Hazırlama URL 'SI uygulamanın V3 öğesine hizmet eder.

    ![Tarayıcı pencerelerini karşılaştıran ekran görüntüsü](./media/deploying-to-app-service/ready-to-swap.png)

7. Cloud Shell, doğrulanan/warmed hazırlama yuvasını üretim olarak değiştirin.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. İki tarayıcı pencerelerini yenileyerek değiştirme gerçekleştiğini doğrulayın.

    ![Değiştirme sonrasında tarayıcı pencerelerini karşılaştırma](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Özet

Bu bölümde, aşağıdaki görevler tamamlanmıştır:

* Örnek uygulama indirildi ve oluşturuldu.
* Azure Cloud Shell kullanarak bir Azure App Service Web uygulaması oluşturuldu.
* Git kullanarak örnek uygulamayı Azure 'a dağıttınız.
* Visual Studio kullanılarak uygulamaya bir değişiklik dağıtıldı.
* Web uygulamasına bir hazırlama yuvası eklendi.
* Hazırlama yuvasına bir güncelleştirme dağıtıldı.
* Hazırlama ve üretim yuvaları takas edilen.

Sonraki bölümde Azure Pipelines bir DevOps işlem hattı oluşturmayı öğreneceksiniz.

## <a name="additional-reading"></a>Ek okuma

* [Web Apps genel bakış](/azure/app-service/app-service-web-overview)
* [Azure App Service’te .NET Core ve SQL Veritabanı web uygulaması oluşturma](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Azure App Service için dağıtım kimlik bilgilerini yapılandırma](/azure/app-service/app-service-deployment-credentials)
* [Azure App Service’te hazırlık ortamları ayarlama](/azure/app-service/web-sites-staged-publishing)
