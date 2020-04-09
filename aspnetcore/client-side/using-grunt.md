---
title: ASP.NET Core'da Grunt'u Kullanma
author: rick-anderson
description: ASP.NET Core'da Grunt'u Kullanma
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657593"
---
# <a name="use-grunt-in-aspnet-core"></a>ASP.NET Core'da Grunt'u Kullanma

Grunt komut dosyası minification, TypeScript derleme, kod kalitesi "tiftik" araçları, CSS ön işlemciler ve istemci gelişimini desteklemek için yapılması gereken herhangi bir tekrarlayan iş hemen otomatikleştirir bir JavaScript görev koşucusudur. Grunt, Visual Studio'da tam olarak desteklenmiştir.

Bu örnek, istemci oluşturma işlemini sıfırdan nasıl otomatikleştireceğimi göstermek için başlangıç noktası olarak boş bir ASP.NET Core projesini kullanır.

Bitmiş örnek hedef dağıtım dizini temizler, JavaScript dosyalarını birleştirir, kod kalitesini denetler, JavaScript dosya içeriğini yoğunlaştırır ve web uygulamanızın köküne dağıtır. Aşağıdaki paketleri kullanacağız:

* **homurtu**: Grunt görev koşucu paketi.

* **grunt-contrib-clean**: Dosyaları veya dizinleri kaldıran bir eklenti.

* **grunt-contrib-jshint**: JavaScript kod kalitesini inceleyen bir eklenti.

* **grunt-contrib-concat**: Dosyaları tek bir dosyada birleştiren eklenti.

* **grunt-contrib-uglify**: Boyutu azaltmak için JavaScript'i küçülten bir eklenti.

* **grunt-contrib-watch**: Dosya etkinliğini izleyen bir eklenti.

## <a name="preparing-the-application"></a>Başvurunun hazırlanması

Başlamak için yeni bir boş web uygulaması ayarlayın ve TypeScript örnek dosyaları ekleyin. TypeScript dosyaları varsayılan Visual Studio ayarları kullanılarak JavaScript'e otomatik olarak derlenir ve Grunt'u kullanmak için hammaddemiz olacaktır.

1. Visual Studio'da yeni `ASP.NET Web Application`bir .

2. Yeni **ASP.NET Projesi** iletişim kutusunda, ASP.NET Boş **ASP.NET** şablonu seçin ve Tamam düğmesini tıklatın.

3. Çözüm Gezgini'nde proje yapısını gözden geçirin. Klasör `\src` boş `wwwroot` ve `Dependencies` düğümleri içerir.

    ![boş web çözümü](using-grunt/_static/grunt-solution-explorer.png)

4. Proje dizininize `TypeScript` yeni bir klasör ekleyin.

5. Herhangi bir dosya eklemeden önce Visual Studio'nun, denetlenen TypeScript dosyaları için 'kaydet'i derleme seçeneğine sahip olduğundan emin olun. **AraçlarA** > Git**Seçenekleri** > **Metin Düzenleyici** > **Saktib** > **Projesi**:

    ![TypeScript dosyalarının otomatik derlemesini ayarlama seçenekleri](using-grunt/_static/typescript-options.png)

6. Dizin etıklayın `TypeScript` ve bağlam menüsünden **Yeni Öğe > ekle'yi** seçin. **JavaScript dosya** öğesini seçin ve *Tastes.ts* \*dosyasını adlandırın (.ts uzantısına dikkat edin). Aşağıdaki TypeScript kodunun satırını dosyaya kopyalayın (kaydettiğinizde JavaScript kaynağında yeni bir *Tastes.js* dosyası görüntülenir).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. **TypeScript** dizinine ikinci bir dosya ekleyin `Food.ts`ve adlandırın. Aşağıdaki kodu dosyaya kopyalayın.

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a>NPM'yi yapılandırma

Ardından, Homurtu ve homurtu görevlerini karşıdan yükecek Şekilde NPM'yi yapılandırın.

1. Çözüm Gezgini'nde projeyi sağ tıklatın ve bağlam menüsünden **Yeni Öğe > ekle'yi** seçin. **NPM yapılandırma dosya** öğesini seçin, varsayılan adı, *package.json'u*bırakın ve **Ekle** düğmesini tıklatın.

