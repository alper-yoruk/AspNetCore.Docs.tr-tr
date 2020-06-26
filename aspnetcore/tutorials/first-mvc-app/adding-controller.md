---
title: 2. bölüm, ASP.NET Core MVC uygulamasına denetleyici ekleme
author: rick-anderson
description: ASP.NET Core MVC 'deki öğretici serisinin 2. bölümü.
ms.author: riande
ms.date: 08/05/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: 30272900b2ff19879921f1954a1c9b3eba203ca4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400459"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a>2. bölüm, ASP.NET Core MVC uygulamasına denetleyici ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Model-View-Controller (MVC) mimari modeli, bir uygulamayı üç ana bileşene ayırır: **M**odel, **V**IEW ve **C**ontroller. MVC deseninin daha kararlı ve geleneksel tek parçalı uygulamalardan güncelleştirilmesi daha kolay olan uygulamalar oluşturmanıza yardımcı olur. MVC tabanlı uygulamalar şunları içerir:

* **D**odels: uygulamanın verilerini temsil eden sınıflar. Model sınıfları, bu veriler için iş kurallarını zorlamak üzere doğrulama mantığını kullanır. Genellikle, model nesneleri bir veritabanında model durumunu alır ve saklar. Bu öğreticide, bir `Movie` model bir veritabanından film verileri alır, bunu görünüme sağlar veya güncelleştirir. Güncelleştirilmiş veriler bir veritabanına yazılır.

* **V**ıews: görünümler, uygulamanın kullanıcı ARABIRIMINI (UI) görüntüleyen bileşenlerdir. Genellikle, bu kullanıcı arabirimi model verilerini görüntüler.

* **C**ontrolleyiciler: tarayıcı Isteklerini işleyen sınıflar. Model verileri alır ve yanıt döndüren çağrı görünümü şablonları. MVC uygulamasında, görünüm yalnızca bilgileri görüntüler; denetleyici, Kullanıcı girişini ve etkileşimini işler ve yanıtlar. Örneğin, denetleyici rota verilerini ve sorgu dizesi değerlerini işler ve bu değerleri modele geçirir. Model bu değerleri veritabanını sorgulamak için kullanabilir. Örneğin, `https://localhost:5001/Home/Privacy` `Home` (denetleyici) ve `Privacy` (ana denetleyicide çağrılacak eylem yöntemi) verilerinin yolunu içerir. `https://localhost:5001/Movies/Edit/5`filmi film denetleyicisi kullanarak, ID = 5 olan filmi düzenleme isteği. Rota verileri öğreticide daha sonra açıklanmaktadır.

MVC deseninin uygulamanın farklı yönlerini (Giriş mantığı, iş mantığı ve Kullanıcı arabirimi mantığı) ayıran uygulamalar oluşturmanıza yardımcı olur. bu öğeler arasında gevşek bir bağ sağlanır. Bu model, her bir mantık türünün uygulamada nerede bulunması gerektiğini belirtir. Kullanıcı arabirimi mantığı görünüme aittir. Giriş mantığı denetleyiciye aittir. İş mantığı modele aittir. Bu ayrım, bir uygulama oluşturduğunuzda karmaşıklığın yönetilmesine yardımcı olur, çünkü uygulamanın bir tek tarafında, başka bir kodu etkilemeden bir kez çalışmanıza olanak sağlar. Örneğin, iş mantığı koduna bağlı kalmadan görünüm kodu üzerinde çalışabilirsiniz.

Bu kavramları, bu öğretici serisinde ele alınmaktadır ve bir film uygulaması oluşturmak için nasıl kullanacağınızı gösterir. MVC projesi *denetleyiciler* ve *Görünümler*için klasörler içerir.

## <a name="add-a-controller"></a>Denetleyici ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini**, denetleyiciler öğesine sağ tıklayın **> > denetleyicisi** 
   ![ bağlamsal menü ekleyin](adding-controller/_static/add_controller.png)

* **Yapı Iskelesi Ekle** iletişim kutusunda, **MVC denetleyicisi-boş** seçeneğini belirleyin

  ![MVC denetleyicisi ekleme ve adlandırma](adding-controller/_static/ac.png)

