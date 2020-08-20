---
title: ASP.NET Core Grönyı kullanma
author: rick-anderson
description: ASP.NET Core Grönyı kullanma
ms.author: riande
ms.date: 12/05/2019
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
uid: client-side/using-grunt
ms.openlocfilehash: e8e4459f7fe496135d6cfd7f4ff52511a5e1c064
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628033"
---
# <a name="use-grunt-in-aspnet-core"></a>ASP.NET Core Grönyı kullanma

Grrete, komut dosyası küçültmeye, TypeScript derlemesini, kod kalitesi "Lint" araçlarını, CSS ön işlemcilerini ve yalnızca istemci geliştirmeyi desteklemek için gereken tüm yinelenen chore 'yi otomatikleştiren bir JavaScript görev Çalıştırıcısı. Gryeniden bağlama Visual Studio 'da tam olarak desteklenir.

Bu örnek, başlangıç noktası olarak boş bir ASP.NET Core projesi kullanarak, istemci derleme işleminin sıfırdan nasıl otomatikleştirileceğini gösterir.

Tamamlanmış örnek, hedef dağıtım dizinini temizler, JavaScript dosyalarını birleştirir, kod kalitesini denetler, JavaScript dosya içeriğini daraltabilir ve Web uygulamanızın köküne dağıtır. Aşağıdaki paketleri kullanacağız:

* **gryeniden bağlama**: gryeniden görev Çalıştırıcısı paketi.

* **gryeniden bağlama-contrib-Clean**: dosya veya dizinleri kaldıran bir eklenti.

* **grkıt-contrib-jshınt**: JavaScript kod kalitesini inceleyen bir eklenti.

* **gryeniden bağlama-contrib-Concat**: dosyaları tek bir dosyaya birleştiren bir eklenti.

* **gryeniden bağlama-contrib-utimy**: boyutu azaltmak Için JavaScript 'i mini görüntüleyen bir eklenti.

* **gryeniden bağlama-contrib-Watch**: dosya etkinliğini izleyen bir eklenti.

## <a name="preparing-the-application"></a>Uygulama hazırlanıyor

Başlamak için yeni bir boş Web uygulaması ayarlayın ve TypeScript örnek dosyaları ekleyin. TypeScript dosyaları varsayılan Visual Studio ayarları kullanılarak JavaScript 'e otomatik olarak derlenir ve Grönyı kullanarak işlemek için ham malzemelerimiz olacaktır.

1. Visual Studio 'da yeni bir oluştur `ASP.NET Web Application` .

2. **Yeni ASP.NET projesi** Iletişim kutusunda **boş** şablon ASP.NET Core seçin ve Tamam düğmesine tıklayın.

3. Çözüm Gezgini, proje yapısını gözden geçirin. `\src`Klasör boş `wwwroot` ve düğümleri içerir `Dependencies` .

    ![boş Web çözümü](using-grunt/_static/grunt-solution-explorer.png)

4. Proje dizininize adlı yeni bir klasör ekleyin `TypeScript` .

5. Herhangi bir dosya eklemeden önce, Visual Studio 'Nun TypeScript dosyaları için ' kaydetme sırasında derle ' seçeneğinin işaretli olduğundan emin olun. **Araçlar**  >  **Seçenekler**  >  **metin Düzenleyicisi**  >  **TypeScript**  >  **projesinde**git:

    ![TypeScript dosyalarının otomatik derlemesini ayarlama seçenekleri](using-grunt/_static/typescript-options.png)

6. Dizine sağ tıklayın `TypeScript` ve bağlam menüsünden **> yeni öğe Ekle** ' yi seçin. **JavaScript dosya** öğesini seçin ve dosyayı *tastes. TS* olarak adlandırın ( \* . TS uzantısını aklınızda). Aşağıdaki TypeScript kodu satırını dosyasına kopyalayın (kaydettiğinizde, JavaScript kaynağıyla yeni bir *Tastes.js* dosyası görünür).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. **TypeScript** dizinine ikinci bir dosya ekleyin ve bunu adlandırın `Food.ts` . Aşağıdaki kodu dosyasına kopyalayın.

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

