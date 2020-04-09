---
title: Sürekli tümleştirme ve dağıtım - ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps'lerde sürekli tümleştirme ve dağıtım
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655836"
---
# <a name="continuous-integration-and-deployment"></a>Sürekli tümleştirme ve dağıtım

Önceki bölümde, Basit Özet Okuyucu uygulaması için yerel bir Git deposu oluşturdunuz. Bu bölümde, bu kodu bir GitHub deposunda yayımlar ve Azure Pipelines kullanarak bir Azure DevOps Hizmetleri ardışık hattı oluşturacaksınız. Boru hattı, uygulamanın sürekli olarak oluşturulmasını ve dağıtımını sağlar. GitHub deposuna yapılan herhangi bir taahhüt, bir yapıyı ve Azure Web Uygulaması'nın evreleme yuvasına dağıtımı tetikler.

Bu bölümde, aşağıdaki görevleri tamamlayaceksiniz:

* Uygulamanın kodunu GitHub'da yayımla
* Yerel Git dağıtımını kesme
* Azure DevOps kuruluşu oluşturma
* Azure DevOps Hizmetlerinde ekip projesi oluşturma
* Derleme tanımı oluşturma
* Yayın işlem hattı oluşturma
* GitHub'daki değişiklikleri işleme ve Azure'a otomatik olarak dağıtma
* Azure Ardışık Hatlar ardışık hattını inceleyin

## <a name="publish-the-apps-code-to-github"></a>Uygulamanın kodunu GitHub'da yayımla

1. Tarayıcı penceresi açın ve `https://github.com`''ye gidin.
1. Üstbilgideki **+** açılır bırakmayı tıklatın ve **Yeni depoyu**seçin:

    ![GitHub Yeni Depo seçeneği](media/cicd/github-new-repo.png)

1. **Sahibi** açılır durumda hesabınızı seçin ve **Depo adı** metin kutusuna basit *özet okuyucu* girin.
1. Oluştur **deposu düğmesini** tıklatın.
1. Yerel makinenizin komut kabuğunu açın. *Basit besleme okuyucu* Git deposunun depolandığı dizine gidin.
1. Varolan *orijin* uzaktan kumandasını yukarı akışa yeniden *adlandırın.* Aşağıdaki komutu yürütün:

    ```console
    git remote rename origin upstream
    ```

1. GitHub'daki deponun kopyanıza işaret eden yeni bir *başlangıç* uzaktan kumandası ekleyin. Aşağıdaki komutu yürütün:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Yerel Git deponuzu yeni oluşturulan GitHub deposunda yayımlayın. Aşağıdaki komutu yürütün:

    ```console
    git push -u origin master
    ```

1. Tarayıcı penceresi açın ve `https://github.com/<GitHub_username>/simple-feed-reader/`''ye gidin. Kodunuzun GitHub deposunda göründüğünü doğrulayın.

## <a name="disconnect-local-git-deployment"></a>Yerel Git dağıtımını kesme

Aşağıdaki adımlarla yerel Git dağıtımını kaldırın. Azure Denetim Hatları (Azure DevOps hizmeti) bu işlevselliğin hem yerini alır hem de genişletir.

