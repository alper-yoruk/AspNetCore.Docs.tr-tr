---
title: ASP.NET Core Web API 'sindeki özel formatıcılar
author: rick-anderson
description: ASP.NET Core Web API 'Leri için özel biçimleri oluşturma ve kullanma hakkında bilgi edinin.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 0836fc288a015adb9a6223c5a2b681b1b03bded4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777325"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="4c71c-103">ASP.NET Core Web API 'sindeki özel formatıcılar</span><span class="sxs-lookup"><span data-stu-id="4c71c-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="4c71c-104">[Tom Dykstra](https://github.com/tdykstra) tarafından</span><span class="sxs-lookup"><span data-stu-id="4c71c-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="4c71c-105">ASP.NET Core MVC, giriş ve çıkış formatlayıcıları kullanılarak Web API 'Lerinde veri değişimini destekler.</span><span class="sxs-lookup"><span data-stu-id="4c71c-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="4c71c-106">Giriş formatlayıcıları [model bağlama](xref:mvc/models/model-binding)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c71c-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="4c71c-107">Çıkış biçimleri, [yanıtları biçimlendirmek](xref:web-api/advanced/formatting)için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4c71c-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="4c71c-108">Framework, JSON ve XML için yerleşik giriş ve çıkış biçimleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="4c71c-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="4c71c-109">Düz metin için yerleşik bir çıkış biçimlendiricisi sağlar, ancak düz metin için bir giriş biçimlendiricisi sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="4c71c-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="4c71c-110">Bu makalede, özel formatlayıcılar oluşturarak ek biçimler için nasıl destek ekleneceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="4c71c-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="4c71c-111">Düz metin için özel bir giriş biçimlendirici örneği için GitHub 'da [Textplainınputformatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4c71c-111">For an example of a custom input formatter for plain text, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="4c71c-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c71c-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="4c71c-113">Özel formatlayıcılar ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="4c71c-113">When to use custom formatters</span></span>

<span data-ttu-id="4c71c-114">[İçerik anlaşma](xref:web-api/advanced/formatting#content-negotiation) işleminin yerleşik biçimleri tarafından desteklenmeyen bir içerik türünü desteklemesini istediğinizde, özel bir biçimlendirici kullanın.</span><span class="sxs-lookup"><span data-stu-id="4c71c-114">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters.</span></span>

<span data-ttu-id="4c71c-115">Örneğin, Web API 'niz için bazı istemciler [prototip](https://github.com/google/protobuf) biçimini işleyebildiğinden, daha verimli olduğundan bu istemcilerle prototip kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4c71c-115">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="4c71c-116">Ya da Web API 'nizin kişi adlarını ve adresleri, kişi verilerini değiş tokuş için sık kullanılan bir biçimde, [vCard](https://wikipedia.org/wiki/VCard) biçiminde göndermesini isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4c71c-116">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="4c71c-117">Bu makalede belirtilen örnek uygulama, basit bir vCard biçimlendiricisi uygular.</span><span class="sxs-lookup"><span data-stu-id="4c71c-117">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="4c71c-118">Özel biçimlendirici kullanma konusuna genel bakış</span><span class="sxs-lookup"><span data-stu-id="4c71c-118">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="4c71c-119">Özel bir biçimlendirici oluşturma ve kullanma adımları aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4c71c-119">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="4c71c-120">İstemciye göndermek için veri serileştirmek istiyorsanız çıkış biçimlendirici sınıfı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4c71c-120">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="4c71c-121">İstemciden alınan verilerin serisini kaldırmak istiyorsanız bir giriş biçimlendirici sınıfı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4c71c-121">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="4c71c-122">[Mvcoptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions)içindeki `InputFormatters` ve `OutputFormatters` koleksiyonlarına Biçimlendiriciler örneklerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4c71c-122">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="4c71c-123">Aşağıdaki bölümlerde, bu adımların her biri için rehberlik ve kod örnekleri sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="4c71c-123">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="4c71c-124">Özel bir biçimlendirici sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="4c71c-124">How to create a custom formatter class</span></span>

<span data-ttu-id="4c71c-125">Bir biçimlendirici oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="4c71c-125">To create a formatter:</span></span>

* <span data-ttu-id="4c71c-126">Sınıfı uygun temel sınıftan türet.</span><span class="sxs-lookup"><span data-stu-id="4c71c-126">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="4c71c-127">Oluşturucuda geçerli medya türleri ve kodlamalar belirtin.</span><span class="sxs-lookup"><span data-stu-id="4c71c-127">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="4c71c-128">Geçersiz `CanReadType` / `CanWriteType` kılma yöntemleri</span><span class="sxs-lookup"><span data-stu-id="4c71c-128">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="4c71c-129">Geçersiz `ReadRequestBodyAsync` / `WriteResponseBodyAsync` kılma yöntemleri</span><span class="sxs-lookup"><span data-stu-id="4c71c-129">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="4c71c-130">Uygun taban sınıftan türet</span><span class="sxs-lookup"><span data-stu-id="4c71c-130">Derive from the appropriate base class</span></span>

<span data-ttu-id="4c71c-131">Metin medya türleri (örneğin, vCard) için, [Textinputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) veya [Textoutputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) temel sınıfından türetirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4c71c-131">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="4c71c-132">Bir giriş biçimlendirici örneği için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="4c71c-132">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="4c71c-133">İkili türler için [inputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) veya [outputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) temel sınıfından türetirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4c71c-133">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="4c71c-134">Geçerli medya türlerini ve kodlamaları belirtin</span><span class="sxs-lookup"><span data-stu-id="4c71c-134">Specify valid media types and encodings</span></span>

<span data-ttu-id="4c71c-135">Oluşturucuda, `SupportedMediaTypes` ve `SupportedEncodings` koleksiyonlarına ekleyerek geçerli medya türlerini ve kodlamaları belirtin.</span><span class="sxs-lookup"><span data-stu-id="4c71c-135">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="4c71c-136">Bir giriş biçimlendirici örneği için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="4c71c-136">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="4c71c-137">Bir biçimlendirici sınıfında Oluşturucu bağımlılığı ekleme yapamazsınız.</span><span class="sxs-lookup"><span data-stu-id="4c71c-137">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="4c71c-138">Örneğin, oluşturucuya bir günlükçü parametresi ekleyerek günlükçü alamazsınız.</span><span class="sxs-lookup"><span data-stu-id="4c71c-138">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="4c71c-139">Hizmetlere erişmek için yöntemlerinize geçirilen bağlam nesnesini kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="4c71c-139">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="4c71c-140">[Aşağıdaki](#read-write) kod örneği bunun nasıl yapılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="4c71c-140">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="4c71c-141">CanReadType/CanWriteType geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="4c71c-141">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="4c71c-142">`CanReadType` Veya `CanWriteType` yöntemlerini geçersiz kılarak seri durumdan çıkarabilen veya seri hale getirekullanabileceğiniz türü belirtin.</span><span class="sxs-lookup"><span data-stu-id="4c71c-142">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="4c71c-143">Örneğin, yalnızca bir `Contact` türden vCard metni oluşturabileceğiniz gibi, tam tersi de olabilir.</span><span class="sxs-lookup"><span data-stu-id="4c71c-143">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="4c71c-144">Bir giriş biçimlendirici örneği için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="4c71c-144">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="4c71c-145">CanWriteResult yöntemi</span><span class="sxs-lookup"><span data-stu-id="4c71c-145">The CanWriteResult method</span></span>

<span data-ttu-id="4c71c-146">Bazı senaryolarda yerine geçersiz kılmanız `CanWriteResult` gerekir. `CanWriteType`</span><span class="sxs-lookup"><span data-stu-id="4c71c-146">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="4c71c-147">Aşağıdaki `CanWriteResult` koşullar doğruysa kullanın:</span><span class="sxs-lookup"><span data-stu-id="4c71c-147">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="4c71c-148">Eylem yönteminiz bir model sınıfı döndürür.</span><span class="sxs-lookup"><span data-stu-id="4c71c-148">Your action method returns a model class.</span></span>
* <span data-ttu-id="4c71c-149">Çalışma zamanında döndürülebilecek türetilmiş sınıflar var.</span><span class="sxs-lookup"><span data-stu-id="4c71c-149">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="4c71c-150">Eylem tarafından türetilen sınıfın döndürüldüğü çalışma zamanında bilmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="4c71c-150">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="4c71c-151">Örneğin, `Person` eylem yöntemi imzanızın bir tür döndürdüğünden, ancak ondan `Student` `Instructor` `Person`türetilen bir veya türü döndürebildiğini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="4c71c-151">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="4c71c-152">Biçimlendiricinin yalnızca `Student` nesneleri işlemesini istiyorsanız, `CanWriteResult` metoduna sunulan bağlam nesnesindeki [nesne](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) türünü denetleyin.</span><span class="sxs-lookup"><span data-stu-id="4c71c-152">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="4c71c-153">Eylem yönteminin döndürdüğü `CanWriteResult` `IActionResult`zaman kullanılması gerekmediğini unutmayın; Bu durumda, `CanWriteType` yöntemi çalışma zamanı türünü alır.</span><span class="sxs-lookup"><span data-stu-id="4c71c-153">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="4c71c-154">ReadRequestBodyAsync/WriteResponseBodyAsync geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="4c71c-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="4c71c-155">Ya `ReadRequestBodyAsync` `WriteResponseBodyAsync`da ' de serileştirilin veya serileştirme fiili işi yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4c71c-155">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="4c71c-156">Aşağıdaki örnekte vurgulanan satırlarda, bağımlılık ekleme kapsayıcısından hizmetlerin nasıl alınacağı gösterilmektedir (Bu parametreleri Oluşturucu parametrelerinden alamazsınız).</span><span class="sxs-lookup"><span data-stu-id="4c71c-156">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="4c71c-157">Bir giriş biçimlendirici örneği için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="4c71c-157">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="4c71c-158">MVC 'yi özel bir biçimlendirici kullanacak şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4c71c-158">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="4c71c-159">Özel bir biçimlendirici kullanmak için, `InputFormatters` veya `OutputFormatters` koleksiyonuna biçimlendirici sınıfının bir örneğini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4c71c-159">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="4c71c-160">Biçimlendiriciler, eklediğiniz sırada değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="4c71c-160">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="4c71c-161">Birincisi bir öncelik alır.</span><span class="sxs-lookup"><span data-stu-id="4c71c-161">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4c71c-162">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="4c71c-162">Next steps</span></span>

* <span data-ttu-id="4c71c-163">[Bu belge için](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)basit vCard giriş ve çıkış biçimleri uygulayan örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="4c71c-163">[Sample app for this doc](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="4c71c-164">Uygulamalar aşağıdaki örnekteki gibi görünen vCard 'ları okur ve yazar:</span><span class="sxs-lookup"><span data-stu-id="4c71c-164">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR]
uid:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="4c71c-165">VCard çıktısını görmek için, uygulamayı çalıştırın ve "metin/vCard" onay üst bilgisine sahip bir get isteği gönderin `http://localhost:63313/api/contacts/` (Visual Studio 'dan çalıştırıldığında) veya `http://localhost:5000/api/contacts/` (komut satırından çalıştırıldığında).</span><span class="sxs-lookup"><span data-stu-id="4c71c-165">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="4c71c-166">Bir vCard 'ı bellek içi kişiler koleksiyonuna eklemek için, Içerik türü üst bilgisi "metin/vCard" ile aynı URL 'ye bir post isteği gönderin ve gövdesinde örnek olarak biçimlendirilen vCard metni yazın.</span><span class="sxs-lookup"><span data-stu-id="4c71c-166">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
