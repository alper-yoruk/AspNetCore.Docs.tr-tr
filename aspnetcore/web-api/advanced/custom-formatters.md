---
title: ASP.NET Core Web API 'sindeki özel formatıcılar
author: rick-anderson
description: ASP.NET Core Web API 'Leri için özel biçimleri oluşturma ve kullanma hakkında bilgi edinin.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 9f87d02dd3abe6dca8db495e482ccf9c440a2469
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627552"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="318bc-103">ASP.NET Core Web API 'sindeki özel formatıcılar</span><span class="sxs-lookup"><span data-stu-id="318bc-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="318bc-104">, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Tom Dykstra](https://github.com/tdykstra)tarafından yapılır.</span><span class="sxs-lookup"><span data-stu-id="318bc-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="318bc-105">ASP.NET Core MVC, giriş ve çıkış formatlayıcıları kullanılarak Web API 'Lerinde veri değişimini destekler.</span><span class="sxs-lookup"><span data-stu-id="318bc-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="318bc-106">Giriş formatlayıcıları [model bağlama](xref:mvc/models/model-binding)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="318bc-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="318bc-107">Çıkış biçimleri, [yanıtları biçimlendirmek](xref:web-api/advanced/formatting)için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="318bc-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="318bc-108">Framework, JSON ve XML için yerleşik giriş ve çıkış biçimleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="318bc-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="318bc-109">Düz metin için yerleşik bir çıkış biçimlendiricisi sağlar, ancak düz metin için bir giriş biçimlendiricisi sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="318bc-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="318bc-110">Bu makalede, özel formatlayıcılar oluşturarak ek biçimler için nasıl destek ekleneceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="318bc-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="318bc-111">Özel düz metin girişi biçimlendiricisi örneği için GitHub 'da [Textplainınputformatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="318bc-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="318bc-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="318bc-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="318bc-113">Özel formatlayıcılar ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="318bc-113">When to use custom formatters</span></span>

<span data-ttu-id="318bc-114">Yerleşik biçimleyiciler tarafından işlenmeyen bir içerik türü için destek eklemek üzere özel bir biçimlendirici kullanın.</span><span class="sxs-lookup"><span data-stu-id="318bc-114">Use a custom formatter to add support for a content type that isn't handled by the built-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="318bc-115">Özel biçimlendirici kullanma konusuna genel bakış</span><span class="sxs-lookup"><span data-stu-id="318bc-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="318bc-116">Özel bir biçimlendirici oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="318bc-116">To create a custom formatter:</span></span>

* <span data-ttu-id="318bc-117">İstemciye gönderilen verileri seri hale getirmek için bir çıkış biçimlendirici sınıfı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="318bc-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="318bc-118">İstemciden alınan verilerin serisini kaldırma için bir giriş biçimlendirici sınıfı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="318bc-118">For deserializing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="318bc-119">İçindeki ve koleksiyonlarına biçimlendirici sınıf örnekleri ekleyin `InputFormatters` `OutputFormatters` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="318bc-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="318bc-120">Özel bir biçimlendirici sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="318bc-120">How to create a custom formatter class</span></span>

<span data-ttu-id="318bc-121">Bir biçimlendirici oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="318bc-121">To create a formatter:</span></span>

* <span data-ttu-id="318bc-122">Sınıfı uygun temel sınıftan türet.</span><span class="sxs-lookup"><span data-stu-id="318bc-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="318bc-123">Örnek uygulama <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> ve ' den türetilir <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="318bc-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="318bc-124">Oluşturucuda geçerli medya türleri ve kodlamalar belirtin.</span><span class="sxs-lookup"><span data-stu-id="318bc-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="318bc-125"><xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A>Ve yöntemlerini geçersiz kılın <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> .</span><span class="sxs-lookup"><span data-stu-id="318bc-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="318bc-126"><xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A>Ve yöntemlerini geçersiz kılın `WriteResponseBodyAsync` .</span><span class="sxs-lookup"><span data-stu-id="318bc-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="318bc-127">Aşağıdaki kod, `VcardOutputFormatter` [örnekten](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)sınıfını gösterir:</span><span class="sxs-lookup"><span data-stu-id="318bc-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="318bc-128">Uygun taban sınıftan türet</span><span class="sxs-lookup"><span data-stu-id="318bc-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="318bc-129">Metin medya türleri için (örneğin, vCard), <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> veya temel sınıftan türetirsiniz <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="318bc-129">For text media types (for example, vCard), derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> base class.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

<span data-ttu-id="318bc-130">İkili türler için <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> veya temel sınıftan türetirsiniz <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="318bc-130">For binary types, derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="318bc-131">Geçerli medya türlerini ve kodlamaları belirtin</span><span class="sxs-lookup"><span data-stu-id="318bc-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="318bc-132">Oluşturucuda, ve koleksiyonlarına ekleyerek geçerli medya türlerini ve kodlamaları belirtin `SupportedMediaTypes` `SupportedEncodings` .</span><span class="sxs-lookup"><span data-stu-id="318bc-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

<span data-ttu-id="318bc-133">Bir biçimlendirici sınıfı, bağımlılıkları için Oluşturucu Ekleme **işlemini kullanamaz.**</span><span class="sxs-lookup"><span data-stu-id="318bc-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="318bc-134">Örneğin, `ILogger<VcardOutputFormatter>` oluşturucuya bir parametre olarak eklenemez.</span><span class="sxs-lookup"><span data-stu-id="318bc-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="318bc-135">Hizmetlere erişmek için yöntemlere geçirilen bağlam nesnesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="318bc-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="318bc-136">Bu makaledeki bir kod örneği ve [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) bunun nasıl yapılacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="318bc-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="318bc-137">CanReadType ve CanWriteType geçersiz kılınır</span><span class="sxs-lookup"><span data-stu-id="318bc-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="318bc-138">Veya yöntemlerini geçersiz kılarak içinden seri durumdan çıkarılacak veya seri hale getirilecek türü belirtin `CanReadType` `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="318bc-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="318bc-139">Örneğin, bir türden vCard metni oluşturma `Contact` ve tam tersi.</span><span class="sxs-lookup"><span data-stu-id="318bc-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="318bc-140">CanWriteResult yöntemi</span><span class="sxs-lookup"><span data-stu-id="318bc-140">The CanWriteResult method</span></span>

<span data-ttu-id="318bc-141">Bazı senaryolarda, `CanWriteResult` yerine geçersiz kılınmalıdır `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="318bc-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="318bc-142">`CanWriteResult`Aşağıdaki koşullar doğruysa kullanın:</span><span class="sxs-lookup"><span data-stu-id="318bc-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="318bc-143">Eylem yöntemi bir model sınıfı döndürür.</span><span class="sxs-lookup"><span data-stu-id="318bc-143">The action method returns a model class.</span></span>
* <span data-ttu-id="318bc-144">Çalışma zamanında döndürülebilecek türetilmiş sınıflar var.</span><span class="sxs-lookup"><span data-stu-id="318bc-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="318bc-145">Eylem tarafından döndürülen türetilmiş sınıf, çalışma zamanında bilinmelidir.</span><span class="sxs-lookup"><span data-stu-id="318bc-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="318bc-146">Örneğin, eylem yöntemini varsayalım:</span><span class="sxs-lookup"><span data-stu-id="318bc-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="318bc-147">İmza bir `Person` tür döndürür.</span><span class="sxs-lookup"><span data-stu-id="318bc-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="318bc-148">, `Student` `Instructor` Öğesinden türetilen bir veya türünü döndürebilir `Person` .</span><span class="sxs-lookup"><span data-stu-id="318bc-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="318bc-149">Biçimlendiricisi yalnızca nesneleri işlemek için `Student` , <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> metoduna sunulan bağlam nesnesindeki türünü denetleyin `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="318bc-149">For the formatter to handle only `Student` objects, check the type of <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="318bc-150">Eylem yöntemi şunu döndürdüğünde `IActionResult` :</span><span class="sxs-lookup"><span data-stu-id="318bc-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="318bc-151">Kullanılması gerekli değildir `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="318bc-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="318bc-152">`CanWriteType`Yöntemi, çalışma zamanı türünü alır.</span><span class="sxs-lookup"><span data-stu-id="318bc-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="318bc-153">ReadRequestBodyAsync ve WriteResponseBodyAsync geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="318bc-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="318bc-154">Seri durumdan çıkarma veya serileştirme gerçekleştirilir `ReadRequestBodyAsync` `WriteResponseBodyAsync` .</span><span class="sxs-lookup"><span data-stu-id="318bc-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="318bc-155">Aşağıdaki örnek, bağımlılık ekleme kapsayıcısından hizmetlerin nasıl alınacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="318bc-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="318bc-156">Hizmetler, Oluşturucu parametrelerinden elde alınamaz.</span><span class="sxs-lookup"><span data-stu-id="318bc-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="318bc-157">MVC 'yi özel bir biçimlendirici kullanacak şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="318bc-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="318bc-158">Özel bir biçimlendirici kullanmak için, veya koleksiyonuna biçimlendirici sınıfının bir örneğini ekleyin `InputFormatters` `OutputFormatters` .</span><span class="sxs-lookup"><span data-stu-id="318bc-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="318bc-159">Biçimlendiriciler, eklediğiniz sırada değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="318bc-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="318bc-160">Birincisi bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="318bc-160">The first one takes precedence.</span></span>

## <a name="the-complete-vcardinputformatter-class"></a><span data-ttu-id="318bc-161">Tüm `VcardInputFormatter` sınıf</span><span class="sxs-lookup"><span data-stu-id="318bc-161">The complete `VcardInputFormatter` class</span></span>

<span data-ttu-id="318bc-162">Aşağıdaki kod, `VcardInputFormatter` [örnekten](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)sınıfını gösterir:</span><span class="sxs-lookup"><span data-stu-id="318bc-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a><span data-ttu-id="318bc-163">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="318bc-163">Test the app</span></span>

<span data-ttu-id="318bc-164">[Bu makale için](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), temel vCard giriş ve çıkış formatlayıcıları uygulayan örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="318bc-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="318bc-165">Uygulama, vCard 'ları aşağıdakine benzer şekilde okur ve yazar:</span><span class="sxs-lookup"><span data-stu-id="318bc-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="318bc-166">VCard çıktısını görmek için, uygulamayı çalıştırın ve Accept üst bilgisine sahip bir get isteği gönderin `text/vcard` `https://localhost:5001/api/contacts` .</span><span class="sxs-lookup"><span data-stu-id="318bc-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="318bc-167">Bellek içi kişiler koleksiyonuna vCard eklemek için:</span><span class="sxs-lookup"><span data-stu-id="318bc-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="318bc-168">`Post` `/api/contacts` Postman gibi bir araçla bir istek gönderin.</span><span class="sxs-lookup"><span data-stu-id="318bc-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="318bc-169">`Content-Type`Üstbilgiyi olarak ayarlayın `text/vcard` .</span><span class="sxs-lookup"><span data-stu-id="318bc-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="318bc-170">`vCard`Gövdedeki metni, önceki örnekte olduğu gibi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="318bc-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="318bc-171">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="318bc-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
