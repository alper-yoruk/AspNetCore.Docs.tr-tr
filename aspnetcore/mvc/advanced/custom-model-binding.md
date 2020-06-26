---
title: ASP.NET Core özel model bağlama
author: ardalis
description: Model bağlamanın, denetleyici eylemlerinin ASP.NET Core doğrudan model türleriyle nasıl çalışmasına izin verdiğini öğrenin.
ms.author: riande
ms.date: 01/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 54080191d76df674444019d43180a7f9d84b471c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403644"
---
# <a name="custom-model-binding-in-aspnet-core"></a>ASP.NET Core özel model bağlama

::: moniker range=">= aspnetcore-3.0"

[Steve Smith](https://ardalis.com/) ve [Kirk larkaya](https://twitter.com/serpent5) tarafından

Model bağlama, denetleyici eylemlerinin HTTP istekleri yerine model türleriyle doğrudan (Yöntem bağımsız değişkenleri olarak geçirilir) çalışmasına izin verir. Gelen istek verileri ve uygulama modelleri arasındaki eşleme, model ciltleri tarafından işlenir. Geliştiriciler, özel model ciltlerinizi uygulayarak yerleşik model bağlama işlevini genişletebilir (genellikle kendi sağlayıcınızı yazmanız gerekmez).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Varsayılan model Ciltçi sınırlamaları

Varsayılan model ciltleri ortak .NET Core veri türlerinin çoğunu destekler ve çoğu geliştiricilerin ihtiyaçlarını karşılamaları gerekir. İstek içinden doğrudan model türlerine metin tabanlı giriş bağlamayı bekler. Bağlamayı bağlamadan önce girişi dönüştürmeniz gerekebilir. Örneğin, model verilerini aramak için kullanılabilecek bir anahtarınız olduğunda. Anahtar temelinde veri getirmek için özel bir model Bağlayıcısı kullanabilirsiniz.

## <a name="model-binding-review"></a>Model bağlama incelemesi

Model bağlama, üzerinde çalıştığı türler için belirli tanımları kullanır. *Basit bir tür* , girişte tek bir dizeden dönüştürülür. *Karmaşık bir tür* birden çok giriş değerinden dönüştürülür. Framework, a 'nın varlığına göre farkı belirler `TypeConverter` . `string`  ->  Dış kaynak gerektirmeyen basit bir eşlemeye sahipseniz bir tür dönüştürücüsü oluşturmanız önerilir `SomeType` .

Kendi özel model cilinkini oluşturmadan önce, mevcut model ciltçileri 'nin nasıl uygulandığını gözden geçirdik. <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>Base64 ile kodlanmış dizeleri bayt dizilerine dönüştürmek için kullanılabilecek ' i göz önünde bulundurun. Bayt dizileri genellikle dosya veya veritabanı blobu alanları olarak depolanır.

### <a name="working-with-the-bytearraymodelbinder"></a>Bytearraymodelciltçi ile çalışma

İkili verileri temsil etmek için Base64 kodlamalı dizeler kullanılabilir. Örneğin, bir görüntü dize olarak kodlanır. Örnek, [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt)Base64 kodlamalı dize olarak bir görüntü içerir.

ASP.NET Core MVC, Base64 kodlamalı bir dize alabilir ve `ByteArrayModelBinder` bunu bir bayt dizisine dönüştürmek için kullanabilir. <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> `byte[]` Bağımsız değişkenleri şu şekilde eşler `ByteArrayModelBinder` :

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

Kendi özel model cilinkini oluştururken kendi `IModelBinderProvider` türünü uygulayabilir veya kullanabilirsiniz <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .

Aşağıdaki örnek, bir `ByteArrayModelBinder` base64 kodlu dizeyi öğesine dönüştürmek `byte[]` ve sonucu bir dosyaya kaydetmek için nasıl kullanılacağını gösterir:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

[Postman](https://www.getpostman.com/)gibi bir araç kullanarak bu API yöntemine Base64 kodlamalı bir dize gönderebilirsiniz:

![Postman](custom-model-binding/images/postman.png "Postman")

Bağlayıcı, istek verilerini uygun şekilde adlandırılmış özelliklere veya bağımsız değişkenlere bağlayabildiğinden, model bağlama başarılı olur. Aşağıdaki örnek, `ByteArrayModelBinder` bir görünüm modeliyle nasıl kullanılacağını gösterir:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a>Özel model Ciltçi örneği

Bu bölümde, özel bir model cildi uygulayacağız:

- Gelen istek verilerini kesin belirlenmiş anahtar bağımsız değişkenlerine dönüştürür.
- İlişkili varlığı getirmek için Entity Framework Core kullanır.
- İlişkili varlığı eylem yöntemine bir bağımsız değişken olarak geçirir.

Aşağıdaki örnek `ModelBinder` modeldeki özniteliğini kullanır `Author` :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

Yukarıdaki kodda `ModelBinder` öznitelik, `IModelBinder` eylem parametrelerini bağlamak için kullanılması gereken türünü belirtir `Author` .

Aşağıdaki `AuthorEntityBinder` sınıf, `Author` Entity Framework Core ve ' i kullanarak bir veri kaynağından varlığı getirerek bir parametreyi bağlar `authorId` :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> Önceki `AuthorEntityBinder` sınıf özel bir model cildi göstermeye yöneliktir. Sınıfı, bir arama senaryosu için en iyi yöntemleri göstermeye yönelik değildir. Arama için, veritabanını bağlayın `authorId` ve bir eylem yöntemine sorgulayın. Bu yaklaşım, model bağlama başarısızlıklarını `NotFound` durumlardan ayırır.

Aşağıdaki kod, `AuthorEntityBinder` bir eylem yönteminin nasıl kullanılacağını gösterir:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

`ModelBinder`Özniteliği, `AuthorEntityBinder` varsayılan kuralları kullanmayan parametreleri uygulamak için kullanılabilir:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

Bu örnekte, bağımsız değişkenin adı varsayılan olmadığından `authorId` parametresi kullanılarak parametresi belirtilir `ModelBinder` . Hem denetleyici hem de eylem yöntemi, eylem yöntemindeki varlığı aramaya kıyasla basitleştirilmiştir. Entity Framework Core kullanarak yazarı getirme mantığı model cilde taşınır. Bu, modele bağlanan çeşitli yöntemlere sahip olduğunuzda büyük ölçüde basitleştirmesi olabilir `Author` .

`ModelBinder`Özniteliği, yalnızca bu tür veya eylem için belirli bir model cildi veya model adı belirtmek üzere tek tek model özelliklerine (ViewModel üzerinde gibi) veya eylem yöntemi parametrelerine uygulayabilirsiniz.

### <a name="implementing-a-modelbinderprovider"></a>ModelBinderProvider uygulama

Bir özniteliği uygulamak yerine, öğesini uygulayabilirsiniz `IModelBinderProvider` . Yerleşik çerçeve ciltçileri uygulanır. Cildin üzerinde çalıştığı türü belirttiğinizde, cildin kabul ettiği girişi **değil** , oluşturduğu bağımsız değişkenin türünü belirtirsiniz. Aşağıdaki Ciltçi sağlayıcısı ile birlikte kullanılabilir `AuthorEntityBinder` . MVC 'nin sağlayıcılar koleksiyonuna eklendiğinde, `ModelBinder` `Author` veya `Author` türü belirlenmiş parametrelerde özniteliğini kullanmanız gerekmez.

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Note: Yukarıdaki kod bir döndürür `BinderTypeModelBinder` . `BinderTypeModelBinder`Model ciltleri için bir fabrika işlevi görür ve bağımlılık ekleme (dı) sağlar. İçin `AuthorEntityBinder` dı 'nin EF Core erişimi gerekir. `BinderTypeModelBinder`Model Ciltçi 'nın dı 'den hizmet gerektirmesi durumunda kullanın.

Özel model Ciltçi sağlayıcısını kullanmak için, içine ekleyin `ConfigureServices` :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

Model ciltleri değerlendirilirken, sağlayıcı koleksiyonu sırayla incelenir. Bir cildi döndüren ilk sağlayıcı kullanılır. Sağlayıcınızı koleksiyonun sonuna eklemek, özel ciltçinin bir şansı olmadan önce yerleşik bir model cilde yol açabilir. Bu örnekte, özel sağlayıcı, eylem bağımsız değişkenleri için kullanıldığından emin olmak için koleksiyonun başlangıcına eklenir `Author` .

### <a name="polymorphic-model-binding"></a>Polimorfik model bağlama

Türetilmiş türlerin farklı modellerine bağlama polimorfik model bağlama olarak bilinir. İstek değeri, belirli türetilmiş model türüne bağlanması gerektiğinde polimorfik özel model bağlama gerekir. Polimorfik model bağlama:

* Tüm dillerle birlikte çalışmak üzere tasarlanan bir REST API için tipik değildir.
* , Bağlantılı modeller hakkında neden olmasını zorlaştırır.

Ancak, bir uygulama çok biçimli model bağlama gerektiriyorsa, bir uygulama aşağıdaki koda benzeyebilir:

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Öneriler ve en iyi uygulamalar

Özel model ciltleri:

- Durum kodları veya dönüş sonuçları ayarlanmamalıdır (örneğin, 404 bulunamadı). Model bağlama başarısız olursa, eylem yönteminin kendisi içindeki bir [eylem filtresinin](xref:mvc/controllers/filters) veya mantığının hata işlemesi gerekir.
- , Yinelenen kodu ve eylem yöntemlerinden çapraz kesme sorunlarını ortadan kaldırmak için en yararlı seçenektir.
- Genellikle bir dizeyi özel bir türe dönüştürmek için kullanılmamalıdır, <xref:System.ComponentModel.TypeConverter> genellikle daha iyi bir seçenektir.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

[Steve Smith](https://ardalis.com/) tarafından

Model bağlama, denetleyici eylemlerinin HTTP istekleri yerine model türleriyle doğrudan (Yöntem bağımsız değişkenleri olarak geçirilir) çalışmasına izin verir. Gelen istek verileri ve uygulama modelleri arasındaki eşleme, model ciltleri tarafından işlenir. Geliştiriciler, özel model ciltlerinizi uygulayarak yerleşik model bağlama işlevini genişletebilir (genellikle kendi sağlayıcınızı yazmanız gerekmez).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Varsayılan model Ciltçi sınırlamaları

Varsayılan model ciltleri ortak .NET Core veri türlerinin çoğunu destekler ve çoğu geliştiricilerin ihtiyaçlarını karşılamaları gerekir. İstek içinden doğrudan model türlerine metin tabanlı giriş bağlamayı bekler. Bağlamayı bağlamadan önce girişi dönüştürmeniz gerekebilir. Örneğin, model verilerini aramak için kullanılabilecek bir anahtarınız olduğunda. Anahtar temelinde veri getirmek için özel bir model Bağlayıcısı kullanabilirsiniz.

## <a name="model-binding-review"></a>Model bağlama incelemesi

Model bağlama, üzerinde çalıştığı türler için belirli tanımları kullanır. *Basit bir tür* , girişte tek bir dizeden dönüştürülür. *Karmaşık bir tür* birden çok giriş değerinden dönüştürülür. Framework, a 'nın varlığına göre farkı belirler `TypeConverter` . `string`  ->  Dış kaynak gerektirmeyen basit bir eşlemeye sahipseniz bir tür dönüştürücüsü oluşturmanız önerilir `SomeType` .

Kendi özel model cilinkini oluşturmadan önce, mevcut model ciltçileri 'nin nasıl uygulandığını gözden geçirdik. <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>Base64 ile kodlanmış dizeleri bayt dizilerine dönüştürmek için kullanılabilecek ' i göz önünde bulundurun. Bayt dizileri genellikle dosya veya veritabanı blobu alanları olarak depolanır.

### <a name="working-with-the-bytearraymodelbinder"></a>Bytearraymodelciltçi ile çalışma

İkili verileri temsil etmek için Base64 kodlamalı dizeler kullanılabilir. Örneğin, bir görüntü dize olarak kodlanır. Örnek, [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt)Base64 kodlamalı dize olarak bir görüntü içerir.

ASP.NET Core MVC, Base64 kodlamalı bir dize alabilir ve `ByteArrayModelBinder` bunu bir bayt dizisine dönüştürmek için kullanabilir. <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> `byte[]` Bağımsız değişkenleri şu şekilde eşler `ByteArrayModelBinder` :

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

Kendi özel model cilinkini oluştururken kendi `IModelBinderProvider` türünü uygulayabilir veya kullanabilirsiniz <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .

Aşağıdaki örnek, bir `ByteArrayModelBinder` base64 kodlu dizeyi öğesine dönüştürmek `byte[]` ve sonucu bir dosyaya kaydetmek için nasıl kullanılacağını gösterir:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

[Postman](https://www.getpostman.com/)gibi bir araç kullanarak bu API yöntemine Base64 kodlamalı bir dize gönderebilirsiniz:

![Postman](custom-model-binding/images/postman.png "Postman")

Bağlayıcı, istek verilerini uygun şekilde adlandırılmış özelliklere veya bağımsız değişkenlere bağlayabildiğinden, model bağlama başarılı olur. Aşağıdaki örnek, `ByteArrayModelBinder` bir görünüm modeliyle nasıl kullanılacağını gösterir:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Özel model Ciltçi örneği

Bu bölümde, özel bir model cildi uygulayacağız:

- Gelen istek verilerini kesin belirlenmiş anahtar bağımsız değişkenlerine dönüştürür.
- İlişkili varlığı getirmek için Entity Framework Core kullanır.
- İlişkili varlığı eylem yöntemine bir bağımsız değişken olarak geçirir.

Aşağıdaki örnek `ModelBinder` modeldeki özniteliğini kullanır `Author` :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

Yukarıdaki kodda `ModelBinder` öznitelik, `IModelBinder` eylem parametrelerini bağlamak için kullanılması gereken türünü belirtir `Author` .

Aşağıdaki `AuthorEntityBinder` sınıf, `Author` Entity Framework Core ve ' i kullanarak bir veri kaynağından varlığı getirerek bir parametreyi bağlar `authorId` :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> Önceki `AuthorEntityBinder` sınıf özel bir model cildi göstermeye yöneliktir. Sınıfı, bir arama senaryosu için en iyi yöntemleri göstermeye yönelik değildir. Arama için, veritabanını bağlayın `authorId` ve bir eylem yöntemine sorgulayın. Bu yaklaşım, model bağlama başarısızlıklarını `NotFound` durumlardan ayırır.

Aşağıdaki kod, `AuthorEntityBinder` bir eylem yönteminin nasıl kullanılacağını gösterir:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

`ModelBinder`Özniteliği, `AuthorEntityBinder` varsayılan kuralları kullanmayan parametreleri uygulamak için kullanılabilir:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

Bu örnekte, bağımsız değişkenin adı varsayılan olmadığından `authorId` parametresi kullanılarak parametresi belirtilir `ModelBinder` . Hem denetleyici hem de eylem yöntemi, eylem yöntemindeki varlığı aramaya kıyasla basitleştirilmiştir. Entity Framework Core kullanarak yazarı getirme mantığı model cilde taşınır. Bu, modele bağlanan çeşitli yöntemlere sahip olduğunuzda büyük ölçüde basitleştirmesi olabilir `Author` .

`ModelBinder`Özniteliği, yalnızca bu tür veya eylem için belirli bir model cildi veya model adı belirtmek üzere tek tek model özelliklerine (ViewModel üzerinde gibi) veya eylem yöntemi parametrelerine uygulayabilirsiniz.

### <a name="implementing-a-modelbinderprovider"></a>ModelBinderProvider uygulama

Bir özniteliği uygulamak yerine, öğesini uygulayabilirsiniz `IModelBinderProvider` . Yerleşik çerçeve ciltçileri uygulanır. Cildin üzerinde çalıştığı türü belirttiğinizde, cildin kabul ettiği girişi **değil** , oluşturduğu bağımsız değişkenin türünü belirtirsiniz. Aşağıdaki Ciltçi sağlayıcısı ile birlikte kullanılabilir `AuthorEntityBinder` . MVC 'nin sağlayıcılar koleksiyonuna eklendiğinde, `ModelBinder` `Author` veya `Author` türü belirlenmiş parametrelerde özniteliğini kullanmanız gerekmez.

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Note: Yukarıdaki kod bir döndürür `BinderTypeModelBinder` . `BinderTypeModelBinder`Model ciltleri için bir fabrika işlevi görür ve bağımlılık ekleme (dı) sağlar. İçin `AuthorEntityBinder` dı 'nin EF Core erişimi gerekir. `BinderTypeModelBinder`Model Ciltçi 'nın dı 'den hizmet gerektirmesi durumunda kullanın.

Özel model Ciltçi sağlayıcısını kullanmak için, içine ekleyin `ConfigureServices` :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

Model ciltleri değerlendirilirken, sağlayıcı koleksiyonu sırayla incelenir. Bir cildi döndüren ilk sağlayıcı kullanılır. Sağlayıcınızı koleksiyonun sonuna eklemek, özel ciltçinin bir şansı olmadan önce yerleşik bir model cilde yol açabilir. Bu örnekte, özel sağlayıcı, eylem bağımsız değişkenleri için kullanıldığından emin olmak için koleksiyonun başlangıcına eklenir `Author` .

### <a name="polymorphic-model-binding"></a>Polimorfik model bağlama

Türetilmiş türlerin farklı modellerine bağlama polimorfik model bağlama olarak bilinir. İstek değeri, belirli türetilmiş model türüne bağlanması gerektiğinde polimorfik özel model bağlama gerekir. Polimorfik model bağlama:

* Tüm dillerle birlikte çalışmak üzere tasarlanan bir REST API için tipik değildir.
* , Bağlantılı modeller hakkında neden olmasını zorlaştırır.

Ancak, bir uygulama çok biçimli model bağlama gerektiriyorsa, bir uygulama aşağıdaki koda benzeyebilir:

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Öneriler ve en iyi uygulamalar

Özel model ciltleri:

- Durum kodları veya dönüş sonuçları ayarlanmamalıdır (örneğin, 404 bulunamadı). Model bağlama başarısız olursa, eylem yönteminin kendisi içindeki bir [eylem filtresinin](xref:mvc/controllers/filters) veya mantığının hata işlemesi gerekir.
- , Yinelenen kodu ve eylem yöntemlerinden çapraz kesme sorunlarını ortadan kaldırmak için en yararlı seçenektir.
- Genellikle bir dizeyi özel bir türe dönüştürmek için kullanılmamalıdır, <xref:System.ComponentModel.TypeConverter> genellikle daha iyi bir seçenektir.

::: moniker-end
