---
title: ASP.NET Core'da Istek ve Yanıt İşlemleri
author: jkotalik
description: İstek gövdesini nasıl okuyup yanıt gövdesini ASP.NET Core'a nasıl yazabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b473fa02e1d23f02bc5d2e15fa54ab7b1dbbb17c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667218"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="eacba-103">ASP.NET Core'da istek ve yanıt işlemleri</span><span class="sxs-lookup"><span data-stu-id="eacba-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="eacba-104">Yazar: [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="eacba-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="eacba-105">Bu makalede, istek gövdesinden nasıl okunup yanıt gövdesine yazılanın caizdir.</span><span class="sxs-lookup"><span data-stu-id="eacba-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="eacba-106">Middleware yazarken bu işlemler için kod gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="eacba-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="eacba-107">İşlemleri MVC ve Razor Pages tarafından işlenir, çünkü ara yazma dışında, özel kod genellikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="eacba-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="eacba-108">İstek ve yanıt organları için iki <xref:System.IO.Stream> soyutlama vardır: ve <xref:System.IO.Pipelines.Pipe>.</span><span class="sxs-lookup"><span data-stu-id="eacba-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="eacba-109">İstek okuma için, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) <xref:System.IO.Stream> `HttpRequest.BodyReader` bir <xref:System.IO.Pipelines.PipeReader>, ve bir .</span><span class="sxs-lookup"><span data-stu-id="eacba-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="eacba-110">Yanıt yazma için, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) <xref:System.IO.Stream> `HttpResponse.BodyWriter` bir <xref:System.IO.Pipelines.PipeWriter>, ve bir .</span><span class="sxs-lookup"><span data-stu-id="eacba-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="eacba-111">[Boru hatları](/dotnet/standard/io/pipelines) akarsular üzerinde önerilir.</span><span class="sxs-lookup"><span data-stu-id="eacba-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="eacba-112">Akışların bazı basit işlemler için kullanımı daha kolay olabilir, ancak ardışık işlemler bir performans avantajına sahiptir ve çoğu senaryoda kullanımı daha kolaydır.</span><span class="sxs-lookup"><span data-stu-id="eacba-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="eacba-113">ASP.NET Core dahili akarsular yerine boru hatları kullanmaya başlıyor.</span><span class="sxs-lookup"><span data-stu-id="eacba-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="eacba-114">Örneklere şunlar dahildir:</span><span class="sxs-lookup"><span data-stu-id="eacba-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="eacba-115">Akışlar çerçeveden kaldırılıyor.</span><span class="sxs-lookup"><span data-stu-id="eacba-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="eacba-116">Akışlar .NET boyunca kullanılmaya devam eder ve birçok akış türü nde `FileStreams` boru `ResponseCompression`eşdeğerleri yoktur.</span><span class="sxs-lookup"><span data-stu-id="eacba-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="eacba-117">Akış örnekleri</span><span class="sxs-lookup"><span data-stu-id="eacba-117">Stream examples</span></span>

<span data-ttu-id="eacba-118">Amaç, tüm istek gövdesini yeni satırlara bölünerek dizeleri listesi olarak okuyan bir ara yazılım oluşturmak olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="eacba-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="eacba-119">Basit bir akış uygulaması aşağıdaki örnek gibi görünebilir:</span><span class="sxs-lookup"><span data-stu-id="eacba-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="eacba-120">Bu kod çalışır, ancak bazı sorunlar vardır:</span><span class="sxs-lookup"><span data-stu-id="eacba-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="eacba-121">Ekolarak `StringBuilder`, örnek hemen atılır başka`encodedString`bir dize ( ) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="eacba-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="eacba-122">Bu işlem akıştaki tüm baytlar için oluşur, bu nedenle sonuç tüm istek gövdesinin boyutuna fazladan bellek ayırmadır.</span><span class="sxs-lookup"><span data-stu-id="eacba-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="eacba-123">Örnek, yeni satırlara bölmeden önce tüm dizeyi okur.</span><span class="sxs-lookup"><span data-stu-id="eacba-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="eacba-124">Bayt dizisinde yeni satırları denetlemek daha verimlidir.</span><span class="sxs-lookup"><span data-stu-id="eacba-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="eacba-125">Aşağıda, önceki sorunlardan bazılarını gideren bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="eacba-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="eacba-126">Bu önceki örnek:</span><span class="sxs-lookup"><span data-stu-id="eacba-126">This preceding example:</span></span>

