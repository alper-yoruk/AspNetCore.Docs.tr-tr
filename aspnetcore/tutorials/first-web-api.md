---
title: "Öğretici: ASP.NET Core bir Web API 'SI oluşturma"
author: rick-anderson
description: ASP.NET Core ile Web API 'SI oluşturmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: ad6eac246e5bc7039158981bbe96036389512e4f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019241"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Öğretici: ASP.NET Core bir Web API 'SI oluşturma

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Mike te son](https://github.com/mikewasson)

Bu öğreticide, ASP.NET Core ile Web API 'SI oluşturmanın temelleri öğretilir.

::: moniker range=">= aspnetcore-3.0"

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir Web API projesi oluşturun.
> * Bir model sınıfı ve bir veritabanı bağlamı ekleyin.
> * CRUD yöntemleriyle bir denetleyiciyi dolandırın.
> * Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.
> * Postman ile Web API 'sini çağırın.

Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.

## <a name="overview"></a>Genel Bakış

Bu öğretici aşağıdaki API 'YI oluşturur:

|API | Açıklama | İstek gövdesi | Yanıt gövdesi |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | Tüm yapılacaklar öğelerini Al | Yok | Yapılacaklar öğeleri dizisi|
|`GET /api/TodoItems/{id}` | KIMLIĞE göre öğe al | Yok | Yapılacaklar öğesi|
|`POST /api/TodoItems` | Yeni öğe Ekle | Yapılacaklar öğesi | Yapılacaklar öğesi |
|`PUT /api/TodoItems/{id}` | Mevcut bir öğeyi güncelleştir&nbsp; | Yapılacaklar öğesi | Yok |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | Öğe &nbsp; silme&nbsp; | Yok | Yok|

Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.

![İstemci, sol taraftaki bir kutu ile temsil edilir. Bir istek gönderir ve sağ tarafta çizilmiş bir kutu olan uygulamadan bir yanıt alır. Uygulama kutusu içinde, üç kutu denetleyiciyi, modeli ve veri erişim katmanını temsil eder. İstek uygulamanın denetleyicisine gelir ve denetleyici ile veri erişim katmanı arasında okuma/yazma işlemleri gerçekleştirilir. Model serileştirilir ve yanıtta istemciye döndürülür.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Web projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
* **ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.
* Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.
* **Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın. **API** şablonunu seçin ve **Oluştur**' a tıklayın.

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.
* Aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.

  Önceki komutlar:

  * Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.
  * Sonraki bölümde gerekli olan NuGet paketlerini ekler.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **yeni çözüm**' ü seçin.

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin. Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri** ' yi seçin.

  ![macOS API şablonu seçimi](first-web-api-mac/_static/api_template.png)

* **Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda en son .NET Core 3. x **hedef çerçevesini**seçin. **İleri**’yi seçin.

* **Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>API’yi test etme

Proje şablonu bir API oluşturur `WeatherForecast` . `Get`Uygulamayı test etmek için bir tarayıcıdan yöntemi çağırın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın. Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>/WeatherForecast` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.

IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet**' i seçin. Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet**' i seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın. Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/WeatherForecast` .

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Uygulamayı **başlatmak**  >  için**hata ayıklamayı Başlat** ' ı seçin. Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır. HTTP 404 (bulunamadı) hatası döndürüldü. `/WeatherForecast`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/WeatherForecast` ).

---

Aşağıdakine benzer bir JSON döndürülür:

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

*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir. Bu uygulamanın modeli tek bir `TodoItem` sınıftır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini**, projeye sağ tıklayın. **Add**  >  **Yeni klasör**Ekle ' yi seçin. Klasör *modellerini*adlandırın.

* *Modeller* klasörüne sağ tıklayın ve sınıf **Ekle**' yi seçin  >  **Class**. Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.

* Şablon kodunu şu kodla değiştirin:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller*adlı bir klasör ekleyin.

* `TodoItem` *Modeller* klasörüne aşağıdaki kodla bir sınıf ekleyin:

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Projeye sağ tıklayın. **Add**  >  **Yeni klasör**Ekle ' yi seçin. Klasör *modellerini*adlandırın.

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* *Modeller* klasörüne sağ tıklayın ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı**' nı seçin.

* Sınıfı *TodoItem*olarak adlandırın ve ardından **Yeni**' ye tıklayın.

* Şablon kodunu şu kodla değiştirin:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.

Model sınıfları projede herhangi bir yere gidebilir, ancak *modeller* klasörü kural tarafından kullanılır.

## <a name="add-a-database-context"></a>Veritabanı bağlamı ekleme

*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır. Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>NuGet paketlerini ekleme

* **Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet**' i seçin.
* **Araştır** sekmesini seçin ve arama kutusuna **Microsoft. Entityframeworkcore. SqlServer** yazın.
* Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.
* Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install**' ı seçin.
* Yukarıdaki yönergeleri kullanarak **Microsoft. EntityFrameworkCore. InMemory** NuGet paketini ekleyin.

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>TodoContext veritabanı bağlamını ekleme

* *Modeller* klasörüne sağ tıklayın ve sınıf **Ekle**' yi seçin  >  **Class**. Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* `TodoContext` *Modeller* klasörüne bir sınıf ekleyin.

---

* Aşağıdaki kodu girin:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir. Kapsayıcı hizmeti denetleyicilere sağlar.

Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Yukarıdaki kod:

* Kullanılmayan `using` bildirimleri kaldırır.
* Veritabanı bağlamını dı kapsayıcısına ekler.
* Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.

## <a name="scaffold-a-controller"></a>Denetleyiciyi bir denetleyiciye katlama

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Denetleyiciler* klasörüne sağ tıklayın.
* **Add** > **Yeni yapı iskelesi öğesi Ekle öğesini**seçin.
* **Entity Framework kullanarak ve eylemler Içeren API denetleyicisi**' ni seçin ve ardından **Ekle**' yi seçin.
* **API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:

  * **Model sınıfında** **TodoItem (TodoApi. modeller)** öğesini seçin.
  * **Veri bağlamı sınıfında** **TodoContext (TodoApi. modeller)** öğesini seçin.
  * **Add (Ekle)** seçeneğini belirleyin.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Önceki komutlar:

* Yapı iskelesi için gereken NuGet paketlerini ekleyin.
* Scafkatlama altyapısını ( `dotnet-aspnet-codegenerator` ) kurar.
* Yapı iskelesi `TodoItemsController` .

---

Oluşturulan kod:

* Sınıfını [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) özniteliğiyle işaretler. Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir. Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..
* Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` . Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.

İçin ASP.NET Core şablonları:

* Görünümleri olan denetleyiciler `[action]` yol şablonuna dahildir.
* API denetleyicileri `[action]` yol şablonuna dahil değildir.

`[action]`Belirteç yol şablonunda olmadığında, [eylem](xref:mvc/controllers/routing#action) adı rotadan çıkarılır. Diğer bir deyişle, eylemin ilişkili Yöntem adı eşleşen rotada kullanılmaz.

## <a name="examine-the-posttodoitem-create-method"></a>PostTodoItem Create metodunu inceleyin

İçindeki return ifadesini, `PostTodoItem` [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanmak için değiştirin:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) . Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Yöntemi:

* Başarılı olursa bir HTTP 201 durum kodu döndürür. HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.
* Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler. `Location`Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin [URI](https://developer.mozilla.org/docs/Glossary/URI) 'sini belirtir. Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* `GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` . C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .

### <a name="install-postman"></a>Postman yükleme

Bu öğretici, Web API 'sini test etmek için Postman kullanır.

* [Postman](https://www.getpostman.com/downloads/) yükleme
* Web uygulamasını başlatın.
* Postman 'ı başlatın.
* **SSL sertifikası doğrulamasını** devre dışı bırak
  * **Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.
    > [!WARNING]
    > Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Postman ile test PostTodoItem

* Yeni bir istek oluşturun.
* HTTP yöntemini olarak ayarlayın `POST` .
* **Gövde** sekmesini seçin.
* **Ham** radyo düğmesini seçin.
* Türü **JSON (Application/JSON)** olarak ayarlayın.
* İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* **Gönder**’i seçin.

  ![Oluşturma isteğiyle Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Konum üst bilgisi URI 'sini test etme

* **Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.
* **Konum** üst bilgisi değerini kopyalayın:

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/3/create.png)

* ALıNACAK yöntemi ayarlayın.
* URI 'yi yapıştırın (örneğin, `https://localhost:5001/api/TodoItems/1` ).
* **Gönder**’i seçin.

## <a name="examine-the-get-methods"></a>GET yöntemlerini inceleyin

Bu yöntemler iki al uç noktası uygular:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin. Örnek:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Şuna benzer bir yanıt, şu çağrı tarafından üretilir `GetTodoItems` :

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Postman ile test al

* Yeni bir istek oluşturun.
* **Almak**için http yöntemini ayarlayın.
* İstek URL 'sini olarak ayarlayın `https://localhost:<port>/api/TodoItems` . Örneğin, `https://localhost:5001/api/TodoItems`.
* Postman 'da **iki bölme görünümü** ayarlayın.
* **Gönder**’i seçin.

Bu uygulama, bellek içi bir veritabanını kullanır. Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez. Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .

## <a name="routing-and-url-paths"></a>Yönlendirme ve URL yolları

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir. Her yöntemin URL yolu şu şekilde oluşturulur:

* Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* `[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir. Bu örnek için denetleyici sınıfı adı **todoıtems**denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur. ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.
* `[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin. Bu örnek, bir şablon kullanmaz. Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir. `GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Dönüş değerleri

`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar. Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir. İşlenmemiş özel durumlar 5 xx hataya çevrilir.

`ActionResult`dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir. Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:

* İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem bir 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> hata kodu döndürür.
* Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür. `item`Sonuçları BIR HTTP 200 yanıtına döndürme.

## <a name="the-puttodoitem-method"></a>PutTodoItem yöntemi

`PutTodoItem` yöntemini inceleyin:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir. Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir. Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.

Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.

### <a name="test-the-puttodoitem-method"></a>PutTodoItem yöntemini test etme

Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır. Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır. PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.

ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>DeleteTodoItem yöntemi

`DeleteTodoItem` yöntemini inceleyin:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>DeleteTodoItem yöntemini test etme

Bir yapılacaklar öğesini silmek için Postman kullanın:

* Yöntemini olarak ayarlayın `DELETE` .
* Silinecek nesnenin URI 'sini ayarlayın (örneğin `https://localhost:5001/api/TodoItems/1` ).
* **Gönder**’i seçin.

<a name="over-post"></a>

## <a name="prevent-over-posting"></a>Fazla nakletmeyi engelle

Şu anda örnek uygulama tüm nesneyi kullanıma sunar `TodoItem` . Üretim uygulamaları tipik olarak, bir modelin alt kümesini kullanarak girdi ve döndürülen verileri sınırlandırır. Bunun arkasında birden çok neden vardır ve güvenlik önemli bir değer. Bir modelin alt kümesi genellikle Veri Aktarımı nesnesi (DTO), giriş modeli veya görünüm modeli olarak adlandırılır. Bu makalede **DTO** kullanılır.

Bir DTO için kullanılabilir:

* Fazla nakletmeyi önleyin.
* İstemcilerin görüntülemesi beklenen özellikleri gizleyin.
* Yük boyutunu azaltmak için bazı özellikleri atlayın.
* İç içe geçmiş nesneler içeren nesne grafiklerini düzleştirin. Düzleştirilmiş nesne grafikleri istemciler için daha uygun olabilir.

DTO yaklaşımını göstermek için, `TodoItem` sınıfı gizli bir alan içerecek şekilde güncelleştirin:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

Gizli alanın bu uygulamadan gizlenmesi gerekir, ancak bir yönetim uygulaması onu kullanıma sunmayı seçebilir.

Gizli dizi alanını nakledebildiğinizi ve alabilirim.

Bir DTO modeli oluşturun:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

`TodoItemsController`Kullanmak için öğesini güncelleştirin `TodoItemDTO` :

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Gizli dizi alanını nakledemeyeceğinizi veya alamazsınız.

## <a name="call-the-web-api-with-javascript"></a>JavaScript ile Web API 'sini çağırma

Bkz. [öğretici: JavaScript ile ASP.NET Core Web API 'Si çağırma](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir Web API projesi oluşturun.
> * Bir model sınıfı ve bir veritabanı bağlamı ekleyin.
> * Denetleyici ekleyin.
> * CRUD yöntemleri ekleyin.
> * Yönlendirme ve URL yollarını yapılandırın.
> * Dönüş değerlerini belirtin.
> * Postman ile Web API 'sini çağırın.
> * JavaScript ile Web API 'sini çağırın.

Sonunda, ilişkisel bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.

## <a name="overview"></a>Genel Bakış

Bu öğretici aşağıdaki API 'YI oluşturur:

|API | Açıklama | İstek gövdesi | Yanıt gövdesi |
|--- | ---- | ---- | ---- |
|/Api/TodoItems al | Tüm yapılacaklar öğelerini Al | Yok | Yapılacaklar öğeleri dizisi|
|/Api/TodoItems/{id} al | KIMLIĞE göre öğe al | Yok | Yapılacaklar öğesi|
|POST/api/TodoItems | Yeni öğe Ekle | Yapılacaklar öğesi | Yapılacaklar öğesi |
|/Api/TodoItems/{id} koy | Mevcut bir öğeyi güncelleştir&nbsp; | Yapılacaklar öğesi | Yok |
|/Api/TodoItems/{id} &nbsp; Sil&nbsp; | Öğe &nbsp; silme&nbsp; | Yok | Yok|

Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.

![İstemci, sol taraftaki bir kutu ile temsil edilir. Bir istek gönderir ve sağ tarafta çizilmiş bir kutu olan uygulamadan bir yanıt alır. Uygulama kutusu içinde, üç kutu denetleyiciyi, modeli ve veri erişim katmanını temsil eder. İstek uygulamanın denetleyicisine gelir ve denetleyici ile veri erişim katmanı arasında okuma/yazma işlemleri gerçekleştirilir. Model serileştirilir ve yanıtta istemciye döndürülür.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Web projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
* **ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.
* Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.
* **Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 2,2** ' un seçili olduğunu doğrulayın. **API** şablonunu seçin ve **Oluştur**' a tıklayın. **Docker desteğini etkinleştir** **' i seçmeyin** .

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.
* Aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Bu komutlar yeni bir Web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code yeni bir örneğini açar.

* Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **yeni çözüm**' ü seçin.

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin. Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.
  
* **Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda en son .NET Core 2. x **hedef çerçevesini**seçin. **İleri**’yi seçin.

* **Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>API’yi test etme

Proje şablonu bir API oluşturur `values` . `Get`Uygulamayı test etmek için bir tarayıcıdan yöntemi çağırın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın. Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>/api/values` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.

IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet**' i seçin. Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet**' i seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın. Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/api/values` .

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Uygulamayı **başlatmak**  >  için**hata ayıklamayı Başlat** ' ı seçin. Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır. HTTP 404 (bulunamadı) hatası döndürüldü. `/api/values`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/api/values` ).

---

Aşağıdaki JSON döndürülür:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Model sınıfı ekleme

*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir. Bu uygulamanın modeli tek bir `TodoItem` sınıftır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini**, projeye sağ tıklayın. **Add**  >  **Yeni klasör**Ekle ' yi seçin. Klasör *modellerini*adlandırın.

* *Modeller* klasörüne sağ tıklayın ve sınıf **Ekle**' yi seçin  >  **Class**. Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.

* Şablon kodunu şu kodla değiştirin:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller*adlı bir klasör ekleyin.

* `TodoItem` *Modeller* klasörüne aşağıdaki kodla bir sınıf ekleyin:

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Projeye sağ tıklayın. **Add**  >  **Yeni klasör**Ekle ' yi seçin. Klasör *modellerini*adlandırın.

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* *Modeller* klasörüne sağ tıklayın ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı**' nı seçin.

* Sınıfı *TodoItem*olarak adlandırın ve ardından **Yeni**' ye tıklayın.

* Şablon kodunu şu kodla değiştirin:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.

Model sınıfları projede herhangi bir yere gidebilir, ancak *modeller* klasörü kural tarafından kullanılır.

## <a name="add-a-database-context"></a>Veritabanı bağlamı ekleme

*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır. Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Modeller* klasörüne sağ tıklayın ve sınıf **Ekle**' yi seçin  >  **Class**. Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* `TodoContext` *Modeller* klasörüne bir sınıf ekleyin.

---

* Şablon kodunu şu kodla değiştirin:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir. Kapsayıcı hizmeti denetleyicilere sağlar.

Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Yukarıdaki kod:

* Kullanılmayan `using` bildirimleri kaldırır.
* Veritabanı bağlamını dı kapsayıcısına ekler.
* Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.

## <a name="add-a-controller"></a>Denetleyici ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Denetleyiciler* klasörüne sağ tıklayın.
* **Add** > **Yeni öğe**Ekle ' yi seçin.
* **Yeni öğe Ekle** iletişim kutusunda, **API denetleyici sınıfı** şablonunu seçin.
* Sınıfı *TodoController*olarak adlandırın ve **Ekle**' yi seçin.

  ![Arama kutusu ve Web API denetleyicisi seçiliyken denetleyiciyi içeren yeni öğe iletişim kutusu Ekle](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* *Denetleyiciler* klasöründe adlı bir sınıf oluşturun `TodoController` .

---

* Şablon kodunu şu kodla değiştirin:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Yukarıdaki kod:

* Yöntemler olmadan bir API denetleyici sınıfı tanımlar.
* Sınıfını [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) özniteliğiyle işaretler. Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir. Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..
* Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` . Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.
* Veritabanı boşsa veritabanına adlı bir öğe ekler `Item1` . Bu kod oluşturucuda yer aldığı için, her yeni HTTP isteği olduğunda çalışır. Tüm öğeleri silerseniz, `Item1` BIR API yönteminin bir sonraki çağrılışında Oluşturucu yeniden oluşturulur. Bu nedenle, aslında çalışırken silme işe yaramadı gibi görünebilir.

## <a name="add-get-methods"></a>Get yöntemleri ekleme

Yapılacaklar öğelerini alan bir API sağlamak için aşağıdaki yöntemleri `TodoController` sınıfına ekleyin:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Bu yöntemler iki al uç noktası uygular:

* `GET /api/todo`
* `GET /api/todo/{id}`

Hala çalışıyorsa uygulamayı durdurun. Ardından, en son değişiklikleri dahil etmek için yeniden çalıştırın.

Bir tarayıcıdan iki uç noktayı çağırarak uygulamayı test edin. Örnek:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

Aşağıdaki HTTP yanıtı, çağrısı tarafından oluşturulur `GetTodoItems` :

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

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir. Her yöntemin URL yolu şu şekilde oluşturulur:

* Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* `[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir. Bu örnek için denetleyici sınıf adı **Todo**Controller olduğundan, denetleyici adı "Todo" olur. ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.
* `[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin. Bu örnek, bir şablon kullanmaz. Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir. `GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Dönüş değerleri

`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar. Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir. İşlenmemiş özel durumlar 5 xx hataya çevrilir.

`ActionResult`dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir. Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:

* İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem bir 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> hata kodu döndürür.
* Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür. `item`Sonuçları BIR HTTP 200 yanıtına döndürme.

## <a name="test-the-gettodoitems-method"></a>GetTodoItems yöntemini test etme

Bu öğretici, Web API 'sini test etmek için Postman kullanır.

* [Postman](https://www.getpostman.com/downloads/)'yi yükleme.
* Web uygulamasını başlatın.
* Postman 'ı başlatın.
* **SSL sertifikası doğrulamasını**devre dışı bırakın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* **Postman**  >  **tercihleri** ' nden (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın. Alternatif olarak, wranı seçin ve **Ayarlar**' ı seçip SSL sertifikası doğrulamasını devre dışı bırakın.

---
  
> [!WARNING]
> Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.

* Yeni bir istek oluşturun.
  * **Almak**için http yöntemini ayarlayın.
  * İstek URL 'sini olarak ayarlayın `https://localhost:<port>/api/todo` . Örneğin, `https://localhost:5001/api/todo`.
* Postman 'da **iki bölme görünümü** ayarlayın.
* **Gönder**’i seçin.

![Get isteği ile Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Create yöntemi ekle

`PostTodoItem` *Controllers/TodoController. cs*içindeki şu yöntemi ekleyin: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) . Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.

`CreatedAtAction`Yöntemi:

* Başarılı olursa bir HTTP 201 durum kodu döndürür. HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.
* Yanıta bir `Location` üst bilgi ekler. `Location`Üst bilgi, yeni oluşturulan Yapılacaklar ÖĞESININ URI 'sini belirtir. Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* `GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` . C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>PostTodoItem yöntemini test etme

* Projeyi derleyin.
* Postman 'da HTTP yöntemini olarak ayarlayın `POST` .
* **Gövde** sekmesini seçin.
* **Ham** radyo düğmesini seçin.
* Türü **JSON (Application/JSON)** olarak ayarlayın.
* İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* **Gönder**’i seçin.

  ![Oluşturma isteğiyle Postman](first-web-api/_static/create.png)

  Bir 405 yöntemine Izin verilmiyor hatası alırsanız, yöntem eklendikten sonra projeyi derlememe sonucu büyük olasılıkla oluşur `PostTodoItem` .

### <a name="test-the-location-header-uri"></a>Konum üst bilgisi URI 'sini test etme

* **Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.
* **Konum** üst bilgisi değerini kopyalayın:

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/pmc2.png)

* ALıNACAK yöntemi ayarlayın.
* URI 'yi yapıştırın (örneğin, `https://localhost:5001/api/Todo/2` ).
* **Gönder**’i seçin.

## <a name="add-a-puttodoitem-method"></a>PutTodoItem yöntemi ekleme

Aşağıdaki yöntemi ekleyin `PutTodoItem` :

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir. Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir. Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.

Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.

### <a name="test-the-puttodoitem-method"></a>PutTodoItem yöntemini test etme

Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır. Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır. PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.

ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>DeleteTodoItem yöntemi ekleme

Aşağıdaki yöntemi ekleyin `DeleteTodoItem` :

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem`Yanıt 204 ' dir [(içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>DeleteTodoItem yöntemini test etme

Bir yapılacaklar öğesini silmek için Postman kullanın:

* Yöntemini olarak ayarlayın `DELETE` .
* Silinecek nesnenin URI 'sini ayarlayın (örneğin, `https://localhost:5001/api/todo/1` ).
* **Gönder**’i seçin.

Örnek uygulama, tüm öğeleri silmenizi sağlar. Ancak, son öğe silindiğinde, API 'nin bir sonraki çağrılışında model sınıfı Oluşturucu tarafından yeni bir tane oluşturulur.

## <a name="call-the-web-api-with-javascript"></a>JavaScript ile Web API 'sini çağırma

Bu bölümde, Web API 'sini çağırmak için JavaScript kullanan bir HTML sayfası eklenir. jQuery isteği başlatır. JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.

Uygulamayı [statik dosyalara sunacak](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) şekilde yapılandırın ve aşağıdaki vurgulanmış kodla *Startup.cs* güncelleştirerek [varsayılan dosya eşlemesini etkinleştirin](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) :

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Proje dizininde bir *Wwwroot* klasörü oluşturun.

*Wwwroot* dizinine *index.html* adlı bir HTML dosyası ekleyin. İçeriğini aşağıdaki biçimlendirmeyle değiştirin:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

*Wwwroot* dizinine *site.js* adlı bir JavaScript dosyası ekleyin. Dosyanın içeriğini aşağıdaki kod ile değiştirin:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarındaki bir değişikliğin yapılması gerekebilir:

* *ÜzerindeProperties\launchSettings.js*açın.
* `launchUrl`Uygulamayı projenin varsayılan dosyasında *index.html*'de açmaya zorlamak için özelliği kaldırın &mdash; .

Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır. API çağrılarının açıklamaları aşağıda verilmiştir.

### <a name="get-a-list-of-to-do-items"></a>Yapılacaklar öğelerinin bir listesini alın

jQuery, bir to-do öğesi dizisini temsil eden JSON döndüren Web API 'sine bir HTTP GET isteği gönderir. `success`Geri çağırma işlevi, istek başarılı olursa çağrılır. Geri aramada, DOM, yapılacaklar bilgileriyle güncelleştirilir.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Yapılacaklar öğesi ekleme

jQuery, istek gövdesinde Yapılacaklar öğesiyle bir HTTP POST isteği gönderir. `accepts`Ve `contentType` seçenekleri, `application/json` alınan ve gönderilen medya türünü belirtmek için olarak ayarlanır. Yapılacaklar öğesi [JSON. stringbelirt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)kullanılarak JSON 'a dönüştürülür. API başarılı bir durum kodu döndürdüğünde, `getData` Işlev HTML tablosunu güncelleştirmek için çağrılır.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Yapılacaklar öğesini güncelleştirme

Bir yapılacaklar öğesinin güncelleştirilmesi, bir tane eklemeye benzer. `url`Öğenin benzersiz tanımlayıcısını ekleme değişiklikleri ve öğesi `type` `PUT` .

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Bir yapılacaklar öğesini silme

Bir yapılacaklar öğesinin silinmesi, `type` Ajax çağrısının üzerine ayarlanarak `DELETE` ve öğenin URL 'sindeki benzersiz tanımlayıcısını belirterek gerçekleştirilir.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Web API 'sine kimlik doğrulama desteği ekleme

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a>Ek kaynaklar

[Bu öğretici için örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Bkz. [indirme](xref:index#how-to-download-a-sample).

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=TTkhEyGBfAk)
