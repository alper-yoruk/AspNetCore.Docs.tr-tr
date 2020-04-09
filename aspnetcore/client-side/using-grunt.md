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
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="b23f6-103">ASP.NET Core'da Grunt'u Kullanma</span><span class="sxs-lookup"><span data-stu-id="b23f6-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="b23f6-104">Grunt komut dosyası minification, TypeScript derleme, kod kalitesi "tiftik" araçları, CSS ön işlemciler ve istemci gelişimini desteklemek için yapılması gereken herhangi bir tekrarlayan iş hemen otomatikleştirir bir JavaScript görev koşucusudur.</span><span class="sxs-lookup"><span data-stu-id="b23f6-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="b23f6-105">Grunt, Visual Studio'da tam olarak desteklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="b23f6-106">Bu örnek, istemci oluşturma işlemini sıfırdan nasıl otomatikleştireceğimi göstermek için başlangıç noktası olarak boş bir ASP.NET Core projesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="b23f6-107">Bitmiş örnek hedef dağıtım dizini temizler, JavaScript dosyalarını birleştirir, kod kalitesini denetler, JavaScript dosya içeriğini yoğunlaştırır ve web uygulamanızın köküne dağıtır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="b23f6-108">Aşağıdaki paketleri kullanacağız:</span><span class="sxs-lookup"><span data-stu-id="b23f6-108">We will use the following packages:</span></span>

* <span data-ttu-id="b23f6-109">**homurtu**: Grunt görev koşucu paketi.</span><span class="sxs-lookup"><span data-stu-id="b23f6-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="b23f6-110">**grunt-contrib-clean**: Dosyaları veya dizinleri kaldıran bir eklenti.</span><span class="sxs-lookup"><span data-stu-id="b23f6-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="b23f6-111">**grunt-contrib-jshint**: JavaScript kod kalitesini inceleyen bir eklenti.</span><span class="sxs-lookup"><span data-stu-id="b23f6-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="b23f6-112">**grunt-contrib-concat**: Dosyaları tek bir dosyada birleştiren eklenti.</span><span class="sxs-lookup"><span data-stu-id="b23f6-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="b23f6-113">**grunt-contrib-uglify**: Boyutu azaltmak için JavaScript'i küçülten bir eklenti.</span><span class="sxs-lookup"><span data-stu-id="b23f6-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="b23f6-114">**grunt-contrib-watch**: Dosya etkinliğini izleyen bir eklenti.</span><span class="sxs-lookup"><span data-stu-id="b23f6-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="b23f6-115">Başvurunun hazırlanması</span><span class="sxs-lookup"><span data-stu-id="b23f6-115">Preparing the application</span></span>

