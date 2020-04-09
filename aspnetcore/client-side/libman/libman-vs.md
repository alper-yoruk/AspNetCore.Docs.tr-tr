---
title: Visual Studio'da ASP.NET Core ile LibMan'ı Kullanın
author: scottaddie
description: Visual Studio ile ASP.NET Core projesinde LibMan'ı nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658314"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Visual Studio'da ASP.NET Core ile LibMan'ı Kullanın

Yazar: [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio, ASP.NET Core projelerinde [LibMan'a](xref:client-side/libman/index) şunları dahil olmak üzere yerleşik destek vermektedir:

* LibMan geri yükleme işlemlerini yapılandırma ve çalıştırma desteği.
* LibMan geri yükleme ve temizleme işlemlerini tetiklemek için menü öğeleri.
* Kitaplıkları bulmak ve dosyaları projeye eklemek için iletişim kutusunda arama yapın.
* LibMan manifesto dosyası *libman.json*&mdash;için düzenleme desteği.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(nasıl indirilir)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Ön koşullar

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) **ASP.NET ve web geliştirme** iş yükü ile

## <a name="add-library-files"></a>Kitaplık dosyaları ekleme

Kitaplık dosyaları ASP.NET Core projesine iki farklı şekilde eklenebilir:

1. [İstemci Tarafı Kitaplığı Ekle iletişim kutusunu kullanma](#use-the-add-client-side-library-dialog)
1. [LibMan bildirim dosya girişlerini el ile yapılandırma](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>İstemci Tarafı Kitaplığı Ekle iletişim kutusunu kullanma

İstemci tarafı kitaplığı yüklemek için aşağıdaki adımları izleyin:

* **Çözüm Gezgini'nde,** dosyaların eklenmesi gereken proje klasörüne sağ tıklayın. **İstemci Tarafı Kitaplığı** **Ekle'yi** > seçin. **İstemci-Yan Kitaplık Ekle** iletişim kutusu görüntülenir:

  ![İstemci Tarafı Kitaplığı ekle iletişim kutusu](_static/add-library-dialog.png)

* **Sağlayıcı** açılır tarafından kitaplık sağlayıcısını seçin. CDNJS varsayılan sağlayıcıdır.
* **Kitaplık** metin kutusuna getirmek için kitaplık adını yazın. IntelliSense, sağlanan metinden başlayarak kitaplıkların bir listesini sağlar.
* IntelliSense listesinden kitaplığı seçin. Kitaplık adının `@` sembolle ve seçili sağlayıcıtarafından bilinen en son kararlı sürümle sabitlenmiş olduğuna dikkat edin.
* Hangi dosyaların ekleneceğine karar verin:
  * Kitaplığın tüm dosyalarını eklemek için **tüm kitaplık dosyalarını ekle** düğmesini seçin.
  * Kitaplığın dosyalarının bir alt kümesini eklemek için **belirli dosyaları** seç düğmesini seçin. Radyo düğmesi seçildiğinde, dosya seçici ağaç etkinleştirilir. İndirmek için dosya adlarının solundaki kutuları işaretleyin.
* Dosyaları **Hedef Konum** metin kutusunda depolamak için proje klasörünü belirtin. Öneri olarak, her kitaplığı ayrı bir klasörde saklayın.

  Önerilen **Hedef Konum** klasörü, iletişim kutusunun başlatıldığı konuma bağlıdır:

  * Proje kökünden başlatılırsa:
    * *wwwroot/lib* *varsa* kullanılır.
    * *wwwroot* yoksa *wwwroot* lib kullanılır.
  * Bir proje klasöründen başlatılırsa, ilgili klasör adı kullanılır.

  Klasör önerisi kitaplık adı ile sabitlenir. Aşağıdaki tabloda, jilet sayfaları projesine jQuery yüklerken klasör önerileri gösterilebilir.
  
  |Fırlatma konumu                           |Önerilen klasör      |
  |------------------------------------------|----------------------|
  |proje kökü *(wwwroot* varsa)        |*wwwroot/lib/jquery/ adresinden* |
  |proje kökü *(wwwroot* yoksa) |*lib/jquery/*         |
  |Projedeki *sayfalar* klasörü                 |*Sayfalar/jquery/*       |

* Dosyaları indirmek için **yükle** düğmesini tıklatın, *libman.json'daki*yapılandırmaya göre.
* Yükleme ayrıntıları için **Çıktı** penceresinin **Kitaplık Yöneticisi** akışını gözden geçirin. Örneğin:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>LibMan bildirim dosya girişlerini el ile yapılandırma

Visual Studio'daki tüm LibMan işlemleri, proje kökünün LibMan manifestosunun *(libman.json)* içeriğine dayanmaktadır. Proje için kitaplık dosyalarını yapılandırmak için *libman.json'u* el ile edinebilirsiniz. Visual *Studio, libman.json* kaydedildikten sonra tüm kitaplık dosyalarını geri yükler.

Düzenleme için *libman.json'u* açmak için aşağıdaki seçenekler vardır:

* **Çözüm Gezgini'ndeki** *libman.json* dosyasına çift tıklayın.
* **Solution Explorer'da** projeyi sağ tıklatın ve **İstemci Tarafı Kitaplıklarını Yönet'i**seçin. **&#8224;**
* Visual Studio **Project** menüsünden **İstemci Tarafı Kitaplıklarını Yönet'i** seçin. **&#8224;**

**&#8224;** *libman.json* dosyası proje kökünde zaten yoksa, varsayılan öğe şablonu içeriğiyle oluşturulur.

Visual Studio renklendirme, biçimlendirme, IntelliSense ve şema doğrulama gibi zengin JSON düzenleme desteği sunar. LibMan manifestosunun JSON şeması [https://json.schemastore.org/libman](https://json.schemastore.org/libman).

Aşağıdaki bildirim dosyasıyla, LibMan `libraries` özellikte tanımlanan yapılandırmaya göre dosyaları alır. İçinde `libraries` tanımlanan nesne literals bir açıklama:

* [jQuery](https://jquery.com/) sürüm 3.3.1'in bir alt kümesi CDNJS sağlayıcısından alınır. Alt `files` küme özelliği&mdash;*jquery.min.js*, *jquery.js*ve *jquery.min.map*tanımlanır. Dosyalar projenin *wwwroot/lib/jquery* klasörüne yerleştirilir.
* [Bootstrap](https://getbootstrap.com/) sürüm 4.1.3'ün tamamı alınır ve *wwwroot/lib/bootstrap* klasörüne yerleştirilir. Nesneliter'in `provider` özelliği `defaultProvider` özellik değerini geçersiz kılar. LibMan, Bootstrap dosyalarını unpkg sağlayıcısından alır.
* [Lodash'in](https://lodash.com/) bir alt kümesi organizasyon içindeki bir yönetim organı tarafından onaylandı. *Lodash.js* ve *lodash.min.js* dosyaları C yerel dosya sisteminden *alınır:\\temp\\lodash\\*. Dosyalar projenin *wwwroot/lib/lodash* klasörüne kopyalanır.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan, her sağlayıcıdan her kitaplığın yalnızca bir sürümünü destekler. *Libman.json* dosyası, belirli bir sağlayıcı için aynı kitaplık adına sahip iki kitaplık içeriyorsa şema doğrulamayı başarısız olur.

## <a name="restore-library-files"></a>Kitaplık dosyalarını geri yükleme

Visual Studio içinden kitaplık dosyalarını geri yüklemek için proje kökünde geçerli bir *libman.json* dosyası olmalıdır. Geri yüklenen dosyalar projeye her kitaplık için belirtilen konuma yerleştirilir.

Kitaplık dosyaları ASP.NET Core projesinde iki şekilde geri yüklenebilir:

1. [Yapı sırasında dosyaları geri yükleme](#restore-files-during-build)
1. [Dosyaları el ile geri yükleme](#restore-files-manually)

### <a name="restore-files-during-build"></a>Yapı sırasında dosyaları geri yükleme

LibMan, yapı işleminin bir parçası olarak tanımlanan kitaplık dosyalarını geri yükleyebilir. Varsayılan olarak, *yeniden oluşturma* davranışı devre dışı bırakılır.

Yeniden oluşturma davranışını etkinleştirmek ve sınamak için:

* **Solution Explorer'da** *libman.json'a* sağ tıklayın ve bağlam menüsünden **Yapı'da İstemci Tarafı Kitaplıklarını Geri Yükle'yi** seçin.
* NuGet paketi yüklemesi istendiğinde **Evet** düğmesini tıklatın. [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet paketi projeye eklenir:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* LibMan dosya geri yüklemesinin gerçekleştiğini onaylamak için projeyi oluşturun. Paket, `Microsoft.Web.LibraryManager.Build` projenin oluşturma işlemi sırasında LibMan'ı çalıştıran bir MSBuild hedefi enjekte eder.
* LibMan etkinlik günlüğü için **Çıktı** penceresinin **Yapı** akışını gözden geçirin:

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

Yeniden oluşturma davranışı etkinleştirildiğinde, *libman.json* bağlam **menüsü, Yapı'da Istemci Tarafı Kitaplıklarını Devre Dışı Nı Devre-Devre Teşlede** seçeneğini görüntüler. Bu seçeneğin seçilmesi, `Microsoft.Web.LibraryManager.Build` paket başvurularını proje dosyasından kaldırır. Sonuç olarak, istemci tarafındaki kitaplıklar artık her yapıda geri yüklenmez.

Yeniden oluşturma ayarından bağımsız olarak, *libman.json* bağlam menüsünden istediğiniz zaman el ile geri yükleyebilirsiniz. Daha fazla bilgi için [dosyaları el ile geri yükle'ye](#restore-files-manually)bakın.

### <a name="restore-files-manually"></a>Dosyaları el ile geri yükleme

Kitaplık dosyalarını el ile geri yüklemek için:

* Çözümdeki tüm projeler için:
  * **Çözüm Gezgini'nde**çözüm adını sağ tıklatın.
  * **İstemci Tarafı Kitaplıklarını Geri Yükle** seçeneğini belirleyin.
* Belirli bir proje için:
  * **Solution Explorer'daki** *libman.json* dosyasına sağ tıklayın.
  * **İstemci Tarafı Kitaplıklarını Geri Yükle** seçeneğini belirleyin.

Geri yükleme işlemi çalışırken:

* Visual Studio durum çubuğundaki Görev Durum Merkezi (TSC) simgesi animasyonlu olacak ve yeniden başlatılan *geri yükleme işlemi*okunacak. Simgeyi tıklattığınızda, bilinen arka plan görevlerini listeleyen bir araç ipucu açılır.
* İletiler durum çubuğuna ve **Çıktı** penceresinin **Kitaplık Yöneticisi** akışına gönderilir. Örneğin:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>Kitaplık dosyalarını silme

Visual Studio'da daha önce geri yüklenen kitaplık dosyalarını silen *temiz* işlemi gerçekleştirmek için:

* **Solution Explorer'daki** *libman.json* dosyasına sağ tıklayın.
* **İstemci Tarafı Kitaplıklarını Temizle** seçeneğini belirleyin.

Kitaplık dışı dosyaların istemeden kaldırılmasını önlemek için, temiz işlem tüm dizinleri silmez. Yalnızca önceki geri yüklemede bulunan dosyaları kaldırır.

Temiz işlem çalışırken:

* Visual Studio durum çubuğundaki TSC simgesi animasyonlu olacak ve *okunacak İstemci kitaplıkları işlemi başlatıldı.* Simgeyi tıklattığınızda, bilinen arka plan görevlerini listeleyen bir araç ipucu açılır.
* İletiler durum çubuğuna ve **Çıktı** penceresinin **Kitaplık Yöneticisi** akışına gönderilir. Örneğin:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

Temiz işlem yalnızca projedeki dosyaları siler. Kitaplık dosyaları, gelecekteki geri yükleme işlemlerinde daha hızlı alma için önbellekte kalır. Yerel makinenin önbelleğinde depolanan kitaplık dosyalarını yönetmek için [LibMan CLI'yi](xref:client-side/libman/libman-cli)kullanın.

## <a name="uninstall-library-files"></a>Kitaplık dosyalarını kaldırma

Kitaplık dosyalarını kaldırmak için:

* Açık *libman.json*.
* Caret'i ilgili `libraries` nesnenin içine yerleştirin.
* Sol kenar boşluğunda görünen ampul simgesini tıklatın ve library_name **kaldır \<> library_name@\<library_version>: **

  ![Kitaplık bağlamı menüsü seçeneğini kaldırma](_static/uninstall-menu-option.png)

Alternatif olarak, el ile edinebilir ve LibMan bildirimi *(libman.json)* kaydedebilirsiniz. Dosya kaydedildiğinde [geri yükleme işlemi](#restore-library-files) çalışır. *Libman.json'da* artık tanımlanmayan kitaplık dosyaları projeden kaldırılır.

## <a name="update-library-version"></a>Kitaplık sürümünü güncelleştirme

Güncelleştirilmiş kitaplık sürümünü denetlemek için:

* Açık *libman.json*.
* Caret'i ilgili `libraries` nesnenin içine yerleştirin.
* Sol kenar boşluğunda görünen ampul simgesini tıklatın. **Güncellemeler için Kontrol'ün**üzerine yıkın.

LibMan, yüklenen sürümden daha yeni bir kitaplık sürümünü denetler. Aşağıdaki sonuçlar ortaya çıkabilir:

* A **No güncelleştirmeleri bulunan** ileti, en son sürüm zaten yüklenmişse görüntülenir.
* En son kararlı sürüm zaten yüklü değilse görüntülenir.

  ![Güncelleştirmeler bağlam menüsü seçeneğini denetle](_static/update-menu-option.png)

* Yüklenen sürümden daha yeni bir ön sürüm varsa, ön sürüm görüntülenir.

Eski bir kitaplık sürümüne düşürmek için *libman.json* dosyasını el ile edin. Dosya kaydedildiğinde, LibMan [geri yükleme işlemi:](#restore-library-files)

* Önceki sürümden gereksiz dosyaları kaldırır.
* Yeni sürümden yeni ve güncelleştirilmiş dosyalar ekler.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:client-side/libman/libman-cli>
* [LibMan GitHub deposu](https://github.com/aspnet/LibraryManager)