1. Azure [portalını](https://portal.azure.com/)açın ve *evreleme (mywebapp\<unique_number\>/evreleme)* Web Uygulamasına gidin. Web Uygulaması, portalın arama kutusuna *evreleme* girerek hızlı bir şekilde bulunabilir:

    ![evreleme Web App arama terimi](media/cicd/portal-search-box.png)

1. **Dağıtım Merkezi'ni**tıklatın. Yeni bir panel görüntülenir. Önceki bölümde eklenen yerel Git kaynak denetimi yapılandırmasını kaldırmak için **Bağlantıyı Kesme'yi** tıklatın. **Evet** düğmesini tıklatarak kaldırma işlemini onaylayın.
1. >App Service *unique_number<mywebapp'a* gidin. Bir hatırlatma olarak, portalın arama kutusu, Uygulama Hizmetini hızla bulmak için kullanılabilir.
1. **Dağıtım Merkezi'ni**tıklatın. Yeni bir panel görüntülenir. Önceki bölümde eklenen yerel Git kaynak denetimi yapılandırmasını kaldırmak için **Bağlantıyı Kesme'yi** tıklatın. **Evet** düğmesini tıklatarak kaldırma işlemini onaylayın.

## <a name="create-an-azure-devops-organization"></a>Azure DevOps kuruluşu oluşturma

1. Bir tarayıcı açın ve [Azure DevOps kuruluş oluşturma sayfasına](https://go.microsoft.com/fwlink/?LinkId=307137)gidin.
1. Azure DevOps kuruluşunuza erişmek için URL oluşturmak için **unutulmaz bir ad metin** kutusu seçin'e benzersiz bir ad yazın.
1. Kod GitHub deposunda barındırılan git **radyo** düğmesini seçin.
1. Devam **et** düğmesini tıklatın. Kısa bir bekleyişten sonra *MyFirstProject*adında bir hesap ve ekip projesi oluşturulur.

    ![Azure DevOps organizasyon oluşturma sayfası](media/cicd/vsts-account-creation.png)

1. Azure DevOps kuruluşunun ve projesinin kullanıma hazır olduğunu belirten onay e-postasını açın. **Projenizi Başlat** düğmesini tıklatın:

    ![Proje düğmenizi başlatın](media/cicd/vsts-start-project.png)

1. Bir tarayıcı * \<account_name\>.visualstudio.com*açılır. Projenin DevOps ardışık hattını yapılandırmaya başlamak için *MyFirstProject* bağlantısını tıklatın.

## <a name="configure-the-azure-pipelines-pipeline"></a>Azure Ardışık Hatlar ardışık hattını yapılandırma

Tamamlanması gereken üç farklı adım vardır. Aşağıdaki üç bölümdeki adımların tamamlanması, operasyonel bir DevOps boru hattıyla sonuçlanır.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Azure DevOps'lerin GitHub deposuna erişimini verme

1. Harici **bir depo akordeonundan kodu genişletin veya oluşturun.** Kurulum **Yapı** düğmesini tıklatın:

    ![Kurulum Yap düğmesi](media/cicd/vsts-setup-build.png)

1. **Kaynak** seç bölümünden **GitHub** seçeneğini seçin:

    ![Kaynak seçin - GitHub](media/cicd/vsts-select-source.png)

1. Azure DevOps'ların GitHub deponuza erişebilmeleri için yetkilendirme gerekir. **Bağlantı adı** metin kutusuna GitHub_username *> GitHub bağlantısı<* girin. Örneğin:

    ![GitHub bağlantı adı](media/cicd/vsts-repo-authz.png)

1. GitHub hesabınızda iki faktörlü kimlik doğrulama sısağlanırsa, kişisel erişim belirteci gereklidir. Bu durumda, **GitHub kişisel erişim belirteci bağlantısıyla Yetkilendirme'yi** tıklatın. Yardım için [resmi GitHub kişisel erişim belirteci oluşturma yönergelerine](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) bakın. İzinlerin yalnızca *repo* kapsamı gereklidir. Aksi takdirde, **OAuth düğmesini kullanarak Yetkilendirme'yi** tıklatın.
1. İstendiğinde GitHub hesabınızda oturum açın. Ardından Azure DevOps kuruluşunuza erişim izni vermek için Yetki'yi seçin. Başarılı olursa, yeni bir hizmet bitiş noktası oluşturulur.
1. Depo düğmesinin yanındaki elips **düğmesini** tıklatın. Listeden *<GitHub_username>/basit besleme okuyucu* deposunu seçin. **Seç** düğmesini tıklatın.
1. El ile ve **zamanlanmış yapılar** açılır bırakmak için Varsayılan daldan *ana* dalı seçin. Devam **et** düğmesini tıklatın. Şablon seçim sayfası görüntülenir.

### <a name="create-the-build-definition"></a>Yapı tanımını oluşturma

1. Şablon seçim sayfasından, arama kutusuna *ASP.NET Core* girin:

    ![şablon sayfasında ASP.NET Çekirdek arama](media/cicd/vsts-template-selection.png)

1. Şablon arama sonuçları görüntülenir. **ASP.NET Core** şablonuna basın ve **Uygula** düğmesini tıklatın.
1. Yapı tanımının **Görevler** sekmesi görüntülenir. **Tetikleyiciler** sekmesini tıklatın.
1. Sürekli **tümleştirmeyi etkinleştir** kutusunu işaretleyin. Branch **filtreleri** bölümünün altında, **Tür** açılır bırakma nın *Dahil*olacak şekilde ayarlı olduğunu onaylayın. Şube **belirtimini** *ana*olarak ayarlayın.

    ![Sürekli tümleştirme ayarlarını etkinleştirme](media/cicd/vsts-enable-ci.png)

    Bu ayarlar, herhangi bir değişiklik GitHub deposunun *ana* dalına itildiğinde bir yapının tetiklenebilmelidir. Sürekli tümleştirme, [GitHub'daki Commit değişikliklerinde](#commit-changes-to-github-and-automatically-deploy-to-azure) sınanıyor ve otomatik olarak Azure bölümüne dağıtılıyor.

1. & **sırayı kaydet** düğmesini tıklatın ve **Kaydet** seçeneğini seçin:

    ![Kaydet düğmesi](media/cicd/vsts-save-build.png)

1. Aşağıdaki modal iletişim kutusu görüntülenir:

    ![Yapı tanımını kaydet - modal iletişim kutusu](media/cicd/vsts-save-modal.png)

    Varsayılan klasörü *\\*kullanın ve **Kaydet** düğmesini tıklatın.

### <a name="create-the-release-pipeline"></a>Sürüm ardışık hattını oluşturma

1. Takım projenizin **Sürümler** sekmesini tıklatın. Yeni **ardışık hat lar** düğmesini tıklatın.

    ![Sürümler sekmesi - Yeni tanım düğmesi](media/cicd/vsts-new-release-definition.png)

    Şablon seçim bölmesi görüntülenir.

1. Şablon seçim sayfasından, arama kutusuna *Uygulama Hizmeti* girin:

    ![Yayın boru hattı şablonu arama kutusu](media/cicd/vsts-release-template-search.png)

1. Şablon arama sonuçları görüntülenir. **Slot şablonuyla Azure Uygulama Hizmeti Dağıtımı'nın** üzerine tıklayın ve **Uygula** düğmesini tıklatın. Sürüm ardışık **alanının Ardışık Hatlar sekmesi** görüntülenir.

    ![Sürüm ardışık hatlar ardışık hatlar sekmesi](media/cicd/vsts-release-definition-pipeline.png)

1. **Artefakt** lar **kutusundaekle** düğmesini tıklatın. **Yapı ekle** paneli görüntülenir:

    ![Sürüm ardışık hattı - Yapı paneli ekle](media/cicd/vsts-release-add-artifact.png)

1. **Kaynak türü** bölümünden **Yapı** döşemesini seçin. Bu tür, sürüm ardışık hattının yapı tanımına bağlanmasına olanak tanır.
1. **Proje** açılır tarafından *MyFirstProject'i* seçin.
1. **Kaynak (Yapı tanımı)** açılır tarafından yapı tanım adını, *MyFirstProject-ASP.NET Core-CI'yi*seçin.
1. **Varsayılan sürüm** açılır tarafından *En Son'u* seçin. Bu seçenek, yapı tanımının en son çalıştırımı tarafından üretilen yapıları oluşturur.
1. **Kaynak takma ad** metin kutusundaki metni *Drop*ile değiştirin.
1. **Ekle** düğmesini tıklatın. Değişiklikler görüntülemek için **Eserler** bölümü güncellenir.
1. Sürekli dağıtımları etkinleştirmek için yıldırım simgesini tıklatın:

    ![Yayın boru hattı Artifakı - yıldırım simgesi](media/cicd/vsts-artifacts-lightning-bolt.png)

    Bu seçenek etkinleştirildiğinde, her yeni yapı kullanılabilir olduğunda bir dağıtım oluşur.
1. **Sürekli dağıtım tetikleyici** paneli sağda görünür. Özelliği etkinleştirmek için geçiş düğmesini tıklatın. **Çekme isteği tetikleyicisini**etkinleştirmek gerekli değildir.
1. **Yap dalı filtreleri** bölümünde **açılan** ekle'yi tıklatın. Yapı **Tanımı'nın varsayılan dal** seçeneğini seçin. Bu filtre, sürümün yalnızca GitHub deposunun *ana* dalından bir yapı için tetiklemeye neden olur.
1. **Kaydet** düğmesine tıklayın. Ortaya çıkan **Kaydet** modal iletişim kutusunda **Tamam** düğmesini tıklatın.
1. Çevre **1** kutusunu tıklatın. Sağda bir **Çevre** paneli görünür. **Çevre adı** textbox'taki *Çevre 1* metnini *Üretim*olarak değiştirin.

   ![Yayın ardışık hattı - Çevre adı textbox](media/cicd/vsts-environment-name-textbox.png)

1. **Üretim** **kutusunda1 faz, 2 görev** bağlantısını tıklatın:

    ![Yayın boru hattı - Üretim ortamı link.png](media/cicd/vsts-production-link.png)

    Ortamın **Görevler** sekmesi görüntülenir.
1. Slot görevi **için Azure Uygulama Hizmetini Dağıt'ı** tıklatın. Ayarları sağdaki panelde görünür.
1. Azure abonelik açılır tarafından Uygulama Hizmetiile ilişkili Azure **aboneliğini** seçin. Seçildikten sonra **Yetkilendirme** düğmesini tıklatın.
1. **Uygulama türü** açılır sayfasından *Web Uygulamasını* seçin.
1. **Uygulama hizmeti adı** açılır *unique_number/>mywebapp/<'ı* seçin.
1. **Kaynak grubu** açılır tarafından *AzureTutorial'i* seçin.
1. **Yuva** açılır yerden *evreleme* seçin.
1. **Kaydet** düğmesine tıklayın.
1. Varsayılan sürüm ardışık ad üzerinde gezinmek. Bunu yapmak için kalem simgesini tıklatın. Ad olarak *MyFirstProject-ASP.NET Core-CD* kullanın.

    ![Sürüm ardışık adı](media/cicd/vsts-release-definition-name.png)

1. **Kaydet** düğmesine tıklayın.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>GitHub'daki değişiklikleri işleme ve Azure'a otomatik olarak dağıtma

1. *SimpleFeedReader.sln'i* Visual Studio'da açın.
1. Çözüm Gezgini'nde, *Sayfalar\Index.cshtml'i*açın. 'ye `<h2>Simple Feed Reader - V4</h2>`değiştirin. `<h2>Simple Feed Reader - V3</h2>`
1. Uygulamayı oluşturmak için **Ctrl**+**Shift**+**B** tuşuna basın.
1. Dosyayı GitHub deposuna ada. Visual Studio'nun *Takım Gezgini* sekmesinde **Değişiklikler** sayfasını kullanın veya yerel makinenin komut kabuğunu kullanarak aşağıdakileri uygulayın:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. *Ana* daldaki değişikliği GitHub deponuzun *başlangıç* uzak merkezine itin:

    ```console
    git push origin master
    ```

    Commit, GitHub deposunun *ana* dalında görünür:

    ![GitHub ana şube taahhüt](media/cicd/github-commit.png)

    Yapı tanımının **Tetikleyiciler** sekmesinde sürekli tümleştirme etkinleştirildiğinden, yapı tetiklenir:

    ![sürekli entegrasyon sağlamak](media/cicd/enable-ci.png)

1. Azure DevOps Hizmetleri'nde **Azure Ardışık Hatları** > **Oluşturur** sayfasının **Sıralı** sekmesine gidin. Sıralanan yapı, yapıyı tetikleyen dalı ve işlemeyi gösterir:

    ![sıraya alı](media/cicd/build-queued.png)

1. Yapı başarılı olduktan sonra Azure'a bir dağıtım gerçekleşir. Tarayıcıdaki uygulamaya gidin. "V4" metninin başlıkta göründüğüne dikkat edin:

    ![güncelleştirilmiş uygulama](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Azure Ardışık Hatlar ardışık hattını inceleyin

### <a name="build-definition"></a>Derleme tanımı

*Core-CI*adı ile bir yapı tanımı MyFirstProject-ASP.NET oluşturuldu. Tamamlandıktan sonra, yapı yayımlanacak varlıkları içeren bir *.zip* dosyası üretir. Sürüm ardışık alanı bu varlıkları Azure'a dağır.

Yapı tanımının **Görevler** sekmesi, kullanılan tek tek adımları listeler. Beş yapı görevi vardır.

![tanım görevleri oluşturma](media/cicd/build-definition-tasks.png)

1. **Geri Yükleme** &mdash; `dotnet restore` Uygulamanın NuGet paketlerini geri yüklemek için komutu yürütür. Kullanılan varsayılan paket akışı nuget.org.
1. **Build** &mdash; Uygulamanın `dotnet build --configuration release` kodunu derlemek için komutuyguluyor. Bu `--configuration` seçenek, kodun üretim ortamına dağıtım için uygun olan en iyi leştirilmiş sürümünü oluşturmak için kullanılır. Yapı tanımının **Değişkenler** sekmesinde, örneğin hata ayıklama yapılandırması *gerekiyorsa, Yapı Yapılandırma* değişkenini değiştirin.
1. **Test,** &mdash; uygulamanın `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` birim testlerini çalıştırmak için komutu yürütür. Birim testleri `**/*Tests/*.csproj` glob deseni eşleşen herhangi bir C# projesi içinde yürütülür. Test sonuçları opsiyonun belirttiği yerde *bir .trx* dosyasına `--results-directory` kaydedilir. Herhangi bir test başarısız olursa, yapı başarısız olur ve dağıtılmaz.

    > [!NOTE]
    > Birim testlerinin çalıştığını doğrulamak için *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* siyi testlerden birini kasıtlı olarak kırmak için değiştirin. Örneğin, `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` yöntemde değiştirin. `Returns_News_Stories_Given_Valid_Uri` Değişikliği GitHub'a bağlayıp itin. Yapı tetiklenir ve başarısız olur. Yapı ardışık yapı durumu **başarısız olacak**şekilde değişir. Değişikliği geri çevirin, taahhüt edin ve tekrar itin. Yapı başarılı oldu.

1. **Yayımlama,** &mdash; `dotnet publish --configuration release --output <local_path_on_build_agent>` dağıtılacak yapılarla birlikte *bir .zip* dosyası oluşturmak için komutu yürütür. `--output` *Seçenek,.zip* dosyasının yayımlama konumunu belirtir. Bu konum, önceden [tanımlanmış](/azure/devops/pipelines/build/variables) bir `$(build.artifactstagingdirectory)`değişken in '' adlı bir değişken geçerek belirtilir. Bu değişken, yapı aracısı üzerinde *c:\agent\_work\1\a*gibi yerel bir yola genişler.
1. **Yayımlama Artefakt,** &mdash; **Yayımlama** görevi tarafından üretilen *.zip* dosyasını yayımlar. Görev, *.zip* dosya konumunu önceden tanımlanmış değişken `$(build.artifactstagingdirectory)`olan bir parametre olarak kabul eder. *.zip* dosyası *damla*adlı bir klasör olarak yayımlanır.

Yapı geçmişinin tanımıyla birlikte görüntülenebilmek için yapı tanımının **Özet** bağlantısını tıklatın:

![Yapı tanım geçmişini gösteren ekran görüntüsü](media/cicd/build-definition-summary.png)

Ortaya çıkan sayfada, benzersiz yapı numarasına karşılık gelen bağlantıyı tıklatın:

![Yapı tanımı özet sayfasını gösteren ekran görüntüsü](media/cicd/build-definition-completed.png)

Bu özel yapının bir özeti görüntülenir. **Yapılar** sekmesini tıklatın ve yapı tarafından üretilen *açılan* klasörün listelenmiş olduğunu fark edin:

![Yapı tanımı yapılarını gösteren ekran görüntüsü - drop klasörü](media/cicd/build-definition-artifacts.png)

Yayımlanmış eserleri incelemek için **İndir** ve **Araştır** bağlantılarını kullanın.

### <a name="release-pipeline"></a>Yayın işlem hattı

*Core-CD*adı ile bir sürüm boru hattı oluşturuldu MyFirstProject-ASP.NET:

![Sürüm ardışık genel görünümünü gösteren ekran görüntüsü](media/cicd/release-definition-overview.png)

Sürüm ardışık iki ana bileşeni **Eserler** ve **Ortamlar**vardır. **Eserler** bölümündeki kutuyu tıklattığınızda aşağıdaki panel ortaya çıkarır:

![Sürüm ardışık yapıtlarını gösteren ekran görüntüsü](media/cicd/release-definition-artifacts.png)

**Kaynak (Yapı tanımı)** değeri, bu sürüm ardışık hattının bağlı olduğu yapı tanımını temsil eder. Yapı tanımının başarılı bir şekilde çalıştırılması yla üretilen *.zip* dosyası, Azure'a dağıtım için *Üretim* ortamına sağlanır. Sürüm ardışık işleri görmek için *Üretim* ortamı kutusundaki *1 faz, 2 görev* bağlantısını tıklatın:

![Sürüm ardışık iş aksama görevlerini gösteren ekran görüntüsü](media/cicd/release-definition-tasks.png)

Sürüm ardışık alanı iki görevden oluşur: *Azure Uygulama Hizmetini Yuvaya Dağıtın* ve *Azure Uygulama Hizmetini Yönetin - Yuva Değiştirme*. İlk görevi tıklattığınızda aşağıdaki görev yapılandırması ortaya çıkarır:

![Sürüm ardışık alan dağıtım görevini gösteren ekran görüntüsü](media/cicd/release-definition-task1.png)

Azure aboneliği, hizmet türü, web uygulaması adı, kaynak grubu ve dağıtım yuvası dağıtım görevinde tanımlanır. **Paket veya klasör** textbox ayıklanacak ve *mywebapp\<unique_number\> * web *uygulamasının evreleme* yuvasına dağıtılacak *.zip* dosya yolunu tutar.

Yuva değiştirme görevini tıklattığınızda aşağıdaki görev yapılandırması ortaya çıkarır:

![Sürüm ardışık ardışık yuva değiştirme görevini gösteren ekran görüntüsü](media/cicd/release-definition-task2.png)

Abonelik, kaynak grubu, hizmet türü, web uygulaması adı ve dağıtım yuvası ayrıntıları sağlanır. **Üretimle Takas** onay kutusu işaretlenir. Sonuç olarak, *evreleme* yuvasına dağıtılan bitler üretim ortamına dönüştürülr.

## <a name="additional-reading"></a>Ek okuma

* [Azure Ardışık Hatları ile ilk ardışık sisteminizi oluşturun](/azure/devops/pipelines/get-started-yaml)
* [Yapı ve .NET Core projesi](/azure/devops/pipelines/languages/dotnet-core)
* [Azure Pipelines ile bir web uygulaması dağıtma](/azure/devops/pipelines/targets/webapp)