<span data-ttu-id="b23f6-116">Başlamak için yeni bir boş web uygulaması ayarlayın ve TypeScript örnek dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="b23f6-117">TypeScript dosyaları varsayılan Visual Studio ayarları kullanılarak JavaScript'e otomatik olarak derlenir ve Grunt'u kullanmak için hammaddemiz olacaktır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="b23f6-118">Visual Studio'da yeni `ASP.NET Web Application`bir .</span><span class="sxs-lookup"><span data-stu-id="b23f6-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="b23f6-119">Yeni **ASP.NET Projesi** iletişim kutusunda, ASP.NET Boş **ASP.NET** şablonu seçin ve Tamam düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="b23f6-120">Çözüm Gezgini'nde proje yapısını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="b23f6-121">Klasör `\src` boş `wwwroot` ve `Dependencies` düğümleri içerir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![boş web çözümü](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="b23f6-123">Proje dizininize `TypeScript` yeni bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="b23f6-124">Herhangi bir dosya eklemeden önce Visual Studio'nun, denetlenen TypeScript dosyaları için 'kaydet'i derleme seçeneğine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="b23f6-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="b23f6-125">**AraçlarA** > Git**Seçenekleri** > **Metin Düzenleyici** > **Saktib** > **Projesi**:</span><span class="sxs-lookup"><span data-stu-id="b23f6-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![TypeScript dosyalarının otomatik derlemesini ayarlama seçenekleri](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="b23f6-127">Dizin etıklayın `TypeScript` ve bağlam menüsünden **Yeni Öğe > ekle'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="b23f6-128">**JavaScript dosya** öğesini seçin ve *Tastes.ts* \*dosyasını adlandırın (.ts uzantısına dikkat edin).</span><span class="sxs-lookup"><span data-stu-id="b23f6-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="b23f6-129">Aşağıdaki TypeScript kodunun satırını dosyaya kopyalayın (kaydettiğinizde JavaScript kaynağında yeni bir *Tastes.js* dosyası görüntülenir).</span><span class="sxs-lookup"><span data-stu-id="b23f6-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="b23f6-130">**TypeScript** dizinine ikinci bir dosya ekleyin `Food.ts`ve adlandırın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="b23f6-131">Aşağıdaki kodu dosyaya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-131">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="b23f6-132">NPM'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b23f6-132">Configuring NPM</span></span>

<span data-ttu-id="b23f6-133">Ardından, Homurtu ve homurtu görevlerini karşıdan yükecek Şekilde NPM'yi yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="b23f6-134">Çözüm Gezgini'nde projeyi sağ tıklatın ve bağlam menüsünden **Yeni Öğe > ekle'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="b23f6-135">**NPM yapılandırma dosya** öğesini seçin, varsayılan adı, *package.json'u*bırakın ve **Ekle** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="b23f6-136">*Package.json* dosyasında, `devDependencies` nesne ayraçlarının içinde "homurtu" girin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="b23f6-137">Intellisense listesinden seçin `grunt` ve Enter tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="b23f6-138">Visual Studio homurtu paket adını alıntı ve bir üst üste ekler.</span><span class="sxs-lookup"><span data-stu-id="b23f6-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="b23f6-139">Üst üste, Intellisense listesinin en üstünden paketin en son kararlı sürümünü `Ctrl-Space` seçin (Intellisense görünmüyorsa basın).</span><span class="sxs-lookup"><span data-stu-id="b23f6-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![homurtu Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="b23f6-141">NPM bağımlılıkları düzenlemek için [anlamsal sürüm](https://semver.org/) kullanır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="b23f6-142">SemVer olarak da bilinen anlamsal sürüm, paketleri numaralandırma \<şeması ana> tanımlar. \<küçük>. \<yama>.</span><span class="sxs-lookup"><span data-stu-id="b23f6-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="b23f6-143">Intellisense sadece birkaç ortak seçenek göstererek anlamsal sürümü kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="b23f6-144">Intellisense listesindeki en üst öğe (yukarıdaki örnekte 0,4,5) paketin en son kararlı sürümü olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="b23f6-145">Caret (^) sembolü en son ana sürüme eşleşir ve tilde (~) en son küçük sürümle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="b23f6-146">SemVer'in sağladığı tam ifade için bir rehber olarak [NPM semver sürüm parer referansına](https://www.npmjs.com/package/semver) bakın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="b23f6-147">\* *Temizlemek*için homurtu-contrib-paketleri yüklemek için daha fazla bağımlılık ekleyin , *jshint*, *concat*, *uglify*, ve aşağıdaki örnekte gösterildiği gibi *izleyin.*</span><span class="sxs-lookup"><span data-stu-id="b23f6-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="b23f6-148">Sürümlerin örnekle eşleşmesine gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="b23f6-148">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="b23f6-149">*Paket.json* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-149">Save the *package.json* file.</span></span>

<span data-ttu-id="b23f6-150">Her `devDependencies` öğe için paketler, her paketin gerektirdiği dosyalarla birlikte karşıdan yüklenecektir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="b23f6-151">**Çözüm Gezgini'nde** **Tüm Dosyaları Göster** düğmesini etkinleştirerek paket dosyalarını *node_modules* dizinde bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b23f6-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![homurtu node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="b23f6-153">Gerekirse, **Paketleri Geri Yükle** menüsü seçeneğini sağ tıklayıp `Dependencies\NPM` seçerek Çözüm **Gezgini'ndeki** bağımlılıkları el ile geri yükleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b23f6-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![paketleri geri yükleme](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="b23f6-155">Grunt'ı Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b23f6-155">Configuring Grunt</span></span>

<span data-ttu-id="b23f6-156">Grunt, Visual Studio'daki olaylara göre el ile çalıştırılabilen veya otomatik olarak çalışacak şekilde yapılandırılabilen görevleri tanımlayan, yükleyen ve kaydeden *Gruntfile.js* adlı bir manifesto kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="b23f6-157">Projeyi sağ tıklatın ve**Yeni Öğe** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="b23f6-158">**JavaScript Dosya** öğesi şablonu seçin, *gruntfile.js*adını değiştirin ve **Ekle** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="b23f6-159">*Gruntfile.js'ye*aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="b23f6-160">İşlev, `initConfig` her paket için seçenekler ayarlar ve modül yüklerinin geri kalanı ve kayıt görevleri.</span><span class="sxs-lookup"><span data-stu-id="b23f6-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="b23f6-161">İşlevin `initConfig` içinde, aşağıdaki `clean` *Gruntfile.js* örneğinde gösterildiği gibi görev için seçenekler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="b23f6-162">Görev `clean` dizin dizeleri bir dizi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b23f6-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="b23f6-163">Bu görev dosyaları *wwwroot/lib'den* kaldırır ve tüm */geçici* dizini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="b23f6-164">Fonksiyonun `initConfig` altında, ''ye `grunt.loadNpmTasks`bir çağrı ekleyin</span><span class="sxs-lookup"><span data-stu-id="b23f6-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="b23f6-165">Bu, görevi Visual Studio'dan çalıştırılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="b23f6-166">*Gruntfile.js*kaydet.</span><span class="sxs-lookup"><span data-stu-id="b23f6-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="b23f6-167">Dosya aşağıdaki ekran görüntüsü gibi bir şey görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-167">The file should look something like the screenshot below.</span></span>

    ![ilk gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="b23f6-169">*Gruntfile.js'yi* sağ tıklatın ve bağlam menüsünden **Task Runner Explorer'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="b23f6-170">**Görev Koşucusu Gezgini Penceresi** açılır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-170">The **Task Runner Explorer** window will open.</span></span>

    ![görev koşucusu explorer menüsü](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="b23f6-172">Görev `clean` **Koşucusu Gezgini Gezgini'nde** **Görevler'in** altında gösteriyi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![görev koşucusu explorer görev listesi](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="b23f6-174">Temiz görev sağ tıklatın ve bağlam menüsünden **Çalıştır'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="b23f6-175">Komut penceresi görevin ilerlemesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="b23f6-175">A command window displays progress of the task.</span></span>

    ![görev koşucusu explorer temiz görev çalıştırın](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="b23f6-177">Henüz temizleyecek dosya veya dizin yok.</span><span class="sxs-lookup"><span data-stu-id="b23f6-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="b23f6-178">İsterseniz, bunları Çözüm Gezgini'nde el ile oluşturabilir ve temiz görevi test olarak çalıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b23f6-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="b23f6-179">İşlevolarak, `initConfig` aşağıdaki kodu `concat` kullanmak için bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="b23f6-180">Özellik `src` dizisi, birleştirilmesi gereken sırayla birleştirecek dosyaları listeler.</span><span class="sxs-lookup"><span data-stu-id="b23f6-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="b23f6-181">Özellik, `dest` üretilen birleştirilmiş dosyaya giden yolu atar.</span><span class="sxs-lookup"><span data-stu-id="b23f6-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="b23f6-182">Yukarıdaki `all` koddaki özellik bir hedefin adıdır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="b23f6-183">Hedefler, birden çok yapı ortamına izin vermek için bazı Homurtu görevlerinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="b23f6-184">Yerleşik hedefleri IntelliSense'i kullanarak görüntüleyebilir veya kendi hedefinizi atayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b23f6-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="b23f6-185">Aşağıdaki `jshint` kodu kullanarak görevi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="b23f6-186">Jshint `code-quality` yardımcı programı *geçici* dizinde bulunan her JavaScript dosyasına karşı çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="b23f6-187">"-W069" seçeneği, JavaScript nokta gösterimi yerine bir özellik atamak için parantez sözdizimini kullandığında jshint `Tastes.Sweet`tarafından üretilen bir hatadır. `Tastes["Sweet"]`</span><span class="sxs-lookup"><span data-stu-id="b23f6-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="b23f6-188">Seçenek, işlemin geri kalanının devam etmesine izin vermek için uyarıyı kapatır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="b23f6-189">Aşağıdaki `uglify` kodu kullanarak görevi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="b23f6-190">Görev, geçici dizinde bulunan *combined.js* dosyasını minifies ve standart adlandırma kuralı \* \<\>dosya adı .min.js\*aşağıdaki wwwroot / lib sonuç dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b23f6-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="b23f6-191">Bu yükleriçin `grunt.loadNpmTasks` `grunt-contrib-clean`arama altında, jshint, concat için aynı çağrı dahil ve aşağıdaki kodu kullanarak uglify.</span><span class="sxs-lookup"><span data-stu-id="b23f6-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="b23f6-192">*Gruntfile.js*kaydet.</span><span class="sxs-lookup"><span data-stu-id="b23f6-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="b23f6-193">Dosya aşağıdaki örnek gibi bir şey görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-193">The file should look something like the example below.</span></span>

    ![tam homurtu dosya örneği](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="b23f6-195">**Görev Koşucusu Gezgini** Görevleri `clean` `concat`listesinin , `jshint` ve `uglify` görevleri içerdiğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="b23f6-196">Her görevi sırayla çalıştırın ve Çözüm Gezgini'ndeki sonuçları **gözlemleyin.**</span><span class="sxs-lookup"><span data-stu-id="b23f6-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="b23f6-197">Her görev hatasız çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-197">Each task should run without errors.</span></span>

    ![görev koşucusu gezgini her görevi çalıştırın](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="b23f6-199">Concat görevi yeni bir *combined.js* dosyası oluşturur ve geçici dizine yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="b23f6-200">Görev `jshint` yalnızca çalışır ve çıktı üretmez.</span><span class="sxs-lookup"><span data-stu-id="b23f6-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="b23f6-201">Görev `uglify` yeni bir *combined.min.js* dosyası oluşturur ve *wwwroot/lib'e*yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="b23f6-202">Tamamlandığında, çözüm aşağıdaki ekran görüntüsü gibi bir şey görünmelidir:</span><span class="sxs-lookup"><span data-stu-id="b23f6-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![tüm görevden sonra çözüm gezgini](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="b23f6-204">Her paket için seçenekler hakkında daha [https://www.npmjs.com/](https://www.npmjs.com/) fazla bilgi için, ana sayfadaki arama kutusundapaket adını ziyaret edin ve arayın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="b23f6-205">Örneğin, tüm parametrelerini açıklayan bir belge bağlantısı almak için homurdanan-contrib-temiz paketi arayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b23f6-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="b23f6-206">Özet</span><span class="sxs-lookup"><span data-stu-id="b23f6-206">All together now</span></span>

<span data-ttu-id="b23f6-207">Belirli bir sırada `registerTask()` bir dizi görevi çalıştırmak için Grunt yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="b23f6-208">Örneğin, yukarıdaki örnek adımları temiz -> concat -> jshint -> uglify olarak yukarıdaki örnek adımları çalıştırmak için, modüle aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="b23f6-209">Kod, initConfig dışında loadNpmTasks() çağrılarıyla aynı düzeye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="b23f6-210">Yeni görev, Diğer Ad Görevleri altında Görev Koşucusu Gezgini Gezgini'nde gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="b23f6-211">Diğer görevlerde olduğu gibi sağ tıklayıp çalıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b23f6-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="b23f6-212">Görev `all` , `clean`, `concat` `jshint` ve `uglify`, sırayla çalışacaktır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![diğer ad homurtu görevleri](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="b23f6-214">Değişiklikleri izleme</span><span class="sxs-lookup"><span data-stu-id="b23f6-214">Watching for changes</span></span>

<span data-ttu-id="b23f6-215">Görev, `watch` dosyaları ve dizinleri izler.</span><span class="sxs-lookup"><span data-stu-id="b23f6-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="b23f6-216">Saat, değişiklikleri algılarsa görevleri otomatik olarak tetikler.</span><span class="sxs-lookup"><span data-stu-id="b23f6-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="b23f6-217">TypeScript dizinindeki \*.js dosyalarındaki değişiklikleri izlemek için aşağıdaki kodu initConfig'e ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="b23f6-218">Bir JavaScript dosyası `watch` değiştirilirse, `all` görevi çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="b23f6-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="b23f6-219">Görevi Koşucu `loadNpmTasks()` Gezgini `watch` Gezgini'nde göstermek için bir arama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="b23f6-220">Görev Koşucusu Gezgini Gezgini'nde izleme görevine sağ tıklayın ve bağlam menüsünden Çalıştır'ı seçin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="b23f6-221">Çalışan izleme görevini gösteren komut penceresi bir "Bekleme..." görüntülenir. İleti.</span><span class="sxs-lookup"><span data-stu-id="b23f6-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="b23f6-222">TypeScript dosyalarından birini açın, bir boşluk ekleyin ve ardından dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="b23f6-223">Bu, izleme görevini tetikler ve diğer görevleri sırayla çalıştırmasını tetikler.</span><span class="sxs-lookup"><span data-stu-id="b23f6-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="b23f6-224">Aşağıdaki ekran görüntüsü örnek bir çalışma gösterir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-224">The screenshot below shows a sample run.</span></span>

![görev çıktısını çalıştırma](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="b23f6-226">Visual Studio etkinliklerine bağlanma</span><span class="sxs-lookup"><span data-stu-id="b23f6-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="b23f6-227">Visual Studio'da her çalıştığınızda görevlerinizi el ile başlatmak istemiyorsanız, görevleri **Oluşturmadan Önce**, **İnşdan Sonra**, **Temizle**ve **Project Open** etkinliklerine bağlayın.</span><span class="sxs-lookup"><span data-stu-id="b23f6-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="b23f6-228">Visual `watch` Studio her açıldığında çalışır böylece bind.</span><span class="sxs-lookup"><span data-stu-id="b23f6-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="b23f6-229">Görev Koşucusu Gezgini Gezgini'nde, izleme görevini sağ tıklatın ve bağlam menüsünden **Bağlayıcılar** > **Projesi Aç'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![bir görevi proje açılışına bağlama](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="b23f6-231">Projeyi boşaltın ve yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="b23f6-231">Unload and reload the project.</span></span> <span data-ttu-id="b23f6-232">Proje yeniden yüklendiğinde, izleme görevi otomatik olarak çalışmaya başlar.</span><span class="sxs-lookup"><span data-stu-id="b23f6-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="b23f6-233">Özet</span><span class="sxs-lookup"><span data-stu-id="b23f6-233">Summary</span></span>

<span data-ttu-id="b23f6-234">Grunt, istemci oluşturma görevlerinin çoğunu otomatikleştirmek için kullanılabilecek güçlü bir görev koşucusudur.</span><span class="sxs-lookup"><span data-stu-id="b23f6-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="b23f6-235">Grunt, paketlerini teslim etmek için NPM'den yararlanır ve Visual Studio ile takım entegrasyonu na sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b23f6-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="b23f6-236">Visual Studio'nun Görev Koşucusu Gezgini Gezgini yapılandırma dosyalarındaki değişiklikleri algılar ve görevleri çalıştırmak, çalışan görevleri görüntülemek ve görevleri Visual Studio etkinliklerine bağlamak için kullanışlı bir arayüz sağlar.</span><span class="sxs-lookup"><span data-stu-id="b23f6-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