2. *Package.json* dosyasında, `devDependencies` nesne ayraçlarının içinde "homurtu" girin. Intellisense listesinden seçin `grunt` ve Enter tuşuna basın. Visual Studio homurtu paket adını alıntı ve bir üst üste ekler. Üst üste, Intellisense listesinin en üstünden paketin en son kararlı sürümünü `Ctrl-Space` seçin (Intellisense görünmüyorsa basın).

    ![homurtu Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM bağımlılıkları düzenlemek için [anlamsal sürüm](https://semver.org/) kullanır. SemVer olarak da bilinen anlamsal sürüm, paketleri numaralandırma \<şeması ana> tanımlar. \<küçük>. \<yama>. Intellisense sadece birkaç ortak seçenek göstererek anlamsal sürümü kolaylaştırır. Intellisense listesindeki en üst öğe (yukarıdaki örnekte 0,4,5) paketin en son kararlı sürümü olarak kabul edilir. Caret (^) sembolü en son ana sürüme eşleşir ve tilde (~) en son küçük sürümle eşleşir. SemVer'in sağladığı tam ifade için bir rehber olarak [NPM semver sürüm parer referansına](https://www.npmjs.com/package/semver) bakın.

3. \* *Temizlemek*için homurtu-contrib-paketleri yüklemek için daha fazla bağımlılık ekleyin , *jshint*, *concat*, *uglify*, ve aşağıdaki örnekte gösterildiği gibi *izleyin.* Sürümlerin örnekle eşleşmesine gerek yoktur.

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. *Paket.json* dosyasını kaydedin.

Her `devDependencies` öğe için paketler, her paketin gerektirdiği dosyalarla birlikte karşıdan yüklenecektir. **Çözüm Gezgini'nde** **Tüm Dosyaları Göster** düğmesini etkinleştirerek paket dosyalarını *node_modules* dizinde bulabilirsiniz.

![homurtu node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Gerekirse, **Paketleri Geri Yükle** menüsü seçeneğini sağ tıklayıp `Dependencies\NPM` seçerek Çözüm **Gezgini'ndeki** bağımlılıkları el ile geri yükleyebilirsiniz.

![paketleri geri yükleme](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Grunt'ı Yapılandırma

Grunt, Visual Studio'daki olaylara göre el ile çalıştırılabilen veya otomatik olarak çalışacak şekilde yapılandırılabilen görevleri tanımlayan, yükleyen ve kaydeden *Gruntfile.js* adlı bir manifesto kullanılarak yapılandırılır.

1. Projeyi sağ tıklatın ve**Yeni Öğe** **Ekle'yi** > seçin. **JavaScript Dosya** öğesi şablonu seçin, *gruntfile.js*adını değiştirin ve **Ekle** düğmesini tıklatın.

1. *Gruntfile.js'ye*aşağıdaki kodu ekleyin. İşlev, `initConfig` her paket için seçenekler ayarlar ve modül yüklerinin geri kalanı ve kayıt görevleri.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. İşlevin `initConfig` içinde, aşağıdaki `clean` *Gruntfile.js* örneğinde gösterildiği gibi görev için seçenekler ekleyin. Görev `clean` dizin dizeleri bir dizi kabul eder. Bu görev dosyaları *wwwroot/lib'den* kaldırır ve tüm */geçici* dizini kaldırır.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Fonksiyonun `initConfig` altında, ''ye `grunt.loadNpmTasks`bir çağrı ekleyin Bu, görevi Visual Studio'dan çalıştırılabilir hale getirir.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. *Gruntfile.js*kaydet. Dosya aşağıdaki ekran görüntüsü gibi bir şey görünmelidir.

    ![ilk gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. *Gruntfile.js'yi* sağ tıklatın ve bağlam menüsünden **Task Runner Explorer'ı** seçin. **Görev Koşucusu Gezgini Penceresi** açılır.

    ![görev koşucusu explorer menüsü](using-grunt/_static/task-runner-explorer-menu.png)

1. Görev `clean` **Koşucusu Gezgini Gezgini'nde** **Görevler'in** altında gösteriyi doğrulayın.

    ![görev koşucusu explorer görev listesi](using-grunt/_static/task-runner-explorer-tasks.png)

1. Temiz görev sağ tıklatın ve bağlam menüsünden **Çalıştır'ı** seçin. Komut penceresi görevin ilerlemesini görüntüler.

    ![görev koşucusu explorer temiz görev çalıştırın](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Henüz temizleyecek dosya veya dizin yok. İsterseniz, bunları Çözüm Gezgini'nde el ile oluşturabilir ve temiz görevi test olarak çalıştırabilirsiniz.

1. İşlevolarak, `initConfig` aşağıdaki kodu `concat` kullanmak için bir giriş ekleyin.

    Özellik `src` dizisi, birleştirilmesi gereken sırayla birleştirecek dosyaları listeler. Özellik, `dest` üretilen birleştirilmiş dosyaya giden yolu atar.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > Yukarıdaki `all` koddaki özellik bir hedefin adıdır. Hedefler, birden çok yapı ortamına izin vermek için bazı Homurtu görevlerinde kullanılır. Yerleşik hedefleri IntelliSense'i kullanarak görüntüleyebilir veya kendi hedefinizi atayabilirsiniz.

1. Aşağıdaki `jshint` kodu kullanarak görevi ekleyin.

    Jshint `code-quality` yardımcı programı *geçici* dizinde bulunan her JavaScript dosyasına karşı çalıştırılır.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > "-W069" seçeneği, JavaScript nokta gösterimi yerine bir özellik atamak için parantez sözdizimini kullandığında jshint `Tastes.Sweet`tarafından üretilen bir hatadır. `Tastes["Sweet"]` Seçenek, işlemin geri kalanının devam etmesine izin vermek için uyarıyı kapatır.

1. Aşağıdaki `uglify` kodu kullanarak görevi ekleyin.

    Görev, geçici dizinde bulunan *combined.js* dosyasını minifies ve standart adlandırma kuralı * \<\>dosya adı .min.js*aşağıdaki wwwroot / lib sonuç dosyası oluşturur.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Bu yükleriçin `grunt.loadNpmTasks` `grunt-contrib-clean`arama altında, jshint, concat için aynı çağrı dahil ve aşağıdaki kodu kullanarak uglify.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. *Gruntfile.js*kaydet. Dosya aşağıdaki örnek gibi bir şey görünmelidir.

    ![tam homurtu dosya örneği](using-grunt/_static/gruntfile-js-complete.png)

1. **Görev Koşucusu Gezgini** Görevleri `clean` `concat`listesinin , `jshint` ve `uglify` görevleri içerdiğine dikkat edin. Her görevi sırayla çalıştırın ve Çözüm Gezgini'ndeki sonuçları **gözlemleyin.** Her görev hatasız çalıştırılmalıdır.

    ![görev koşucusu gezgini her görevi çalıştırın](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Concat görevi yeni bir *combined.js* dosyası oluşturur ve geçici dizine yerleştirir. Görev `jshint` yalnızca çalışır ve çıktı üretmez. Görev `uglify` yeni bir *combined.min.js* dosyası oluşturur ve *wwwroot/lib'e*yerleştirir. Tamamlandığında, çözüm aşağıdaki ekran görüntüsü gibi bir şey görünmelidir:

    ![tüm görevden sonra çözüm gezgini](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Her paket için seçenekler hakkında daha [https://www.npmjs.com/](https://www.npmjs.com/) fazla bilgi için, ana sayfadaki arama kutusundapaket adını ziyaret edin ve arayın. Örneğin, tüm parametrelerini açıklayan bir belge bağlantısı almak için homurdanan-contrib-temiz paketi arayabilirsiniz.

### <a name="all-together-now"></a>Özet

Belirli bir sırada `registerTask()` bir dizi görevi çalıştırmak için Grunt yöntemini kullanın. Örneğin, yukarıdaki örnek adımları temiz -> concat -> jshint -> uglify olarak yukarıdaki örnek adımları çalıştırmak için, modüle aşağıdaki kodu ekleyin. Kod, initConfig dışında loadNpmTasks() çağrılarıyla aynı düzeye eklenmelidir.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

Yeni görev, Diğer Ad Görevleri altında Görev Koşucusu Gezgini Gezgini'nde gösterilmektedir. Diğer görevlerde olduğu gibi sağ tıklayıp çalıştırabilirsiniz. Görev `all` , `clean`, `concat` `jshint` ve `uglify`, sırayla çalışacaktır.

![diğer ad homurtu görevleri](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Değişiklikleri izleme

Görev, `watch` dosyaları ve dizinleri izler. Saat, değişiklikleri algılarsa görevleri otomatik olarak tetikler. TypeScript dizinindeki \*.js dosyalarındaki değişiklikleri izlemek için aşağıdaki kodu initConfig'e ekleyin. Bir JavaScript dosyası `watch` değiştirilirse, `all` görevi çalıştırılır.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Görevi Koşucu `loadNpmTasks()` Gezgini `watch` Gezgini'nde göstermek için bir arama ekleyin.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Görev Koşucusu Gezgini Gezgini'nde izleme görevine sağ tıklayın ve bağlam menüsünden Çalıştır'ı seçin. Çalışan izleme görevini gösteren komut penceresi bir "Bekleme..." görüntülenir. İleti. TypeScript dosyalarından birini açın, bir boşluk ekleyin ve ardından dosyayı kaydedin. Bu, izleme görevini tetikler ve diğer görevleri sırayla çalıştırmasını tetikler. Aşağıdaki ekran görüntüsü örnek bir çalışma gösterir.

![görev çıktısını çalıştırma](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Visual Studio etkinliklerine bağlanma

Visual Studio'da her çalıştığınızda görevlerinizi el ile başlatmak istemiyorsanız, görevleri **Oluşturmadan Önce**, **İnşdan Sonra**, **Temizle**ve **Project Open** etkinliklerine bağlayın.

Visual `watch` Studio her açıldığında çalışır böylece bind. Görev Koşucusu Gezgini Gezgini'nde, izleme görevini sağ tıklatın ve bağlam menüsünden **Bağlayıcılar** > **Projesi Aç'ı** seçin.

![bir görevi proje açılışına bağlama](using-grunt/_static/bindings-project-open.png)

Projeyi boşaltın ve yeniden yükleyin. Proje yeniden yüklendiğinde, izleme görevi otomatik olarak çalışmaya başlar.

## <a name="summary"></a>Özet

Grunt, istemci oluşturma görevlerinin çoğunu otomatikleştirmek için kullanılabilecek güçlü bir görev koşucusudur. Grunt, paketlerini teslim etmek için NPM'den yararlanır ve Visual Studio ile takım entegrasyonu na sahiptir. Visual Studio'nun Görev Koşucusu Gezgini Gezgini yapılandırma dosyalarındaki değişiklikleri algılar ve görevleri çalıştırmak, çalışan görevleri görüntülemek ve görevleri Visual Studio etkinliklerine bağlamak için kullanışlı bir arayüz sağlar.
