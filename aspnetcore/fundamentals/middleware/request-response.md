---
title: ASP.NET Core 'de istek ve yanıt işlemleri
author: jkotalik
description: İstek gövdesini okumayı ve yanıt gövdesini ASP.NET Core yazmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b6fc7a115cb0f4696d10bf036eadb59028dfb605
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404138"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="d3153-103">ASP.NET Core 'de istek ve yanıt işlemleri</span><span class="sxs-lookup"><span data-stu-id="d3153-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="d3153-104">, [Kotin Kotalik](https://github.com/jkotalik) tarafından</span><span class="sxs-lookup"><span data-stu-id="d3153-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="d3153-105">Bu makalede, istek gövdesinden okuma ve yanıt gövdesine yazma işlemleri açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d3153-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="d3153-106">Bu işlemler için kod, ara yazılım yazılırken gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="d3153-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="d3153-107">İşlemler MVC ve sayfalar tarafından işlendiği için, yazma ara yazılımı dışında, özel kod genellikle gerekli değildir Razor .</span><span class="sxs-lookup"><span data-stu-id="d3153-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="d3153-108">İstek ve yanıt gövdelerinin iki soyutlamaları vardır: <xref:System.IO.Stream> ve <xref:System.IO.Pipelines.Pipe> .</span><span class="sxs-lookup"><span data-stu-id="d3153-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="d3153-109">İstek okuma için <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> bir <xref:System.IO.Stream> , ve `HttpRequest.BodyReader` olur <xref:System.IO.Pipelines.PipeReader> .</span><span class="sxs-lookup"><span data-stu-id="d3153-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="d3153-110">Yanıt yazma için <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> bir <xref:System.IO.Stream> , ve `HttpResponse.BodyWriter` olur <xref:System.IO.Pipelines.PipeWriter> .</span><span class="sxs-lookup"><span data-stu-id="d3153-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="d3153-111">İşlem [hatları](/dotnet/standard/io/pipelines) akışlar üzerinde önerilir.</span><span class="sxs-lookup"><span data-stu-id="d3153-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="d3153-112">Akışlar bazı basit işlemler için daha kolay olabilir, ancak işlem hatları performans avantajına sahiptir ve çoğu senaryoda daha kolay kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d3153-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="d3153-113">ASP.NET Core dahili akışlar yerine işlem hatlarını kullanmaya başlıyor.</span><span class="sxs-lookup"><span data-stu-id="d3153-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="d3153-114">Örnekler arasında şunlar yer almaktadır:</span><span class="sxs-lookup"><span data-stu-id="d3153-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="d3153-115">Akışlar çerçeveden kaldırılmıyor.</span><span class="sxs-lookup"><span data-stu-id="d3153-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="d3153-116">Akışlar .NET genelinde çalışmaya devam eder ve birçok akış türü, ve gibi kanal eşdeğerlerine sahip değildir `FileStreams` `ResponseCompression` .</span><span class="sxs-lookup"><span data-stu-id="d3153-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="d3153-117">Stream örnekleri</span><span class="sxs-lookup"><span data-stu-id="d3153-117">Stream examples</span></span>

<span data-ttu-id="d3153-118">Hedefin, yeni satırları bölerek tüm istek gövdesini bir dizeler listesi olarak okuyan bir ara yazılım oluşturduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="d3153-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="d3153-119">Basit bir akış uygulamasının aşağıdaki örnekteki gibi görünebilir:</span><span class="sxs-lookup"><span data-stu-id="d3153-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="d3153-120">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="d3153-120">The following code:</span></span>
> * <span data-ttu-id="d3153-121">, İstek gövdesini okumak için bir kanal kullanmayan sorunları göstermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d3153-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="d3153-122">, Üretim uygulamalarında kullanılmak üzere tasarlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="d3153-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="d3153-123">Bu kod işe yarar ancak bazı sorunlar vardır:</span><span class="sxs-lookup"><span data-stu-id="d3153-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="d3153-124">' A eklemeden önce `StringBuilder` , örnek hemen oluşturulan başka bir dize ( `encodedString` ) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d3153-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="d3153-125">Bu işlem akıştaki tüm baytlar için gerçekleşir, bu nedenle sonuç, tüm istek gövdesinin boyutunun fazladan bellek ayırmasından oluşur.</span><span class="sxs-lookup"><span data-stu-id="d3153-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="d3153-126">Örnek, yeni satırlara bölmeden önce tüm dizeyi okur.</span><span class="sxs-lookup"><span data-stu-id="d3153-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="d3153-127">Bayt dizisindeki yeni satırları denetlemek daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="d3153-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="d3153-128">Yukarıdaki sorunlardan bazılarını düzelten bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d3153-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="d3153-129">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="d3153-129">The following code:</span></span>
> * <span data-ttu-id="d3153-130">, Tüm sorunları çözmediğinden önceki koddaki bazı sorunlara yönelik çözümleri göstermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d3153-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="d3153-131">, Üretim uygulamalarında kullanılmak üzere tasarlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="d3153-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="d3153-132">Bu önceki örnek:</span><span class="sxs-lookup"><span data-stu-id="d3153-132">This preceding example:</span></span>

* <span data-ttu-id="d3153-133">`StringBuilder`Herhangi bir yeni satır karakteri olmadıkça tüm istek gövdesini arabelleğe almaz.</span><span class="sxs-lookup"><span data-stu-id="d3153-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="d3153-134">Dizeye çağrı yapmaz `Split` .</span><span class="sxs-lookup"><span data-stu-id="d3153-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="d3153-135">Ancak, yine de bazı sorunlar vardır:</span><span class="sxs-lookup"><span data-stu-id="d3153-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="d3153-136">Yeni satır karakterleri seyrek ise, dize içinde istek gövdesinin büyük bir bölümü arabelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="d3153-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="d3153-137">Kod dizeler () oluşturmaya devam eder `remainingString` ve bunları dize arabelleğine ekler ve bu da ek bir ayırmaya neden olur.</span><span class="sxs-lookup"><span data-stu-id="d3153-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="d3153-138">Bu sorunlar düzeltilebilir, ancak kod çok daha karmaşık bir geliştirme sayesinde daha karmaşık hale geliyor.</span><span class="sxs-lookup"><span data-stu-id="d3153-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="d3153-139">İşlem hatları, en az kod karmaşıklığı ile bu sorunları çözmenin bir yolunu sağlar.</span><span class="sxs-lookup"><span data-stu-id="d3153-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="d3153-140">Pipelines</span><span class="sxs-lookup"><span data-stu-id="d3153-140">Pipelines</span></span>

<span data-ttu-id="d3153-141">Aşağıdaki örnek, bir [Pipereader](/dotnet/standard/io/pipelines#pipe)kullanılarak aynı senaryonun nasıl işlenebileceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d3153-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="d3153-142">Bu örnek, akışlar uygulamalarında bulunan birçok sorunu düzeltir:</span><span class="sxs-lookup"><span data-stu-id="d3153-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="d3153-143">Kullanılmayan baytları işlediği için bir dize arabelleği gerekmez `PipeReader` .</span><span class="sxs-lookup"><span data-stu-id="d3153-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="d3153-144">Kodlanmış dizeler doğrudan döndürülen dizeler listesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="d3153-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="d3153-145">`ToArray`Çağrı dışında ve dize tarafından kullanılan bellek, dize oluşturma ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="d3153-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="d3153-146">Örünü</span><span class="sxs-lookup"><span data-stu-id="d3153-146">Adapters</span></span>

<span data-ttu-id="d3153-147">`Body`, Ve `BodyReader` `BodyWriter` özellikleri ve için kullanılabilir `HttpRequest` `HttpResponse` .</span><span class="sxs-lookup"><span data-stu-id="d3153-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="d3153-148">`Body`Farklı bir akışa ayarlandığında, yeni bir bağdaştırıcı kümesi, her türü otomatik olarak birbirlerine uyarlar.</span><span class="sxs-lookup"><span data-stu-id="d3153-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="d3153-149">`HttpRequest.Body`Yeni bir akışa ayarlarsanız, `HttpRequest.BodyReader` otomatik olarak sarmalanmış yeni olarak ayarlanır `PipeReader` `HttpRequest.Body` .</span><span class="sxs-lookup"><span data-stu-id="d3153-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="d3153-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d3153-150">StartAsync</span></span>

<span data-ttu-id="d3153-151">`HttpResponse.StartAsync`üstbilgilerin değiştirilemeyen ve geri çağırmaların çalıştırıldığı belirtmek için kullanılır `OnStarting` .</span><span class="sxs-lookup"><span data-stu-id="d3153-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="d3153-152">Sunucu olarak Kestrel kullanırken, `StartAsync` `PipeReader` tarafından döndürülen belleğin, `GetMemory` <xref:System.IO.Pipelines.Pipe> dış bir arabellek yerine Kestrel 'ın iç öğesine ait olduğunu garanti altına almadan önce çağrılıyor.</span><span class="sxs-lookup"><span data-stu-id="d3153-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3153-153">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d3153-153">Additional resources</span></span>

* [<span data-ttu-id="d3153-154">.NET 'teki System. ıO. işlem hatları</span><span class="sxs-lookup"><span data-stu-id="d3153-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
