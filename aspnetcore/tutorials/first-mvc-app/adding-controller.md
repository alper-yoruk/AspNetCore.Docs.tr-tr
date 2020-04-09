---
title: ASP.NET Core MVC uygulamasına denetleyici ekleme
author: rick-anderson
description: Basit bir ASP.NET Core MVC uygulamasına nasıl denetleyici ekleyeceğinizi öğrenin.
ms.author: riande
ms.date: 08/05/2017
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: fb670902b0dafa7dce2b3372e550095387844936
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666994"
---
# <a name="add-a-controller-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC uygulamasına denetleyici ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Model-View-Controller (MVC) mimari deseni bir uygulamayı üç ana bileşene ayırır: **M**odel, **V**iew ve **C**ontroller. MVC deseni, geleneksel yekpare uygulamalara göre daha test edilebilir ve güncellenebilen uygulamalar oluşturmanıza yardımcı olur. MVC tabanlı uygulamalar şunları içerir:

* **M**odels: Uygulamanın verilerini temsil eden sınıflar. Model sınıfları, söz sahibi veriler için iş kurallarını zorlamak için doğrulama mantığını kullanır. Genellikle, model nesneleri bir veritabanında model durumunu alır ve depolar. Bu öğreticide, `Movie` bir model film verilerini bir veritabanından alır, görünüme sağlar veya güncelleştirir. Güncelleştirilmiş veriler bir veritabanına yazılır.

* **V**iews: Görünümler, uygulamanın kullanıcı arabirimini (UI) görüntüleyen bileşenlerdir. Genellikle, bu ui model verilerini görüntüler.

* **C**ontrollers: Tarayıcı isteklerini işleyen sınıflar. Bir yanıtı döndüren model verilerini ve arama görünümü şablonlarını alırlar. Bir MVC uygulamasında, görünüm yalnızca bilgileri görüntüler; denetleyici, kullanıcı girişini ve etkileşimini işler ve yanıtlar. Örneğin, denetleyici rota verilerini ve sorgu dize değerlerini işler ve bu değerleri modele geçirir. Model veritabanını sorgulamak için bu değerleri kullanabilir. Örneğin, `https://localhost:5001/Home/Privacy` `Home` (denetleyici) ve `Privacy` (ev denetleyicisini çağırmak için eylem yöntemi) yol verileri vardır. `https://localhost:5001/Movies/Edit/5`film denetleyicisini kullanarak FILMI ID=5 ile birlikte yapılan bir istektir. Rota verileri daha sonra öğreticide açıklanır.

MVC deseni, uygulamanın farklı yönlerini (giriş mantığı, iş mantığı ve UI mantığı) ayıran uygulamalar oluşturmanıza yardımcı olurken, bu öğeler arasında gevşek bir bağlantı sağlar. Desen, her bir mantığın uygulamada nerede bulunması gerektiğini belirtir. UI mantığı görünüme aittir. Giriş mantığı denetleyiciye aittir. İş mantığı modele aittir. Bu ayrım, bir uygulama oluştururken karmaşıklığı yönetmenize yardımcı olur, çünkü uygulamanın bir yönü üzerinde başka bir uygulamanın kodunu etkilemeden çalışmanızı sağlar. Örneğin, iş mantığı koduna bağlı olmadan görünüm kodu üzerinde çalışabilirsiniz.

Bu öğretici seride bu kavramları kapsıyor ve bir film uygulaması oluşturmak için bunları nasıl kullanacağınızı gösteriyoruz. MVC projesi *Denetleyiciler* ve *Görünümler*için klasörler içerir.

## <a name="add-a-controller"></a>Denetleyici ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Solution Explorer'da, **Denetleyiciler > > Denetleyici**
  ![Bağlamsal menüsünü sağ tıklatın **Solution Explorer**](adding-controller/_static/add_controller.png)

* İskele **Ekle** iletişim kutusunda **MVC Denetleyicisi '** ni seçin - Boş

  ![MVC denetleyicisi ekleyin ve adlandırın](adding-controller/_static/ac.png)

