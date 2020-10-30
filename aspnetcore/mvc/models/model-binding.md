---
title: ASP.NET Core 'de model bağlama
author: rick-anderson
description: ASP.NET Core model bağlamasının nasıl çalıştığını ve davranışını nasıl özelleştireceğinizi öğrenin.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- appsettings.json
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
uid: mvc/models/model-binding
ms.openlocfilehash: a3be22134246c76b0a809ddb97b33ff97ace9a5b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057511"
---
# <a name="model-binding-in-aspnet-core"></a>ASP.NET Core 'de model bağlama

::: moniker range=">= aspnetcore-3.0"

Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>Model bağlama nedir?

Denetleyiciler ve Razor Sayfalar, http isteklerinden gelen verilerle çalışır. Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir. Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir. Model bağlama bu işlemi otomatikleştirir. Model bağlama sistemi:

* Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.
* RazorYöntem parametreleri ve ortak özellikler içindeki denetleyicilere ve sayfalara verileri sağlar.
* Dize verilerini .NET türlerine dönüştürür.
* Karmaşık türlerin özelliklerini güncelleştirir.

## <a name="example"></a>Örnek

Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

Ve uygulama şu URL ile bir istek alıyor:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:

* Adlı bir tamsayı olan ilk parametresini bulur `GetByID` `id` .
* HTTP isteğindeki kullanılabilir kaynakları arar ve `id` route verilerinde = "2" bulur.
* "2" dizesini tamsayı 2 ' ye dönüştürür.
* `GetByID`Adlı bir Boole değeri olan bir sonraki parametresini bulur `dogsOnly` .
* Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur. Ad eşleştirme, büyük/küçük harfe duyarlı değildir.
* "True" dizesini Boole değerine dönüştürür `true` .

Framework daha sonra yöntemi çağırır `GetById` , parametresi için 2 ' ye geçerek `id` ve `true` `dogsOnly` parametresi için.

Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir. Hedefler, karmaşık bir türün özellikleri de olabilir. Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur. Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır. Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.

## <a name="targets"></a>Targets

Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:

* Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.
* RazorBir isteğin yönlendirildiği sayfa işleyicisi yönteminin parametreleri. 
* Bir denetleyicinin veya `PageModel` sınıfın öznitelikleri tarafından belirtilmişse ortak özellikleri.

### <a name="bindproperty-attribute"></a>[BindProperty] özniteliği

, `PageModel` Model bağlamasının bu özelliği hedeflemesini sağlamak için bir denetleyicinin veya sınıfın ortak özelliğine uygulanabilir:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a>[BindProperties] özniteliği

ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.  `PageModel`Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya sınıfa uygulanabilir:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>HTTP GET istekleri için model bağlama

Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir. Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir. Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır. Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur. GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliği şu şekilde ayarlayın `true` :

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Kaynaklar

Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:

1. Form alanları
1. İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)
1. Veri yönlendirme
1. Sorgu dizesi parametreleri
1. Karşıya yüklenen dosyalar

Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır. Birkaç özel durum vardır:

* Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.
* Karşıya yüklenen dosyalar yalnızca veya uygulayan hedef türlere bağlanır `IFormFile` `IEnumerable<IFormFile>` .

Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Sorgu dizesinden değerleri alır. 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Rota verilerinden değerleri alır.
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Postalanan Form alanlarındaki değerleri alır.
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -İstek gövdesinden değerleri alır.
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP başlıklarındaki değerleri alır.

Bu öznitelikler:

* Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin. Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır. Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>[FromBody] özniteliği

`[FromBody]`Özniteliği BIR http isteği gövdesinden doldurmak için bir parametreye özniteliğini uygulayın. ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder. Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.

`[FromBody]`Karmaşık bir tür parametresine uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır. Örneğin, aşağıdaki eylem, `Create` `pet` parametresinin gövdeden doldurulduğunu belirtir:

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

`Pet`Sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

Yukarıdaki örnekte:

* `[FromQuery]`Öznitelik yoksayıldı.
* `Breed`Özellik bir sorgu dizesi parametresinden doldurulmamış. 

Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın. Gövdede uygun bir değer bulunursa, bu değer özelliği doldurmak için kullanılır `Breed` .