* **Boş MVC denetleyicisi Ekle iletişim kutusunda**, **Merhaba worldcontroller** yazın ve **Ekle**' yi seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Gezgin** simgesini seçin ve ardından **yeni dosya > denetleyiciler** ' i (sağ tıklayın) ve yeni dosyayı *HelloWorldController.cs*olarak adlandırın.

  ![Bağlamsal menü](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

**Çözüm Gezgini**, denetleyiciler ' e sağ tıklayın **> yeni > dosya ekleyin**.
![Bağlamsal menü](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

**ASP.NET Core** ve **MVC denetleyici sınıfı**' nı seçin.

Denetleyiciyi **Merhaba Dünya denetleyicisine**adlandırın.

![MVC denetleyicisi ekleme ve adlandırma](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

*Controllers/HelloWorldController. cs* içeriğini aşağıdakiler ile değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

`public`Bir denetleyicideki her yöntem BIR HTTP uç noktası olarak çağrılabilir. Yukarıdaki örnekte her iki yöntem de bir dize döndürür. Her yöntemden önceki açıklamalara göz önüne alın.

HTTP uç noktası, gibi Web uygulamasındaki bir hedeflenebilir URL 'sidir `https://localhost:5001/HelloWorld` ve kullanılan protokolü, `HTTPS` Web sunucusunun ağ konumunu (TCP bağlantı noktası dahil): `localhost:5001` ve hedef URI 'yi birleştirir `HelloWorld` .

İlk açıklama bu, temel URL 'ye eklenerek çağrılan bir [http get](https://www.w3schools.com/tags/ref_httpmethods.asp) yöntemi olduğunu belirtir `/HelloWorld/` . İkinci açıklama URL 'ye eklenerek çağrılan bir [http get](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) yöntemini belirtir `/HelloWorld/Welcome/` . Öğreticide daha sonra, verileri güncelleştiren Yöntemler oluşturmak için scafkatlama altyapısı kullanılır `HTTP POST` .

Uygulamayı hata ayıklama modunda çalıştırın ve adres çubuğundaki yola "HelloWorld" ekleyin. `Index`Yöntemi bir dize döndürür.

![Bu varsayılan eylemm olan uygulama yanıtını gösteren tarayıcı penceresi](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC, gelen URL 'ye bağlı olarak denetleyici sınıflarını (ve içindeki eylem yöntemlerini) çağırır. MVC tarafından kullanılan varsayılan [URL yönlendirme mantığı](xref:mvc/controllers/routing) , çağrılacak kodu belirlemek için şöyle bir biçim kullanır:

`/[Controller]/[ActionName]/[Parameters]`

Yönlendirme biçimi `Configure` *Startup.cs* dosyasındaki yönteminde ayarlanır.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Uygulamaya gözatıp hiçbir URL kesimini sağlamadığınızda, varsayılan olarak "giriş" denetleyicisi ve yukarıda vurgulanan şablon satırında belirtilen "Dizin" yöntemi varsayılan olarak belirtilir.

İlk URL segmenti, çalıştırılacak denetleyici sınıfını belirler. Bu nedenle `localhost:{PORT}/HelloWorld` **HelloWorld**Controller sınıfıyla eşlenir. URL segmentinin ikinci bölümü, sınıfındaki Action metodunu belirler. Bu nedenle, `localhost:{PORT}/HelloWorld/Index` `Index` sınıfın yönteminin çalışmasına neden olur `HelloWorldController` . Yalnızca göz atmanızı `localhost:{PORT}/HelloWorld` ve `Index` yönteme varsayılan olarak çağrıldığına dikkat edin. Bunun nedeni `Index` , açıkça bir yöntem adı belirtilmemişse bir denetleyicide çağrılacak varsayılan yöntemdir. URL segmentinin () üçüncü bölümü `id` Rota verileri içindir. Rota verileri öğreticide daha sonra açıklanmaktadır.

`https://localhost:{PORT}/HelloWorld/Welcome` adresine gidin. `Welcome`Yöntemi çalışır ve dizeyi döndürür `This is the Welcome action method...` . Bu URL için denetleyici, `HelloWorld` ve `Welcome` eylem yöntemidir. `[Parameters]`URL 'nin bir bölümünü henüz kullanmadınız.

![Uygulamanın uygulama yanıtını gösteren tarayıcı penceresi, hoş geldiniz eylemi yöntemidir](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

URL 'den denetleyiciye bazı parametre bilgilerini geçirmek için kodu değiştirin. Örneğin, `/HelloWorld/Welcome?name=Rick&numtimes=4`. `Welcome`Yöntemi aşağıdaki kodda gösterildiği gibi iki parametre içerecek şekilde değiştirin.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Yukarıdaki kod:

* `numTimes`Bu parametre için hiçbir değer geçirilmemişse parametrenin varsayılan olarak 1 ' e ait olduğunu belirtmek Için C# isteğe bağlı parametresi özelliğini kullanır. <!-- remove for simplified -->
* `HtmlEncoder.Default.Encode`Uygulamayı kötü amaçlı girişten korumak için kullanır (yani JavaScript).
* İçinde [enterpolasyonlu dizeler](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) kullanır `$"Hello {name}, NumTimes is: {numTimes}"` . <!-- remove for simplified -->

Uygulamayı çalıştırın ve şu konuma gidin:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

( `{PORT}` Bağlantı noktası numaranız ile değiştirin.) URL 'de ve için farklı değerler `name` deneyebilirsiniz `numtimes` . MVC [model bağlama](xref:mvc/models/model-binding) sistemi, adlandırılmış parametreleri adres çubuğundaki sorgu dizesinden yöntemdeki parametrelere otomatik olarak eşler. Daha fazla bilgi için bkz. [model bağlama](xref:mvc/models/model-binding) .

![Hello Rick uygulama yanıtını gösteren tarayıcı penceresi, NumTimes 4 ' dir \:](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Yukarıdaki görüntüde, URL segmenti ( `Parameters` ) kullanılmaz, `name` ve `numTimes` parametreleri [sorgu dizesine](https://wikipedia.org/wiki/Query_string)geçirilir. `?`YUKARıDAKI URL 'deki (soru işareti) bir ayırıcı ve sorgu dizesi aşağıdaki gibidir. `&`Karakter, alan-değer çiftlerini ayırır.

`Welcome` yöntemini aşağıdaki kod ile değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Uygulamayı çalıştırın ve aşağıdaki URL 'YI girin:`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Bu kez, üçüncü URL segmenti rota parametresiyle eşleşti `id` . `Welcome`Yöntemi, `id` yöntemindeki URL şablonuyla eşleşen bir parametre içerir `MapControllerRoute` . Sondaki `?` (içinde `id?` ) `id` parametresinin isteğe bağlı olduğunu gösterir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Bu örneklerde, denetleyici MVC 'nin "VC" bölümünü (yani, **V**IEW ve **C**) çalışır. Denetleyici HTML 'i doğrudan döndürüyor. Genellikle, bu, kod ve bakım için çok daha fazla hale geldiği için denetleyicilerin doğrudan HTML döndürmesini istemezsiniz. Bunun yerine Razor , genellıkle HTML yanıtı oluşturmak için ayrı bir görünüm şablonu dosyası kullanırsınız. Bunu bir sonraki öğreticide yapabilirsiniz.

> [!div class="step-by-step"]
> [Önceki](start-mvc.md) 
>  [Sonraki](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Model-View-Controller (MVC) mimari modeli, bir uygulamayı üç ana bileşene ayırır: **M**odel, **V**IEW ve **C**ontroller. MVC deseninin daha kararlı ve geleneksel tek parçalı uygulamalardan güncelleştirilmesi daha kolay olan uygulamalar oluşturmanıza yardımcı olur. MVC tabanlı uygulamalar şunları içerir:

* **D**odels: uygulamanın verilerini temsil eden sınıflar. Model sınıfları, bu veriler için iş kurallarını zorlamak üzere doğrulama mantığını kullanır. Genellikle, model nesneleri bir veritabanında model durumunu alır ve saklar. Bu öğreticide, bir `Movie` model bir veritabanından film verileri alır, bunu görünüme sağlar veya güncelleştirir. Güncelleştirilmiş veriler bir veritabanına yazılır.

* **V**ıews: görünümler, uygulamanın kullanıcı ARABIRIMINI (UI) görüntüleyen bileşenlerdir. Genellikle, bu kullanıcı arabirimi model verilerini görüntüler.

* **C**ontrolleyiciler: tarayıcı Isteklerini işleyen sınıflar. Model verileri alır ve yanıt döndüren çağrı görünümü şablonları. MVC uygulamasında, görünüm yalnızca bilgileri görüntüler; denetleyici, Kullanıcı girişini ve etkileşimini işler ve yanıtlar. Örneğin, denetleyici rota verilerini ve sorgu dizesi değerlerini işler ve bu değerleri modele geçirir. Model bu değerleri veritabanını sorgulamak için kullanabilir. Örneğin, `https://localhost:5001/Home/About` `Home` (denetleyici) ve `About` (ana denetleyicide çağrılacak eylem yöntemi) verilerinin yolunu içerir. `https://localhost:5001/Movies/Edit/5`filmi film denetleyicisi kullanarak, ID = 5 olan filmi düzenleme isteği. Rota verileri öğreticide daha sonra açıklanmaktadır.

MVC deseninin uygulamanın farklı yönlerini (Giriş mantığı, iş mantığı ve Kullanıcı arabirimi mantığı) ayıran uygulamalar oluşturmanıza yardımcı olur. bu öğeler arasında gevşek bir bağ sağlanır. Bu model, her bir mantık türünün uygulamada nerede bulunması gerektiğini belirtir. Kullanıcı arabirimi mantığı görünüme aittir. Giriş mantığı denetleyiciye aittir. İş mantığı modele aittir. Bu ayrım, bir uygulama oluşturduğunuzda karmaşıklığın yönetilmesine yardımcı olur, çünkü uygulamanın bir tek tarafında, başka bir kodu etkilemeden bir kez çalışmanıza olanak sağlar. Örneğin, iş mantığı koduna bağlı kalmadan görünüm kodu üzerinde çalışabilirsiniz.

Bu kavramları, bu öğretici serisinde ele alınmaktadır ve bir film uygulaması oluşturmak için nasıl kullanacağınızı gösterir. MVC projesi *denetleyiciler* ve *Görünümler*için klasörler içerir.

## <a name="add-a-controller"></a>Denetleyici ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini**, denetleyiciler öğesine sağ tıklayın **> > denetleyicisi** 
   ![ bağlamsal menü ekleyin](adding-controller/_static/add_controller.png)

* **Yapı Iskelesi Ekle** iletişim kutusunda, **MVC denetleyicisi-boş** seçeneğini belirleyin

  ![MVC denetleyicisi ekleme ve adlandırma](adding-controller/_static/ac.png)

* **Boş MVC denetleyicisi Ekle iletişim kutusunda**, **Merhaba worldcontroller** yazın ve **Ekle**' yi seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Gezgin** simgesini seçin ve ardından **yeni dosya > denetleyiciler** ' i (sağ tıklayın) ve yeni dosyayı *HelloWorldController.cs*olarak adlandırın.

  ![Bağlamsal menü](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

**Çözüm Gezgini**, denetleyiciler ' e sağ tıklayın **> yeni > dosya ekleyin**.
![Bağlamsal menü](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

**ASP.NET Core** ve **MVC denetleyici sınıfı**' nı seçin.

Denetleyiciyi **Merhaba Dünya denetleyicisine**adlandırın.

![MVC denetleyicisi ekleme ve adlandırma](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

*Controllers/HelloWorldController. cs* içeriğini aşağıdakiler ile değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

`public`Bir denetleyicideki her yöntem BIR HTTP uç noktası olarak çağrılabilir. Yukarıdaki örnekte her iki yöntem de bir dize döndürür. Her yöntemden önceki açıklamalara göz önüne alın.

HTTP uç noktası, gibi Web uygulamasındaki bir hedeflenebilir URL 'sidir `https://localhost:5001/HelloWorld` ve kullanılan protokolü, `HTTPS` Web sunucusunun ağ konumunu (TCP bağlantı noktası dahil): `localhost:5001` ve hedef URI 'yi birleştirir `HelloWorld` .

İlk açıklama bu, temel URL 'ye eklenerek çağrılan bir [http get](https://www.w3schools.com/tags/ref_httpmethods.asp) yöntemi olduğunu belirtir `/HelloWorld/` . İkinci açıklama URL 'ye eklenerek çağrılan bir [http get](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) yöntemini belirtir `/HelloWorld/Welcome/` . Öğreticide daha sonra, verileri güncelleştiren Yöntemler oluşturmak için scafkatlama altyapısı kullanılır `HTTP POST` .

Uygulamayı hata ayıklama modunda çalıştırın ve adres çubuğundaki yola "HelloWorld" ekleyin. `Index`Yöntemi bir dize döndürür.

![Bu varsayılan eylemm olan uygulama yanıtını gösteren tarayıcı penceresi](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC, gelen URL 'ye bağlı olarak denetleyici sınıflarını (ve içindeki eylem yöntemlerini) çağırır. MVC tarafından kullanılan varsayılan [URL yönlendirme mantığı](xref:mvc/controllers/routing) , çağrılacak kodu belirlemek için şöyle bir biçim kullanır:

`/[Controller]/[ActionName]/[Parameters]`

Yönlendirme biçimi `Configure` *Startup.cs* dosyasındaki yönteminde ayarlanır.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Uygulamaya gözatıp hiçbir URL kesimini sağlamadığınızda, varsayılan olarak "giriş" denetleyicisi ve yukarıda vurgulanan şablon satırında belirtilen "Dizin" yöntemi varsayılan olarak belirtilir.

İlk URL segmenti, çalıştırılacak denetleyici sınıfını belirler. Bu nedenle `localhost:{PORT}/HelloWorld` , `HelloWorldController` sınıfıyla eşlenir. URL segmentinin ikinci bölümü, sınıfındaki Action metodunu belirler. Bu nedenle, `localhost:{PORT}/HelloWorld/Index` `Index` sınıfın yönteminin çalışmasına neden olur `HelloWorldController` . Yalnızca göz atmanızı `localhost:{PORT}/HelloWorld` ve `Index` yönteme varsayılan olarak çağrıldığına dikkat edin. Bunun nedeni, `Index` açıkça bir yöntem adı belirtilmemişse bir denetleyicide çağrılacak varsayılan yöntemdir. URL segmentinin () üçüncü bölümü `id` Rota verileri içindir. Rota verileri öğreticide daha sonra açıklanmaktadır.

`https://localhost:{PORT}/HelloWorld/Welcome` adresine gidin. `Welcome`Yöntemi çalışır ve dizeyi döndürür `This is the Welcome action method...` . Bu URL için denetleyici, `HelloWorld` ve `Welcome` eylem yöntemidir. `[Parameters]`URL 'nin bir bölümünü henüz kullanmadınız.

![Uygulamanın uygulama yanıtını gösteren tarayıcı penceresi, hoş geldiniz eylemi yöntemidir](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

URL 'den denetleyiciye bazı parametre bilgilerini geçirmek için kodu değiştirin. Örneğin, `/HelloWorld/Welcome?name=Rick&numtimes=4`. `Welcome`Yöntemi aşağıdaki kodda gösterildiği gibi iki parametre içerecek şekilde değiştirin.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Yukarıdaki kod:

* `numTimes`Bu parametre için hiçbir değer geçirilmemişse parametrenin varsayılan olarak 1 ' e ait olduğunu belirtmek Için C# isteğe bağlı parametresi özelliğini kullanır. <!-- remove for simplified -->
* `HtmlEncoder.Default.Encode`Uygulamayı kötü amaçlı girişten korumak için kullanır (yani JavaScript).
* İçinde [enterpolasyonlu dizeler](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) kullanır `$"Hello {name}, NumTimes is: {numTimes}"` . <!-- remove for simplified -->

Uygulamayı çalıştırın ve şu konuma gidin:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

( `{PORT}` Bağlantı noktası numaranız ile değiştirin.) URL 'de ve için farklı değerler `name` deneyebilirsiniz `numtimes` . MVC [model bağlama](xref:mvc/models/model-binding) sistemi, adlandırılmış parametreleri adres çubuğundaki sorgu dizesinden yöntemdeki parametrelere otomatik olarak eşler. Daha fazla bilgi için bkz. [model bağlama](xref:mvc/models/model-binding) .

![Hello Rick uygulama yanıtını gösteren tarayıcı penceresi, NumTimes 4 ' dir \:](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Yukarıdaki görüntüde, URL segmenti ( `Parameters` ) kullanılmaz, `name` ve `numTimes` parametreleri [sorgu dizesine](https://wikipedia.org/wiki/Query_string)geçirilir. `?`YUKARıDAKI URL 'deki (soru işareti) bir ayırıcı ve sorgu dizesi aşağıdaki gibidir. `&`Karakter, alan-değer çiftlerini ayırır.

`Welcome` yöntemini aşağıdaki kod ile değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Uygulamayı çalıştırın ve aşağıdaki URL 'YI girin:`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Bu kez, üçüncü URL segmenti rota parametresiyle eşleşti `id` . `Welcome`Yöntemi, `id` yöntemindeki URL şablonuyla eşleşen bir parametre içerir `MapRoute` . Sondaki `?` (içinde `id?` ) `id` parametresinin isteğe bağlı olduğunu gösterir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

Bu örneklerde, denetleyici MVC 'nin "VC" bölümünü (yani, görünüm ve denetleyici çalışır) yapıyor. Denetleyici HTML 'i doğrudan döndürüyor. Genellikle, bu, kod ve bakım için çok daha fazla hale geldiği için denetleyicilerin doğrudan HTML döndürmesini istemezsiniz. Bunun yerine Razor , genellıkle HTML yanıtı oluşturmaya yardımcı olması için ayrı bir görünüm şablonu dosyası kullanırsınız. Bunu bir sonraki öğreticide yapabilirsiniz.

> [!div class="step-by-step"]
> [Önceki](start-mvc.md) 
>  [Sonraki](adding-view.md)

::: moniker-end
