---
title: ASP.NET Core ve Azure ile sürekli tümleştirme ve dağıtım-DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps 'da sürekli tümleştirme ve dağıtım
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/cicd
ms.openlocfilehash: f5b0e0ee1c903de26188815c7dc01ed547cca97e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767141"
---
# <a name="continuous-integration-and-deployment"></a>Sürekli tümleştirme ve dağıtım

Önceki bölümde, basit akış okuyucusu uygulaması için yerel bir git deposu oluşturdunuz. Bu bölümde, bu kodu bir GitHub deposuna yayımlayacaksınız ve Azure Pipelines kullanarak Azure DevOps Services bir işlem hattı oluşturacaksınız. İşlem hattı, uygulamanın sürekli derlemelerini ve dağıtımlarını mümkün bir şekilde sunar. GitHub deposuna yapılan her türlü kayıt, bir derlemeyi ve Azure Web uygulamasının hazırlama yuvasına bir dağıtımını tetikler.

Bu bölümde, aşağıdaki görevleri tamamlayacaksınız:

* Uygulamanın kodunu GitHub 'a yayımlama
* Yerel git dağıtımının bağlantısını kes
* Azure DevOps kuruluşu oluşturma
* Azure DevOps Services takım projesi oluşturma
* Derleme tanımı oluşturma
* Yayın işlem hattı oluşturma
* GitHub'daki değişiklikleri işleme ve Azure'a otomatik olarak dağıtma
* Azure Pipelines işlem hattını inceleyin

## <a name="publish-the-apps-code-to-github"></a>Uygulamanın kodunu GitHub 'a yayımlama

1. Bir tarayıcı penceresi açın ve adresine `https://github.com`gidin.
1. Başlıktaki aşağı **+** açılan düğmesine tıklayın ve **yeni depo**' ı seçin:

    ![GitHub yeni depo seçeneği](media/cicd/github-new-repo.png)

1. **Sahip** açılır penceresinde hesabınızı seçin ve **Depo adı** metin kutusuna *basit-Feed-Reader* girin.
1. **Depo oluştur** düğmesine tıklayın.
1. Yerel makinenizin komut kabuğunu açın. *Basit akış okuyucusu* git deposunun depolandığı dizine gidin.
1. Var olan *kaynağı* uzak *yukarı akış*olarak yeniden adlandırın. Aşağıdaki komutu yürütün:

    ```console
    git remote rename origin upstream
    ```

1. GitHub 'daki deponun kopyasına işaret eden yeni bir *Başlangıç noktası* ekleyin. Aşağıdaki komutu yürütün:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Yerel git deponuzu yeni oluşturulan GitHub deposuna yayımlayın. Aşağıdaki komutu yürütün:

    ```console
    git push -u origin master
    ```

1. Bir tarayıcı penceresi açın ve adresine `https://github.com/<GitHub_username>/simple-feed-reader/`gidin. Kodunuzun GitHub deposunda göründüğünü doğrulayın.

## <a name="disconnect-local-git-deployment"></a>Yerel git dağıtımının bağlantısını kes

Yerel git dağıtımını aşağıdaki adımlarla kaldırın. Azure Pipelines (bir Azure DevOps hizmeti) her ikisi de bu işlevi değiştirir ve azaltır.

