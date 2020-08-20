---
title: ASP.NET Core 'de istek ve yanıt işlemleri
author: jkotalik
description: İstek gövdesini okumayı ve yanıt gövdesini ASP.NET Core yazmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
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
uid: fundamentals/middleware/request-response
ms.openlocfilehash: ce7357ccbb52736bfb44cd8e041c68a0992bf319
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627786"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>ASP.NET Core 'de istek ve yanıt işlemleri

, [Kotin Kotalik](https://github.com/jkotalik) tarafından

Bu makalede, istek gövdesinden okuma ve yanıt gövdesine yazma işlemleri açıklanmaktadır. Bu işlemler için kod, ara yazılım yazılırken gerekli olabilir. İşlemler MVC ve sayfalar tarafından işlendiği için, yazma ara yazılımı dışında, özel kod genellikle gerekli değildir Razor .

İstek ve yanıt gövdelerinin iki soyutlamaları vardır: <xref:System.IO.Stream> ve <xref:System.IO.Pipelines.Pipe> . İstek okuma için <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> bir <xref:System.IO.Stream> , ve `HttpRequest.BodyReader` olur <xref:System.IO.Pipelines.PipeReader> . Yanıt yazma için <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> bir <xref:System.IO.Stream> , ve `HttpResponse.BodyWriter` olur <xref:System.IO.Pipelines.PipeWriter> .

İşlem [hatları](/dotnet/standard/io/pipelines) akışlar üzerinde önerilir. Akışlar bazı basit işlemler için daha kolay olabilir, ancak işlem hatları performans avantajına sahiptir ve çoğu senaryoda daha kolay kullanılır. ASP.NET Core dahili akışlar yerine işlem hatlarını kullanmaya başlıyor. Örneklere şunlar dahildir:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Akışlar çerçeveden kaldırılmıyor. Akışlar .NET genelinde çalışmaya devam eder ve birçok akış türü, ve gibi kanal eşdeğerlerine sahip değildir `FileStreams` `ResponseCompression` .

## <a name="stream-examples"></a>Stream örnekleri

Hedefin, yeni satırları bölerek tüm istek gövdesini bir dizeler listesi olarak okuyan bir ara yazılım oluşturduğunu varsayalım. Basit bir akış uygulamasının aşağıdaki örnekteki gibi görünebilir:

> [!WARNING]
> Aşağıdaki kod:
> * , İstek gövdesini okumak için bir kanal kullanmayan sorunları göstermek için kullanılır.
> * , Üretim uygulamalarında kullanılmak üzere tasarlanmamıştır.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Bu kod işe yarar ancak bazı sorunlar vardır:

* ' A eklemeden önce `StringBuilder` , örnek hemen oluşturulan başka bir dize ( `encodedString` ) oluşturur. Bu işlem akıştaki tüm baytlar için gerçekleşir, bu nedenle sonuç, tüm istek gövdesinin boyutunun fazladan bellek ayırmasından oluşur.
* Örnek, yeni satırlara bölmeden önce tüm dizeyi okur. Bayt dizisindeki yeni satırları denetlemek daha etkilidir.

Yukarıdaki sorunlardan bazılarını düzelten bir örnek aşağıda verilmiştir:

> [!WARNING]
> Aşağıdaki kod:
> * , Tüm sorunları çözmediğinden önceki koddaki bazı sorunlara yönelik çözümleri göstermek için kullanılır.
> * , Üretim uygulamalarında kullanılmak üzere tasarlanmamıştır.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Bu önceki örnek:

* `StringBuilder`Herhangi bir yeni satır karakteri olmadıkça tüm istek gövdesini arabelleğe almaz.
* Dizeye çağrı yapmaz `Split` .

Ancak, yine de bazı sorunlar vardır:

* Yeni satır karakterleri seyrek ise, dize içinde istek gövdesinin büyük bir bölümü arabelleğe alınır.
* Kod dizeler () oluşturmaya devam eder `remainingString` ve bunları dize arabelleğine ekler ve bu da ek bir ayırmaya neden olur.

Bu sorunlar düzeltilebilir, ancak kod çok daha karmaşık bir geliştirme sayesinde daha karmaşık hale geliyor. İşlem hatları, en az kod karmaşıklığı ile bu sorunları çözmenin bir yolunu sağlar.

## <a name="pipelines"></a>Pipelines

Aşağıdaki örnek, bir [Pipereader](/dotnet/standard/io/pipelines#pipe)kullanılarak aynı senaryonun nasıl işlenebileceğini göstermektedir:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Bu örnek, akışlar uygulamalarında bulunan birçok sorunu düzeltir:

* Kullanılmayan baytları işlediği için bir dize arabelleği gerekmez `PipeReader` .
* Kodlanmış dizeler doğrudan döndürülen dizeler listesine eklenir.
* `ToArray`Çağrı dışında ve dize tarafından kullanılan bellek, dize oluşturma ücretsizdir.

## <a name="adapters"></a>Örünü

`Body`, Ve `BodyReader` `BodyWriter` özellikleri ve için kullanılabilir `HttpRequest` `HttpResponse` . `Body`Farklı bir akışa ayarlandığında, yeni bir bağdaştırıcı kümesi, her türü otomatik olarak birbirlerine uyarlar. `HttpRequest.Body`Yeni bir akışa ayarlarsanız, `HttpRequest.BodyReader` otomatik olarak sarmalanmış yeni olarak ayarlanır `PipeReader` `HttpRequest.Body` .

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` üstbilgilerin değiştirilemeyen ve geri çağırmaların çalıştırıldığı belirtmek için kullanılır `OnStarting` . Sunucu olarak Kestrel kullanırken, `StartAsync` `PipeReader` tarafından döndürülen belleğin, `GetMemory` <xref:System.IO.Pipelines.Pipe> dış bir arabellek yerine Kestrel 'ın iç öğesine ait olduğunu garanti altına almadan önce çağrılıyor.

## <a name="additional-resources"></a>Ek kaynaklar

* [.NET 'teki System. ıO. işlem hatları](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
