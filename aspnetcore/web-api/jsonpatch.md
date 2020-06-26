---
title: ASP.NET Core Web API 'sinde JsonPatch
author: rick-anderson
description: ASP.NET Core Web API 'sindeki JSON Patch isteklerini nasıl işleyeceğinizi öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/jsonpatch
ms.openlocfilehash: 08ae366859c4466e6957592f78dda813d6670bb4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405035"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>ASP.NET Core Web API 'sinde JsonPatch

, [Tom Dykstra](https://github.com/tdykstra) ve [Kirk larkabağı](https://github.com/serpent5) tarafından

::: moniker range=">= aspnetcore-3.0"

Bu makalede, ASP.NET Core Web API 'sinde JSON Patch isteklerinin nasıl işleneceği açıklanır.

## <a name="package-installation"></a>Paket yüklemesi

Uygulamanızda JSON yama desteğini etkinleştirmek için aşağıdaki adımları izleyin:

1. [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)NuGet paketini yükler.
1. `Startup.ConfigureServices`Çağırmak için projenin metodunu güncelleştirin <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> . Örneğin:

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

`AddNewtonsoftJson`, MVC hizmeti kayıt yöntemleriyle uyumludur:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a>JSON Patch, AddNewtonsoftJson ve System.Text.Js

`AddNewtonsoftJson``System.Text.Json` **Tüm** JSON içeriğini biçimlendirmek için kullanılan tabanlı giriş ve çıkış biçimlerini değiştirir. Kullanarak JSON Patch desteği eklemek için `Newtonsoft.Json` , diğer biçimleri değişmeden bırakarak, projenin `Startup.ConfigureServices` yöntemini aşağıdaki gibi güncelleştirin:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

Yukarıdaki kod, `Microsoft.AspNetCore.Mvc.NewtonsoftJson` paketi ve aşağıdaki `using` deyimlerini gerektirir:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>PATCH HTTP istek yöntemi

PUT ve [Patch](https://tools.ietf.org/html/rfc5789) yöntemleri, var olan bir kaynağı güncelleştirmek için kullanılır. Bunlar arasındaki fark, PUT 'ın tüm kaynağı değiştirmesi, ancak düzeltme eki yalnızca değişiklikleri belirttiğinde.

## <a name="json-patch"></a>JSON yaması

[JSON yaması](https://tools.ietf.org/html/rfc6902) , bir kaynağa uygulanacak güncelleştirmelerin belirtilmesine yönelik bir biçimdir. JSON yama belgesinde bir dizi *işlem*vardır. Her işlem belirli bir değişiklik türünü tanımlar. Bu tür değişikliklere örnek olarak bir dizi öğesi ekleme veya bir özellik değeri değiştirme sayılabilir.

Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve düzeltme eki işlemlerini uygulamanın sonucunu temsil eder.

### <a name="resource-example"></a>Kaynak örneği

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>JSON Patch örneği

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

Önceki JSON 'da:

* `op`Özelliği, işlem türünü gösterir.
* `path`Özelliği güncelleştirilecek öğeyi gösterir.
* `value`Özelliği yeni değeri sağlar.

### <a name="resource-after-patch"></a>Düzeltme ekiyle sonra kaynak

Önceki JSON Patch belgesi uygulandıktan sonra kaynak şu şekildedir:

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Bir kaynak için bir JSON Patch belgesi uygulanarak yapılan değişiklikler atomik. Listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.

## <a name="path-syntax"></a>Yol sözdizimi

Bir işlem nesnesinin [Path](https://tools.ietf.org/html/rfc6901) özelliği düzeyler arasında eğik çizgi içeriyor. Örneğin, `"/address/zipCode"`.

Sıfır tabanlı dizinler, dizi öğelerini belirtmek için kullanılır. Dizinin ilk öğesi `addresses` `/addresses/0` . `add`Bir dizinin sonuna kadar, `-` Dizin numarası yerine bir tire () kullanın: `/addresses/-` .

### <a name="operations"></a>Operations

Aşağıdaki tabloda, [JSON Patch belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlanan desteklenen işlemler gösterilmektedir:

|Çalışma  | Notlar |
|-----------|--------------------------------|
| `add`     | Bir özellik veya dizi öğesi ekleyin. Var olan özellik için: set değeri.|
| `remove`  | Bir özellik veya dizi öğesi kaldırın. |
| `replace` | Aynı `remove` konumdaki ve sonrasında aynı `add` . |
| `move`    | Kaynaktaki `remove` `add` değeri kullanarak kaynağından sonra hedefle aynı. |
| `copy`    | `add`Kaynaktaki değeri kullanarak hedefle aynı olacak şekilde aynı. |
| `test`    | Değer: belirtilmişse başarı durum kodu döndürür `path` `value` .|

## <a name="json-patch-in-aspnet-core"></a>ASP.NET Core JSON yaması

JSON düzeltme ekinin ASP.NET Core uygulanması [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sunulmaktadır.

## <a name="action-method-code"></a>Eylem yöntemi kodu

Bir API denetleyicisinde, JSON yaması için bir eylem yöntemi:

* , Özniteliğiyle açıklama eklenir `HttpPatch` .
* `JsonPatchDocument<T>`, Genellikle ile kabul eder `[FromBody]` .
* `ApplyTo`Değişiklikleri uygulamak için düzeltme eki belgesindeki çağrılar.

İşte bir örnek:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Örnek uygulamadaki Bu kod aşağıdaki modelle birlikte kullanılabilir `Customer` :

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Örnek eylem yöntemi:

* Bir oluşturur `Customer` .
* Düzeltme ekini uygular.
* Yanıtın gövdesinde sonucu döndürür.

Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve düzeltme ekini uyguladıktan sonra veritabanını güncelleştirir.

### <a name="model-state"></a>Model durumu

Önceki eylem yöntemi örneği, `ApplyTo` parametrelerinden biri olarak model durumunu alan aşırı yüklemesini çağırır. Bu seçenekle, yanıtlardan hata iletileri alabilirsiniz. Aşağıdaki örnekte bir işlem için 400 Hatalı Istek yanıtının gövdesi gösterilmektedir `test` :

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dinamik nesneler

Aşağıdaki eylem yöntemi örneği, dinamik bir nesneye nasıl düzeltme eki uygulanacağını gösterir:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Ekleme işlemi

* `path`Bir dizi öğesine işaret ediyorsa: tarafından belirtiden önce yeni bir öğe ekler `path` .
* `path`Bir özelliğe işaret ediyorsa: özellik değerini ayarlar.
* `path`Varolmayan bir konuma işaret ediyorsa:
  * Yama yapılacak kaynak dinamik bir nesnedir: bir özellik ekler.
  * Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.

Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Order` dizinin sonuna bir nesnesi ekler `Orders` .

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Kaldırma işlemi

* `path`Bir dizi öğesine işaret ediyorsa: öğesini kaldırır.
* `path`Bir özelliğe işaret ediyorsa:
  * Yayama kaynağı dinamik bir nesne ise: özelliğini kaldırır.
  * Yama yapılacak kaynak statik bir nesnese:
    * Özellik null atanabilir ise: null olarak ayarlar.
    * Özellik null atanamaz ise, olarak ayarlar `default<T>` .

Aşağıdaki örnek düzeltme eki belgesi `CustomerName` null ve siler olarak ayarlanır `Orders[0]` :

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Değiştirme işlemi

Bu işlem, bir `remove` sonrasında bir ile aynıdır `add` .

Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Orders[0]` Yeni bir `Order` nesneyle değiştirir:

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Taşıma işlemi

* `path`Bir dizi öğesinin işaret ediyorsa: öğesini öğesinin `from` konumuna kopyalar `path` , sonra `remove` öğesi üzerinde bir işlem çalıştırır `from` .
* `path`Bir özelliğe işaret ediyorsa: özelliğin değerini özelliğine kopyalar `from` `path` ve sonra `remove` özelliği üzerinde bir işlem çalıştırır `from` .
* `path`Varolmayan bir özelliğe işaret ediyorsa:
  * Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.
  * Yaması gereken kaynak dinamik bir nesnedir: `from` özelliği tarafından belirtilen konuma kopyalar `path` , sonra `remove` özellik üzerinde bir işlem çalıştırır `from` .

Aşağıdaki örnek düzeltme eki belgesi:

* Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .
* `Orders[0].OrderName`Null olarak ayarlar.
* `Orders[1]`Öncesine gider `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Kopyalama işlemi

Bu işlem, `move` son adım olmadan işlem ile aynı şekilde aynıdır `remove` .

Aşağıdaki örnek düzeltme eki belgesi:

* Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .
* Daha önce bir kopyası `Orders[1]` ekler `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Test işlemi

Tarafından belirtilen konumdaki değer `path` içinde belirtilen değerden farklıysa `value` , istek başarısız olur. Bu durumda, yama belgesindeki diğer tüm işlemler başka bir şekilde başarılı olsa bile, tüm yama isteği başarısız olur.

İşlem, bir `test` eşzamanlılık çakışması olduğunda bir güncelleştirmeyi engellemek için yaygın olarak kullanılır.

Aşağıdaki örnek düzeltme eki belgesinin ilk değeri `CustomerName` "John" ise, test başarısız olursa hiçbir etkisi yoktur:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Kodu alma

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples). ([İndirme](xref:index#how-to-download-a-sample)).

Örneği test etmek için, uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:

* 'DEKI`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* HTTP yöntemi:`PATCH`
* Üst bilgi`Content-Type: application/json-patch+json`
* Gövde: *JSON proje klasöründen JSON Patch* belgesi örneklerinden birini kopyalayıp yapıştırın.

## <a name="additional-resources"></a>Ek kaynaklar

* [IETF RFC 5789 PATCH yöntemi belirtimi](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON Patch belirtimi](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON Patch yolu biçim belirtimi](https://tools.ietf.org/html/rfc6901)
* [JSON yama belgeleri](https://jsonpatch.com/). JSON yama belgeleri oluşturmak için kaynakların bağlantılarını içerir.
* [ASP.NET Core JSON Patch kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede, ASP.NET Core Web API 'sinde JSON Patch isteklerinin nasıl işleneceği açıklanır.

## <a name="patch-http-request-method"></a>PATCH HTTP istek yöntemi

PUT ve [Patch](https://tools.ietf.org/html/rfc5789) yöntemleri, var olan bir kaynağı güncelleştirmek için kullanılır. Bunlar arasındaki fark, PUT 'ın tüm kaynağı değiştirmesi, ancak düzeltme eki yalnızca değişiklikleri belirttiğinde.

## <a name="json-patch"></a>JSON yaması

[JSON yaması](https://tools.ietf.org/html/rfc6902) , bir kaynağa uygulanacak güncelleştirmelerin belirtilmesine yönelik bir biçimdir. JSON yama belgesinde bir dizi *işlem*vardır. Her işlem, bir dizi öğesi ekleme veya bir özellik değerini değiştirme gibi belirli bir değişiklik türünü tanımlar.

Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve düzeltme eki işlemlerini uygulamanın sonucunu temsil eder.

### <a name="resource-example"></a>Kaynak örneği

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>JSON Patch örneği

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

Önceki JSON 'da:

* `op`Özelliği, işlem türünü gösterir.
* `path`Özelliği güncelleştirilecek öğeyi gösterir.
* `value`Özelliği yeni değeri sağlar.

### <a name="resource-after-patch"></a>Düzeltme ekiyle sonra kaynak

Önceki JSON Patch belgesi uygulandıktan sonra kaynak şu şekildedir:

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Bir kaynak için bir JSON Patch belgesi uygulanarak yapılan değişiklikler atomik: listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.

## <a name="path-syntax"></a>Yol sözdizimi

Bir işlem nesnesinin [Path](https://tools.ietf.org/html/rfc6901) özelliği düzeyler arasında eğik çizgi içeriyor. Örneğin, `"/address/zipCode"`.

Sıfır tabanlı dizinler, dizi öğelerini belirtmek için kullanılır. Dizinin ilk öğesi `addresses` `/addresses/0` . `add`Bir dizinin sonuna kadar, dizin numarası yerine bir tire (-) kullanın: `/addresses/-` .

### <a name="operations"></a>Operations

Aşağıdaki tabloda, [JSON Patch belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlanan desteklenen işlemler gösterilmektedir:

|Çalışma  | Notlar |
|-----------|--------------------------------|
| `add`     | Bir özellik veya dizi öğesi ekleyin. Var olan özellik için: set değeri.|
| `remove`  | Bir özellik veya dizi öğesi kaldırın. |
| `replace` | Aynı `remove` konumdaki ve sonrasında aynı `add` . |
| `move`    | Kaynaktaki `remove` `add` değeri kullanarak kaynağından sonra hedefle aynı. |
| `copy`    | `add`Kaynaktaki değeri kullanarak hedefle aynı olacak şekilde aynı. |
| `test`    | Değer: belirtilmişse başarı durum kodu döndürür `path` `value` .|

## <a name="jsonpatch-in-aspnet-core"></a>ASP.NET Core 'de JsonPatch

JSON düzeltme ekinin ASP.NET Core uygulanması [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sunulmaktadır. Paket, [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) metapackage 'e dahildir.

## <a name="action-method-code"></a>Eylem yöntemi kodu

Bir API denetleyicisinde, JSON yaması için bir eylem yöntemi:

* , Özniteliğiyle açıklama eklenir `HttpPatch` .
* `JsonPatchDocument<T>`, Genellikle ile kabul eder `[FromBody]` .
* `ApplyTo`Değişiklikleri uygulamak için düzeltme eki belgesindeki çağrılar.

İşte bir örnek:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Örnek uygulamadaki Bu kod aşağıdaki modelle birlikte kullanılabilir `Customer` .

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Örnek eylem yöntemi:

* Bir oluşturur `Customer` .
* Düzeltme ekini uygular.
* Yanıtın gövdesinde sonucu döndürür.

 Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve düzeltme ekini uyguladıktan sonra veritabanını güncelleştirir.

### <a name="model-state"></a>Model durumu

Önceki eylem yöntemi örneği, `ApplyTo` parametrelerinden biri olarak model durumunu alan aşırı yüklemesini çağırır. Bu seçenekle, yanıtlardan hata iletileri alabilirsiniz. Aşağıdaki örnekte bir işlem için 400 Hatalı Istek yanıtının gövdesi gösterilmektedir `test` :

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dinamik nesneler

Aşağıdaki eylem yöntemi örneği, dinamik bir nesne için bir düzeltme ekinin nasıl uygulanacağını gösterir.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Ekleme işlemi

* `path`Bir dizi öğesine işaret ediyorsa: tarafından belirtiden önce yeni bir öğe ekler `path` .
* `path`Bir özelliğe işaret ediyorsa: özellik değerini ayarlar.
* `path`Varolmayan bir konuma işaret ediyorsa:
  * Yama yapılacak kaynak dinamik bir nesnedir: bir özellik ekler.
  * Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.

Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Order` dizinin sonuna bir nesnesi ekler `Orders` .

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Kaldırma işlemi

* `path`Bir dizi öğesine işaret ediyorsa: öğesini kaldırır.
* `path`Bir özelliğe işaret ediyorsa:
  * Yayama kaynağı dinamik bir nesne ise: özelliğini kaldırır.
  * Yama yapılacak kaynak statik bir nesnese:
    * Özellik null atanabilir ise: null olarak ayarlar.
    * Özellik null atanamaz ise, olarak ayarlar `default<T>` .

Aşağıdaki örnek düzeltme eki belgesi `CustomerName` null ve siler olarak ayarlanır `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Değiştirme işlemi

Bu işlem, bir `remove` sonrasında bir ile aynıdır `add` .

Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Orders[0]` Yeni bir `Order` nesneyle değiştirir.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Taşıma işlemi

* `path`Bir dizi öğesinin işaret ediyorsa: öğesini öğesinin `from` konumuna kopyalar `path` , sonra `remove` öğesi üzerinde bir işlem çalıştırır `from` .
* `path`Bir özelliğe işaret ediyorsa: özelliğin değerini özelliğine kopyalar `from` `path` ve sonra `remove` özelliği üzerinde bir işlem çalıştırır `from` .
* `path`Varolmayan bir özelliğe işaret ediyorsa:
  * Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.
  * Yaması gereken kaynak dinamik bir nesnedir: `from` özelliği tarafından belirtilen konuma kopyalar `path` , sonra `remove` özellik üzerinde bir işlem çalıştırır `from` .

Aşağıdaki örnek düzeltme eki belgesi:

* Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .
* `Orders[0].OrderName`Null olarak ayarlar.
* `Orders[1]`Öncesine gider `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Kopyalama işlemi

Bu işlem, `move` son adım olmadan işlem ile aynı şekilde aynıdır `remove` .

Aşağıdaki örnek düzeltme eki belgesi:

* Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .
* Daha önce bir kopyası `Orders[1]` ekler `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Test işlemi

Tarafından belirtilen konumdaki değer `path` içinde belirtilen değerden farklıysa `value` , istek başarısız olur. Bu durumda, yama belgesindeki diğer tüm işlemler başka bir şekilde başarılı olsa bile, tüm yama isteği başarısız olur.

İşlem, bir `test` eşzamanlılık çakışması olduğunda bir güncelleştirmeyi engellemek için yaygın olarak kullanılır.

Aşağıdaki örnek düzeltme eki belgesinin ilk değeri `CustomerName` "John" ise, test başarısız olursa hiçbir etkisi yoktur:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Kodu alma

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([İndirme](xref:index#how-to-download-a-sample)).

Örneği test etmek için, uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:

* 'DEKI`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* HTTP yöntemi:`PATCH`
* Üst bilgi`Content-Type: application/json-patch+json`
* Gövde: *JSON proje klasöründen JSON Patch* belgesi örneklerinden birini kopyalayıp yapıştırın.

## <a name="additional-resources"></a>Ek kaynaklar

* [IETF RFC 5789 PATCH yöntemi belirtimi](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON Patch belirtimi](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON Patch yolu biçim belirtimi](https://tools.ietf.org/html/rfc6901)
* [JSON yama belgeleri](https://jsonpatch.com/). JSON yama belgeleri oluşturmak için kaynakların bağlantılarını içerir.
* [ASP.NET Core JSON Patch kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