`[FromBody]`Action yöntemi başına birden fazla parametreye uygulanmaz. İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer parametreleri bağlamak için artık bir daha okunamaz `[FromBody]` .

### <a name="additional-sources"></a>Ek kaynaklar

Kaynak verileri, model bağlama sistemine *değer sağlayıcılara* göre sağlanır. Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz. Örneğin, verilerin cookie veya oturum durumunun olmasını isteyebilirsiniz. Yeni bir kaynaktan veri almak için:

* Uygulayan bir sınıf oluşturun `IValueProvider` .
* Uygulayan bir sınıf oluşturun `IValueProviderFactory` .
* Fabrika sınıfını içine kaydedin `Startup.ConfigureServices` .

Örnek uygulama, ' den değerler alan bir [değer sağlayıcısı](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) ve [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) örneği içerir cookie . Kayıt kodu aşağıda verilmiştir `Startup.ConfigureServices` :

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.  Bunu listede ilk yapmak için `Insert(0, new CookieValueProviderFactory())` yerine çağırın `Add` .

## <a name="no-source-for-a-model-property"></a>Model özelliği için kaynak yok

Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz. Özelliği null veya varsayılan bir değer olarak ayarlanır:

* Null yapılabilir basit türler olarak ayarlanır `null` .
* Null yapılamayan değer türleri olarak ayarlanır `default(T)` . Örneğin, bir parametre `int id` 0 olarak ayarlanır.
* Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.
* Diziler olarak ayarlanır `Array.Empty<T>()` , ancak `byte[]` diziler olarak ayarlanır `null` .

Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [`[BindRequired]`](#bindrequired-attribute) özniteliğini kullanın.

Bu `[BindRequired]` davranışın, bir istek GÖVDESINDE JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın. İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.

## <a name="type-conversion-errors"></a>Tür dönüştürme hataları

Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir. Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.

Özniteliği olan bir API denetleyicisinde `[ApiController]` , geçersiz model durumu OTOMATIK HTTP 400 yanıtına neden olur.

Bir Razor sayfada, sayfayı bir hata iletisiyle yeniden görüntüleyin:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

İstemci tarafı doğrulama, aksi takdirde bir sayfalar formuna gönderilemeyen hatalı verileri yakalar Razor . Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır. Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir. Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.

Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez. Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır. Geçersiz giriş bir hata iletisinde görüntülenir. Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.

Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir. Bu durumda model özelliğini bir dize yapın.

## <a name="simple-types"></a>Basit türler

Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:

* [Boolean](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [Tarih Saat](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Kategori](xref:System.ComponentModel.DecimalConverter)
* [Çift](xref:System.ComponentModel.DoubleConverter)
* [Sabit listesi](xref:System.ComponentModel.EnumConverter)
* ['İni](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Tek](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [Kullanılmamışsa](xref:System.UriTypeConverter)
* [Sürüm](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Karmaşık türler

Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır. Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur. 

Karmaşık türün her özelliği için model bağlama *prefix.property_name* ad deseninin kaynaklarını arar. Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.

Bir parametreye bağlama için, önek parametre adıdır. Ortak özelliğe bağlama için `PageModel` , önek ortak özellik adıdır. Bazı özniteliklerin `Prefix` , parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan bir özelliği vardır.

Örneğin, karmaşık türün aşağıdaki sınıf olduğunu varsayalım `Instructor` :

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Önek = parametre adı

Bağlanacak model adlı bir parametre ise `instructorToUpdate` :

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

Model bağlama, anahtar kaynaklarına bakarak başlar `instructorToUpdate.ID` . Bu bulunamazsa, `ID` öneki olmadan arar.

### <a name="prefix--property-name"></a>Önek = Özellik adı

Bağlanacak model, `Instructor` denetleyicinin veya sınıfın adında bir özelliktir `PageModel` :

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` . Bu bulunamazsa, `ID` öneki olmadan arar.

### <a name="custom-prefix"></a>Özel ön ek

Bağlanacak model adlı bir parametre ise `instructorToUpdate` ve bir `Bind` öznitelik `Instructor` önek olarak belirtirse:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` . Bu bulunamazsa, `ID` öneki olmadan arar.

### <a name="attributes-for-complex-type-targets"></a>Karmaşık tür hedefleri için öznitelikler

Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler. Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlarını **etkilemez.** Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.

### <a name="bind-attribute"></a>[Bind] özniteliği

, Bir sınıfa veya yöntem parametresine uygulanabilir. Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir. `[Bind]`Giriş _*_formatlarını etkilemez._*_

Aşağıdaki örnekte, `Instructor` herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

Aşağıdaki örnekte, `Instructor` yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır `OnPost` :

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

`[Bind]`Özniteliği, _Create * senaryolarında fazla nakline karşı korumak için kullanılabilir. Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz. Fazla naklin savunma için, öznitelik yerine görüntüleme modelleri önerilir `[Bind]` . Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).

### <a name="bindrequired-attribute"></a>[BindRequired] özniteliği

, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir. Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur. Aşağıda bir örnek verilmiştir:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

Ayrıca bkz `[Required]` . [model doğrulama](xref:mvc/models/validation#required-attribute)içindeki öznitelik tartışması.

### <a name="bindnever-attribute"></a>[Bindhiç] özniteliği

, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir. Model bağlamasının model özelliğini değiştirmesini engeller. Aşağıda bir örnek verilmiştir:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a>Koleksiyonlar

Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name* ile eşleşmeleri arar. Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar. Örneğin:

* Bağlanacak parametrenin adlı bir dizi olduğunu varsayalım `selectedCourses` :

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* Aşağıdaki biçim yalnızca form verilerinde desteklenir:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametreye geçirir:

  * Selectedkurslar [0] = 1050
  * Selectedkurslar [1] = 2000

  Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır. Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır. Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.

## <a name="dictionaries"></a>Sözlükler

`Dictionary`Hedefler için, model bağlama *parameter_name* veya *property_name* eşleşme arar. Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar. Örneğin:

* Hedef parametrenin bir adlandırılmış olduğunu varsayalım `Dictionary<int, string>` `selectedCourses` :

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin bir sözlüğünü `selectedCourses` parametreye geçirir:

  * Selectedkurslar ["1050"] = "Chemistry"
  * Selectedkurslar ["2000"] = "Ekonomiks"
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a>Oluşturucu bağlama ve kayıt türleri

Model bağlama, karmaşık türlerin parametresiz bir oluşturucuya sahip olmasını gerektirir. Hem hem de `System.Text.Json` `Newtonsoft.Json` tabanlı giriş formatlayıcıları parametresiz oluşturucusu olmayan sınıfların serisini kaldırma desteği. 

C# 9, succinctly ağ üzerinden verileri temsil etmenin harika bir yolu olan kayıt türlerini tanıtır. ASP.NET Core, tek bir Oluşturucu ile model bağlama ve kayıt türlerini doğrulama desteği ekler:

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

`Person/Index.cshtml`:

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

Kayıt türleri doğrulanırken, çalışma zamanı, doğrulama meta verilerini özellikle özellikler yerine parametreler üzerinde arar.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>Model bağlama yolu verileri ve sorgu dizeleri için Genelleştirme davranışı

ASP.NET Core yol değeri sağlayıcısı ve sorgu dizesi değer sağlayıcısı:

* Değerleri sabit kültür olarak değerlendirin.
* URL 'Lerin kültür sabiti olmasını bekler.

Buna karşılık, form verilerinden gelen değerler kültüre duyarlı bir dönüştürmeye gider. Bu, URL 'Lerin yerel ayarlarda paylaşılabilir olması için tasarımdır.

ASP.NET Core yol değeri sağlayıcısını ve sorgu dizesi değeri sağlayıcısını, kültüre duyarlı bir dönüşüme dönüştürmek için:

* Şuradan devralma <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>
* [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) veya [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) adresinden kodu kopyalayın
* Değer sağlayıcısı oluşturucusuna geçirilen [kültür değerini](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) ile değiştirin
* MVC seçeneklerinde varsayılan değer sağlayıcı fabrikasını yeni bir değerle değiştirin:

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>Özel veri türleri

Model bağlamanın işleyebileceği bazı özel veri türleri vardır.

### <a name="iformfile-and-iformfilecollection"></a>Iformfile ve ıformfilecollection

HTTP isteğine eklenen karşıya yüklenen dosya.  Ayrıca, `IEnumerable<IFormFile>` birden çok dosya için de desteklenir.

### <a name="cancellationtoken"></a>CancellationToken

Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.

### <a name="formcollection"></a>Form koleksiyonu

Postalanan form verilerinden tüm değerleri almak için kullanılır.

## <a name="input-formatters"></a>Giriş biçimleri

İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir. Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır. Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir. Diğer içerik türleri için başka biçim ekleyebilirsiniz.

ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer. Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.

Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:

* `Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet paketini yükler.

* İçinde `Startup.ConfigureServices` , veya öğesini çağırın <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* Özniteliği, `Consumes` istek GÖVDESINDE XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).

### <a name="customize-model-binding-with-input-formatters"></a>Giriş formatlayıcıları ile model bağlamayı özelleştirme

Giriş biçimlendiricisi, istek gövdesinden veri okumak için tam sorumluluğa sahiptir. Bu işlemi özelleştirmek için, giriş biçimlendirici tarafından kullanılan API 'Leri yapılandırın. Bu bölümde, `System.Text.Json` adlı özel bir türü anlamak için tabanlı giriş biçimlendiricinin nasıl özelleştirileceği açıklanmaktadır `ObjectId` . 

Adlı özel bir özellik içeren aşağıdaki modeli göz önünde bulundurun `ObjectId` `Id` :

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

Kullanırken model bağlama işlemini özelleştirmek için `System.Text.Json` , öğesinden türetilmiş bir sınıf oluşturun <xref:System.Text.Json.Serialization.JsonConverter%601> :

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

Özel bir dönüştürücü kullanmak için, <xref:System.Text.Json.Serialization.JsonConverterAttribute> türüne özniteliğini uygulayın. Aşağıdaki örnekte, `ObjectId` türü `ObjectIdConverter` özel dönüştürücü olarak ile yapılandırılır:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

Daha fazla bilgi için bkz. [özel dönüştürücüler yazma](/dotnet/standard/serialization/system-text-json-converters-how-to).

## <a name="exclude-specified-types-from-model-binding"></a>Belirtilen türleri model bağlamalarından Dışla

Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor. `ModelMetadata` [Mvcoptions. modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek özelleştirebilirsiniz. Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.

Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> `Startup.ConfigureServices` . Örneğin, türü tüm modeller üzerinde model bağlamayı devre dışı bırakmak için `System.Version` :

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` . Örneğin, türündeki özelliklerde doğrulamayı devre dışı bırakmak için `System.Guid` :

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a>Özel model ciltleri

Özel bir model cildi yazarak ve `[ModelBinder]` belirli bir hedef için seçmek üzere özniteliğini kullanarak model bağlamayı genişletebilirsiniz. [Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.

## <a name="manual-model-binding"></a>El ile model bağlama 

Model bağlama yöntemi kullanılarak el ile çağrılabilir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> . Yöntemi hem hem de sınıflarında tanımlanmıştır `ControllerBase` `PageModel` . Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar. Yöntemi `false` model bağlamanın başarısız olup olmadığını döndürür. Aşağıda bir örnek verilmiştir:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  , form gövdesinden, sorgu dizesinden ve veri yönlendirme verilerinden veri almak için değer sağlayıcılarını kullanır. `TryUpdateModelAsync` genellikle: 

* Daha Razor fazla nakletmeyi engellemek için denetleyiciler ve görünümler kullanan sayfalarla ve MVC uygulamalarıyla kullanılır.
* Form verilerinden, sorgu dizelerinden ve veri rotalarından tüketilmediği müddetçe bir Web API 'SI ile kullanılmaz. JSON kullanan Web API uç noktaları, istek gövdesini bir nesne olarak seri durumdan çıkarmak için [giriş formatlarını](#input-formatters) kullanır.

Daha fazla bilgi için bkz. [Tryupdatemodelasync](xref:data/ef-rp/crud#TryUpdateModelAsync).

## <a name="fromservices-attribute"></a>[FromServices] özniteliği

Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar. Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur. [Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır. Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>Model bağlama nedir?

Denetleyiciler ve Razor Sayfalar, http isteklerinden gelen verilerle çalışır. Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir. Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir. Model bağlama bu işlemi otomatikleştirir. Model bağlama sistemi:

* Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.
* RazorYöntem parametreleri ve ortak özellikler içindeki denetleyicilere ve sayfalara verileri sağlar.
* Dize verilerini .NET türlerine dönüştürür.
* Karmaşık türlerin özelliklerini güncelleştirir.

## <a name="example"></a>Örnek

Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

Ve uygulama şu URL ile bir istek alıyor:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:

* Adlı bir tamsayı olan ilk parametresini bulur `GetByID` `id` .
* HTTP isteğindeki kullanılabilir kaynakları arar ve `id` route verilerinde = "2" bulur.
* "2" dizesini tamsayı 2 ' ye dönüştürür.
* `GetByID`Adlı bir Boole değeri olan bir sonraki parametresini bulur `dogsOnly` .
* Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur. Ad eşleştirme, büyük/küçük harfe duyarlı değildir.
* "True" dizesini Boole değerine dönüştürür `true` .

Framework daha sonra yöntemi çağırır `GetById` , parametresi için 2 ' ye geçerek `id` ve `true` `dogsOnly` parametresi için.

Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir. Hedefler, karmaşık bir türün özellikleri de olabilir. Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur. Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır. Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.

## <a name="targets"></a>Targets

Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:

* Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.
* RazorBir isteğin yönlendirildiği sayfa işleyicisi yönteminin parametreleri. 
* Bir denetleyicinin veya `PageModel` sınıfın öznitelikleri tarafından belirtilmişse ortak özellikleri.

### <a name="bindproperty-attribute"></a>[BindProperty] özniteliği

, `PageModel` Model bağlamasının bu özelliği hedeflemesini sağlamak için bir denetleyicinin veya sınıfın ortak özelliğine uygulanabilir:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a>[BindProperties] özniteliği

ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.  `PageModel`Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya sınıfa uygulanabilir:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>HTTP GET istekleri için model bağlama

Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir. Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir. Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır. Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur. GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliği şu şekilde ayarlayın `true` :

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Kaynaklar

Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:

1. Form alanları
1. İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)
1. Veri yönlendirme
1. Sorgu dizesi parametreleri
1. Karşıya yüklenen dosyalar

Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır. Birkaç özel durum vardır:

* Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.
* Karşıya yüklenen dosyalar yalnızca veya uygulayan hedef türlere bağlanır `IFormFile` `IEnumerable<IFormFile>` .

Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Sorgu dizesinden değerleri alır. 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Rota verilerinden değerleri alır.
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Postalanan Form alanlarındaki değerleri alır.
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -İstek gövdesinden değerleri alır.
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP başlıklarındaki değerleri alır.

Bu öznitelikler:

* Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin. Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır. Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>[FromBody] özniteliği

`[FromBody]`Özniteliği BIR http isteği gövdesinden doldurmak için bir parametreye özniteliğini uygulayın. ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder. Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.

`[FromBody]`Karmaşık bir tür parametresine uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır. Örneğin, aşağıdaki eylem, `Create` `pet` parametresinin gövdeden doldurulduğunu belirtir:

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

`Pet`Sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

Yukarıdaki örnekte:

* `[FromQuery]`Öznitelik yoksayıldı.
* `Breed`Özellik bir sorgu dizesi parametresinden doldurulmamış. 

Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın. Gövdede uygun bir değer bulunursa, bu değer özelliği doldurmak için kullanılır `Breed` .

`[FromBody]`Action yöntemi başına birden fazla parametreye uygulanmaz. İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer parametreleri bağlamak için artık bir daha okunamaz `[FromBody]` .

### <a name="additional-sources"></a>Ek kaynaklar

Kaynak verileri, model bağlama sistemine *değer sağlayıcılara* göre sağlanır. Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz. Örneğin, verilerin cookie veya oturum durumunun olmasını isteyebilirsiniz. Yeni bir kaynaktan veri almak için:

* Uygulayan bir sınıf oluşturun `IValueProvider` .
* Uygulayan bir sınıf oluşturun `IValueProviderFactory` .
* Fabrika sınıfını içine kaydedin `Startup.ConfigureServices` .

Örnek uygulama, ' den değerler alan bir [değer sağlayıcısı](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) ve [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) örneği içerir cookie . Kayıt kodu aşağıda verilmiştir `Startup.ConfigureServices` :

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.  Bunu listede ilk yapmak için `Insert(0, new CookieValueProviderFactory())` yerine çağırın `Add` .

## <a name="no-source-for-a-model-property"></a>Model özelliği için kaynak yok

Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz. Özelliği null veya varsayılan bir değer olarak ayarlanır:

* Null yapılabilir basit türler olarak ayarlanır `null` .
* Null yapılamayan değer türleri olarak ayarlanır `default(T)` . Örneğin, bir parametre `int id` 0 olarak ayarlanır.
* Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.
* Diziler olarak ayarlanır `Array.Empty<T>()` , ancak `byte[]` diziler olarak ayarlanır `null` .

Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [`[BindRequired]`](#bindrequired-attribute) özniteliğini kullanın.

Bu `[BindRequired]` davranışın, bir istek GÖVDESINDE JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın. İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.

## <a name="type-conversion-errors"></a>Tür dönüştürme hataları

Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir. Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.

Özniteliği olan bir API denetleyicisinde `[ApiController]` , geçersiz model durumu OTOMATIK HTTP 400 yanıtına neden olur.

Bir Razor sayfada, sayfayı bir hata iletisiyle yeniden görüntüleyin:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

İstemci tarafı doğrulama, aksi takdirde bir sayfalar formuna gönderilemeyen hatalı verileri yakalar Razor . Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır. Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir. Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.

Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez. Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır. Geçersiz giriş bir hata iletisinde görüntülenir. Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.

Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir. Bu durumda model özelliğini bir dize yapın.

## <a name="simple-types"></a>Basit türler

Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:

* [Boolean](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [Tarih Saat](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Kategori](xref:System.ComponentModel.DecimalConverter)
* [Çift](xref:System.ComponentModel.DoubleConverter)
* [Sabit listesi](xref:System.ComponentModel.EnumConverter)
* ['İni](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Tek](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [Kullanılmamışsa](xref:System.UriTypeConverter)
* [Sürüm](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Karmaşık türler

Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır. Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur. 

Karmaşık türün her özelliği için model bağlama *prefix.property_name* ad deseninin kaynaklarını arar. Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.

Bir parametreye bağlama için, önek parametre adıdır. Ortak özelliğe bağlama için `PageModel` , önek ortak özellik adıdır. Bazı özniteliklerin `Prefix` , parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan bir özelliği vardır.

Örneğin, karmaşık türün aşağıdaki sınıf olduğunu varsayalım `Instructor` :

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Önek = parametre adı

Bağlanacak model adlı bir parametre ise `instructorToUpdate` :

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

Model bağlama, anahtar kaynaklarına bakarak başlar `instructorToUpdate.ID` . Bu bulunamazsa, `ID` öneki olmadan arar.

### <a name="prefix--property-name"></a>Önek = Özellik adı

Bağlanacak model, `Instructor` denetleyicinin veya sınıfın adında bir özelliktir `PageModel` :

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` . Bu bulunamazsa, `ID` öneki olmadan arar.

### <a name="custom-prefix"></a>Özel ön ek

Bağlanacak model adlı bir parametre ise `instructorToUpdate` ve bir `Bind` öznitelik `Instructor` önek olarak belirtirse:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` . Bu bulunamazsa, `ID` öneki olmadan arar.

### <a name="attributes-for-complex-type-targets"></a>Karmaşık tür hedefleri için öznitelikler

Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler. Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlayıcıları 'nı etkilemez. Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.
>
> Ayrıca bkz `[Required]` . [model doğrulama](xref:mvc/models/validation#required-attribute)içindeki öznitelik tartışması.

### <a name="bindrequired-attribute"></a>[BindRequired] özniteliği

, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir. Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur. Aşağıda bir örnek verilmiştir:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>[Bindhiç] özniteliği

, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir. Model bağlamasının model özelliğini değiştirmesini engeller. Aşağıda bir örnek verilmiştir:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>[Bind] özniteliği

, Bir sınıfa veya yöntem parametresine uygulanabilir. Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir.

Aşağıdaki örnekte, `Instructor` herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

Aşağıdaki örnekte, `Instructor` yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır `OnPost` :

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

`[Bind]`Özniteliği, *oluşturma* senaryolarında fazla nakline karşı korumak için kullanılabilir. Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz. Fazla naklin savunma için, öznitelik yerine görüntüleme modelleri önerilir `[Bind]` . Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).

## <a name="collections"></a>Koleksiyonlar

Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name* ile eşleşmeleri arar. Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar. Örneğin:

* Bağlanacak parametrenin adlı bir dizi olduğunu varsayalım `selectedCourses` :

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* Aşağıdaki biçim yalnızca form verilerinde desteklenir:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametreye geçirir:

  * Selectedkurslar [0] = 1050
  * Selectedkurslar [1] = 2000

  Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır. Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır. Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.

## <a name="dictionaries"></a>Sözlükler

`Dictionary`Hedefler için, model bağlama *parameter_name* veya *property_name* eşleşme arar. Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar. Örneğin:

* Hedef parametrenin bir adlandırılmış olduğunu varsayalım `Dictionary<int, string>` `selectedCourses` :

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin bir sözlüğünü `selectedCourses` parametreye geçirir:

  * Selectedkurslar ["1050"] = "Chemistry"
  * Selectedkurslar ["2000"] = "Ekonomiks"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>Model bağlama yolu verileri ve sorgu dizeleri için Genelleştirme davranışı

ASP.NET Core yol değeri sağlayıcısı ve sorgu dizesi değer sağlayıcısı:

* Değerleri sabit kültür olarak değerlendirin.
* URL 'Lerin kültür sabiti olmasını bekler.

Buna karşılık, form verilerinden gelen değerler kültüre duyarlı bir dönüştürmeye gider. Bu, URL 'Lerin yerel ayarlarda paylaşılabilir olması için tasarımdır.

ASP.NET Core yol değeri sağlayıcısını ve sorgu dizesi değeri sağlayıcısını, kültüre duyarlı bir dönüşüme dönüştürmek için:

* Şuradan devralma <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>
* [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) veya [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) adresinden kodu kopyalayın
* Değer sağlayıcısı oluşturucusuna geçirilen [kültür değerini](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) ile değiştirin
* MVC seçeneklerinde varsayılan değer sağlayıcı fabrikasını yeni bir değerle değiştirin:

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>Özel veri türleri

Model bağlamanın işleyebileceği bazı özel veri türleri vardır.

### <a name="iformfile-and-iformfilecollection"></a>Iformfile ve ıformfilecollection

HTTP isteğine eklenen karşıya yüklenen dosya.  Ayrıca, `IEnumerable<IFormFile>` birden çok dosya için de desteklenir.

### <a name="cancellationtoken"></a>CancellationToken

Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.

### <a name="formcollection"></a>Form koleksiyonu

Postalanan form verilerinden tüm değerleri almak için kullanılır.

## <a name="input-formatters"></a>Giriş biçimleri

İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir. Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır. Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir. Diğer içerik türleri için başka biçim ekleyebilirsiniz.

ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer. Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.

Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:

* `Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet paketini yükler.

* İçinde `Startup.ConfigureServices` , veya öğesini çağırın <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* Özniteliği, `Consumes` istek GÖVDESINDE XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).

## <a name="exclude-specified-types-from-model-binding"></a>Belirtilen türleri model bağlamalarından Dışla

Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor. `ModelMetadata` [Mvcoptions. modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek özelleştirebilirsiniz. Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.

Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> `Startup.ConfigureServices` . Örneğin, türü tüm modeller üzerinde model bağlamayı devre dışı bırakmak için `System.Version` :

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` . Örneğin, türündeki özelliklerde doğrulamayı devre dışı bırakmak için `System.Guid` :

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>Özel model ciltleri

Özel bir model cildi yazarak ve `[ModelBinder]` belirli bir hedef için seçmek üzere özniteliğini kullanarak model bağlamayı genişletebilirsiniz. [Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.

## <a name="manual-model-binding"></a>El ile model bağlama

Model bağlama yöntemi kullanılarak el ile çağrılabilir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> . Yöntemi hem hem de sınıflarında tanımlanmıştır `ControllerBase` `PageModel` . Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar. Yöntemi `false` model bağlamanın başarısız olup olmadığını döndürür. Aşağıda bir örnek verilmiştir:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>[FromServices] özniteliği

Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar. Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur. [Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır. Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
