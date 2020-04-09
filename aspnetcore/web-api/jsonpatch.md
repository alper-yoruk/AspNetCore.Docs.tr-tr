---
title: JsonPatch in ASP.NET Core web API
author: rick-anderson
description: ASP.NET Core web API'sinde JSON Yama isteklerini nasıl işleyeceğinizi öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
uid: web-api/jsonpatch
ms.openlocfilehash: be4115e870dac818aeb6b1e65ddfb21e89d9cf25
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625876"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch in ASP.NET Core web API

Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Kirk Larkin](https://github.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

Bu makalede, ASP.NET Core web API'de JSON Yama isteklerinin nasıl işleyeceğini açıklanmaktadır.

## <a name="package-installation"></a>Paket kurulumu

Uygulamanızda JSON Patch desteğini etkinleştirmek için aşağıdaki adımları tamamlayın:

1. [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet paketini yükleyin.
1. Aramak <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>için projenin `Startup.ConfigureServices` yöntemini güncelleştirin. Örneğin:

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

`AddNewtonsoftJson`MVC hizmet kayıt yöntemleri ile uyumludur:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a>JSON Yama, AddNewtonsoftJson ve System.Text.Json

`AddNewtonsoftJson``System.Text.Json` **tüm** JSON içeriğini biçimlendirmek için kullanılan -tabanlı giriş ve çıktı formatters değiştirir. JSON Patch için destek `Newtonsoft.Json`eklemek için, diğer konuları değiştirmeden bırakırken, projenin `Startup.ConfigureServices` yöntemini aşağıdaki gibi güncelleştirin:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

Önceki kod `Microsoft.AspNetCore.Mvc.NewtonsoftJson` paketi ve aşağıdaki `using` ifadeleri gerektirir:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>PATCH HTTP istek yöntemi

PUT ve [PATCH](https://tools.ietf.org/html/rfc5789) yöntemleri varolan bir kaynağı güncelleştirmek için kullanılır. Bunlar arasındaki fark, PUT'un tüm kaynağın yerini alması, PATCH ise yalnızca değişiklikleri belirtir.

## <a name="json-patch"></a>JSON Yama

[JSON Yama,](https://tools.ietf.org/html/rfc6902) bir kaynağa uygulanacak güncelleştirmeleri belirtmek için bir biçimdir. JSON Yama belgesinde bir dizi *işlem*vardır. Her işlem belirli bir değişiklik türünü tanımlar. Bu tür değişikliklere örnek olarak bir dizi öğesi eklemek veya bir özellik değerini değiştirmek verilebilir.

Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON Yama belgesini ve Yama işlemlerini uygulama sonucunu temsil emektedir.

### <a name="resource-example"></a>Kaynak örneği

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>JSON yama örneği

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

Önceki JSON olarak:

* Özellik, `op` işlem türünü gösterir.
* Özellik, `path` güncelleştirilen öğeyi gösterir.
* Özellik `value` yeni değeri sağlar.

### <a name="resource-after-patch"></a>Yama sonrası kaynak

Yukarıdaki JSON Yama belgesini uyguladıktan sonra kaynak aşağıda vesiyonu aşağıda veda edebilirsiniz:

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

Bir kaynağa JSON Yama belgesi uygulanarak yapılan değişiklikler atomiktir. Listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.

## <a name="path-syntax"></a>Yol sözdizimi

İşlem nesnesinin [yol](https://tools.ietf.org/html/rfc6901) özelliği düzeyleri arasında kesikler vardır. Örneğin, `"/address/zipCode"`.

Dizi öğelerini belirtmek için sıfır tabanlı dizinler kullanılır. `addresses` Dizinin ilk öğesi `/addresses/0`. Dizinin `add` sonuna kadar, dizin numarası yerine`-`tire ( ) `/addresses/-`kullanın: .

### <a name="operations"></a>İşlemler

Aşağıdaki tablo, [JSON Yama belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlandığı şekilde desteklenen işlemleri gösterir:

|İşlem  | Notlar |
|-----------|--------------------------------|
| `add`     | Bir özellik veya dizi öğesi ekleyin. Varolan özellik için: değer ayarlayın.|
| `remove`  | Bir özellik veya dizi öğesini kaldırın. |
| `replace` | Aynı `remove` yerde `add` takip edildiği gibi. |
| `move`    | Kaynaktan `remove` gelen değeri `add` kullanarak hedef tarafından takip aynı. |
| `copy`    | Kaynaktan `add` gelen değeri kullanarak hedefe gitmekle aynı. |
| `test`    | `path` Değer = sağlanan ise başarı `value`durum kodunu döndürün .|

## <a name="json-patch-in-aspnet-core"></a>JSON Patch ASP.NET Çekirdek

JSON Patch'in ASP.NET Core uygulaması [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sağlanır.

## <a name="action-method-code"></a>Eylem yöntemi kodu

Bir API denetleyicisinde, JSON Patch için bir eylem yöntemi:

* Öznitelik ile `HttpPatch` açıklamalı.
* Genellikle `JsonPatchDocument<T>` `[FromBody]`.
* Değişiklikleri `ApplyTo` uygulamak için yama belgesini çağırır.

Bir örneği aşağıda verilmiştir:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Örnek uygulamadan bu kod aşağıdaki `Customer` modelle çalışır:

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Örnek eylem yöntemi:

* Bir `Customer`.
* Yama uygular.
* Yanıtı gövdesinde sonucu döndürür.

Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve yama uyguladıktan sonra veritabanını günceller.

### <a name="model-state"></a>Model durumu

Önceki eylem yöntemi örneği, model `ApplyTo` durumunu parametrelerinden biri olarak alan aşırı yüklemeyi çağırır. Bu seçenekle, yanıtlarda hata iletileri alabilirsiniz. Aşağıdaki örnek, bir `test` işlem için 400 Kötü İstek yanıtının gövdesini gösterir:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dinamik nesneler

Aşağıdaki eylem yöntemi örneği, dinamik bir nesneye yamanın nasıl uygulanacağı gösterilmektedir:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Ekleme işlemi

* Bir `path` dizi öğesine işaret ederse: `path`'' tarafından belirtilenöğeden önce yeni öğe ekler.
* Bir `path` özelliği işaret ederse: özellik değerini ayarlar.
* Var `path` olmayan bir konuma işaret ediyorsa:
  * Yama için kaynak dinamik bir nesne ise: bir özellik ekler.
  * Yama için kaynak statik bir nesne ise: istek başarısız olur.

Aşağıdaki örnek yama belgesi, `CustomerName` `Orders` dizinin `Order` değerini ayarlar ve dizinin sonuna bir nesne ekler.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Kaldırma işlemi

* Bir `path` dizi öğesini işaret ederse: öğeyi kaldırır.
* Bir `path` özelliği işaret ederse:
  * Yama için kaynak dinamik bir nesne ise: özelliği kaldırır.
  * Yama için kaynak statik bir nesne ise:
    * Özellik nullable ise: null ayarlar.
    * Özellik nullable değilse, `default<T>`ayarlar.

Aşağıdaki örnek yama `CustomerName` belge null `Orders[0]`ayarlar ve siler:

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Değiştirme işlemi

Bu işlem işlevsel olarak bir `remove` `add`takip ile aynıdır.

Aşağıdaki örnek yama belgesi `CustomerName` değerini ayarlar `Orders[0]`ve `Order` yerine yeni bir nesne alır:

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Taşıma işlemi

* Bir `path` dizi öğesine işaret `from` ediyorsa: `path` öğenin konumuna `remove` öğekopyalar, sonra `from` öğe üzerinde bir işlem çalıştırır.
* Bir `path` özelliği işaret ediyorsa: `from` özelliğin `path` değerini mülke `remove` kopyalar, `from` sonra özellik üzerinde bir işlem çalıştırır.
* Var `path` olmayan bir özelliğe işaret ediyorsa:
  * Yama için kaynak statik bir nesne ise: istek başarısız olur.
  * Yama için kaynak dinamik bir nesne `from` ise: tarafından `path`belirtilen konuma `remove` özelliği `from` kopyalar, sonra özellik üzerinde bir işlem çalıştırır.

Aşağıdaki örnek yama belgesi:

* Değerini `Orders[0].OrderName` `CustomerName`kopyalar.
* Null `Orders[0].OrderName` ayarlar.
* Öncesine `Orders[1]` `Orders[0]`taşınır.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Kopyalama işlemi

Bu işlem işlevsel olarak son `move` `remove` adım olmadan bir işlem olarak aynıdır.

Aşağıdaki örnek yama belgesi:

* Değerini `Orders[0].OrderName` `CustomerName`kopyalar.
* Önce'nin `Orders[1]` `Orders[0]`bir kopyasını ekler.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Test işlemi

Belirtilen konumdaki değer, `path` sağlanan değerden `value`farklıysa, istek başarısız olur. Bu durumda, yama belgesindeki diğer tüm işlemler başarılı olsa bile tüm PATCH isteği başarısız olur.

İşlem `test` genellikle eşzamanlılık çakışması olduğunda bir güncelleştirmeyi önlemek için kullanılır.

Test başarısız olduğundan, aşağıdaki örnek yama `CustomerName` belgesinin ilk değeri "John" ise hiçbir etkisi yoktur:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Kodu alma

[Örnek kodu görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples) ([Nasıl indirilir).](xref:index#how-to-download-a-sample)

Örneği test etmek için uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:

* Url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* HTTP yöntemi:`PATCH`
* Üstbilgi:`Content-Type: application/json-patch+json`
* Gövde: *JSON* proje klasöründen JSON yama belge örneklerinden birini kopyalayın ve yapıştırın.

## <a name="additional-resources"></a>Ek kaynaklar

* [IETF RFC 5789 PATCH yöntemi belirtimi](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON Patch belirtimi](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON Patch yol formatı spec](https://tools.ietf.org/html/rfc6901)
* [JSON Patch belgeleri](https://jsonpatch.com/). JSON Patch belgeleri oluşturmak için kaynaklara bağlantılar içerir.
* [ASP.NET Core JSON Patch kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede, ASP.NET Core web API'de JSON Yama isteklerinin nasıl işleyeceğini açıklanmaktadır.

## <a name="patch-http-request-method"></a>PATCH HTTP istek yöntemi

PUT ve [PATCH](https://tools.ietf.org/html/rfc5789) yöntemleri varolan bir kaynağı güncelleştirmek için kullanılır. Bunlar arasındaki fark, PUT'un tüm kaynağın yerini alması, PATCH ise yalnızca değişiklikleri belirtir.

## <a name="json-patch"></a>JSON Yama

[JSON Yama,](https://tools.ietf.org/html/rfc6902) bir kaynağa uygulanacak güncelleştirmeleri belirtmek için bir biçimdir. JSON Yama belgesinde bir dizi *işlem*vardır. Her işlem, bir dizi öğesi ekleme veya bir özellik değerini değiştirme gibi belirli bir değişiklik türünü tanımlar.

Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve yama işlemlerini uygulama sonucunu temsil emektedir.

### <a name="resource-example"></a>Kaynak örneği

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>JSON yama örneği

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

Önceki JSON olarak:

* Özellik, `op` işlem türünü gösterir.
* Özellik, `path` güncelleştirilen öğeyi gösterir.
* Özellik `value` yeni değeri sağlar.

### <a name="resource-after-patch"></a>Yama sonrası kaynak

Yukarıdaki JSON Yama belgesini uyguladıktan sonra kaynak aşağıda vesiyonu aşağıda veda edebilirsiniz:

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

Bir kaynağa JSON Yama belgesi uygulanarak yapılan değişiklikler atomiktir: listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.

## <a name="path-syntax"></a>Yol sözdizimi

İşlem nesnesinin [yol](https://tools.ietf.org/html/rfc6901) özelliği düzeyleri arasında kesikler vardır. Örneğin, `"/address/zipCode"`.

Dizi öğelerini belirtmek için sıfır tabanlı dizinler kullanılır. `addresses` Dizinin ilk öğesi `/addresses/0`. Dizinin `add` sonuna kadar dizin numarası yerine tire (-) kullanın: `/addresses/-`.

### <a name="operations"></a>İşlemler

Aşağıdaki tablo, [JSON Yama belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlandığı şekilde desteklenen işlemleri gösterir:

|İşlem  | Notlar |
|-----------|--------------------------------|
| `add`     | Bir özellik veya dizi öğesi ekleyin. Varolan özellik için: değer ayarlayın.|
| `remove`  | Bir özellik veya dizi öğesini kaldırın. |
| `replace` | Aynı `remove` yerde `add` takip edildiği gibi. |
| `move`    | Kaynaktan `remove` gelen değeri `add` kullanarak hedef tarafından takip aynı. |
| `copy`    | Kaynaktan `add` gelen değeri kullanarak hedefe gitmekle aynı. |
| `test`    | `path` Değer = sağlanan ise başarı `value`durum kodunu döndürün .|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch ASP.NET Core içinde

JSON Patch'in ASP.NET Core uygulaması [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sağlanır. Paket [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) meta paketine dahildir.

## <a name="action-method-code"></a>Eylem yöntemi kodu

Bir API denetleyicisinde, JSON Patch için bir eylem yöntemi:

* Öznitelik ile `HttpPatch` açıklamalı.
* Genellikle `JsonPatchDocument<T>` `[FromBody]`.
* Değişiklikleri `ApplyTo` uygulamak için yama belgesini çağırır.

Bir örneği aşağıda verilmiştir:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Örnek uygulamadan alınan bu kod `Customer` aşağıdaki modelle çalışır.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Örnek eylem yöntemi:

* Bir `Customer`.
* Yama uygular.
* Yanıtı gövdesinde sonucu döndürür.

 Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve yama uyguladıktan sonra veritabanını günceller.

### <a name="model-state"></a>Model durumu

Önceki eylem yöntemi örneği, model `ApplyTo` durumunu parametrelerinden biri olarak alan aşırı yüklemeyi çağırır. Bu seçenekle, yanıtlarda hata iletileri alabilirsiniz. Aşağıdaki örnek, bir `test` işlem için 400 Kötü İstek yanıtının gövdesini gösterir:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dinamik nesneler

Aşağıdaki eylem yöntemi örneği, dinamik bir nesneye yamanın nasıl uygulanacağı gösterilmektedir.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Ekleme işlemi

* Bir `path` dizi öğesine işaret ederse: `path`'' tarafından belirtilenöğeden önce yeni öğe ekler.
* Bir `path` özelliği işaret ederse: özellik değerini ayarlar.
* Var `path` olmayan bir konuma işaret ediyorsa:
  * Yama için kaynak dinamik bir nesne ise: bir özellik ekler.
  * Yama için kaynak statik bir nesne ise: istek başarısız olur.

Aşağıdaki örnek yama belgesi, `CustomerName` `Orders` dizinin `Order` değerini ayarlar ve dizinin sonuna bir nesne ekler.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Kaldırma işlemi

* Bir `path` dizi öğesini işaret ederse: öğeyi kaldırır.
* Bir `path` özelliği işaret ederse:
  * Yama için kaynak dinamik bir nesne ise: özelliği kaldırır.
  * Yama için kaynak statik bir nesne ise:
    * Özellik nullable ise: null ayarlar.
    * Özellik nullable değilse, `default<T>`ayarlar.

Aşağıdaki örnek yama `CustomerName` belgesi null `Orders[0]`ayarlar ve siler.

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Değiştirme işlemi

Bu işlem işlevsel olarak bir `remove` `add`takip ile aynıdır.

Aşağıdaki örnek yama belgesi `CustomerName` değerini ayarlar `Orders[0]`ve `Order` yerine yeni bir nesne alır.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Taşıma işlemi

* Bir `path` dizi öğesine işaret `from` ediyorsa: `path` öğenin konumuna `remove` öğekopyalar, sonra `from` öğe üzerinde bir işlem çalıştırır.
* Bir `path` özelliği işaret ediyorsa: `from` özelliğin `path` değerini mülke `remove` kopyalar, `from` sonra özellik üzerinde bir işlem çalıştırır.
* Var `path` olmayan bir özelliğe işaret ediyorsa:
  * Yama için kaynak statik bir nesne ise: istek başarısız olur.
  * Yama için kaynak dinamik bir nesne `from` ise: tarafından `path`belirtilen konuma `remove` özelliği `from` kopyalar, sonra özellik üzerinde bir işlem çalıştırır.

Aşağıdaki örnek yama belgesi:

* Değerini `Orders[0].OrderName` `CustomerName`kopyalar.
* Null `Orders[0].OrderName` ayarlar.
* Öncesine `Orders[1]` `Orders[0]`taşınır.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Kopyalama işlemi

Bu işlem işlevsel olarak son `move` `remove` adım olmadan bir işlem olarak aynıdır.

Aşağıdaki örnek yama belgesi:

* Değerini `Orders[0].OrderName` `CustomerName`kopyalar.
* Önce'nin `Orders[1]` `Orders[0]`bir kopyasını ekler.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Test işlemi

Belirtilen konumdaki değer, `path` sağlanan değerden `value`farklıysa, istek başarısız olur. Bu durumda, yama belgesindeki diğer tüm işlemler başarılı olsa bile tüm PATCH isteği başarısız olur.

İşlem `test` genellikle eşzamanlılık çakışması olduğunda bir güncelleştirmeyi önlemek için kullanılır.

Test başarısız olduğundan, aşağıdaki örnek yama `CustomerName` belgesinin ilk değeri "John" ise hiçbir etkisi yoktur:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Kodu alma

[Örnek kodu görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2) ([Nasıl indirilir).](xref:index#how-to-download-a-sample)

Örneği test etmek için uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:

* Url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* HTTP yöntemi:`PATCH`
* Üstbilgi:`Content-Type: application/json-patch+json`
* Gövde: *JSON* proje klasöründen JSON yama belge örneklerinden birini kopyalayın ve yapıştırın.

## <a name="additional-resources"></a>Ek kaynaklar

* [IETF RFC 5789 PATCH yöntemi belirtimi](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON Patch belirtimi](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON Patch yol formatı spec](https://tools.ietf.org/html/rfc6901)
* [JSON Patch belgeleri](https://jsonpatch.com/). JSON Patch belgeleri oluşturmak için kaynaklara bağlantılar içerir.
* [ASP.NET Core JSON Patch kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
