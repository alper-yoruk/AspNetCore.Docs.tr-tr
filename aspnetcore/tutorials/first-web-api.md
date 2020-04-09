---
title: 'Öğretici: ASP.NET Core ile bir web API oluşturma'
author: rick-anderson
description: ASP.NET Core ile nasıl web API oluşturarak nasıl oluşturabildiğini öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 7418e962076fae3ebdbb25381838757b09046578
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417653"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Öğretici: ASP.NET Core ile bir web API oluşturma

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), ve Mike [Wasson](https://github.com/mikewasson)

Bu öğretici ASP.NET Core ile bir web API oluşturma temellerini öğretir.

::: moniker range=">= aspnetcore-3.0"

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Bir web API projesi oluşturun.
> * Model sınıfı ve veritabanı bağlamı ekleyin.
> * CRUD yöntemleri ile bir denetleyici iskele.
> * Yönlendirmeyi, URL yollarını ve döndürme değerlerini yapılandırın.
> * Postacı ile web API'yi arayın.

Sonunda, veritabanında depolanan "yapılacaklar" öğelerini yönetebilen bir web API'nız vardır.

## <a name="overview"></a>Genel Bakış

Bu öğretici aşağıdaki API'yi oluşturur:

|API | Açıklama | İstek gövdesi | Yanıt gövdesi |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Tüm yapılacak öğeleri alın | None | Yapılacak öğeler dizisi|
|GET /api/TodoItems/{id} | Bir öğeyi kimlikle alma | None | Yapılacaklar öğesi|
|POST /api/TodoItems | Yeni bir öğe ekleme | Yapılacaklar öğesi | Yapılacaklar öğesi |
|PUT /api/TodoItems/{id} | Varolan bir öğeyi güncelleştirme&nbsp; | Yapılacaklar öğesi | None |
|DELETE /api/TodoItems/{id} &nbsp;&nbsp; | Öğeyi &nbsp; silme&nbsp; | None | None|

Aşağıdaki diyagram, uygulamanın tasarımını gösterir.

![İstemci soldaki bir kutu yla temsil edilir. Bir istek gönderir ve uygulamadan bir yanıt alır, sağda çizilmiş bir kutu. Uygulama kutusunda, denetleyiciyi, modeli ve veri erişim katmanını üç kutu temsil eleştirir. İstek uygulamanın denetleyicisine gelir ve denetleyici ile veri erişim katmanı arasında okuma/yazma işlemleri gerçekleşir. Model seri hale getirilir ve yanıt olarak istemciye döndürülür.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Web projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Dosya** menüsünden **Yeni** > **Proje'yi**seçin.
* ASP.NET **Çekirdek Web Uygulaması** şablonunu seçin ve **İleri'yi**tıklatın.
* Proje *todoApi* adı ve **Oluştur'u**tıklatın.
* Yeni **bir ASP.NET Core Web Uygulaması Oluştur** iletişim kutusunda **,.NET Core** ve ASP.NET Core **3.1'in** seçildiğini doğrulayın. **API** şablonu seçin ve **Oluştur'u**tıklatın.

![VS yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri`cd`( ) proje klasörünü içeren klasörle değiştirin.
* Aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinisi sorduğunda **Evet'i**seçin.

  Önceki komutlar:

  * Yeni bir web API projesi oluşturur ve Visual Studio Code'da açar.
  * Bir sonraki bölümde gerekli olan NuGet paketlerini ekler.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **Yeni Çözüm**seçin.

  ![macOS Yeni çözüm](first-web-api-mac/_static/sln.png)

* **Sonraki** **.NET Çekirdek** > **Uygulama** > **API'yi** > seçin.

  ![macOS Yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* Yeni **ASP.NET Core Web API iletişim günlüğünüzü yapılandırın,** **.NET Core 3.1'in* **Hedef Çerçevesi'ni** seçin.

* **Proje Adı** için *TodoApi* girin ve ardından **Oluştur'u**seçin.

  ![config iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>API’yi test etme

Proje şablonu bir `WeatherForecast` API oluşturur. Uygulamayı `Get` test etmek için yöntemi bir tarayıcıdan arayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uygulamayı çalıştırmak için Ctrl+F5 tuşuna basın. Visual Studio bir tarayıcı başlatıyor `https://localhost:<port>/WeatherForecast`ve `<port>` rastgele seçilen bir bağlantı noktası numarası olan yere doğru yol alar.

IIS Express sertifikasına güvenip güvenmemeniz gerektiğini soran bir iletişim kutusu **alırsanız, Evet'i**seçin. Bir sonraki görünen **Güvenlik Uyarısı** iletişim kutusunda **Evet'i**seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uygulamayı çalıştırmak için Ctrl+F5 tuşuna basın. Bir tarayıcıda, aşağıdaki URL'ye gidin: `https://localhost:5001/WeatherForecast`.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Uygulamayı başlatmak için**Başlat Hata Ayıklama'yı** **çalıştır'ı** > seçin. Mac için Visual Studio bir tarayıcı `https://localhost:<port>`başlatıyor `<port>` ve rastgele seçilen bir bağlantı noktası numarası olan yere doğru yol aldı. Bir HTTP 404 (Bulunamadı) hatası döndürülür. URL'ye ek `/WeatherForecast` (URL'yi `https://localhost:<port>/WeatherForecast`değiştirin).

---

JSON benzer aşağıdaki döndürülür:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>Model sınıfı ekleme

*Model,* uygulamanın yönettiği verileri temsil eden sınıflar kümesidir. Bu uygulamanın modeli tek `TodoItem` bir sınıftır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini'nde**projeyi sağ tıklatın. **Yeni Klasör** **Ekle'yi** > seçin. *Klasör*Modelleri'ni adlandırın.

* *Modeller* klasörüne sağ tıklayın ve**Sınıf** **Ekle'yi** > seçin. Sınıf *TodoItem'i* adlandırın ve **Ekle'yi**seçin.

* Şablon kodunu aşağıdaki kodla değiştirin:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller*adlı bir klasör ekleyin.

* Aşağıdaki `TodoItem` kodu içeren *Modeller* klasörüne bir sınıf ekleyin:

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Projeyi sağ tıklatın. **Yeni Klasör** **Ekle'yi** > seçin. *Klasör*Modelleri'ni adlandırın.

  ![yeni klasör](first-web-api-mac/_static/folder.png)

* *Modeller* klasörüne sağ tıklayın ve **Yeni Dosya** > **Genel** > **Boş Sınıf** **Ekle'yi** > seçin.

* Sınıf *TodoItem'i*adlandırın ve ardından **Yeni'yi**tıklatın.

* Şablon kodunu aşağıdaki kodla değiştirin:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

Özellik, `Id` ilişkisel bir veritabanında benzersiz anahtar olarak işlev görür.

Model sınıfları projede herhangi bir yere gidebilir, ancak *Modeller* klasörü kural kuralı tarafından kullanılır.

## <a name="add-a-database-context"></a>Veritabanı bağlamı ekleme

*Veritabanı bağlamı,* bir veri modeli için Varlık Çerçevesi işlevselliğini koordine eden ana sınıftır. Bu `Microsoft.EntityFrameworkCore.DbContext` sınıf, sınıftan türeerek oluşturulur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Microsoft.EntityFrameworkCore.sqlserver ekle

* **Araçlar** menüsünden **NuGet Paket Yöneticisi > Çözüm Için Paketleri Yönet'i**seçin.
* **Gözat** sekmesini seçin ve ardından arama kutusuna **Microsoft.EntityFrameworkCore.SqlServer** girin.
* Sol bölmede **Microsoft.EntityFrameworkCore.SqlServer'ı** seçin.
* Sağ bölmedeki **Proje** onay kutusunu seçin ve sonra **Yükle'yi**seçin.
* NuGet paketini `Microsoft.EntityFrameworkCore.InMemory` eklemek için önceki yönergeleri kullanın.

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>TodoContext veritabanı bağlamını ekleme

* *Modeller* klasörüne sağ tıklayın ve**Sınıf** **Ekle'yi** > seçin. Sınıf *TodoContext'ı* adlandırın ve **Ekle'yi**tıklatın.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* Modeller `TodoContext` klasörüne *Models* bir sınıf ekleyin.

---

* Aşağıdaki kodu girin:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

ASP.NET Core'da, DB bağlamı gibi hizmetlerin [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilmesi gerekir. Kapsayıcı denetleyicilere hizmet sağlar.

Aşağıdaki vurgulanan kod ile *Startup.cs* güncelleştirin:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Yukarıdaki kod:

* Kullanılmayan `using` bildirimleri kaldırır.
* Veritabanı bağlamını DI kapsayıcısına ekler.
* Veritabanı bağlamında bellek içi bir veritabanı kullanacağını belirtir.

## <a name="scaffold-a-controller"></a>İskele bir denetleyici

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Denetleyiciler* klasörüne sağ tıklayın.
* **Yeni İskele**Öğesi **Ekle'yi** > seçin.
* **Eylemlerle API Denetleyicisi'ni seç, Entity Framework'ünü kullanarak**ve sonra **Ekle'yi**seçin.
* **Eylemlerle api denetleyicisi ekle, Entity Framework** iletişim kutusunu kullanarak:

  * **Model sınıfında** **TodoItem (TodoApi.Models)** seçeneğini belirleyin.
  * **Veri bağlamı sınıfında** **TodoContext (TodoApi.Models)** seçeneğini belirleyin.
  * **Ekle'yi**seçin.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Önceki komutlar:

* İskele için gerekli NuGet paketlerini ekleyin.
* İskele motorini kurar`dotnet-aspnet-codegenerator`( ).
* İskeleler `TodoItemsController`.

---

Oluşturulan kod:

* Sınıfı öznitelik [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) ile işaretler. Bu öznitelik denetleyiciweb API isteklerine yanıt gösterir. Özniteliğin etkinleştirdığı belirli davranışlar hakkında bilgi <xref:web-api/index>için bkz.
* Veritabanı bağlamını denetleyiciye`TodoContext`enjekte etmek için DI kullanır. Veritabanı bağlamı denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinin her birinde kullanılır.

ASP.NET Core şablonları:

* Görünümleri olan `[action]` denetleyiciler rota şablonuna dahil edilir.
* API denetleyicileri rota `[action]` şablonuna dahil etmez.

`[action]` Belirteç rota şablonunda yoksa, [eylem](xref:mvc/controllers/routing#action) adı rotanın dışında tutulur. Diğer bir deyişle, eylemin ilişkili yöntem adı eşleşen rotada kullanılmaz.

## <a name="examine-the-posttodoitem-create-method"></a>PostTodoItem oluşturma yöntemini inceleyin

Operatör adını kullanmak `PostTodoItem` için iade [deyimini değiştirin:](/dotnet/csharp/language-reference/operators/nameof)

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Önceki kod, öznitelikte [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) belirtildiği gibi bir HTTP POST yöntemidir. Yöntem, yapılacaklar öğesinin değerini HTTP isteğinin gövdesinden alır.

Yöntem: <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>

* Başarılı olursa http 201 durum kodunu döndürür. HTTP 201, sunucuda yeni bir kaynak oluşturan bir HTTP POST yönteminin standart yanıtıdır.
* Yanıta [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üstbilgisi ekler. Üstbilgi, `Location` yeni oluşturulan yapılacaklar öğesinin [URI'sini](https://developer.mozilla.org/docs/Glossary/URI) belirtir. Daha fazla bilgi için [bkz.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
* `Location` Üstbilginin `GetTodoItem` URI'sini oluşturmak için eyleme başvurur. C# `nameof` anahtar `CreatedAtAction` kelimesi, çağrıdaki eylem adını sert kodlamaktan kaçınmak için kullanılır.

### <a name="install-postman"></a>Postacı Yükle

Bu öğretici, web API test etmek için Postacı kullanır.

* [Postacı](https://www.getpostman.com/downloads/) Yükle
* Web uygulamasını başlatın.
* Postacı'yı başlat.
* **SSL sertifika doğrulamayı** devre dışı
  * **Dosya** > **Ayarları** 'ndan **(Genel** sekmesi), **SSL sertifika doğrulamasını**devre dışı.
    > [!WARNING]
    > Denetleyiciyi test ettikten sonra SSL sertifika doğrulamasını yeniden etkinleştirin.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Postman ile Test PostTodoItem

* Yeni bir istek oluşturun.
* HTTP yöntemini `POST`' ye ayarlayın.
* **Vücut** sekmesini seçin.
* **Ham** radyo düğmesini seçin.
* Türü **JSON (uygulama/json)** olarak ayarlayın.
* İstek gövdesinde yapılacaklar öğesi için JSON girin:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* **Gönder**’i seçin.

  ![Create request ile Postacı](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Konum üstbilgisini URI test edin

* **Yanıt** bölmesinde **Üstbilgi** sekmesini seçin.
* **Konum** üstbilgisi değerini kopyalayın:

  ![Postacı konsolunun üstbilgi sekmesi](first-web-api/_static/3/create.png)

* Yöntemi GET olarak ayarlayın.
* URI yapıştırın (örneğin, `https://localhost:5001/api/TodoItems/1`).
* **Gönder**’i seçin.

## <a name="examine-the-get-methods"></a>GET yöntemlerini inceleyin

Bu yöntemler iki GET uç noktası uygular:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Uygulamayı bir tarayıcıdan veya Postacı'dan iki uç noktayı arayarak test edin. Örneğin:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Aşağıdakilere benzer bir yanıt çağrı tarafından `GetTodoItems`üretilir:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Postacı ile Test Alın

* Yeni bir istek oluşturun.
* HTTP yöntemini **GET**olarak ayarlayın.
* İstek URL'sini `https://localhost:<port>/api/TodoItems`' ' olarak ayarlayın Örneğin, `https://localhost:5001/api/TodoItems`.
* **Postman'da iki bölme görünümü** ayarlayın.
* **Gönder**’i seçin.

Bu uygulama bir bellek veritabanı kullanır. Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veriyi döndürmez. Veri döndürülmezse, verileri uygulamaya [gönderin.](#post)

## <a name="routing-and-url-paths"></a>Yönlendirme ve URL yolları

Öznitelik, [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) HTTP GET isteğine yanıt veren bir yöntemi gösterir. Her yöntemin URL yolu aşağıdaki gibi oluşturulur:

* Denetleyicinin `Route` özniteliğindeki şablon dizesiyle başlayın:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* "Denetleyici" soneki eksi denetleyici sınıf adı olan denetleyicinin adı ile değiştirin. `[controller]` Bu örnek için denetleyici sınıf adı **TodoItems**Denetleyicisi olduğundan denetleyici adı "TodoItems"tır. ASP.NET Çekirdek [yönlendirme](xref:mvc/controllers/routing) durumda duyarsız olduğunu.
* `[HttpGet]` Öznitelik bir rota şablonu varsa `[HttpGet("products")]`(örneğin, ), bunu yola ekleyin. Bu örnek şablon kullanmaz. Daha fazla bilgi için [http[Fiil] öznitelikleri ile Öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)bakın.

Aşağıdaki `GetTodoItem` yöntemde, `"{id}"` yapılacak lar öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir. Çağrıldığı zaman, `GetTodoItem` `"{id}"` URL'deki değeri `id` parametresinde yönteme sağlanır.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Döndürülen değerler

Ve `GetTodoItems` `GetTodoItem` yöntemlerin dönüş türü [ActionResult\<T> türüdür.](xref:web-api/action-return-types#actionresultt-type) ASP.NET Core nesneyi otomatik olarak [JSON'a](https://www.json.org/) serileştirir ve JSON'u yanıt iletisinin gövdesine yazar. İşlenmemiş özel durumlar olmadığını varsayarak, bu iade türü için yanıt kodu 200'dür. Işlenmemiş özel durumlar 5xx hatalarına çevrilir.

`ActionResult`iade türleri çok çeşitli HTTP durum kodlarını temsil edebilir. Örneğin, `GetTodoItem` iki farklı durum değerleri döndürebilir:

* Hiçbir öğe istenen kimlikle eşleşmiyorsa, yöntem 404 NoFound hata kodunu [döndürür.](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)
* Aksi takdirde, yöntem bir JSON yanıt gövdesi ile 200 döndürür. Http `item` 200 yanıtıile sonuçları döndürün.

## <a name="the-puttodoitem-method"></a>PutTodoItem yöntemi

Yöntemi `PutTodoItem` inceleyin:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem`benzer `PostTodoItem`, bu http PUT kullanır dışında. Yanıt [204 (İçerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). HTTP belirtimine göre, PUT isteği istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir. Kısmi güncelleştirmeleri desteklemek [için HTTP PATCH'i](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.

Arama hatası `PutTodoItem`alırsanız, `GET` veritabanında bir öğe olduğundan emin olmak için arayın.

### <a name="test-the-puttodoitem-method"></a>PutTodoItem yöntemini test edin

Bu örnek, uygulama her başlatıldığında başlatılması gereken bir bellek içi veritabanı kullanır. PUT araması yapmadan önce veritabanında bir öğe olmalıdır. PUT araması yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET'i arayın.

ID = 1 olan yapılacaklar öğesini güncelleştirin ve adını "balık beslemek" olarak ayarlayın:

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Aşağıdaki resim Postacı güncelleştirmesini gösterir:

![Postacı konsolu 204 (İçerik Yok) yanıtı gösteriyor](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>DeleteTodoItem yöntemi

Yöntemi `DeleteTodoItem` inceleyin:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>DeleteTodoItem yöntemini test edin

Yapılacaklar öğesini silmek için Postacı'yı kullanın:

* Yöntemi `DELETE`' ye ayarlayın.
* Nesnenin URI'sini silmek için `https://localhost:5001/api/TodoItems/1`ayarlayın (örneğin).
* **Gönder**’i seçin.

<a name="over-post"></a>

## <a name="prevent-over-posting"></a>Aşırı deftere nakil önleme

Şu anda örnek uygulama `TodoItem` tüm nesneyi ortaya çıkarır. Productions uygulamaları genellikle modelin bir alt kümesini kullanarak giriş ve döndürülen verileri sınırlar. Bunun arkasında birden fazla nedeni vardır ve güvenlik önemli bir tanesidir. Bir modelin alt kümesi genellikle Veri Aktarım Nesnesi (DTO), giriş modeli veya görünüm modeli olarak adlandırılır. **DTO** bu makalede kullanılır.

Bir DTO için kullanılabilir:

* Aşırı deftere nakil yi önleyin.
* İstemcilerin görüntülememesi gereken özellikleri gizleyin.
* Taşıma boyutunu azaltmak için bazı özellikleri atla.
* İç içe nesneler içeren nesne grafiklerini düzleştirmek. Düzleştirilmiş nesne grafikleri istemciler için daha uygun olabilir.

DTO yaklaşımını göstermek için `TodoItem` sınıfı gizli bir alan içerecek şekilde güncelleştirin:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

Gizli alanın bu uygulamadan gizlenmesi gerekir, ancak bir yönetim uygulaması uygulamayı ifşa etmeyi seçebilir.

Gönderebileceğinizi ve gizli alanı alabileceğinizi doğrulayın.

Bir DTO modeli oluşturun:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Güncelleme `TodoItemsController` kullanmak `TodoItemDTO`için:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Gizli alanı gönderemezsiniz veya alınamadığınızı doğrulayın.

## <a name="call-the-web-api-with-javascript"></a>JavaScript ile web API'sini arayın

[Bkz. Öğretici: JavaScript ile ASP.NET Core web API'yi arayın.](xref:tutorials/web-api-javascript)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Bir web API projesi oluşturun.
> * Model sınıfı ve veritabanı bağlamı ekleyin.
> * Bir denetleyici ekleyin.
> * CRUD yöntemlerini ekleyin.
> * Yönlendirme ve URL yollarını yapılandırın.
> * İade değerlerini belirtin.
> * Postacı ile web API'yi arayın.
> * JavaScript ile web API'yi arayın.

Sonunda, ilişkisel bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilen bir web API'nız vardır.

## <a name="overview"></a>Genel Bakış

Bu öğretici aşağıdaki API'yi oluşturur:

|API | Açıklama | İstek gövdesi | Yanıt gövdesi |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Tüm yapılacak öğeleri alın | None | Yapılacak öğeler dizisi|
|GET /api/TodoItems/{id} | Bir öğeyi kimlikle alma | None | Yapılacaklar öğesi|
|POST /api/TodoItems | Yeni bir öğe ekleme | Yapılacaklar öğesi | Yapılacaklar öğesi |
|PUT /api/TodoItems/{id} | Varolan bir öğeyi güncelleştirme&nbsp; | Yapılacaklar öğesi | None |
|DELETE /api/TodoItems/{id} &nbsp;&nbsp; | Öğeyi &nbsp; silme&nbsp; | None | None|

Aşağıdaki diyagram, uygulamanın tasarımını gösterir.

![İstemci soldaki bir kutu yla temsil edilir. Bir istek gönderir ve uygulamadan bir yanıt alır, sağda çizilmiş bir kutu. Uygulama kutusunda, denetleyiciyi, modeli ve veri erişim katmanını üç kutu temsil eleştirir. İstek uygulamanın denetleyicisine gelir ve denetleyici ile veri erişim katmanı arasında okuma/yazma işlemleri gerçekleşir. Model seri hale getirilir ve yanıt olarak istemciye döndürülür.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Web projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Dosya** menüsünden **Yeni** > **Proje'yi**seçin.
* ASP.NET **Çekirdek Web Uygulaması** şablonunu seçin ve **İleri'yi**tıklatın.
* Proje *todoApi* adı ve **Oluştur'u**tıklatın.
* Yeni **bir ASP.NET Core Web Uygulaması Oluştur** iletişim kutusunda **,.NET Core** ve ASP.NET Core **2.2'nin** seçildiğini doğrulayın. **API** şablonu seçin ve **Oluştur'u**tıklatın. Docker Desteğini **Enable Docker Support** **Etkinleştir'i seçmeyin.**

![VS yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri`cd`( ) proje klasörünü içeren klasörle değiştirin.
* Aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Bu komutlar yeni bir web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code'un yeni bir örneğini açar.

* Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinisi sorduğunda **Evet'i**seçin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **Yeni Çözüm**seçin.

  ![macOS Yeni çözüm](first-web-api-mac/_static/sln.png)

* **Sonraki** **.NET Çekirdek** > **Uygulama** > **API'yi** > seçin.

  ![macOS Yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* Yeni **ASP.NET Core Web API iletişim günlüğünüzde** **.NET Core 2.2'nin*varsayılan **Hedef Çerçevesini** kabul edin.

* **Proje Adı** için *TodoApi* girin ve ardından **Oluştur'u**seçin.

  ![config iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>API’yi test etme

Proje şablonu bir `values` API oluşturur. Uygulamayı `Get` test etmek için yöntemi bir tarayıcıdan arayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uygulamayı çalıştırmak için Ctrl+F5 tuşuna basın. Visual Studio bir tarayıcı başlatıyor `https://localhost:<port>/api/values`ve `<port>` rastgele seçilen bir bağlantı noktası numarası olan yere doğru yol alar.

IIS Express sertifikasına güvenip güvenmemeniz gerektiğini soran bir iletişim kutusu **alırsanız, Evet'i**seçin. Bir sonraki görünen **Güvenlik Uyarısı** iletişim kutusunda **Evet'i**seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uygulamayı çalıştırmak için Ctrl+F5 tuşuna basın. Bir tarayıcıda, aşağıdaki URL'ye gidin: `https://localhost:5001/api/values`.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Uygulamayı başlatmak için**Başlat Hata Ayıklama'yı** **çalıştır'ı** > seçin. Mac için Visual Studio bir tarayıcı `https://localhost:<port>`başlatıyor `<port>` ve rastgele seçilen bir bağlantı noktası numarası olan yere doğru yol aldı. Bir HTTP 404 (Bulunamadı) hatası döndürülür. URL'ye ek `/api/values` (URL'yi `https://localhost:<port>/api/values`değiştirin).

---

Aşağıdaki JSON döndürülür:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Model sınıfı ekleme

*Model,* uygulamanın yönettiği verileri temsil eden sınıflar kümesidir. Bu uygulamanın modeli tek `TodoItem` bir sınıftır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini'nde**projeyi sağ tıklatın. **Yeni Klasör** **Ekle'yi** > seçin. *Klasör*Modelleri'ni adlandırın.

* *Modeller* klasörüne sağ tıklayın ve**Sınıf** **Ekle'yi** > seçin. Sınıf *TodoItem'i* adlandırın ve **Ekle'yi**seçin.

* Şablon kodunu aşağıdaki kodla değiştirin:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller*adlı bir klasör ekleyin.

* Aşağıdaki `TodoItem` kodu içeren *Modeller* klasörüne bir sınıf ekleyin:

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Projeyi sağ tıklatın. **Yeni Klasör** **Ekle'yi** > seçin. *Klasör*Modelleri'ni adlandırın.

  ![yeni klasör](first-web-api-mac/_static/folder.png)

* *Modeller* klasörüne sağ tıklayın ve **Yeni Dosya** > **Genel** > **Boş Sınıf** **Ekle'yi** > seçin.

* Sınıf *TodoItem'i*adlandırın ve ardından **Yeni'yi**tıklatın.

* Şablon kodunu aşağıdaki kodla değiştirin:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

Özellik, `Id` ilişkisel bir veritabanında benzersiz anahtar olarak işlev görür.

Model sınıfları projede herhangi bir yere gidebilir, ancak *Modeller* klasörü kural kuralı tarafından kullanılır.

## <a name="add-a-database-context"></a>Veritabanı bağlamı ekleme

*Veritabanı bağlamı,* bir veri modeli için Varlık Çerçevesi işlevselliğini koordine eden ana sınıftır. Bu `Microsoft.EntityFrameworkCore.DbContext` sınıf, sınıftan türeerek oluşturulur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Modeller* klasörüne sağ tıklayın ve**Sınıf** **Ekle'yi** > seçin. Sınıf *TodoContext'ı* adlandırın ve **Ekle'yi**tıklatın.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* Modeller `TodoContext` klasörüne *Models* bir sınıf ekleyin.

---

* Şablon kodunu aşağıdaki kodla değiştirin:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

ASP.NET Core'da, DB bağlamı gibi hizmetlerin [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilmesi gerekir. Kapsayıcı denetleyicilere hizmet sağlar.

Aşağıdaki vurgulanan kod ile *Startup.cs* güncelleştirin:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Yukarıdaki kod:

* Kullanılmayan `using` bildirimleri kaldırır.
* Veritabanı bağlamını DI kapsayıcısına ekler.
* Veritabanı bağlamında bellek içi bir veritabanı kullanacağını belirtir.

## <a name="add-a-controller"></a>Denetleyici ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Denetleyiciler* klasörüne sağ tıklayın.
* **Yeni Öğe** **Ekle'yi** > seçin.
* Yeni **Öğe Ekle** iletişim kutusunda **API Denetleyici Sınıfı** şablonu'nu seçin.
* Sınıf *TodoController'ı*adlandırın ve **Ekle'yi**seçin.

  ![Arama kutusunda denetleyici ve web api denetleyicisi seçili yeni Öğe iletişim kutusu ekleme](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* *Denetleyiciler* klasöründe, '. `TodoController`adlı bir sınıf oluşturun.

---

* Şablon kodunu aşağıdaki kodla değiştirin:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Yukarıdaki kod:

* Yöntemsiz bir API denetleyici sınıfı tanımlar.
* Sınıfı öznitelik [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) ile işaretler. Bu öznitelik denetleyiciweb API isteklerine yanıt gösterir. Özniteliğin etkinleştirdığı belirli davranışlar hakkında bilgi <xref:web-api/index>için bkz.
* Veritabanı bağlamını denetleyiciye`TodoContext`enjekte etmek için DI kullanır. Veritabanı bağlamı denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinin her birinde kullanılır.
* Veritabanı boşsa veritabanına adlandırılmış `Item1` bir öğe ekler. Bu kod oluşturucudadır, bu nedenle yeni bir HTTP isteği olduğunda çalışır. Tüm öğeleri silerseniz, bir `Item1` sonraki API yöntemi çağrıldığında oluşturucu yeniden oluşturur. Yani silme işlemi işe yaramamış gibi görünebilir.

## <a name="add-get-methods"></a>Get yöntemlerini ekle

Yapılacak lar öğelerini alan bir API sağlamak için `TodoController` sınıfa aşağıdaki yöntemleri ekleyin:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Bu yöntemler iki GET uç noktası uygular:

* `GET /api/todo`
* `GET /api/todo/{id}`

Çalışmaya devam ediyorsa uygulamayı durdurun. Ardından, en son değişiklikleri içerecek şekilde yeniden çalıştırın.

Uygulamayı tarayıcıdan iki uç noktayı arayarak test edin. Örneğin:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

Aşağıdaki HTTP yanıtı çağrı tarafından `GetTodoItems`üretilir:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Yönlendirme ve URL yolları

Öznitelik, [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) HTTP GET isteğine yanıt veren bir yöntemi gösterir. Her yöntemin URL yolu aşağıdaki gibi oluşturulur:

* Denetleyicinin `Route` özniteliğindeki şablon dizesiyle başlayın:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* "Denetleyici" soneki eksi denetleyici sınıf adı olan denetleyicinin adı ile değiştirin. `[controller]` Bu örnek için denetleyici sınıf adı **Todo**Denetleyicisi'dir, bu nedenle denetleyici adı "todo"dur. ASP.NET Çekirdek [yönlendirme](xref:mvc/controllers/routing) durumda duyarsız olduğunu.
* `[HttpGet]` Öznitelik bir rota şablonu varsa `[HttpGet("products")]`(örneğin, ), bunu yola ekleyin. Bu örnek şablon kullanmaz. Daha fazla bilgi için [http[Fiil] öznitelikleri ile Öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)bakın.

Aşağıdaki `GetTodoItem` yöntemde, `"{id}"` yapılacak lar öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir. Çağrıldığı zaman, `GetTodoItem` `"{id}"` URL'deki değeri`id` parametresinde yönteme sağlanır.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Döndürülen değerler

Ve `GetTodoItems` `GetTodoItem` yöntemlerin dönüş türü [ActionResult\<T> türüdür.](xref:web-api/action-return-types#actionresultt-type) ASP.NET Core nesneyi otomatik olarak [JSON'a](https://www.json.org/) serileştirir ve JSON'u yanıt iletisinin gövdesine yazar. İşlenmemiş özel durumlar olmadığını varsayarak, bu iade türü için yanıt kodu 200'dür. Işlenmemiş özel durumlar 5xx hatalarına çevrilir.

`ActionResult`iade türleri çok çeşitli HTTP durum kodlarını temsil edebilir. Örneğin, `GetTodoItem` iki farklı durum değerleri döndürebilir:

* Hiçbir öğe istenen kimlikle eşleşmiyorsa, yöntem 404 NoFound hata kodunu [döndürür.](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)
* Aksi takdirde, yöntem bir JSON yanıt gövdesi ile 200 döndürür. Http `item` 200 yanıtıile sonuçları döndürün.

## <a name="test-the-gettodoitems-method"></a>GetTodoItems yöntemini test edin

Bu öğretici, web API test etmek için Postacı kullanır.

* [Postacı'yı](https://www.getpostman.com/downloads/)yükleyin.
* Web uygulamasını başlatın.
* Postacı'yı başlat.
* **SSL sertifika doğrulamayı**devre dışı kılmış.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Dosya** > **Ayarları** 'ndan **(Genel** sekmesi), **SSL sertifika doğrulamasını**devre dışı.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* **Postacı** > **Tercihleri** 'nden **(Genel** sekme), **SSL sertifika doğrulamasını**devre dışı. Alternatif olarak, anahtarı seçin ve **Ayarlar'ı**seçin, ardından SSL sertifika doğrulamasını devre dışı bırakır.

---
  
> [!WARNING]
> Denetleyiciyi test ettikten sonra SSL sertifika doğrulamasını yeniden etkinleştirin.

* Yeni bir istek oluşturun.
  * HTTP yöntemini **GET**olarak ayarlayın.
  * İstek URL'sini `https://localhost:<port>/api/todo`' ' olarak ayarlayın Örneğin, `https://localhost:5001/api/todo`.
* **Postman'da iki bölme görünümü** ayarlayın.
* **Gönder**’i seçin.

![Postacı ile İstek alın](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Oluşturma yöntemi ekle

`PostTodoItem` *Denetleyiciler/TodoController.cs*içine aşağıdaki yöntemi ekleyin: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Önceki kod, öznitelikte [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) belirtildiği gibi bir HTTP POST yöntemidir. Yöntem, yapılacaklar öğesinin değerini HTTP isteğinin gövdesinden alır.

Yöntem: `CreatedAtAction`

* Başarılı olursa http 201 durum kodunu döndürür. HTTP 201, sunucuda yeni bir kaynak oluşturan bir HTTP POST yönteminin standart yanıtıdır.
* Yanıta `Location` bir üstbilgi ekler. Üstbilgi, `Location` yeni oluşturulan yapılacaklar öğesinin URI'sini belirtir. Daha fazla bilgi için [bkz.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
* `Location` Üstbilginin `GetTodoItem` URI'sini oluşturmak için eyleme başvurur. C# `nameof` anahtar `CreatedAtAction` kelimesi, çağrıdaki eylem adını sert kodlamaktan kaçınmak için kullanılır.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>PostTodoItem yöntemini test edin

* Projeyi derleyin.
* Postacı'da HTTP yöntemini `POST`.
* **Vücut** sekmesini seçin.
* **Ham** radyo düğmesini seçin.
* Türü **JSON (uygulama/json)** olarak ayarlayın.
* İstek gövdesinde yapılacaklar öğesi için JSON girin:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* **Gönder**’i seçin.

  ![Create request ile Postacı](first-web-api/_static/create.png)

  405 Yöntemi İzin Verilmez hatası alırsanız, bu büyük olasılıkla `PostTodoItem` yöntemi ekledikten sonra projederleme değil sonucu.

### <a name="test-the-location-header-uri"></a>Konum üstbilgisini URI test edin

* **Yanıt** bölmesinde **Üstbilgi** sekmesini seçin.
* **Konum** üstbilgisi değerini kopyalayın:

  ![Postacı konsolunun üstbilgi sekmesi](first-web-api/_static/pmc2.png)

* Yöntemi GET olarak ayarlayın.
* URI yapıştırın (örneğin, `https://localhost:5001/api/Todo/2`).
* **Gönder**’i seçin.

## <a name="add-a-puttodoitem-method"></a>PutTodoItem yöntemi ekleme

Aşağıdaki `PutTodoItem` yöntemi ekleyin:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem`benzer `PostTodoItem`, bu http PUT kullanır dışında. Yanıt [204 (İçerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). HTTP belirtimine göre, PUT isteği istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir. Kısmi güncelleştirmeleri desteklemek [için HTTP PATCH'i](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.

Arama hatası `PutTodoItem`alırsanız, `GET` veritabanında bir öğe olduğundan emin olmak için arayın.

### <a name="test-the-puttodoitem-method"></a>PutTodoItem yöntemini test edin

Bu örnek, uygulama her başlatıldığında başlatılması gereken bir bellek içi veritabanı kullanır. PUT araması yapmadan önce veritabanında bir öğe olmalıdır. PUT araması yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET'i arayın.

id = 1 olan yapılacaklar öğesini güncelleştirin ve adını "balık beslemek" olarak ayarlayın:

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Aşağıdaki resim Postacı güncelleştirmesini gösterir:

![Postacı konsolu 204 (İçerik Yok) yanıtı gösteriyor](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>DeleteTodoItem yöntemi ekleme

Aşağıdaki `DeleteTodoItem` yöntemi ekleyin:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

Yanıt `DeleteTodoItem` [204 (İçerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>DeleteTodoItem yöntemini test edin

Yapılacaklar öğesini silmek için Postacı'yı kullanın:

* Yöntemi `DELETE`' ye ayarlayın.
* Nesnenin URI'sini silmek için `https://localhost:5001/api/todo/1`ayarlayın (örneğin, ).
* **Gönder**’i seçin.

Örnek uygulama tüm öğeleri silmenizi sağlar. Ancak, son öğe silindiğinde, API bir sonraki çağrıldığında model sınıfı oluşturucu tarafından yeni bir öğe oluşturulur.

## <a name="call-the-web-api-with-javascript"></a>JavaScript ile web API'sini arayın

Bu bölümde, web API'sını aramak için JavaScript kullanan bir HTML sayfası eklenir. jQuery isteği başlatır. JavaScript, sayfayı web API'sının yanıtındaki ayrıntılarla güncelleştirir.

Uygulamayı [statik dosyalara hizmet](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) sağlayacak şekilde yapılandırın ve aşağıdaki vurgulanan kodla *Startup.cs* güncelleştirerek varsayılan [dosya eşlemesini etkinleştirin:](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Proje dizininde bir *wwwroot* klasörü oluşturun.

*wwwroot* dizinine *index.html* adlı bir HTML dosyası ekleyin. İçeriğini aşağıdaki biçimlendirmeyle değiştirin:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

*wwwroot* dizinine *site.js* adlı bir JavaScript dosyası ekleyin. İçeriğini aşağıdaki kodla değiştirin:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarında bir değişiklik gerekebilir:

* *Özellikleri Aç\launchSettings.json*.
* Uygulamayı `launchUrl` projenin varsayılan *dosyasıindex.html'de*&mdash;açmaya zorlamak için özelliği kaldırın.

Bu örnek, web API'sının tüm CRUD yöntemlerini çağırır. Aşağıda API'ye yapılan çağrıların açıklamaları yer alıyor.

### <a name="get-a-list-of-to-do-items"></a>Yapılacak lar listesi alın

jQuery, yapılacaklar öğelerini temsil eden JSON'u döndüren web API'sine bir HTTP GET isteği gönderir. İstek `success` başarılı olursa geri arama işlevi çağrılır. Geri aramada, DOM yapılacaklar bilgileriyle güncelleştirilir.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Yapılacaklar öğesi ekleme

jQuery, istek gövdesindeki yapılacaklar öğesiyle birlikte bir HTTP POST isteği gönderir. Ve `accepts` `contentType` seçenekler, alınan `application/json` ve gönderilen ortam türünü belirtmek için ayarlanır. Yapılacaklar öğesi [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)kullanılarak JSON'a dönüştürülür. API başarılı bir durum kodu `getData` döndürdüğünde, işlev HTML tablosunu güncelleştirmek için çağrılır.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Yapılacaklar öğesini güncelleştirme

Yapılacaklar öğesini güncelleştirmek, bir öğe eklemeye benzer. Maddenin `url` benzersiz tanımlayıcıeklemek için değişiklikler ve `type` . `PUT`

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Yapılacaklar öğesini silme

Yapılacaklar öğesi nin silmesi, AJAX `type` çağrısındaki öğeyi `DELETE` ayarlayarak ve URL'de öğenin benzersiz tanımlayıcısını belirterek gerçekleştirilir.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Web API'sine kimlik doğrulama desteği ekleme

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a>Ek kaynaklar

[Bu öğretici için örnek kodu görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples) [Nasıl indirilir bakın.](xref:index#how-to-download-a-sample)

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=TTkhEyGBfAk)
