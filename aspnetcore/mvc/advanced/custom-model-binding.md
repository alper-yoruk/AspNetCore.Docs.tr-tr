---
title: ASP.NET Core özel model bağlama
author: ardalis
description: Model bağlamanın, denetleyici eylemlerinin ASP.NET Core doğrudan model türleriyle nasıl çalışmasına izin verdiğini öğrenin.
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 7e1151b04f39f637f889e690bccc97eb6f0c91ea
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865580"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="48806-103">ASP.NET Core özel model bağlama</span><span class="sxs-lookup"><span data-stu-id="48806-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="48806-104">[Steve Smith](https://ardalis.com/) ve [Kirk larkaya](https://twitter.com/serpent5) tarafından</span><span class="sxs-lookup"><span data-stu-id="48806-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="48806-105">Model bağlama, denetleyici eylemlerinin HTTP istekleri yerine model türleriyle doğrudan (Yöntem bağımsız değişkenleri olarak geçirilir) çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="48806-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="48806-106">Gelen istek verileri ve uygulama modelleri arasındaki eşleme, model ciltleri tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="48806-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="48806-107">Geliştiriciler, özel model ciltlerinizi uygulayarak yerleşik model bağlama işlevini genişletebilir (genellikle kendi sağlayıcınızı yazmanız gerekmez).</span><span class="sxs-lookup"><span data-stu-id="48806-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="48806-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="48806-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="48806-109">Varsayılan model Ciltçi sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="48806-109">Default model binder limitations</span></span>

<span data-ttu-id="48806-110">Varsayılan model ciltleri ortak .NET Core veri türlerinin çoğunu destekler ve çoğu geliştiricilerin ihtiyaçlarını karşılamaları gerekir.</span><span class="sxs-lookup"><span data-stu-id="48806-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="48806-111">İstek içinden doğrudan model türlerine metin tabanlı giriş bağlamayı bekler.</span><span class="sxs-lookup"><span data-stu-id="48806-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="48806-112">Bağlamayı bağlamadan önce girişi dönüştürmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="48806-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="48806-113">Örneğin, model verilerini aramak için kullanılabilecek bir anahtarınız olduğunda.</span><span class="sxs-lookup"><span data-stu-id="48806-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="48806-114">Anahtar temelinde veri getirmek için özel bir model Bağlayıcısı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="48806-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="48806-115">Model bağlama incelemesi</span><span class="sxs-lookup"><span data-stu-id="48806-115">Model binding review</span></span>

<span data-ttu-id="48806-116">Model bağlama, üzerinde çalıştığı türler için belirli tanımları kullanır.</span><span class="sxs-lookup"><span data-stu-id="48806-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="48806-117">*Basit bir tür* , girişte tek bir dizeden dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="48806-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="48806-118">*Karmaşık bir tür* birden çok giriş değerinden dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="48806-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="48806-119">Framework, a 'nın varlığına göre farkı belirler `TypeConverter` .</span><span class="sxs-lookup"><span data-stu-id="48806-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="48806-120">`string`  ->  Dış kaynak gerektirmeyen basit bir eşlemeye sahipseniz bir tür dönüştürücüsü oluşturmanız önerilir `SomeType` .</span><span class="sxs-lookup"><span data-stu-id="48806-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="48806-121">Kendi özel model cilinkini oluşturmadan önce, mevcut model ciltçileri 'nin nasıl uygulandığını gözden geçirdik.</span><span class="sxs-lookup"><span data-stu-id="48806-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="48806-122"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>Base64 ile kodlanmış dizeleri bayt dizilerine dönüştürmek için kullanılabilecek ' i göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="48806-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="48806-123">Bayt dizileri genellikle dosya veya veritabanı blobu alanları olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="48806-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="48806-124">Bytearraymodelciltçi ile çalışma</span><span class="sxs-lookup"><span data-stu-id="48806-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="48806-125">İkili verileri temsil etmek için Base64 kodlamalı dizeler kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="48806-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="48806-126">Örneğin, bir görüntü dize olarak kodlanır.</span><span class="sxs-lookup"><span data-stu-id="48806-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="48806-127">Örnek, [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt)Base64 kodlamalı dize olarak bir görüntü içerir.</span><span class="sxs-lookup"><span data-stu-id="48806-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="48806-128">ASP.NET Core MVC, Base64 kodlamalı bir dize alabilir ve `ByteArrayModelBinder` bunu bir bayt dizisine dönüştürmek için kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="48806-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="48806-129"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> `byte[]` Bağımsız değişkenleri şu şekilde eşler `ByteArrayModelBinder` :</span><span class="sxs-lookup"><span data-stu-id="48806-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

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

<span data-ttu-id="48806-130">Kendi özel model cilinkini oluştururken kendi `IModelBinderProvider` türünü uygulayabilir veya kullanabilirsiniz <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .</span><span class="sxs-lookup"><span data-stu-id="48806-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="48806-131">Aşağıdaki örnek, bir `ByteArrayModelBinder` base64 kodlu dizeyi öğesine dönüştürmek `byte[]` ve sonucu bir dosyaya kaydetmek için nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="48806-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="48806-132">[Postman](https://www.getpostman.com/)gibi bir araç kullanarak bu API yöntemine Base64 kodlamalı bir dize gönderebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="48806-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="48806-133">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="48806-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="48806-134">Bağlayıcı, istek verilerini uygun şekilde adlandırılmış özelliklere veya bağımsız değişkenlere bağlayabildiğinden, model bağlama başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="48806-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="48806-135">Aşağıdaki örnek, `ByteArrayModelBinder` bir görünüm modeliyle nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="48806-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="48806-136">Özel model Ciltçi örneği</span><span class="sxs-lookup"><span data-stu-id="48806-136">Custom model binder sample</span></span>

<span data-ttu-id="48806-137">Bu bölümde, özel bir model cildi uygulayacağız:</span><span class="sxs-lookup"><span data-stu-id="48806-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="48806-138">Gelen istek verilerini kesin belirlenmiş anahtar bağımsız değişkenlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="48806-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="48806-139">İlişkili varlığı getirmek için Entity Framework Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="48806-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="48806-140">İlişkili varlığı eylem yöntemine bir bağımsız değişken olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="48806-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="48806-141">Aşağıdaki örnek `ModelBinder` modeldeki özniteliğini kullanır `Author` :</span><span class="sxs-lookup"><span data-stu-id="48806-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="48806-142">Yukarıdaki kodda `ModelBinder` öznitelik, `IModelBinder` eylem parametrelerini bağlamak için kullanılması gereken türünü belirtir `Author` .</span><span class="sxs-lookup"><span data-stu-id="48806-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="48806-143">Aşağıdaki `AuthorEntityBinder` sınıf, `Author` Entity Framework Core ve ' i kullanarak bir veri kaynağından varlığı getirerek bir parametreyi bağlar `authorId` :</span><span class="sxs-lookup"><span data-stu-id="48806-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="48806-144">Önceki `AuthorEntityBinder` sınıf özel bir model cildi göstermeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="48806-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="48806-145">Sınıfı, bir arama senaryosu için en iyi yöntemleri göstermeye yönelik değildir.</span><span class="sxs-lookup"><span data-stu-id="48806-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="48806-146">Arama için, veritabanını bağlayın `authorId` ve bir eylem yöntemine sorgulayın.</span><span class="sxs-lookup"><span data-stu-id="48806-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="48806-147">Bu yaklaşım, model bağlama başarısızlıklarını `NotFound` durumlardan ayırır.</span><span class="sxs-lookup"><span data-stu-id="48806-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="48806-148">Aşağıdaki kod, `AuthorEntityBinder` bir eylem yönteminin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="48806-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="48806-149">`ModelBinder`Özniteliği, `AuthorEntityBinder` varsayılan kuralları kullanmayan parametreleri uygulamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="48806-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="48806-150">Bu örnekte, bağımsız değişkenin adı varsayılan olmadığından `authorId` parametresi kullanılarak parametresi belirtilir `ModelBinder` .</span><span class="sxs-lookup"><span data-stu-id="48806-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="48806-151">Hem denetleyici hem de eylem yöntemi, eylem yöntemindeki varlığı aramaya kıyasla basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="48806-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="48806-152">Entity Framework Core kullanarak yazarı getirme mantığı model cilde taşınır.</span><span class="sxs-lookup"><span data-stu-id="48806-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="48806-153">Bu, modele bağlanan çeşitli yöntemlere sahip olduğunuzda büyük ölçüde basitleştirmesi olabilir `Author` .</span><span class="sxs-lookup"><span data-stu-id="48806-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="48806-154">`ModelBinder`Özniteliği, yalnızca bu tür veya eylem için belirli bir model cildi veya model adı belirtmek üzere tek tek model özelliklerine (ViewModel üzerinde gibi) veya eylem yöntemi parametrelerine uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="48806-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="48806-155">ModelBinderProvider uygulama</span><span class="sxs-lookup"><span data-stu-id="48806-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="48806-156">Bir özniteliği uygulamak yerine, öğesini uygulayabilirsiniz `IModelBinderProvider` .</span><span class="sxs-lookup"><span data-stu-id="48806-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="48806-157">Yerleşik çerçeve ciltçileri uygulanır.</span><span class="sxs-lookup"><span data-stu-id="48806-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="48806-158">Cildin üzerinde çalıştığı türü belirttiğinizde, cildin kabul ettiği girişi **değil** , oluşturduğu bağımsız değişkenin türünü belirtirsiniz.</span><span class="sxs-lookup"><span data-stu-id="48806-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="48806-159">Aşağıdaki Ciltçi sağlayıcısı ile birlikte kullanılabilir `AuthorEntityBinder` .</span><span class="sxs-lookup"><span data-stu-id="48806-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="48806-160">MVC 'nin sağlayıcılar koleksiyonuna eklendiğinde, `ModelBinder` `Author` veya `Author` türü belirlenmiş parametrelerde özniteliğini kullanmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="48806-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="48806-161">Note: Yukarıdaki kod bir döndürür `BinderTypeModelBinder` .</span><span class="sxs-lookup"><span data-stu-id="48806-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="48806-162">`BinderTypeModelBinder` Model ciltleri için bir fabrika işlevi görür ve bağımlılık ekleme (dı) sağlar.</span><span class="sxs-lookup"><span data-stu-id="48806-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="48806-163">İçin `AuthorEntityBinder` dı 'nin EF Core erişimi gerekir.</span><span class="sxs-lookup"><span data-stu-id="48806-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="48806-164">`BinderTypeModelBinder`Model Ciltçi 'nın dı 'den hizmet gerektirmesi durumunda kullanın.</span><span class="sxs-lookup"><span data-stu-id="48806-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="48806-165">Özel model Ciltçi sağlayıcısını kullanmak için, içine ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="48806-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="48806-166">Model ciltleri değerlendirilirken, sağlayıcı koleksiyonu sırayla incelenir.</span><span class="sxs-lookup"><span data-stu-id="48806-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="48806-167">Giriş modeliyle eşleşen bir cildi döndüren ilk sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="48806-167">The first provider that returns a binder that matches the input model is used.</span></span> <span data-ttu-id="48806-168">Sağlayıcınızı koleksiyonun sonuna eklemek, özel cildin bir şansına girmeden önce yerleşik bir model cildin çağrılmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="48806-168">Adding your provider to the end of the collection may thus result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="48806-169">Bu örnekte, özel sağlayıcı, her zaman eylem bağımsız değişkenleri için kullanıldığından emin olmak için koleksiyonun başlangıcına eklenir `Author` .</span><span class="sxs-lookup"><span data-stu-id="48806-169">In this example, the custom provider is added to the beginning of the collection to ensure it's always used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="48806-170">Polimorfik model bağlama</span><span class="sxs-lookup"><span data-stu-id="48806-170">Polymorphic model binding</span></span>

<span data-ttu-id="48806-171">Türetilmiş türlerin farklı modellerine bağlama polimorfik model bağlama olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="48806-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="48806-172">İstek değeri, belirli türetilmiş model türüne bağlanması gerektiğinde polimorfik özel model bağlama gerekir.</span><span class="sxs-lookup"><span data-stu-id="48806-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="48806-173">Polimorfik model bağlama:</span><span class="sxs-lookup"><span data-stu-id="48806-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="48806-174">Tüm dillerle birlikte çalışmak üzere tasarlanan bir REST API için tipik değildir.</span><span class="sxs-lookup"><span data-stu-id="48806-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="48806-175">, Bağlantılı modeller hakkında neden olmasını zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="48806-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="48806-176">Ancak, bir uygulama çok biçimli model bağlama gerektiriyorsa, bir uygulama aşağıdaki koda benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="48806-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="48806-177">Öneriler ve en iyi uygulamalar</span><span class="sxs-lookup"><span data-stu-id="48806-177">Recommendations and best practices</span></span>

<span data-ttu-id="48806-178">Özel model ciltleri:</span><span class="sxs-lookup"><span data-stu-id="48806-178">Custom model binders:</span></span>

- <span data-ttu-id="48806-179">Durum kodları veya dönüş sonuçları ayarlanmamalıdır (örneğin, 404 bulunamadı).</span><span class="sxs-lookup"><span data-stu-id="48806-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="48806-180">Model bağlama başarısız olursa, eylem yönteminin kendisi içindeki bir [eylem filtresinin](xref:mvc/controllers/filters) veya mantığının hata işlemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="48806-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="48806-181">, Yinelenen kodu ve eylem yöntemlerinden çapraz kesme sorunlarını ortadan kaldırmak için en yararlı seçenektir.</span><span class="sxs-lookup"><span data-stu-id="48806-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="48806-182">Genellikle bir dizeyi özel bir türe dönüştürmek için kullanılmamalıdır, <xref:System.ComponentModel.TypeConverter> genellikle daha iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="48806-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="48806-183">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="48806-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="48806-184">Model bağlama, denetleyici eylemlerinin HTTP istekleri yerine model türleriyle doğrudan (Yöntem bağımsız değişkenleri olarak geçirilir) çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="48806-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="48806-185">Gelen istek verileri ve uygulama modelleri arasındaki eşleme, model ciltleri tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="48806-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="48806-186">Geliştiriciler, özel model ciltlerinizi uygulayarak yerleşik model bağlama işlevini genişletebilir (genellikle kendi sağlayıcınızı yazmanız gerekmez).</span><span class="sxs-lookup"><span data-stu-id="48806-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="48806-187">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="48806-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="48806-188">Varsayılan model Ciltçi sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="48806-188">Default model binder limitations</span></span>

<span data-ttu-id="48806-189">Varsayılan model ciltleri ortak .NET Core veri türlerinin çoğunu destekler ve çoğu geliştiricilerin ihtiyaçlarını karşılamaları gerekir.</span><span class="sxs-lookup"><span data-stu-id="48806-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="48806-190">İstek içinden doğrudan model türlerine metin tabanlı giriş bağlamayı bekler.</span><span class="sxs-lookup"><span data-stu-id="48806-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="48806-191">Bağlamayı bağlamadan önce girişi dönüştürmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="48806-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="48806-192">Örneğin, model verilerini aramak için kullanılabilecek bir anahtarınız olduğunda.</span><span class="sxs-lookup"><span data-stu-id="48806-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="48806-193">Anahtar temelinde veri getirmek için özel bir model Bağlayıcısı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="48806-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="48806-194">Model bağlama incelemesi</span><span class="sxs-lookup"><span data-stu-id="48806-194">Model binding review</span></span>

<span data-ttu-id="48806-195">Model bağlama, üzerinde çalıştığı türler için belirli tanımları kullanır.</span><span class="sxs-lookup"><span data-stu-id="48806-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="48806-196">*Basit bir tür* , girişte tek bir dizeden dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="48806-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="48806-197">*Karmaşık bir tür* birden çok giriş değerinden dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="48806-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="48806-198">Framework, a 'nın varlığına göre farkı belirler `TypeConverter` .</span><span class="sxs-lookup"><span data-stu-id="48806-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="48806-199">`string`  ->  Dış kaynak gerektirmeyen basit bir eşlemeye sahipseniz bir tür dönüştürücüsü oluşturmanız önerilir `SomeType` .</span><span class="sxs-lookup"><span data-stu-id="48806-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="48806-200">Kendi özel model cilinkini oluşturmadan önce, mevcut model ciltçileri 'nin nasıl uygulandığını gözden geçirdik.</span><span class="sxs-lookup"><span data-stu-id="48806-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="48806-201"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>Base64 ile kodlanmış dizeleri bayt dizilerine dönüştürmek için kullanılabilecek ' i göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="48806-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="48806-202">Bayt dizileri genellikle dosya veya veritabanı blobu alanları olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="48806-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="48806-203">Bytearraymodelciltçi ile çalışma</span><span class="sxs-lookup"><span data-stu-id="48806-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="48806-204">İkili verileri temsil etmek için Base64 kodlamalı dizeler kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="48806-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="48806-205">Örneğin, bir görüntü dize olarak kodlanır.</span><span class="sxs-lookup"><span data-stu-id="48806-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="48806-206">Örnek, [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt)Base64 kodlamalı dize olarak bir görüntü içerir.</span><span class="sxs-lookup"><span data-stu-id="48806-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="48806-207">ASP.NET Core MVC, Base64 kodlamalı bir dize alabilir ve `ByteArrayModelBinder` bunu bir bayt dizisine dönüştürmek için kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="48806-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="48806-208"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> `byte[]` Bağımsız değişkenleri şu şekilde eşler `ByteArrayModelBinder` :</span><span class="sxs-lookup"><span data-stu-id="48806-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

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

<span data-ttu-id="48806-209">Kendi özel model cilinkini oluştururken kendi `IModelBinderProvider` türünü uygulayabilir veya kullanabilirsiniz <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .</span><span class="sxs-lookup"><span data-stu-id="48806-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="48806-210">Aşağıdaki örnek, bir `ByteArrayModelBinder` base64 kodlu dizeyi öğesine dönüştürmek `byte[]` ve sonucu bir dosyaya kaydetmek için nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="48806-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="48806-211">[Postman](https://www.getpostman.com/)gibi bir araç kullanarak bu API yöntemine Base64 kodlamalı bir dize gönderebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="48806-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="48806-212">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="48806-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="48806-213">Bağlayıcı, istek verilerini uygun şekilde adlandırılmış özelliklere veya bağımsız değişkenlere bağlayabildiğinden, model bağlama başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="48806-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="48806-214">Aşağıdaki örnek, `ByteArrayModelBinder` bir görünüm modeliyle nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="48806-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="48806-215">Özel model Ciltçi örneği</span><span class="sxs-lookup"><span data-stu-id="48806-215">Custom model binder sample</span></span>

<span data-ttu-id="48806-216">Bu bölümde, özel bir model cildi uygulayacağız:</span><span class="sxs-lookup"><span data-stu-id="48806-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="48806-217">Gelen istek verilerini kesin belirlenmiş anahtar bağımsız değişkenlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="48806-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="48806-218">İlişkili varlığı getirmek için Entity Framework Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="48806-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="48806-219">İlişkili varlığı eylem yöntemine bir bağımsız değişken olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="48806-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="48806-220">Aşağıdaki örnek `ModelBinder` modeldeki özniteliğini kullanır `Author` :</span><span class="sxs-lookup"><span data-stu-id="48806-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="48806-221">Yukarıdaki kodda `ModelBinder` öznitelik, `IModelBinder` eylem parametrelerini bağlamak için kullanılması gereken türünü belirtir `Author` .</span><span class="sxs-lookup"><span data-stu-id="48806-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="48806-222">Aşağıdaki `AuthorEntityBinder` sınıf, `Author` Entity Framework Core ve ' i kullanarak bir veri kaynağından varlığı getirerek bir parametreyi bağlar `authorId` :</span><span class="sxs-lookup"><span data-stu-id="48806-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="48806-223">Önceki `AuthorEntityBinder` sınıf özel bir model cildi göstermeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="48806-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="48806-224">Sınıfı, bir arama senaryosu için en iyi yöntemleri göstermeye yönelik değildir.</span><span class="sxs-lookup"><span data-stu-id="48806-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="48806-225">Arama için, veritabanını bağlayın `authorId` ve bir eylem yöntemine sorgulayın.</span><span class="sxs-lookup"><span data-stu-id="48806-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="48806-226">Bu yaklaşım, model bağlama başarısızlıklarını `NotFound` durumlardan ayırır.</span><span class="sxs-lookup"><span data-stu-id="48806-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="48806-227">Aşağıdaki kod, `AuthorEntityBinder` bir eylem yönteminin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="48806-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="48806-228">`ModelBinder`Özniteliği, `AuthorEntityBinder` varsayılan kuralları kullanmayan parametreleri uygulamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="48806-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="48806-229">Bu örnekte, bağımsız değişkenin adı varsayılan olmadığından `authorId` parametresi kullanılarak parametresi belirtilir `ModelBinder` .</span><span class="sxs-lookup"><span data-stu-id="48806-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="48806-230">Hem denetleyici hem de eylem yöntemi, eylem yöntemindeki varlığı aramaya kıyasla basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="48806-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="48806-231">Entity Framework Core kullanarak yazarı getirme mantığı model cilde taşınır.</span><span class="sxs-lookup"><span data-stu-id="48806-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="48806-232">Bu, modele bağlanan çeşitli yöntemlere sahip olduğunuzda büyük ölçüde basitleştirmesi olabilir `Author` .</span><span class="sxs-lookup"><span data-stu-id="48806-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="48806-233">`ModelBinder`Özniteliği, yalnızca bu tür veya eylem için belirli bir model cildi veya model adı belirtmek üzere tek tek model özelliklerine (ViewModel üzerinde gibi) veya eylem yöntemi parametrelerine uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="48806-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="48806-234">ModelBinderProvider uygulama</span><span class="sxs-lookup"><span data-stu-id="48806-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="48806-235">Bir özniteliği uygulamak yerine, öğesini uygulayabilirsiniz `IModelBinderProvider` .</span><span class="sxs-lookup"><span data-stu-id="48806-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="48806-236">Yerleşik çerçeve ciltçileri uygulanır.</span><span class="sxs-lookup"><span data-stu-id="48806-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="48806-237">Cildin üzerinde çalıştığı türü belirttiğinizde, cildin kabul ettiği girişi **değil** , oluşturduğu bağımsız değişkenin türünü belirtirsiniz.</span><span class="sxs-lookup"><span data-stu-id="48806-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="48806-238">Aşağıdaki Ciltçi sağlayıcısı ile birlikte kullanılabilir `AuthorEntityBinder` .</span><span class="sxs-lookup"><span data-stu-id="48806-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="48806-239">MVC 'nin sağlayıcılar koleksiyonuna eklendiğinde, `ModelBinder` `Author` veya `Author` türü belirlenmiş parametrelerde özniteliğini kullanmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="48806-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="48806-240">Note: Yukarıdaki kod bir döndürür `BinderTypeModelBinder` .</span><span class="sxs-lookup"><span data-stu-id="48806-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="48806-241">`BinderTypeModelBinder` Model ciltleri için bir fabrika işlevi görür ve bağımlılık ekleme (dı) sağlar.</span><span class="sxs-lookup"><span data-stu-id="48806-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="48806-242">İçin `AuthorEntityBinder` dı 'nin EF Core erişimi gerekir.</span><span class="sxs-lookup"><span data-stu-id="48806-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="48806-243">`BinderTypeModelBinder`Model Ciltçi 'nın dı 'den hizmet gerektirmesi durumunda kullanın.</span><span class="sxs-lookup"><span data-stu-id="48806-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="48806-244">Özel model Ciltçi sağlayıcısını kullanmak için, içine ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="48806-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="48806-245">Model ciltleri değerlendirilirken, sağlayıcı koleksiyonu sırayla incelenir.</span><span class="sxs-lookup"><span data-stu-id="48806-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="48806-246">Bir cildi döndüren ilk sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="48806-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="48806-247">Sağlayıcınızı koleksiyonun sonuna eklemek, özel ciltçinin bir şansı olmadan önce yerleşik bir model cilde yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="48806-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="48806-248">Bu örnekte, özel sağlayıcı, eylem bağımsız değişkenleri için kullanıldığından emin olmak için koleksiyonun başlangıcına eklenir `Author` .</span><span class="sxs-lookup"><span data-stu-id="48806-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="48806-249">Polimorfik model bağlama</span><span class="sxs-lookup"><span data-stu-id="48806-249">Polymorphic model binding</span></span>

<span data-ttu-id="48806-250">Türetilmiş türlerin farklı modellerine bağlama polimorfik model bağlama olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="48806-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="48806-251">İstek değeri, belirli türetilmiş model türüne bağlanması gerektiğinde polimorfik özel model bağlama gerekir.</span><span class="sxs-lookup"><span data-stu-id="48806-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="48806-252">Polimorfik model bağlama:</span><span class="sxs-lookup"><span data-stu-id="48806-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="48806-253">Tüm dillerle birlikte çalışmak üzere tasarlanan bir REST API için tipik değildir.</span><span class="sxs-lookup"><span data-stu-id="48806-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="48806-254">, Bağlantılı modeller hakkında neden olmasını zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="48806-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="48806-255">Ancak, bir uygulama çok biçimli model bağlama gerektiriyorsa, bir uygulama aşağıdaki koda benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="48806-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="48806-256">Öneriler ve en iyi uygulamalar</span><span class="sxs-lookup"><span data-stu-id="48806-256">Recommendations and best practices</span></span>

<span data-ttu-id="48806-257">Özel model ciltleri:</span><span class="sxs-lookup"><span data-stu-id="48806-257">Custom model binders:</span></span>

- <span data-ttu-id="48806-258">Durum kodları veya dönüş sonuçları ayarlanmamalıdır (örneğin, 404 bulunamadı).</span><span class="sxs-lookup"><span data-stu-id="48806-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="48806-259">Model bağlama başarısız olursa, eylem yönteminin kendisi içindeki bir [eylem filtresinin](xref:mvc/controllers/filters) veya mantığının hata işlemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="48806-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="48806-260">, Yinelenen kodu ve eylem yöntemlerinden çapraz kesme sorunlarını ortadan kaldırmak için en yararlı seçenektir.</span><span class="sxs-lookup"><span data-stu-id="48806-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="48806-261">Genellikle bir dizeyi özel bir türe dönüştürmek için kullanılmamalıdır, <xref:System.ComponentModel.TypeConverter> genellikle daha iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="48806-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