1. [Azure Portal](https://portal.azure.com/)açın ve *hazırlama (MyWebApp\<Unique_number\>/hazırlama)* Web uygulamasına gidin. Web uygulaması, portalın arama kutusuna *hazırlama* girilerek hızlı bir şekilde bulunabilir:

    ![hazırlama Web uygulaması arama terimi](media/cicd/portal-search-box.png)

1. **Dağıtım Merkezi**' ne tıklayın. Yeni bir panel belirir. Önceki bölümde eklenen yerel git kaynak denetimi yapılandırmasını kaldırmak için **bağlantıyı kes** ' e tıklayın. **Evet** düğmesine tıklayarak kaldırma işlemini onaylayın.
1. *MyWebApp<unique_number>* App Service gidin. Bir anımsatıcı olarak, App Service hızlı bir şekilde bulmak için portalın arama kutusu kullanılabilir.
1. **Dağıtım Merkezi**' ne tıklayın. Yeni bir panel belirir. Önceki bölümde eklenen yerel git kaynak denetimi yapılandırmasını kaldırmak için **bağlantıyı kes** ' e tıklayın. **Evet** düğmesine tıklayarak kaldırma işlemini onaylayın.

## <a name="create-an-azure-devops-organization"></a>Azure DevOps kuruluşu oluşturma

1. Bir tarayıcı açın ve [Azure DevOps kuruluş oluşturma sayfasına](https://go.microsoft.com/fwlink/?LinkId=307137)gidin.
1. Azure DevOps kuruluşunuza erişmek için URL 'YI biçimlendirmek üzere **hatırlayabileceğiniz bir ad seçin** metin kutusuna benzersiz bir ad yazın.
1. Kod bir GitHub deposunda barındırıldığından **Git** radyo düğmesini seçin.
1. **Devam** düğmesine tıklayın. Kısa bir bekleme sonrasında, *Myfirstproject*adlı bir hesap ve takım projesi oluşturulur.

    ![Azure DevOps kuruluş oluşturma sayfası](media/cicd/vsts-account-creation.png)

1. Azure DevOps kuruluşunun ve projesinin kullanıma hazırlandığını belirten onay e-postasını açın. **Projenize başla** düğmesine tıklayın:

    ![Proje düğmesini başlatın](media/cicd/vsts-start-project.png)

1. * \<Account_name\>. VisualStudio.com*için bir tarayıcı açılır. Projenin DevOps ardışık düzenini yapılandırmaya başlamak için *Myfirstproject* bağlantısına tıklayın.

## <a name="configure-the-azure-pipelines-pipeline"></a>Azure Pipelines işlem hattını yapılandırma

Tamamlanacak üç farklı adım vardır. Aşağıdaki üç bölümde bulunan adımların tamamlanması işlemsel bir DevOps işlem hattı ile sonuçlanır.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>GitHub deposuna Azure DevOps erişimi verme

1. **Bir dış depodan gelen veya derleme kodunu** genişletin Accordion. **Kurulum oluştur** düğmesine tıklayın:

    ![Kurulum oluştur düğmesi](media/cicd/vsts-setup-build.png)

1. **Kaynak seçin** bölümünde **GitHub** seçeneğini belirleyin:

    ![Kaynak seçin-GitHub](media/cicd/vsts-select-source.png)

1. Azure DevOps 'ın GitHub deponuza erişebilmesi için yetkilendirme gereklidir. **Bağlantı adı** metin kutusuna *GitHub bağlantısı><GitHub_username* girin. Örneğin:

    ![GitHub bağlantı adı](media/cicd/vsts-repo-authz.png)

1. GitHub hesabınızda iki öğeli kimlik doğrulaması etkinleştirilirse, kişisel erişim belirteci gereklidir. Bu durumda, **GitHub kişisel erişim belirteci Ile yetkilendirme** bağlantısına tıklayın. Yardım için [resmi GitHub kişisel erişim belirteci oluşturma yönergelerine](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) bakın. İzinlerin yalnızca *Depo* kapsamı gereklidir. Aksi takdirde, **OAuth kullanarak Yetkilendir** düğmesine tıklayın.
1. İstendiğinde, GitHub hesabınızda oturum açın. Ardından, Azure DevOps kuruluşunuza erişim vermek için yetkilendir ' i seçin. Başarılı olursa, yeni bir hizmet uç noktası oluşturulur.
1. **Depo** düğmesinin yanındaki üç nokta düğmesine tıklayın. Listeden *<GitHub_username>/Simple-Feed-Reader* deposunu seçin. **Seç** düğmesine tıklayın.
1. **El ile ve zamanlanmış yapılar açılır Için varsayılan daldan** *ana* dalı seçin. **Devam** düğmesine tıklayın. Şablon seçimi sayfası görüntülenir.

### <a name="create-the-build-definition"></a>Derleme tanımı oluşturma

1. Şablon Seçimi sayfasında, arama kutusuna *ASP.NET Core* girin:

    ![Şablon sayfasında arama ASP.NET Core](media/cicd/vsts-template-selection.png)

1. Şablon arama sonuçları görüntülenir. **ASP.NET Core** şablonun üzerine gelin ve **Uygula** düğmesine tıklayın.
1. Yapı tanımının **Görevler** sekmesi görüntülenir. **Tetikleyiciler** sekmesini tıklatın.
1. **Sürekli tümleştirmeyi etkinleştir** kutusunu işaretleyin. **Dal filtreleri** bölümünde, **tür** açılır seçeneğinin *dahil*olarak ayarlandığını doğrulayın. **Dal belirtimi** açılır öğesini *ana*olarak ayarlayın.

    ![Sürekli tümleştirme ayarlarını etkinleştir](media/cicd/vsts-enable-ci.png)

    Bu ayarlar, GitHub deposunun *ana* dalına herhangi bir değişiklik gönderildiğinde bir yapılandırmanın tetiklenmesine neden olur. Sürekli tümleştirme, [GitHub 'daki değişiklikleri Yürüt ve Azure 'a otomatik olarak dağıt](#commit-changes-to-github-and-automatically-deploy-to-azure) bölümünde test edilir.

1. **& kuyruğu kaydet** düğmesine tıklayın ve **Kaydet** seçeneğini belirleyin:

    ![Kaydet düğmesi](media/cicd/vsts-save-build.png)

1. Aşağıdaki kalıcı iletişim kutusu görüntülenir:

    ![Derleme tanımını kaydet-kalıcı iletişim kutusu](media/cicd/vsts-save-modal.png)

    Varsayılan klasörünü *\\*kullanın ve **Kaydet** düğmesine tıklayın.

### <a name="create-the-release-pipeline"></a>Yayın işlem hattını oluşturma

1. Takım projenizin **yayınlar** sekmesine tıklayın. Yeni işlem **hattı** düğmesine tıklayın.

    ![Yayınlar sekmesi-yeni tanım düğmesi](media/cicd/vsts-new-release-definition.png)

    Şablon Seçimi bölmesi görüntülenir.

1. Şablon Seçimi sayfasında, arama kutusuna *App Service* girin:

    ![Yayın işlem hattı şablonu arama kutusu](media/cicd/vsts-release-template-search.png)

1. Şablon arama sonuçları görüntülenir. **Yuva şablonuyla Azure App Service dağıtımının** üzerine gelin ve **Uygula** düğmesine tıklayın. Yayın işlem hattının **ardışık düzen** sekmesi görüntülenir.

    ![Yayın ardışık düzen işlem hattı sekmesi](media/cicd/vsts-release-definition-pipeline.png)

1. **Yapıtlar** kutusunda **Ekle** düğmesine tıklayın. **Yapıt Ekle** paneli görünür:

    ![Yayın işlem hattı-yapıt bölmesi ekleme](media/cicd/vsts-release-add-artifact.png)

1. **Kaynak türü** bölümünden **Yapı** kutucuğunu seçin. Bu tür, yayın işlem hattının derleme tanımına bağlanmasına izin verir.
1. **Proje** açılır listesinden *myfirstproject* ' i seçin.
1. **Kaynak (derleme tanımı)** açılır listesinden derleme tanımı adı, *MYFIRSTPROJECT-ASP.NET Core-CI*' ı seçin.
1. **Varsayılan sürüm** açılır listesinden *en son* ' u seçin. Bu seçenek derleme tanımının en son çalıştırması tarafından oluşturulan yapıtları oluşturur.
1. **Kaynak diğer ad** metin kutusundaki metni *Drop*ile değiştirin.
1. **Ekle** düğmesine tıklayın. **Yapıtlar** bölümü, değişiklikleri görüntüleyecek şekilde güncelleştirilir.
1. Sürekli dağıtımları etkinleştirmek için şimşek simgesine tıklayın:

    ![Yayın işlem hattı yapıtları-şimşek sürgüsü simgesi](media/cicd/vsts-artifacts-lightning-bolt.png)

    Bu seçenek etkinken, her yeni derleme kullanılabilir olduğunda bir dağıtım oluşur.
1. Doğru bir **sürekli dağıtım tetikleme** paneli görüntülenir. Özelliği etkinleştirmek için iki durumlu düğmeye tıklayın. **Çekme isteği tetikleyicisini**etkinleştirmek gerekli değildir.
1. **Yapı Dalı filtreleri** bölümünde **Ekle** açılan düğmesine tıklayın. **Derleme tanımının varsayılan dal** seçeneğini belirleyin. Bu filtre, yayının yalnızca GitHub deposunun *ana* dalından bir derleme için tetiklenmesine neden olur.
1. **Kaydet** düğmesine tıklayın. Elde edilen **kaydetme** kalıcı Iletişim kutusunda **Tamam** düğmesine tıklayın.
1. **Ortam 1** kutusuna tıklayın. Sağ tarafta bir **ortam** paneli görüntülenir. **Ortam adı** metin kutusundaki *ortam 1* metnini *Üretim*olarak değiştirin.

   ![Yayın işlem hattı-ortam adı metin kutusu](media/cicd/vsts-environment-name-textbox.png)

1. **Üretim** kutusunda **1 aşama, 2 görev** bağlantısına tıklayın:

    ![Yayın işlem hattı-üretim ortamı bağlantısı. png](media/cicd/vsts-production-link.png)

    Ortamın **Görevler** sekmesi görüntülenir.
1. **Yuvaya Azure App Service dağıt** görevine tıklayın. Ayarları, sağdaki bir panelde görüntülenir.
1. **Azure aboneliği** açılır listesinden App Service ilişkili Azure aboneliğini seçin. Seçtikten sonra **Yetkilendir** düğmesine tıklayın.
1. **Uygulama türü** açılan listesinden *Web uygulaması* ' nı seçin.
1. **App Service adı** açılır listesinden *mywebapp/<unique_number/>* seçin.
1. **Kaynak grubu** açılır listesinden *AzureTutorial* öğesini seçin.
1. **Yuva** açılır listesinden *hazırlama* ' yı seçin.
1. **Kaydet** düğmesine tıklayın.
1. Varsayılan yayın işlem hattı adının üzerine gelin. Düzenlemek için kalem simgesine tıklayın. Ad olarak *MyFirstProject-ASP.NET Core-CD* kullanın.

    ![Yayın işlem hattı adı](media/cicd/vsts-release-definition-name.png)

1. **Kaydet** düğmesine tıklayın.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>GitHub'daki değişiklikleri işleme ve Azure'a otomatik olarak dağıtma

1. Visual Studio 'da *Simplefeedreader. sln* ' i açın.
1. Çözüm Gezgini, *Pages\ındex.cshtml*dosyasını açın. Olarak `<h2>Simple Feed Reader - V3</h2>` `<h2>Simple Feed Reader - V4</h2>`değiştirin.
1. Uygulamayı derlemek için **CTRL**+**SHIFT**+**B** tuşlarına basın.
1. Dosyayı GitHub deposuna yürütün. Visual Studio 'nun *Takım Gezgini* sekmesindeki **değişiklikler** sayfasını kullanın veya yerel makinenin komut kabuğunu kullanarak aşağıdakini yürütün:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. *Ana* daldaki değişikliği GitHub deponuzdaki *kaynak* uzak adına gönderin:

    ```console
    git push origin master
    ```

    Kayıt, GitHub deposunun *ana* dalında görünür:

    ![Ana dalda GitHub yürütmesi](media/cicd/github-commit.png)

    Derleme, derleme tanımının **Tetikleyiciler** sekmesinde sürekli tümleştirme etkinleştirildiğinden tetiklenir:

    ![Sürekli tümleştirmeyi etkinleştir](media/cicd/enable-ci.png)

1. Azure DevOps Services **Azure Pipelines** > **yapılar** sayfasının **sıraya alınmış** sekmesine gidin. Sıraya alınan yapı, derlemeyi tetikleyen dalı ve yürütmeyi gösterir:

    ![Kuyruğa Alınan derleme](media/cicd/build-queued.png)

1. Oluşturma işlemi başarılı olduktan sonra Azure 'a yönelik bir dağıtım oluşur. Tarayıcıdaki uygulamaya gidin. Başlıkta "v4" metninin göründüğünü unutmayın:

    ![güncelleştirilmiş uygulama](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Azure Pipelines işlem hattını inceleyin

### <a name="build-definition"></a>Derleme tanımı

*MyFirstProject-ASP.NET Core-CI*adında bir derleme tanımı oluşturuldu. Tamamlandıktan sonra, derleme yayımlanacak varlıkları içeren bir *. zip* dosyası üretir. Yayın işlem hattı bu varlıkları Azure 'a dağıtır.

Yapı tanımının **Görevler** sekmesi, kullanılan adımları listeler. Beş derleme görevi vardır.

![derleme tanımı görevleri](media/cicd/build-definition-tasks.png)

1. **Restore** &mdash; , `dotnet restore` uygulamanın NuGet paketlerini geri yüklemek için komutunu yürütür. Kullanılan varsayılan paket akışı nuget.org ' dir.
1. **Derleme** &mdash; , uygulamanın `dotnet build --configuration release` kodunu derlemek için komutunu yürütür. Bu `--configuration` seçenek, bir üretim ortamına dağıtım için uygun olan, kodun iyileştirilmiş bir sürümünü oluşturmak için kullanılır. Örneğin, bir hata ayıklama yapılandırması gerekiyorsa, derleme tanımının **değişkenler** sekmesinde *buildconfiguration* değişkenini değiştirin.
1. **Test** &mdash; , `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` uygulamanın birim testlerini çalıştırmak için komutunu yürütür. Birim testleri, `**/*Tests/*.csproj` glob düzeniyle eşleşen herhangi bir C# projesi içinde yürütülür. Test sonuçları, `--results-directory` seçeneği tarafından belirtilen konumdaki bir *. trx* dosyasına kaydedilir. Herhangi bir test başarısız olursa, yapı başarısız olur ve dağıtılmaz.

    > [!NOTE]
    > Birim testlerinin çalışmasını doğrulamak için, *Simplefeedreader. Tests\Services\NewsServiceTests.cs* ' yi, testlerin birini tam olarak kesin olarak bölmek için değiştirin. Örneğin, `Returns_News_Stories_Given_Valid_Uri` yönteminde olarak `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` değiştirin. Değişiklikleri yürütün ve GitHub 'a gönderin. Derleme tetiklenir ve başarısız olur. Derleme ardışık düzeni durumu **başarısız**olarak değişir. Değişikliği, yürütmeyi ve yeniden gönderin. Derleme başarılı oldu.

1. **Yayımla** &mdash; , `dotnet publish --configuration release --output <local_path_on_build_agent>` dağıtılacak yapıtlar içeren bir *. zip* dosyası üretmek için komutunu yürütür. `--output` Seçeneği, *. zip* dosyasının yayımlama konumunu belirtir. Bu konum adlı `$(build.artifactstagingdirectory)` [önceden tanımlanmış bir değişken](/azure/devops/pipelines/build/variables) geçirilerek belirtilir. Bu değişken, derleme aracısında *c:\agent\_work\1\a*gibi bir yerel yola genişletilir.
1. **Yayımlama yapıtı** &mdash; **Yayımla görevi tarafından** oluşturulan *. zip* dosyasını yayımlar. Görev *. zip* dosya konumunu bir parametre olarak kabul eder, bu, önceden tanımlanmış değişkenidir `$(build.artifactstagingdirectory)`. *. Zip* dosyası *Drop*adlı bir klasör olarak yayımlanır.

Tanım içeren derlemelerin geçmişini görüntülemek için derleme tanımının **Özet** bağlantısına tıklayın:

![Derleme tanımı geçmişini gösteren ekran görüntüsü](media/cicd/build-definition-summary.png)

Sonuç sayfasında, benzersiz derleme numarasına karşılık gelen bağlantıya tıklayın:

![Derleme tanımı Özet sayfasını gösteren ekran görüntüsü](media/cicd/build-definition-completed.png)

Bu özel derleme özeti görüntülenir. **Yapılar** sekmesine tıklayın ve yapı tarafından üretilen *bırakma* klasörünün listelendiğini unutmayın:

![Derleme tanımı yapıtları-bırakma klasörünü gösteren ekran görüntüsü](media/cicd/build-definition-artifacts.png)

Yayımlanan yapıtları incelemek için **İndir** ve **keşfet** bağlantılarını kullanın.

### <a name="release-pipeline"></a>Yayın işlem hattı

*MyFirstProject-ASP.NET Core-CD*adlı bir yayın işlem hattı oluşturuldu:

![Yayın ardışık düzenine genel bakış gösteren ekran görüntüsü](media/cicd/release-definition-overview.png)

Yayın işlem hattının iki ana bileşeni **yapıtlar** ve **ortamlardır**. **Yapıtlar** bölümündeki kutuya tıklanması aşağıdaki paneli ortaya çıkarır:

![Yayın işlem hattı yapılarını gösteren ekran görüntüsü](media/cicd/release-definition-artifacts.png)

**Kaynak (derleme tanımı)** değeri, bu yayın işlem hattının bağlı olduğu derleme tanımını temsil eder. Yapı tanımının başarılı bir çalışması tarafından oluşturulan *. zip* dosyası, Azure 'a dağıtım için *Üretim* ortamına sağlanır. Yayın ardışık düzen görevlerini görüntülemek için, *Üretim* ortamı kutusunda *1 aşama, 2 görev* bağlantısına tıklayın:

![Yayın ardışık düzen görevlerini gösteren ekran görüntüsü](media/cicd/release-definition-tasks.png)

Yayın işlem hattı iki görevden oluşur: *yuvaya Azure App Service dağıtın* ve *Azure App Service yuvası değiştirme 'yi yönetir*. İlk göreve tıkladığınızda aşağıdaki görev yapılandırması görünür:

![Yayın işlem hattı dağıtım görevini gösteren ekran görüntüsü](media/cicd/release-definition-task1.png)

Azure aboneliği, hizmet türü, Web uygulaması adı, kaynak grubu ve dağıtım yuvası dağıtım görevinde tanımlanmıştır. **Package veya Folder** metin kutusu Ayıklanacak ve *\<MyWebApp unique_number\> * Web uygulamasının *hazırlama* yuvasına dağıtılacak *. zip* dosya yolunu tutar.

Yuva takas görevine tıkladığınızda aşağıdaki görev yapılandırması görünür:

![Yayın ardışık düzen yuvası değiştirme görevini gösteren ekran görüntüsü](media/cicd/release-definition-task2.png)

Abonelik, kaynak grubu, hizmet türü, Web uygulaması adı ve dağıtım yuvası ayrıntıları sağlanır. **Üretimle Değiştir** onay kutusu işaretlenir. Sonuç olarak, *hazırlama* yuvasına dağıtılan bitler üretim ortamına değiştirilir.

## <a name="additional-reading"></a>Ek okuma

* [Azure Pipelines ile ilk işlem hattınızı oluşturma](/azure/devops/pipelines/get-started-yaml)
* [Derleme ve .NET Core projesi](/azure/devops/pipelines/languages/dotnet-core)
* [Azure Pipelines bir Web uygulaması dağıtma](/azure/devops/pipelines/targets/webapp)
