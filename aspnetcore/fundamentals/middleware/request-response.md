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
# <a name="request-and-response-operations-in-aspnet-core"></a>ASP.NET Core'da istek ve yanıt işlemleri

Yazar: [Justin Kotalik](https://github.com/jkotalik)

Bu makalede, istek gövdesinden nasıl okunup yanıt gövdesine yazılanın caizdir. Middleware yazarken bu işlemler için kod gerekebilir. İşlemleri MVC ve Razor Pages tarafından işlenir, çünkü ara yazma dışında, özel kod genellikle gerekli değildir.

İstek ve yanıt organları için iki <xref:System.IO.Stream> soyutlama vardır: ve <xref:System.IO.Pipelines.Pipe>. İstek okuma için, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) <xref:System.IO.Stream> `HttpRequest.BodyReader` bir <xref:System.IO.Pipelines.PipeReader>, ve bir . Yanıt yazma için, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) <xref:System.IO.Stream> `HttpResponse.BodyWriter` bir <xref:System.IO.Pipelines.PipeWriter>, ve bir .

[Boru hatları](/dotnet/standard/io/pipelines) akarsular üzerinde önerilir. Akışların bazı basit işlemler için kullanımı daha kolay olabilir, ancak ardışık işlemler bir performans avantajına sahiptir ve çoğu senaryoda kullanımı daha kolaydır. ASP.NET Core dahili akarsular yerine boru hatları kullanmaya başlıyor. Örneklere şunlar dahildir:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Akışlar çerçeveden kaldırılıyor. Akışlar .NET boyunca kullanılmaya devam eder ve birçok akış türü nde `FileStreams` boru `ResponseCompression`eşdeğerleri yoktur.

## <a name="stream-examples"></a>Akış örnekleri

Amaç, tüm istek gövdesini yeni satırlara bölünerek dizeleri listesi olarak okuyan bir ara yazılım oluşturmak olduğunu varsayalım. Basit bir akış uygulaması aşağıdaki örnek gibi görünebilir:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Bu kod çalışır, ancak bazı sorunlar vardır:

* Ekolarak `StringBuilder`, örnek hemen atılır başka`encodedString`bir dize ( ) oluşturur. Bu işlem akıştaki tüm baytlar için oluşur, bu nedenle sonuç tüm istek gövdesinin boyutuna fazladan bellek ayırmadır.
* Örnek, yeni satırlara bölmeden önce tüm dizeyi okur. Bayt dizisinde yeni satırları denetlemek daha verimlidir.

Aşağıda, önceki sorunlardan bazılarını gideren bir örnek verilmiştir:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Bu önceki örnek:

* Yeni hat karakterleri olmadığı `StringBuilder` sürece tüm istek gövdesini arabelleğe almaz.
* İpi çağırmıyor. `Split`

Ancak, hala birkaç sorun vardır:

* Yeni satır karakterleri seyrekse, istek gövdesinin çoğu dizede arabelleğe alınır.
* Kod dizeleri oluşturmaya devam`remainingString`eder ( ) ve bunları dize arabellesine ekler, bu da ek bir ayırmayla sonuçlanır.

Bu sorunlar düzeltilebilir, ancak kod giderek daha az iyileştirme ile karmaşık hale geliyor. Boru hatları, bu sorunları en az kod karmaşıklığıyla çözmenin bir yolunu sağlar.

## <a name="pipelines"></a>İşlem hatları

Aşağıdaki örnek, aynı senaryonun aşağıdakileri `PipeReader`kullanarak nasıl işlenebilir olduğunu gösterir:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Bu örnek, akış uygulamalarının sahip olduğu birçok sorunu giderir:

* Dize arabelleği gerek yoktur, `PipeReader` çünkü tutamaçları kullanılmamıştır.
* Kodlanmış dizeleri doğrudan döndürülen dizeleri listesine eklenir.
* Dize oluşturma dize tarafından kullanılan bellek yanında `ToArray()` ayırma-free (çağrı hariç).

## <a name="adapters"></a>Bağdaştırıcı

Hem `Body` `BodyReader/BodyWriter` de özellikleri `HttpRequest` için `HttpResponse`kullanılabilir ve . Farklı bir `Body` akışa ayarladiğinizde, yeni bir bağdaştırıcı kümesi her türü otomatik olarak diğerine uyarlar. Yeni bir `HttpRequest.Body` akışa ayarlarsanız, `HttpRequest.BodyReader` otomatik olarak `PipeReader` saran `HttpRequest.Body`yeni bir akışa ayarlanır.

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync`üstbilginin değiştirilemez olduğunu belirtmek ve geri `OnStarting` aramaları çalıştırmak için kullanılır. Kestrel'i sunucu olarak `StartAsync` kullanırken, `PipeReader` bellek tarafından `GetMemory` döndürülen garantileri kullanmadan önce <xref:System.IO.Pipelines.Pipe> arama yapmak, harici bir arabellek yerine Kestrel'in dahili sine aittir.

## <a name="additional-resources"></a>Ek kaynaklar

* [System.IO.Pipelines Tanıtımı](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