* Boş **MVC Denetleyicisi Ekle iletişim kutusunda** **HelloWorldController'ı** girin ve **ADD'yi**seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**EXPLORER** simgesini seçin ve ardından yeni **dosyayı > denetleyicileri** (sağ tıklayın) denetleyicileri ve yeni dosyayı *HelloWorldController.cs.*

  ![Bağlamsal menü](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

**Çözüm Gezgini'nde,** Yeni Dosya > ekle > **Denetleyicileri sağ**tıklatın.
![Bağlamsal menü](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

**Core** ve **MVC Denetleyici SınıfıASP.NET**seçin.

Kumandaya **HelloWorldController**adını vereb edin.

![MVC denetleyicisi ekleyin ve adlandırın](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

*Denetleyicilerin/HelloWorldController.cs'lerin* içeriğini aşağıdakilerle değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Denetleyicideki her `public` yöntem HTTP bitiş noktası olarak çağrılabilir. Yukarıdaki örnekte, her iki yöntem bir dize döndürün. Her yöntemden önceki açıklamalara dikkat edin.

HTTP `https://localhost:5001/HelloWorld`bitiş noktası, web uygulamasında, kullanılan protokolü gibi hedeflenebilir bir URL'dir ve kullanılan protokolü birleştirir: `HTTPS`, web `localhost:5001` sunucusunun `HelloWorld`ağ konumu (TCP bağlantı noktası dahil): ve hedef URI .

İlk yorum, bunun [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) temel URL'ye eklenerek `/HelloWorld/` çağrılan bir HTTP GET yöntemi olduğunu belirtir. İkinci yorum, URL'ye eklenerek `/HelloWorld/Welcome/` çağrılan bir HTTP [GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) yöntemini belirtir. Daha sonra öğreticide iskele motoru verileri güncelleştiren yöntemler oluşturmak `HTTP POST` için kullanılır.

Uygulamayı hata ayıklama modunda çalıştırın ve adres çubuğundaki yola "HelloWorld" ekleyin. Yöntem `Index` bir dize döndürür.

![Bu benim varsayılan eylem bir uygulama yanıtı gösteren tarayıcı penceresi](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC, gelen URL'ye bağlı olarak denetleyici sınıflarını (ve bunların içindeki eylem yöntemlerini) çağırır. MVC tarafından kullanılan varsayılan [URL yönlendirme mantığı,](xref:mvc/controllers/routing) hangi kodu niçin çağırılacağa karar vermek için böyle bir biçim kullanır:

`/[Controller]/[ActionName]/[Parameters]`

Yönlendirme *biçimi, Startup.cs* dosyadaki `Configure` yöntemde ayarlanır.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Uygulamaya göz attığınızda ve herhangi bir URL segmenti sağlamadığınızda, bu uygulama varsayılan olarak "Ev" denetleyicisine ve yukarıda vurgulanan şablon satırında belirtilen "Dizin" yöntemine bağlıdır.

İlk URL kesimi çalışacak denetleyici sınıfını belirler. `localhost:{PORT}/HelloWorld` HelloWorld Controller **HelloWorld**sınıfına göre haritalar. URL kesiminin ikinci bölümü, sınıftaki eylem yöntemini belirler. Bu `localhost:{PORT}/HelloWorld/Index` nedenle `Index` `HelloWorldController` sınıfın çalışma yöntemine neden olur. Yalnızca göz atmanız `localhost:{PORT}/HelloWorld` gerektiğine ve `Index` yöntemin varsayılan olarak çağrıldığına dikkat edin. Bunun nedeni, `Index` yöntem adı açıkça belirtilmemişse denetleyicide çağrılacak varsayılan yöntemdir. URL kesiminin üçüncü bölümü `id`( ) rota verileri içindir. Rota verileri daha sonra öğreticide açıklanır.

`https://localhost:{PORT}/HelloWorld/Welcome` adresine gidin. Yöntem `Welcome` çalışır ve dize `This is the Welcome action method...`döndürür. Bu URL için denetleyici `HelloWorld` `Welcome` eylem yöntemidir ve yöntemidir. URL'nin bir `[Parameters]` kısmını henüz kullanmadınız.

![Bu Karşılama eylem yöntemibir uygulama yanıtı gösteren tarayıcı penceresi](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

URL'den denetleyiciye bazı parametre bilgilerini geçirmek için kodu değiştirin. Örneğin, `/HelloWorld/Welcome?name=Rick&numtimes=4`. `Welcome` Yöntemi, aşağıdaki kodda gösterildiği gibi iki parametre içerecek şekilde değiştirin.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Yukarıdaki kod:

* C# isteğe bağlı parametre özelliğini, bu parametre için değer geçmezse parametrenin `numTimes` varsayılan olarak 1'e geçtiğini belirtmek için kullanır. <!-- remove for simplified -->
* Uygulamayı `HtmlEncoder.Default.Encode` kötü amaçlı girişten (yani JavaScript)'e karşı korumak için kullanılır.
* [Enterpolatize Dizeleri](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) kullanır. `$"Hello {name}, NumTimes is: {numTimes}"` <!-- remove for simplified -->

Uygulamayı çalıştırın ve şularına göz atın:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Bağlantı `{PORT}` noktası numaranızla değiştirin.) URL için `name` ve `numtimes` URL'de farklı değerler deneyebilirsiniz. MVC [model bağlama](xref:mvc/models/model-binding) sistemi, adres çubuğundaki sorgu dizesinden adını taşıyan parametreleri yönteminizdeki parametrelere otomatik olarak eşler. Daha fazla bilgi için [Model Bağlama'ya](xref:mvc/models/model-binding) bakın.

![Hello Rick, NumTimes bir uygulama yanıtı\: gösteren tarayıcı penceresi 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Yukarıdaki resimde, URL segmenti`Parameters`( ) `name` kullanılmaz, `numTimes` ve parametreler [sorgu dizesi](https://wikipedia.org/wiki/Query_string)geçirilir. Yukarıdaki `?` URL'deki (soru işareti) bir ayırıcıdır ve sorgu dizesi aşağıdaki gibidir. `&` Karakter alan değeri çiftlerini ayırır.

`Welcome` yöntemini aşağıdaki kod ile değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Uygulamayı çalıştırın ve aşağıdaki URL'yi girin:`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Bu kez üçüncü URL segmenti rota `id`parametresi eşleşti. Yöntem, `Welcome` `MapControllerRoute` yöntemdeki `id` URL şablonuyla eşleşen bir parametre içerir. İzleyen `?` (in) `id?`parametrenin `id` isteğe bağlı olduğunu gösterir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Bu örneklerde denetleyici MVC "VC" kısmını yapıyor - yani, **V**iew ve **C**ontroller çalışma. Denetleyici DOĞRUDAN HTML döndürediyor. Genellikle denetleyicilerin HTML'yi doğrudan döndürmesini istemezsin, çünkü bu kodlama ve bakımı için çok hantal hale gelir. Bunun yerine, HTML yanıtını oluşturmak için genellikle ayrı bir Razor view şablon dosyası kullanırsınız. Bunu bir sonraki derste yapacaksın.

> [!div class="step-by-step"]
> [Önceki](start-mvc.md)
> [Sonraki](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Model-View-Controller (MVC) mimari deseni bir uygulamayı üç ana bileşene ayırır: **M**odel, **V**iew ve **C**ontroller. MVC deseni, geleneksel yekpare uygulamalara göre daha test edilebilir ve güncellenebilen uygulamalar oluşturmanıza yardımcı olur. MVC tabanlı uygulamalar şunları içerir:

* **M**odels: Uygulamanın verilerini temsil eden sınıflar. Model sınıfları, söz sahibi veriler için iş kurallarını zorlamak için doğrulama mantığını kullanır. Genellikle, model nesneleri bir veritabanında model durumunu alır ve depolar. Bu öğreticide, `Movie` bir model film verilerini bir veritabanından alır, görünüme sağlar veya güncelleştirir. Güncelleştirilmiş veriler bir veritabanına yazılır.

* **V**iews: Görünümler, uygulamanın kullanıcı arabirimini (UI) görüntüleyen bileşenlerdir. Genellikle, bu ui model verilerini görüntüler.

* **C**ontrollers: Tarayıcı isteklerini işleyen sınıflar. Bir yanıtı döndüren model verilerini ve arama görünümü şablonlarını alırlar. Bir MVC uygulamasında, görünüm yalnızca bilgileri görüntüler; denetleyici, kullanıcı girişini ve etkileşimini işler ve yanıtlar. Örneğin, denetleyici rota verilerini ve sorgu dize değerlerini işler ve bu değerleri modele geçirir. Model veritabanını sorgulamak için bu değerleri kullanabilir. Örneğin, `https://localhost:5001/Home/About` `Home` (denetleyici) ve `About` (ev denetleyicisini çağırmak için eylem yöntemi) yol verileri vardır. `https://localhost:5001/Movies/Edit/5`film denetleyicisini kullanarak FILMI ID=5 ile birlikte yapılan bir istektir. Rota verileri daha sonra öğreticide açıklanır.

MVC deseni, uygulamanın farklı yönlerini (giriş mantığı, iş mantığı ve UI mantığı) ayıran uygulamalar oluşturmanıza yardımcı olurken, bu öğeler arasında gevşek bir bağlantı sağlar. Desen, her bir mantığın uygulamada nerede bulunması gerektiğini belirtir. UI mantığı görünüme aittir. Giriş mantığı denetleyiciye aittir. İş mantığı modele aittir. Bu ayrım, bir uygulama oluştururken karmaşıklığı yönetmenize yardımcı olur, çünkü uygulamanın bir yönü üzerinde başka bir uygulamanın kodunu etkilemeden çalışmanızı sağlar. Örneğin, iş mantığı koduna bağlı olmadan görünüm kodu üzerinde çalışabilirsiniz.

Bu öğretici seride bu kavramları kapsıyor ve bir film uygulaması oluşturmak için bunları nasıl kullanacağınızı gösteriyoruz. MVC projesi *Denetleyiciler* ve *Görünümler*için klasörler içerir.

## <a name="add-a-controller"></a>Denetleyici ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Solution Explorer'da, **Denetleyiciler > > Denetleyici**
  ![Bağlamsal menüsünü sağ tıklatın **Solution Explorer**](adding-controller/_static/add_controller.png)

* İskele **Ekle** iletişim kutusunda **MVC Denetleyicisi '** ni seçin - Boş

  ![MVC denetleyicisi ekleyin ve adlandırın](adding-controller/_static/ac.png)

* Boş **MVC Denetleyicisi Ekle iletişim kutusunda** **HelloWorldController'ı** girin ve **ADD'yi**seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**EXPLORER** simgesini seçin ve ardından yeni **dosyayı > denetleyicileri** (sağ tıklayın) denetleyicileri ve yeni dosyayı *HelloWorldController.cs.*

  ![Bağlamsal menü](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

**Çözüm Gezgini'nde,** Yeni Dosya > ekle > **Denetleyicileri sağ**tıklatın.
![Bağlamsal menü](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

**Core** ve **MVC Denetleyici SınıfıASP.NET**seçin.

Kumandaya **HelloWorldController**adını vereb edin.

![MVC denetleyicisi ekleyin ve adlandırın](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

*Denetleyicilerin/HelloWorldController.cs'lerin* içeriğini aşağıdakilerle değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Denetleyicideki her `public` yöntem HTTP bitiş noktası olarak çağrılabilir. Yukarıdaki örnekte, her iki yöntem bir dize döndürün. Her yöntemden önceki açıklamalara dikkat edin.

HTTP `https://localhost:5001/HelloWorld`bitiş noktası, web uygulamasında, kullanılan protokolü gibi hedeflenebilir bir URL'dir ve kullanılan protokolü birleştirir: `HTTPS`, web `localhost:5001` sunucusunun `HelloWorld`ağ konumu (TCP bağlantı noktası dahil): ve hedef URI .

İlk yorum, bunun [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) temel URL'ye eklenerek `/HelloWorld/` çağrılan bir HTTP GET yöntemi olduğunu belirtir. İkinci yorum, URL'ye eklenerek `/HelloWorld/Welcome/` çağrılan bir HTTP [GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) yöntemini belirtir. Daha sonra öğreticide iskele motoru verileri güncelleştiren yöntemler oluşturmak `HTTP POST` için kullanılır.

Uygulamayı hata ayıklama modunda çalıştırın ve adres çubuğundaki yola "HelloWorld" ekleyin. Yöntem `Index` bir dize döndürür.

![Bu benim varsayılan eylem bir uygulama yanıtı gösteren tarayıcı penceresi](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC, gelen URL'ye bağlı olarak denetleyici sınıflarını (ve bunların içindeki eylem yöntemlerini) çağırır. MVC tarafından kullanılan varsayılan [URL yönlendirme mantığı,](xref:mvc/controllers/routing) hangi kodu niçin çağırılacağa karar vermek için böyle bir biçim kullanır:

`/[Controller]/[ActionName]/[Parameters]`

Yönlendirme *biçimi, Startup.cs* dosyadaki `Configure` yöntemde ayarlanır.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Uygulamaya göz attığınızda ve herhangi bir URL segmenti sağlamadığınızda, bu uygulama varsayılan olarak "Ev" denetleyicisine ve yukarıda vurgulanan şablon satırında belirtilen "Dizin" yöntemine bağlıdır.

İlk URL kesimi çalışacak denetleyici sınıfını belirler. Yani `localhost:{PORT}/HelloWorld` `HelloWorldController` sınıfa haritalar. URL kesiminin ikinci bölümü, sınıftaki eylem yöntemini belirler. Bu `localhost:{PORT}/HelloWorld/Index` nedenle `Index` `HelloWorldController` sınıfın çalışma yöntemine neden olur. Yalnızca göz atmanız `localhost:{PORT}/HelloWorld` gerektiğine ve `Index` yöntemin varsayılan olarak çağrıldığına dikkat edin. Bunun nedeni, `Index` yöntem adı açıkça belirtilmemişse denetleyicide çağrılacak varsayılan yöntemdir. URL kesiminin üçüncü bölümü `id`( ) rota verileri içindir. Rota verileri daha sonra öğreticide açıklanır.

`https://localhost:{PORT}/HelloWorld/Welcome` adresine gidin. Yöntem `Welcome` çalışır ve dize `This is the Welcome action method...`döndürür. Bu URL için denetleyici `HelloWorld` `Welcome` eylem yöntemidir ve yöntemidir. URL'nin bir `[Parameters]` kısmını henüz kullanmadınız.

![Bu Karşılama eylem yöntemibir uygulama yanıtı gösteren tarayıcı penceresi](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

URL'den denetleyiciye bazı parametre bilgilerini geçirmek için kodu değiştirin. Örneğin, `/HelloWorld/Welcome?name=Rick&numtimes=4`. `Welcome` Yöntemi, aşağıdaki kodda gösterildiği gibi iki parametre içerecek şekilde değiştirin.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Yukarıdaki kod:

* C# isteğe bağlı parametre özelliğini, bu parametre için değer geçmezse parametrenin `numTimes` varsayılan olarak 1'e geçtiğini belirtmek için kullanır. <!-- remove for simplified -->
* Uygulamayı `HtmlEncoder.Default.Encode` kötü amaçlı girişten (yani JavaScript)'e karşı korumak için kullanılır.
* [Enterpolatize Dizeleri](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) kullanır. `$"Hello {name}, NumTimes is: {numTimes}"` <!-- remove for simplified -->

Uygulamayı çalıştırın ve şularına göz atın:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Bağlantı `{PORT}` noktası numaranızla değiştirin.) URL için `name` ve `numtimes` URL'de farklı değerler deneyebilirsiniz. MVC [model bağlama](xref:mvc/models/model-binding) sistemi, adres çubuğundaki sorgu dizesinden adını taşıyan parametreleri yönteminizdeki parametrelere otomatik olarak eşler. Daha fazla bilgi için [Model Bağlama'ya](xref:mvc/models/model-binding) bakın.

![Hello Rick, NumTimes bir uygulama yanıtı\: gösteren tarayıcı penceresi 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Yukarıdaki resimde, URL segmenti`Parameters`( ) `name` kullanılmaz, `numTimes` ve parametreler [sorgu dizesi](https://wikipedia.org/wiki/Query_string)geçirilir. Yukarıdaki `?` URL'deki (soru işareti) bir ayırıcıdır ve sorgu dizesi aşağıdaki gibidir. `&` Karakter alan değeri çiftlerini ayırır.

`Welcome` yöntemini aşağıdaki kod ile değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Uygulamayı çalıştırın ve aşağıdaki URL'yi girin:`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Bu kez üçüncü URL segmenti rota `id`parametresi eşleşti. Yöntem, `Welcome` `MapRoute` yöntemdeki `id` URL şablonuyla eşleşen bir parametre içerir. İzleyen `?` (in) `id?`parametrenin `id` isteğe bağlı olduğunu gösterir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

Bu örneklerde denetleyici MVC "VC" kısmını yapıyor - yani, görünüm ve denetleyici çalışması. Denetleyici DOĞRUDAN HTML döndürediyor. Genellikle denetleyicilerin HTML'yi doğrudan döndürmesini istemezsin, çünkü bu kodlama ve bakımı için çok hantal hale gelir. Bunun yerine, HTML yanıtını oluşturmaya yardımcı olmak için genellikle ayrı bir Razor view şablon dosyası kullanırsınız. Bunu bir sonraki derste yapacaksın.

> [!div class="step-by-step"]
> [Önceki](start-mvc.md)
> [Sonraki](adding-view.md)

::: moniker-end
