---
title: Visual Studio 'da ASP.NET Core LibMan kullanma
author: scottaddie
description: Visual Studio ile ASP.NET Core projesindeki LibMan 'ı nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-vs
ms.openlocfilehash: 77cfced18edd9db5be21265469b42f32e3de274d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625680"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Visual Studio 'da ASP.NET Core LibMan kullanma

[Scott Ade](https://twitter.com/Scott_Addie) tarafından

Visual Studio, aşağıdakiler dahil olmak üzere ASP.NET Core projelerinde [Libman](xref:client-side/libman/index) için yerleşik desteğe sahiptir:

* Derlemede LibMan geri yükleme işlemlerini yapılandırma ve çalıştırma desteği.
* LibMan geri yükleme ve temizleme işlemlerini tetikleyen menü öğeleri.
* Kitaplıkları bulmak ve dosyaları bir projeye eklemek için arama iletişim kutusu.
* *libman.json* &mdash; Libman bildirim dosyasındalibman.jsiçin destek düzenlemesi.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Ön koşullar

* **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="add-library-files"></a>Kitaplık dosyaları Ekle

Kitaplık dosyaları, ASP.NET Core projesine iki farklı şekilde eklenebilir:

1. [Istemci tarafı kitaplığı Ekle iletişim kutusunu kullanın](#use-the-add-client-side-library-dialog)
1. [LibMan bildirim dosyası girişlerini el ile yapılandır](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Istemci tarafı kitaplığı Ekle iletişim kutusunu kullanın

İstemci tarafı kitaplığı yüklemek için şu adımları izleyin:

* **Çözüm Gezgini**, dosyaların eklenmesi gereken proje klasörüne sağ tıklayın. **Add**  >  **İstemci tarafı kitaplığı**Ekle ' yi seçin. **Istemci tarafı kitaplığı Ekle** iletişim kutusu görünür:

  ![Istemci tarafı kitaplığı Ekle iletişim kutusu](_static/add-library-dialog.png)

* **Sağlayıcı** açılan listesinden kitaplık sağlayıcısını seçin. CDNJS varsayılan sağlayıcıdır.
* **Kitaplık** metin kutusuna getirilecek kitaplık adını yazın. IntelliSense, sağlanan metinle başlayan kitaplıkların bir listesini sağlar.
* IntelliSense listesinden kitaplığı seçin. Kitaplık adının, `@` sembol ve seçili sağlayıcının bilinen en son kararlı sürümü ile sonekli olduğuna dikkat edin.
* Hangi dosyaları içerecek olduğuna karar verin:
  * Tüm kitaplık dosyalarını dahil etmek için **tüm kitaplık dosyalarını dahil et** radyo düğmesini seçin.
  * Kitaplık dosyalarının bir alt kümesini dahil etmek için **belirli dosyaları seç** radyo düğmesini seçin. Radyo düğmesi seçildiğinde, dosya seçici ağacı etkinleştirilir. İndirilecek dosya adlarının solundaki kutuları işaretleyin.
* **Hedef konum** metin kutusunda dosyaları depolamak için proje klasörünü belirtin. Öneri olarak, her kitaplığı ayrı bir klasörde saklayın.

  Önerilen **hedef konum** klasörü, iletişim kutusunun başlatıldığı konuma göre belirlenir:

  * Proje kökünden başlatıldığında:
    * *Wwwroot* varsa, *Wwwroot/lib* kullanılır.
    * *Wwwroot* yoksa *lib* kullanılır.
  * Bir proje klasöründen başlatılmışsa, karşılık gelen klasör adı kullanılır.

  Klasör önerisi, Kitaplık adı ile sonekli olarak düzeltilir. Aşağıdaki tabloda, bir sayfalar projesinde jQuery yüklenirken klasör önerileri gösterilmektedir Razor .
  
  |Başlatma konumu                           |Önerilen klasör      |
  |------------------------------------------|----------------------|
  |Proje kökü ( *Wwwroot* varsa)        |*Wwwroot/LIB/jQuery/* |
  |Proje kökü ( *Wwwroot* yoksa) |*LIB/jQuery/*         |
  |Projedeki *Sayfalar* klasörü                 |*Sayfa/jQuery/*       |

* *libman.jsüzerindeki*yapılandırma başına dosyaları Indirmek için **Yükle** düğmesine tıklayın.
* Yükleme ayrıntıları için **Çıkış** penceresinin **Kitaplık Yöneticisi** akışını gözden geçirin. Örnek:

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

### <a name="manually-configure-libman-manifest-file-entries"></a>LibMan bildirim dosyası girişlerini el ile yapılandır

Visual Studio 'daki tüm LibMan işlemleri, proje kökünün LibMan bildiriminin içeriğine dayalıdır (*libman.js*). Projenin kitaplık dosyalarını yapılandırmak için * üzerindelibman.js* el ile düzenleyebilirsiniz. *libman.json* kaydedildiğinde, Visual Studio tüm kitaplık dosyalarını geri yükler.

Düzenlenmek üzere *libman.js* açmak için aşağıdaki seçenekler mevcuttur:

* **Çözüm Gezgini**dosyasında *libman.js* çift tıklayın.
* **Çözüm Gezgini** ' de projeye sağ tıklayın ve **Istemci tarafı kitaplıklarını yönet**' i seçin. **&#8224;**
* Visual Studio **Proje** menüsünden **Istemci tarafı kitaplıklarını yönet** ' i seçin. **&#8224;**

**&#8224;** Dosya * üzerindelibman.js* proje kökünde zaten yoksa, varsayılan öğe şablonu içeriğiyle oluşturulur.

Visual Studio, renklendirme, biçimlendirme, IntelliSense ve şema doğrulaması gibi zengin JSON düzenlemesi desteği sunar. LibMan bildiriminin JSON şeması konumunda bulunur [https://json.schemastore.org/libman](https://json.schemastore.org/libman) .

Aşağıdaki bildirim dosyası ile LibMan, özelliğinde tanımlanan yapılandırma başına dosya alır `libraries` . Aşağıdaki içinde tanımlanan nesne sabit değerlerinin açıklaması `libraries` :

* [JQuery](https://jquery.com/) sürüm 3.3.1 'nin bir alt kümesi CDNJS sağlayıcısından alınmıştır. Alt küme `files` &mdash; *jquery.min.js*, *jquery.js*ve *jQuery. min. Map*özelliğinde tanımlanmıştır. Dosyalar projenin *Wwwroot/lib/jQuery* klasörüne yerleştirilir.
* [Önyükleme](https://getbootstrap.com/) sürümü 4.1.3 tamamen alınır ve bir *Wwwroot/LIB/Bootstrap* klasörüne yerleştirilir. Nesne sabit değerinin `provider` özelliği `defaultProvider` özellik değerini geçersiz kılar. LibMan, önyükleme dosyalarını unpkg sağlayıcısından alır.
* [Lodash](https://lodash.com/) alt kümesi, kuruluş içindeki bir yöneten gövde tarafından onaylandı. *lodash.js* ve *lodash.min.js* dosyaları, *C: \\ Temp \\ lodash \\ *konumundaki yerel dosya sisteminden alınır. Dosyalar projenin *Wwwroot/LIB/lodash* klasörüne kopyalanır.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan, her bir sağlayıcının her bir kitaplığının yalnızca bir sürümünü destekler. Dosyadaki *libman.js* , belirli bir sağlayıcı için aynı kitaplık adına sahip iki kitaplık içeriyorsa şema doğrulamasında başarısız olur.

## <a name="restore-library-files"></a>Kitaplık dosyalarını geri yükleme

Kitaplık dosyalarını Visual Studio içinden geri yüklemek için, proje kökündeki dosyada geçerli bir *libman.js* olması gerekir. Geri yüklenen dosyalar her kitaplık için belirtilen konumda projeye yerleştirilir.

Kitaplık dosyaları, bir ASP.NET Core projesine iki şekilde geri yüklenebilir:

1. [Derleme sırasında dosyaları geri yükleme](#restore-files-during-build)
1. [Dosyaları el ile geri yükleme](#restore-files-manually)

### <a name="restore-files-during-build"></a>Derleme sırasında dosyaları geri yükleme

LibMan, yapı sürecinin bir parçası olarak tanımlanan kitaplık dosyalarını geri yükleyebilir. Varsayılan olarak, *Derleme sonrası geri yükleme* davranışı devre dışıdır.

Derleme sonrası geri yükleme davranışını etkinleştirmek ve test etmek için:

* **Çözüm Gezgini** ' *libman.js* sağ tıklayın ve bağlam menüsünde **derleme üzerinde istemci tarafı kitaplıklarını geri yükle** ' yi seçin.
* Bir NuGet paketi yüklemek isteyip istemediğiniz sorulduğunda **Evet** düğmesine tıklayın. Projeye [Microsoft. Web. LibraryManager. Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet paketi eklenir:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* LibMan dosyasının geri yükleme işlemini onaylamak için projeyi derleyin. `Microsoft.Web.LibraryManager.Build`Paket, projenin derleme işlemi sırasında LibMan çalıştıran bir MSBuild hedefini çıkarır.
* Bir LibMan etkinlik günlüğü için **Çıkış** penceresinin **derleme** akışını gözden geçirin:

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

Derleme sonrası geri yükleme davranışı etkinleştirildiğinde, bağlam menüsündeki *libman.js* , **derleme üzerinde Istemci tarafı kitaplıklarını geri yükle** seçeneğini görüntüler. Bu seçeneğin belirlenmesi, `Microsoft.Web.LibraryManager.Build` paket başvurusunu proje dosyasından kaldırır. Sonuç olarak, istemci tarafı kitaplıkları her derlemede artık geri yüklenmez.

Derleme sonrası geri yükleme ayarından bağımsız olarak, bağlam menüsündeki *libman.js* dilediğiniz zaman el ile geri yükleyebilirsiniz. Daha fazla bilgi için bkz. [dosyaları el Ile geri yükleme](#restore-files-manually).

### <a name="restore-files-manually"></a>Dosyaları el ile geri yükleme

Kitaplık dosyalarını el ile geri yüklemek için:

* Çözümdeki tüm projeler için:
  * **Çözüm Gezgini**çözüm adına sağ tıklayın.
  * **Istemci tarafı kitaplıklarını geri yükle** seçeneğini belirleyin.
* Belirli bir proje için:
  * **Çözüm Gezgini**dosyasında *libman.js* sağ tıklayın.
  * **Istemci tarafı kitaplıklarını geri yükle** seçeneğini belirleyin.

Geri yükleme işlemi çalışırken:

* Visual Studio durum çubuğundaki Görev Durumu Merkezi (TSC) simgesi canlandırılır ve *geri yükleme işlemi başlatılır*. Simgeye tıkladığınızda bilinen arka plan görevlerinin listelendiği bir araç ipucu açılır.
* İletiler durum çubuğuna ve **Çıkış** penceresinin **Kitaplık Yöneticisi** akışına gönderilir. Örnek:

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

## <a name="delete-library-files"></a>Kitaplık dosyalarını sil

Daha önce Visual Studio 'da geri yüklenen kitaplık dosyalarını silen *Temizleme* işlemini gerçekleştirmek için:

* **Çözüm Gezgini**dosyasında *libman.js* sağ tıklayın.
* **Istemci tarafı kitaplıklarını temizle** seçeneğini belirleyin.

Kitaplık olmayan dosyaların yanlışlıkla kaldırılmasını engellemek için, temizleme işlemi tüm dizinleri silmez. Yalnızca önceki geri yüklemeye dahil edilen dosyaları kaldırır.

Temizleme işlemi çalışırken:

* Visual Studio durum çubuğundaki TSC simgesi animasyon alınacaktır ve *istemci kitaplıkları işlemini*okur. Simgeye tıkladığınızda bilinen arka plan görevlerinin listelendiği bir araç ipucu açılır.
* İletiler durum çubuğuna ve **Çıkış** penceresinin **Kitaplık Yöneticisi** akışına gönderilir. Örnek:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

Temizleme işlemi yalnızca projeden dosyaları siler. Kitaplık dosyaları gelecekteki geri yükleme işlemlerinde daha hızlı bir şekilde almak için önbellekte kalır. Yerel makinenin önbelleğinde depolanan kitaplık dosyalarını yönetmek için [Libman CLI](xref:client-side/libman/libman-cli)kullanın.

## <a name="uninstall-library-files"></a>Kitaplık dosyalarını kaldır

Kitaplık dosyalarını kaldırmak için:

* *Üzerindelibman.js*açın.
* Giriş işaretini karşılık gelen `libraries` nesne değişmez değerinin içine konumlandırın.
* Sol kenar boşluğunda görünen ampul simgesine tıklayın ve **Kaldır \<library_name> @ \<library_version> **' ı seçin:

  ![Kitaplığı Kaldır bağlam menü seçeneği](_static/uninstall-menu-option.png)

Alternatif olarak, LibMan bildirimini (*libman.js*) el ile düzenleyebilir ve kaydedebilirsiniz. [Geri yükleme işlemi](#restore-library-files) dosya kaydedildiğinde çalışır. *libman.jsüzerinde* artık tanımlı olmayan kitaplık dosyaları projeden kaldırılır.

## <a name="update-library-version"></a>Kitaplık sürümünü Güncelleştir

Güncelleştirilmiş bir kitaplık sürümünü denetlemek için:

* *Üzerindelibman.js*açın.
* Giriş işaretini karşılık gelen `libraries` nesne değişmez değerinin içine konumlandırın.
* Sol kenar boşluğunda görünen ampul simgesine tıklayın. **Güncelleştirme denetimi**üzerine gelin.

LibMan, yüklü sürümden daha yeni bir kitaplık sürümünü denetler. Aşağıdaki sonuçlar oluşabilir:

* En son sürüm zaten yüklüyse **hiçbir güncelleştirme bulunamadı** iletisi görüntülenir.
* Henüz yüklenmemişse en son kararlı sürüm görüntülenir.

  ![Güncelleştirmeler bağlam menüsü seçeneğini denetle](_static/update-menu-option.png)

* Yüklü sürümden daha yeni bir yayın öncesi sürüm varsa, yayın öncesi görüntülenir.

Daha eski bir kitaplık sürümüne düşürme için, dosyadaki *libman.js* el ile düzenleyin. Dosya kaydedildiğinde, LibMan [geri yükleme işlemi](#restore-library-files):

* Eski sürümden gereksiz dosyaları kaldırır.
* Yeni sürümden yeni ve güncelleştirilmiş dosyalar ekler.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:client-side/libman/libman-cli>
* [LibMan GitHub deposu](https://github.com/aspnet/LibraryManager)