## <a name="configuring-npm"></a>NPM 'yi yapılandırma

Daha sonra, NPM 'yi, grer ve grsıt-görevler 'i indirmek için yapılandırın

1. Çözüm Gezgini, projeye sağ tıklayın ve bağlam menüsünden **> yeni öğe Ekle** ' yi seçin. **NPM yapılandırma dosyası** öğesini seçin, varsayılan adı bırakın, *package.js*ve **Ekle** düğmesine tıklayın.

2. Dosyadaki *package.js* , `devDependencies` nesne ayraçları içinde "grönbağlama" yazın. `grunt`IntelliSense listesinden seçin ve ENTER tuşuna basın. Visual Studio gryeniden paket adını teklif eder ve iki nokta üst üste ekler. İki nokta üst üste sağ tarafta, IntelliSense listesinin en üstünden paketin en son kararlı sürümünü seçin ( `Ctrl-Space` IntelliSense görünmüyorsa tuşuna basın).

    ![gryeniden bağlama IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM, bağımlılıkları düzenlemek için [anlamsal sürüm oluşturmayı](https://semver.org/) kullanır. SemVer olarak da bilinen anlamsal sürüm, numaralandırma düzenine sahip paketleri tanımlar \<major> . \<minor> . \<patch> . IntelliSense yalnızca birkaç ortak seçeneği göstererek anlamsal sürüm oluşturmayı basitleştirir. IntelliSense listesindeki üst öğe (Yukarıdaki örnekte 0.4.5), paketin en son kararlı sürümü olarak değerlendirilir. Şapka (^) simgesi en son ana sürümle eşleşir ve tilde (~) en son ikincil sürümle eşleşir. SemVer 'in sağladığı tam ifade çekimi için bir kılavuz olarak [NPM semver sürüm ayrıştırıcısı başvurusuna](https://www.npmjs.com/package/semver) bakın.

3. \*Aşağıdaki örnekte gösterildiği gibi *Clean*, *jshınt*, *Concat*, *uıshowmy*ve *Watch* için Load grkıt-contrib-Packages için daha fazla bağımlılık ekleyin. Sürümlerin örnekle eşleşmesi gerekmez.

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

4. *package.js* dosyaya kaydedin.

Her `devDependencies` bir öğe için paketler, her paketin gerektirdiği tüm dosyalarla birlikte indirilir. Paket dosyalarını, **Çözüm Gezgini** **tüm dosyaları göster** düğmesini etkinleştirerek *node_modules* dizininde bulabilirsiniz.

![gryeniden bağlama node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Gerekirse, sağ tıklayıp **Solution Explorer** `Dependencies\NPM` **paketleri geri yükle** menü seçeneğini belirleyerek Çözüm Gezgini bağımlılıkları el ile geri yükleyebilirsiniz.

![paketleri geri yükle](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Grönbağlama yapılandırma

Gryeniden Çalıştır, el ile çalıştırılabilecek veya Visual Studio 'daki olaylara göre otomatik olarak çalışacak şekilde yapılandırılmış olan *Gruntfile.js* adlı bir bildirim kullanılarak yapılandırılır.

1. Projeye sağ tıklayın ve **Add**  >  **Yeni öğe**Ekle ' yi seçin. **JavaScript dosya** öğesi şablonunu seçin, adı *Gruntfile.js*olarak değiştirin ve **Ekle** düğmesine tıklayın.

1. *Gruntfile.js*için aşağıdaki kodu ekleyin. `initConfig`İşlevi her bir paket için seçenekleri ayarlar ve modülün geri kalanı görevleri yükler ve kaydeder.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. İşlevin içinde `initConfig` , `clean` Aşağıdaki örnekte gösterildiği gibi görev için seçenekler ekleyin *Gruntfile.js* . `clean`Görev, Dizin dizeleri dizisini kabul eder. Bu görev, dosyaları *Wwwroot/lib* 'den kaldırır ve tüm */Temp klasörüne yazılır* dizinini kaldırır.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. İşlevin altına `initConfig` bir çağrı ekleyin `grunt.loadNpmTasks` . Bu işlem, görevin Visual Studio 'dan bir şekilde bir şekilde bir şekilde bir şekilde

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. *Gruntfile.js*kaydedin. Dosya aşağıdaki ekran görüntüsüne benzer şekilde görünmelidir.

    ![ilk gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. *Gruntfile.js* sağ tıklayın ve bağlam menüsünden **görev Çalıştırıcısı Gezgini** ' ni seçin. **Görev çalıştırıcı Gezgini** penceresi açılır.

    ![görev Çalıştırıcısı Gezgin menüsü](using-grunt/_static/task-runner-explorer-menu.png)

1. `clean` **Görev Çalıştırıcısı Gezgininde** **Görevler** altında göründüğünü doğrulayın.

    ![görev çalıştırıcı Gezgini görev listesi](using-grunt/_static/task-runner-explorer-tasks.png)

1. Temizleme görevine sağ tıklayın ve bağlam menüsünden **Çalıştır** ' ı seçin. Bir komut penceresi, görevin ilerlemesini görüntüler.

    ![görev çalıştırıcı Gezgini temiz görevi çalıştır](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Henüz temizleyene dosya veya dizin yok. İsterseniz, bunları Çözüm Gezgini el ile oluşturabilir ve sonra temiz görevi bir test olarak çalıştırabilirsiniz.

1. `initConfig`İşlevinde, aşağıdaki kodu kullanmak için bir giriş ekleyin `concat` .

    `src`Özellik dizisi birleştirilecek dosyaları, birleştirilmeleri gereken sırayla listeler. `dest`Özelliği, üretilen Birleşik dosyanın yolunu atar.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > `all`Yukarıdaki koddaki özelliği bir hedefin adıdır. Hedefler, bazı Grsi görevlerinde birden çok derleme ortamına izin vermek için kullanılır. IntelliSense kullanarak yerleşik hedefleri görüntüleyebilir veya kendi kendinize atayabilirsiniz.

1. `jshint`Aşağıdaki kodu kullanarak görevi ekleyin.

    Jshınt `code-quality` yardımcı programı, *Temp* dizininde bulunan her JavaScript dosyasında çalışır.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > "-W069" seçeneği, JavaScript, nokta gösterimi yerine bir özellik atamak için köşeli ayraç sözdizimi kullandığında jshınt tarafından oluşturulan bir hatadır, yani yerine. `Tastes["Sweet"]` `Tastes.Sweet` Seçeneği, işlemin geri kalanının devam etmesine izin vermek için uyarıyı kapatır.

1. `uglify`Aşağıdaki kodu kullanarak görevi ekleyin.

    Görev, Temp dizininde bulunan *combined.js* dosyasını mini olarak büyütür ve standart adlandırma kuralı * \<file name\>.min.js*takip eden Wwwroot/lib içinde sonuç dosyasını oluşturur.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. `grunt.loadNpmTasks`Bu yüklere yapılan çağrının altında `grunt-contrib-clean` , aşağıdaki kodu kullanarak jshınt, Concat ve UART comp için aynı çağrıyı dahil edin.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. *Gruntfile.js*kaydedin. Dosya aşağıdaki örnekteki gibi görünmelidir.

    ![Tüm gryeniden dosya örneğini doldurun](using-grunt/_static/gruntfile-js-complete.png)

1. **Görev çalıştırıcı Gezgini** görev listesinin `clean` , `concat` `jshint` ve görevlerini içerdiğine dikkat edin `uglify` . Her görevi sırayla çalıştırın ve **Çözüm Gezgini**sonuçları gözlemleyin. Her görev hatasız çalışmalıdır.

    ![görev çalıştırıcı Gezgini her görevi çalıştır](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Concat görevi yeni bir *combined.js* dosyası oluşturur ve bunu Temp dizinine koyar. `jshint`Görev yalnızca çalışır ve çıkış üretmez. `uglify`Görev yeni bir *combined.min.js* dosyası oluşturur ve bunu *Wwwroot/lib*'e koyar. Tamamlandığında, çözüm aşağıdaki ekran görüntüsüne benzer şekilde görünmelidir:

    ![tüm görevlerden sonra Çözüm Gezgini](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Her bir paket için seçenekler hakkında daha fazla bilgi için, [https://www.npmjs.com/](https://www.npmjs.com/) ana sayfadaki arama kutusunda paket adı ' nı ziyaret edin ve arama yapın. Örneğin, tüm parametrelerini açıklayan bir belge bağlantısı almak için grcönt-contrib-Clean paketini arayabilirsiniz.

### <a name="all-together-now"></a>Şimdi hepsi bir arada

`registerTask()`Belirli bir dizideki bir dizi görevi çalıştırmak Için Gryeniden bağlama yöntemini kullanın. Örneğin, yukarıdaki örnek adımları sırayla çalıştırmak için > Concat-> jshınt-> uıshowmy ' a gidin ve aşağıdaki kodu modüle ekleyin. Kod, InitConfig dışında loadNpmTasks () çağrılarıyla aynı düzeye eklenmelidir.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

Yeni görev, görev Çalıştırıcısı Gezgini 'nde diğer ad görevleri altında görüntülenir. Bunu, diğer görevleri yaptığınız gibi sağ tıklayıp çalıştırabilirsiniz. `all`Görev, `clean` `concat` `jshint` ve sırasıyla çalıştırılır `uglify` .

![diğer ad grer görevleri](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Değişiklik izleme

Bir `watch` görev, dosyalara ve dizinlere göz önünde bulundurur. İzleme, değişiklikleri algılarsa görevleri otomatik olarak tetikler. TypeScript dizinindeki. js dosyalarında yapılan değişiklikleri izlemek için aşağıdaki kodu InitConfig dosyasına ekleyin \* . Bir JavaScript dosyası değiştirilirse, `watch` `all` görevi çalıştırır.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Görev `loadNpmTasks()` Çalıştırıcısı Gezgininde görevi göstermek için öğesine bir çağrı ekleyin `watch` .

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Görev çalıştırıcı Gezgini ' nde gözcü görevine sağ tıklayın ve bağlam menüsünden Çalıştır ' ı seçin. Çalışan izleme görevini gösteren komut penceresinde "bekleniyor..." görüntülenir İleti. TypeScript dosyalarından birini açın, bir boşluk ekleyin ve dosyayı kaydedin. Bu işlem, izleme görevini tetikler ve diğer görevleri sırayla çalışacak şekilde tetikler. Aşağıdaki ekran görüntüsünde örnek bir çalıştırma gösterilmektedir.

![çalışan görevler çıkışı](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Visual Studio olaylarına bağlama

Visual Studio 'da her çalıştığınızda görevlerinizi el ile başlatmak istemediğiniz müddetçe, derleme, **Temizleme**ve **proje açık** olayları **sonrasında** **derlemeden önce**görevleri bağlayın.

`watch`Visual Studio her açıldığında çalışacak şekilde bağlayın. Görev çalıştırıcı Gezgini ' nde, izle görevine sağ tıklayın ve bağlam menüsünden **bağlamalar**  >  **projesi aç** ' ı seçin.

![projeyi açmak için bir görev bağlama](using-grunt/_static/bindings-project-open.png)

Projeyi kaldırın ve yeniden yükleyin. Proje yeniden yüklendiğinde, izleme görevi otomatik olarak çalışmaya başlar.

## <a name="summary"></a>Özet

Gryeniden oluşturma, çoğu istemci derleme görevini otomatikleştirmek için kullanılabilen güçlü bir görev Çalıştırıcısı. Grsıt, paketlerini teslim etmek için NPM 'den yararlanır ve Visual Studio ile tümleştirme özellikleri sağlar. Visual Studio 'nun görev Çalıştırıcısı Gezgini yapılandırma dosyalarındaki değişiklikleri algılar ve görevleri çalıştırmak, çalışan görevleri görüntülemek ve görevleri Visual Studio olaylarına bağlamak için uygun bir arabirim sağlar.