* <span data-ttu-id="eacba-127">Yeni hat karakterleri olmadığı `StringBuilder` sürece tüm istek gövdesini arabelleğe almaz.</span><span class="sxs-lookup"><span data-stu-id="eacba-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="eacba-128">İpi çağırmıyor. `Split`</span><span class="sxs-lookup"><span data-stu-id="eacba-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="eacba-129">Ancak, hala birkaç sorun vardır:</span><span class="sxs-lookup"><span data-stu-id="eacba-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="eacba-130">Yeni satır karakterleri seyrekse, istek gövdesinin çoğu dizede arabelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="eacba-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="eacba-131">Kod dizeleri oluşturmaya devam`remainingString`eder ( ) ve bunları dize arabellesine ekler, bu da ek bir ayırmayla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="eacba-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="eacba-132">Bu sorunlar düzeltilebilir, ancak kod giderek daha az iyileştirme ile karmaşık hale geliyor.</span><span class="sxs-lookup"><span data-stu-id="eacba-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="eacba-133">Boru hatları, bu sorunları en az kod karmaşıklığıyla çözmenin bir yolunu sağlar.</span><span class="sxs-lookup"><span data-stu-id="eacba-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="eacba-134">İşlem hatları</span><span class="sxs-lookup"><span data-stu-id="eacba-134">Pipelines</span></span>

<span data-ttu-id="eacba-135">Aşağıdaki örnek, aynı senaryonun aşağıdakileri `PipeReader`kullanarak nasıl işlenebilir olduğunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="eacba-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="eacba-136">Bu örnek, akış uygulamalarının sahip olduğu birçok sorunu giderir:</span><span class="sxs-lookup"><span data-stu-id="eacba-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="eacba-137">Dize arabelleği gerek yoktur, `PipeReader` çünkü tutamaçları kullanılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="eacba-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="eacba-138">Kodlanmış dizeleri doğrudan döndürülen dizeleri listesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="eacba-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="eacba-139">Dize oluşturma dize tarafından kullanılan bellek yanında `ToArray()` ayırma-free (çağrı hariç).</span><span class="sxs-lookup"><span data-stu-id="eacba-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="eacba-140">Bağdaştırıcı</span><span class="sxs-lookup"><span data-stu-id="eacba-140">Adapters</span></span>

<span data-ttu-id="eacba-141">Hem `Body` `BodyReader/BodyWriter` de özellikleri `HttpRequest` için `HttpResponse`kullanılabilir ve .</span><span class="sxs-lookup"><span data-stu-id="eacba-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="eacba-142">Farklı bir `Body` akışa ayarladiğinizde, yeni bir bağdaştırıcı kümesi her türü otomatik olarak diğerine uyarlar.</span><span class="sxs-lookup"><span data-stu-id="eacba-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="eacba-143">Yeni bir `HttpRequest.Body` akışa ayarlarsanız, `HttpRequest.BodyReader` otomatik olarak `PipeReader` saran `HttpRequest.Body`yeni bir akışa ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="eacba-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="eacba-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="eacba-144">StartAsync</span></span>

<span data-ttu-id="eacba-145">`HttpResponse.StartAsync`üstbilginin değiştirilemez olduğunu belirtmek ve geri `OnStarting` aramaları çalıştırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="eacba-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="eacba-146">Kestrel'i sunucu olarak `StartAsync` kullanırken, `PipeReader` bellek tarafından `GetMemory` döndürülen garantileri kullanmadan önce <xref:System.IO.Pipelines.Pipe> arama yapmak, harici bir arabellek yerine Kestrel'in dahili sine aittir.</span><span class="sxs-lookup"><span data-stu-id="eacba-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eacba-147">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="eacba-147">Additional resources</span></span>

* [<span data-ttu-id="eacba-148">System.IO.Pipelines Tanıtımı</span><span class="sxs-lookup"><span data-stu-id="eacba-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
