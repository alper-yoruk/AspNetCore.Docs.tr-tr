---
title: ASP.NET Temel Performans En İyi Uygulamalar
author: mjrousos
description: ASP.NET Core uygulamalarında performansı artırmak ve yaygın performans sorunlarından kaçınmak için ipuçları.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977190"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Temel Performans En İyi Uygulamalar

Yazar: [Mike Rousos](https://github.com/mjrousos)

Bu makalede, ASP.NET Core ile performans en iyi uygulamalar için yönergeler sağlar.

## <a name="cache-aggressively"></a>Önbellek agresif

Önbelleğe alma bu belgenin çeşitli bölümlerinde ele alınmıştır. Daha fazla bilgi için bkz. <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Sıcak kod yollarını anlama

Bu belgede, *sıcak kod yolu* sık çağrılan ve yürütme süresinin çoğunun gerçekleştiği bir kod yolu olarak tanımlanır. Sıcak kod yolları genellikle uygulama ölçeklendirmesini ve performansını sınırlar ve bu belgenin çeşitli bölümlerinde tartışılır.

## <a name="avoid-blocking-calls"></a>Aramaları engellemekten kaçının

ASP.NET Core uygulamaları birçok isteği aynı anda işlenecek şekilde tasarlanmalıdır. Eşzamanlı API'ler, çağrıları engellemeyi beklemeyerak binlerce eşzamanlı isteği işlemek için küçük bir iş parçacığı havuzuna izin verir. İş parçacığı, tamamlanması için uzun süren bir eşzamanlı görevi beklemek yerine başka bir istek üzerinde çalışabilir.

ASP.NET Core uygulamalarında yaygın bir performans sorunu, eşzamanlı olabilecek aramaları engellemektir. Birçok senkron engelleme çağrıları [Thread Pool açlık](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) ve bozulmuş yanıt süreleri yol açar.

**Etmeyin:**

* [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) veya [Task.Result'ı](/dotnet/api/system.threading.tasks.task-1.result)arayarak eşsenkronizasyon yürütmeyi engelleyin.
* Ortak kod yollarında kilitler edinin. ASP.NET Core uygulamaları, kodu paralel olarak çalıştırmak üzere tasarlandığında en çok performans gösterir.
* [Task.Run'ı](/dotnet/api/system.threading.tasks.task.run) arayın ve hemen bekleyin. ASP.NET Core zaten normal İş Parçacığı Havuzu iş parçacıkları üzerinde uygulama kodu çalışır, bu nedenle Task.Run'ı aramak yalnızca ekstra gereksiz İş Parçacığı Havuzu planlamasıyla sonuçlanır. Zamanlanan kod bir iş parçacığı engelleyecek olsa bile, Task.Run bunu engellemez.

**Yap**:

* [Sıcak kod yollarını](#understand-hot-code-paths) eşzamanlı hale getirin.
* Asynchronous API varsa, veri erişimi, G/Ç ve uzun süren operasyonlar API'lerini eşit olarak arayın. Eşzamanlı **not** API eşzamanlı yapmak için [Task.Run'ı](/dotnet/api/system.threading.tasks.task.run) kullanmayın.
* Denetleyici/Razor Page eylemlerini eşzamanlı hale getirin. Tüm arama yığını [async /await](/dotnet/csharp/programming-guide/concepts/async/) desenleri yararlanmak için asynchronous olduğunu.

[PerfView](https://github.com/Microsoft/perfview)gibi bir profil oluşturucu, [Iş Parçacığı Havuzuna](/windows/desktop/procthread/thread-pools)sık sık eklenen iş parçacıklarını bulmak için kullanılabilir. Olay `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` iş parçacığı havuzuna eklenen bir iş parçacığı gösterir. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>Büyük nesne ayırmaları en aza indirmek

[.NET Core çöp toplayıcı,](/dotnet/standard/garbage-collection/) ASP.NET Core uygulamalarında bellek tahsisini ve serbest bırakılmasını otomatik olarak yönetir. Otomatik çöp toplama genellikle geliştiricilerin bellek nasıl ve ne zaman serbest bırakıldığı konusunda endişelenmenize gerek olmadığı anlamına gelir. Ancak, başvurulmamış nesneleri temizlemek CPU süresini alır, bu nedenle geliştiriciler [in sıcak kod yollarında](#understand-hot-code-paths)nesneleri ayırmayı en aza indirmelidir. Çöp toplama özellikle büyük nesnelerde pahalıdır (> 85 K bayt). Büyük nesneler büyük nesne [yığınında](/dotnet/standard/garbage-collection/large-object-heap) depolanır ve temizlemek için tam (nesil 2) çöp toplama gerektirir. Nesil 0 ve nesil 1 koleksiyonlarının aksine, nesil 2 koleksiyonu uygulama yürütmenin geçici olarak askıya alınmasını gerektirir. Büyük nesnelerin sık sık tahsisi ve de-tahsisi tutarsız performansa neden olabilir.

Öneri:

* Sık kullanılan büyük nesneleri önbelleğe almayı **düşünün.** Büyük nesnelerin önbelleğe ayrılması pahalı ayırmaları önler.
* Büyük dizileri depolamak için [ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) kullanarak havuz arabellekleri **yapın.**
* [Sıcak kod yollarında](#understand-hot-code-paths)çok, kısa ömürlü büyük nesneleri tahsis **etmeyin.**

Önceki gibi bellek sorunları, [PerfView'deki](https://github.com/Microsoft/perfview) çöp toplama (GC) istatistiklerini gözden geçirerek ve inceleyerek tanıkonulabilir:

* Çöp toplama duraklama süresi.
* İşlemci süresinin yüzde kaçı çöp toplamada harcanaz.
* Kaç tane çöp toplama nesli 0, 1 ve 2'dir.

Daha fazla bilgi için [Çöp Toplama ve Performans'a](/dotnet/standard/garbage-collection/performance)bakın.

## <a name="optimize-data-access-and-io"></a>Veri erişimini optimize edin ve G/Ç

Veri deposu ve diğer uzak hizmetlerle etkileşimler genellikle ASP.NET Core uygulamasının en yavaş parçalarıdır. Verileri verimli bir şekilde okumak ve yazmak iyi performans için çok önemlidir.

Öneri:

* Tüm veri erişim API'lerini eşit olarak **arayın.**
* **Do not** Gerekenden daha fazla veri almayın. Geçerli HTTP isteği için yalnızca gerekli olan verileri döndürmek için sorguyazın.
* Bir veritabanından veya uzak hizmetten alınan sık erişilen verileri önbelleğe almayı **düşünün,** eğer biraz güncel olmayan veriler kabul edilebilirse. Senaryoya bağlı olarak [MemoryÖnbellek](xref:performance/caching/memory) veya [DistributedCache](xref:performance/caching/distributed)kullanın. Daha fazla bilgi için bkz. <xref:performance/caching/response>.
* Ağ tur gezileri en aza indirmek **yok.** Amaç, birkaç çağrı yerine tek bir aramada gerekli verileri almaktır.
* Salt okunur amaçlarla verilere erişirken Entity Framework Core'da [hiçbir izleme](/ef/core/querying/tracking#no-tracking-queries) sorgusu **kullanmayın.** EF Core, izleme metotlarının sonuçlarını daha verimli bir şekilde döndürebilir.
* Filtrelemenin veritabanı tarafından gerçekleştirilebilmeleri `.Select`için `.Sum` LINQ sorgularını `.Where`(örneğin, , veya deyimlerle) **filtreleyin** ve toplulatın.
* EF Core'un istemcideki bazı sorgu işleçlerini çözdüğünü **ve** bunun da verimsiz sorgu yürütmesine yol açabileceğini düşünün. Daha fazla bilgi için Bkz. [İstemci değerlendirme performansı sorunları.](/ef/core/querying/client-eval#client-evaluation-performance-issues)
* **Do not** "N + 1" SQL sorgularının yürütülmesiyle sonuçlanabilecek koleksiyonlarda projeksiyon sorguları kullanmayın. Daha fazla bilgi için, [ilişkili alt sorguların Optimizasyonu'na](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)bakın.

Yüksek ölçekli uygulamalarda performansı artırabilecek yaklaşımlar için [EF Yüksek Performansı'na](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) bakın:

* [DbContext havuzlama](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Açıkça derlenmiş sorgular](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Kod tabanını işlemeden önce önceki yüksek performanslı yaklaşımların etkisini ölçmenizi öneririz. Derlenen sorguların ek karmaşıklığı performans geliştirmeyi haklı çıkarmayabilir.

Sorgu sorunları, [Uygulama Öngörüleri](/azure/application-insights/app-insights-overview) ile veya profil oluşturma araçlarıyla verilere erişmek için harcanan süregözden geçirilerek algılanabilir. Çoğu veritabanları, sık sık yürütülen sorgularla ilgili istatistikleri de kullanılabilir hale getirin.

## <a name="pool-http-connections-with-httpclientfactory"></a>HttpClientFactory ile Havuz HTTP bağlantıları

[HttpClient](/dotnet/api/system.net.http.httpclient) arabirimi `IDisposable` uygulasa da, yeniden kullanmak için tasarlanmıştır. Kapalı `HttpClient` örnekler, prizleri `TIME_WAIT` kısa bir süre için eyalette açık bırakır. `HttpClient` Nesneleri oluşturan ve elden çıkaran bir kod yolu sık sık kullanılırsa, uygulama kullanılabilir soketleri tüketebilir. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) bu soruna bir çözüm olarak ASP.NET Core 2.1 tanıtıldı. Performansı ve güvenilirliği optimize etmek için HTTP bağlantılarını bir araya getirme işlemlerini işler.

Öneri:

* `HttpClient` Örnekleri doğrudan **oluşturup atmayın.**
* Örnekleri almak `HttpClient` için [httpClientFactory'yi](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) **kullanın.** Daha fazla bilgi için esnek [HTTP isteklerini uygulamak için HttpClientFactory'yi kullanın' a](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)bakın.

## <a name="keep-common-code-paths-fast"></a>Ortak kod yollarını hızlı tutun

Tüm kodlarınızın hızlı olmasını istiyorsunuz. Sık çağrılan kod yolları en iyi duruma getirmek için en önemlidir. Bunlar:

* Uygulamanın istek işleme ardışık boru hattındaki ara yazılım bileşenleri, özellikle de ara yazılımlar boru hattının başında çalışır. Bu bileşenlerin performans üzerinde büyük bir etkisi vardır.
* Her istek için veya istek başına birden çok kez çalıştırılan kod. Örneğin, özel günlüğe kaydetme, yetkilendirme işleyicileri veya geçici hizmetlerin başlatılması.

Öneri:

* **Do not** Uzun süreli görevleri olan özel ara yazılım bileşenleri kullanmayın.
* [Sıcak kod yollarını](#understand-hot-code-paths)tanımlamak için Visual Studio Diagnostic [Tools](/visualstudio/profiling/profiling-feature-tour) veya [PerfView](https://github.com/Microsoft/perfview)gibi performans profil oluşturma araçlarını **kullanın.**

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>HTTP istekleri dışında uzun süren Görevleri tamamlayın

ASP.NET Core uygulamasına yapılan taleplerin çoğu, gerekli hizmetleri arayan ve http yanıtını geri veren bir denetleyici veya sayfa modeli tarafından işlenebilir. Uzun süren görevleri içeren bazı istekler için, tüm istek yanıt işlemini eşzamanlı hale getirmek daha iyidir.

Öneri:

* **Do not** Normal HTTP isteği işlemenin bir parçası olarak uzun süren görevlerin tamamlanmasını beklemeyin.
* Uzun süren istekleri arka [plan hizmetleriyle](xref:fundamentals/host/hosted-services) veya [bir Azure İşlevi](/azure/azure-functions/)ile işlemeyi **düşünün.** İş dışı işlemleri tamamlamak özellikle CPU yoğun görevler için yararlıdır.
* Müşterilerle eşit bir şekilde iletişim [SignalR](xref:signalr/introduction)kurmak için gerçek zamanlı iletişim seçeneklerini **kullanın.**

## <a name="minify-client-assets"></a>Minify müşteri varlıkları

karmaşık ön uçları olan ASP.NET Core uygulamaları sık sık birçok JavaScript, CSS veya resim dosyası na hizmet eder. İlk yük isteklerinin performansı şu şekilde geliştirilebilir:

* Birden çok dosyayı bir araya getiren Birleştirme.
* Beyaz boşluğu ve yorumları kaldırarak dosyaların boyutunu küçülten minifying.

Öneri:

* ASP.NET Core'un [yerleşik desteğini](xref:client-side/bundling-and-minification) istemci varlıklarını birleştirme ve burdatme için **kullanın.**
* Karmaşık istemci varlık yönetimi için [Webpack](https://webpack.js.org/)gibi diğer üçüncü taraf araçları göz önünde **bulundurun.**

## <a name="compress-responses"></a>Yanıtları sıkıştırın

 Yanıtın boyutunu azaltmak genellikle bir uygulamanın yanıt verme hızını artırır, genellikle önemli ölçüde. Taşıma yük boyutlarını azaltmanın bir yolu, bir uygulamanın yanıtlarını sıkıştırmaktır. Daha fazla bilgi için [Yanıt sıkıştırma'ya](xref:performance/response-compression)bakın.

## <a name="use-the-latest-aspnet-core-release"></a>En son ASP.NET Core sürümü kullanma

ASP.NET Core'un her yeni sürümü performans iyileştirmeleri içerir. .NET Core ve ASP.NET Core'daki optimizasyonlar, yeni sürümlerin genellikle eski sürümlerde daha iyi performans gösterdiği anlamına gelir. Örneğin, .NET Core 2.1 derlenen düzenli ifadeler için destek ekledi ve [Span\<T>. ](https://msdn.microsoft.com/magazine/mt814808.aspx) ASP.NET Core 2.2 HTTP/2 için destek ekledi. [ASP.NET Core 3.0,](xref:aspnetcore-3.0) bellek kullanımını azaltan ve iş veritiyi artıran birçok iyileştirme ekler. Performans bir öncelikse, ASP.NET Core'un geçerli sürümüne yükseltmeyi düşünün.

## <a name="minimize-exceptions"></a>Özel durumları en aza indirin

İstisnalar nadir olmalıdır. Atma ve yakalama özel durumları diğer kod akışı desenleri göre yavaştır. Bu nedenle, özel durumlar normal program akışını denetlemek için kullanılmamalıdır.

Öneri:

* Özellikle [sıcak kod yollarında,](#understand-hot-code-paths)normal program akışı aracı olarak atma veya yakalama özel durumları **kullanmayın.**
* Özel durumlara neden olacak koşulları algılamak ve işlemek için uygulamaya mantık **ekleyin.**
* Olağandışı veya beklenmeyen durumlar için özel durumlar **atın** veya yakalayın.

Uygulama Öngörüleri gibi uygulama tanı lama araçları, performansı etkileyebilecek bir uygulamada sık karşılaşılan özel durumların belirlenmesine yardımcı olabilir.

## <a name="performance-and-reliability"></a>Performans ve güvenilirlik

Aşağıdaki bölümlerde performans ipuçları ve bilinen güvenilirlik sorunları ve çözümleri sağnır.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>HttpRequest/HttpResponse gövdesinde eşzamanlı okuma veya yazmadan kaçının

Core'ASP.NET tüm I/O asynchronous' dur. Sunucular, `Stream` hem senkron hem de eşzamanlı aşırı yüklemeye sahip arabirimi uygular. Asenkron olanlar iş parçacığı havuzu konuları engelleme önlemek için tercih edilmelidir. Engelleme iş parçacıkları iş parçacığı havuzu açlık yol açabilir.

**Bunu yapmayın:** Aşağıdaki örnekte <xref:System.IO.StreamReader.ReadToEnd*>. Sonucu beklemek için geçerli iş parçacığı engeller. Bu, [async üzerinde eşitleme](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)bir örnektir.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

Önceki kodda, `Get` senkronize bellek içine tüm HTTP istek gövdesi okur. İstemci yavaş yavaş yükleniyorsa, uygulama async üzerinden senkronizasyon yapıyor. Kestrel senkron okur **desteklemez** çünkü uygulama async üzerinde senkronize yok.

**Bunu yapın:** Aşağıdaki örnek, <xref:System.IO.StreamReader.ReadToEndAsync*> okurken iş parçacığı kullanır ve engellemez.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Önceki kod eş senkronize bellek içine tüm HTTP istek gövdesi okur.

> [!WARNING]
> İstek büyükse, tüm HTTP istek gövdesini belleğe okumak bellek dışı (OOM) durumuna yol açabilir. OOM, Hizmet Reddi ile sonuçlanabilir.  Daha fazla bilgi için [bkz.](#arlb)

**Bunu yapın:** Aşağıdaki örnek, arabelleğe alamayan bir istek gövdesi ni kullanarak tamamen eşzamanlıdır:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Önceki kod, istek gövdesini c# nesnesine eşzamanlı olarak de-serialize eder.

## <a name="prefer-readformasync-over-requestform"></a>Request.Form yerine ReadFormAsync'i tercih edin

`HttpContext.Request.Form`Yerine kullanın. `HttpContext.Request.ReadFormAsync`
`HttpContext.Request.Form`yalnızca aşağıdaki koşullarla güvenle okunabilir:

* Form bir çağrı `ReadFormAsync`tarafından okundu ve
* Önbelleğe alınmış form değeri kullanılarak okunuyor`HttpContext.Request.Form`

**Bunu yapmayın:** Aşağıdaki örnekkullanır. `HttpContext.Request.Form`  `HttpContext.Request.Form`[async üzerinde senkronizasyon](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) kullanır ve iş parçacığı havuzu açlık yol açabilir.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Bunu yapın:** Aşağıdaki örnek, `HttpContext.Request.ReadFormAsync` form gövdesini eşzamanlı olarak okumak için kullanır.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Büyük istek gövdelerini veya yanıt gövdelerini belleğe okumaktan kaçının

.NET'te, 85 KB'den büyük her nesne ayırma büyük nesne yığınında[(LOH)](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)son laşır. Büyük nesneler iki şekilde pahalıdır:

* Yeni ayrılan büyük bir nesnenin belleği temizlenmesi gerektiği için ayırma maliyeti yüksektir. CLR, yeni ayrılan tüm nesnelerin belleği temizlenir.
* LOH yığının geri kalanı ile toplanır. LOH tam bir [çöp toplama](/dotnet/standard/garbage-collection/fundamentals) veya [Gen2 toplama](/dotnet/standard/garbage-collection/fundamentals#generations)gerektirir.

Bu [blog yazısı](https://adamsitnik.com/Array-Pool/#the-problem) kısaca sorunu açıklar:

> Büyük bir nesne tahsis edildiğinde, Gen 2 nesnesi olarak işaretlenir. Küçük nesneler için gen 0 değil. Sonuçları loh bellek biterse, GC tüm yönetilen yığın, sadece LOH temizler. Bu yüzden LOH dahil Gen 0, Gen 1 ve Gen 2 kadar temizler. Buna tam çöp toplama denir ve en çok zaman alan çöp toplamadır. Birçok uygulama için kabul edilebilir. Ama kesinlikle birkaç büyük bellek arabellekleri ortalama bir web isteği işlemek için gerekli olan yüksek performanslı web sunucuları için değil (bir soket, decompress, decode JSON & daha fazla okuyun).

Safçe tek `byte[]` bir veya `string`içine büyük bir istek veya yanıt gövdesi depolama:

* LOH'da hızla yer tükenmesi ile sonuçlanabilir.
* Tam GC'lerin çalıştırdığı için uygulama için performans sorunlarına neden olabilir.

## <a name="working-with-a-synchronous-data-processing-api"></a>Eşzamanlı veri işleme API'si ile çalışma

Yalnızca senkron okuma ları ve yazmaları destekleyen bir serializer/de-serializer kullanırken (örneğin, [JSON.NET):](https://www.newtonsoft.com/json/help/html/Introduction.htm)

* Verileri, serileştiriciye/de-serializer'a geçirmeden önce belleğe arabelleğe alıyorum.

> [!WARNING]
> İstek büyükse, bellek dışı (OOM) durumuna neden olabilir. OOM, Hizmet Reddi ile sonuçlanabilir.  Daha fazla bilgi için [bkz.](#arlb)

ASP.NET Core 3.0 Varsayılan <xref:System.Text.Json> olarak JSON serileştirme kullanır. <xref:System.Text.Json>:

* Okur ve JSON asynchronously yazıyor.
* UTF-8 metni için optimize edi.
* Genellikle `Newtonsoft.Json`daha yüksek performans .

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>IHttpContextAccessor.HttpContext'ı bir alanda saklamayın

[IHttpContextAccessor.HttpContext,](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) istek `HttpContext` iş parçacığından erişildiğinde etkin isteği döndürür. Bir `IHttpContextAccessor.HttpContext` **not** alan veya değişkende depolanmamalıdır.

**Bunu yapmayın:** Aşağıdaki `HttpContext` örnek, bir alanı depolar ve daha sonra kullanmaya çalışır.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Önceki kod sık sık bir null `HttpContext` veya yanlış oluşturucu yakalar.

**Bunu yapın:** Aşağıdaki örnek:

* Bir <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> tarlada saklar.
* `HttpContext` Alanı doğru zamanda kullanır ve `null`''yi.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Birden çok iş parçacığından HttpContext'a erişme

`HttpContext`iş parçacığı güvenli *DeğİlDir.* Paralel `HttpContext` olarak birden çok iş parçacığından erişim, askıda kalma, kilitlenme ve veri bozulması gibi tanımlanmamış davranışlara neden olabilir.

**Bunu yapmayın:** Aşağıdaki örnek, üç paralel istekte bulundu ve giden HTTP isteğinden önce ve sonra gelen istek yolunu kaydeder. İstek yoluna, paralel olarak birden çok iş parçacığından erişilebilir.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Bunu yapın:** Aşağıdaki örnek, üç paralel istek yapmadan önce gelen istekteki tüm verileri kopyalar.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>İstek tamamlandıktan sonra HttpContext'ı kullanmayın

`HttpContext`yalnızca ASP.NET Core ardışık ASP.NET'da etkin bir HTTP isteği olduğu sürece geçerlidir. Tüm ASP.NET Core ardışık alan, her isteği yürüten eşzamanlı bir delege zinciridir. Bu `Task` zincirden döndürülen tamamlandığında, `HttpContext` geri dönüştürülür.

**Bunu yapmayın:** İlk erişime `async void` `await` ulaşıldığında HTTP isteğini tamamlayan aşağıdaki örnek kullanır:

* Hangi **her zaman** ASP.NET Core uygulamalarda kötü bir uygulamadır.
* HTTP isteği `HttpResponse` tamamlandıktan sonra erişilir.
* İşlemi çökertir.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Bunu yapın:** Aşağıdaki örnek çerçeveye bir `Task` döndürür, böylece http isteği eylem tamamlanana kadar tamamlanmaz.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Arka plan iş parçacıklarında HttpContext'ı yakalamayın

**Bunu yapmayın:** Aşağıdaki örnek, kapatmanın `HttpContext` özelliği yakaladığını `Controller` gösterir. İş öğesi aşağıdakileri yapabilir, bu kötü bir uygulamadır:

* İstek kapsamının dışında çalıştırın.
* Yanlış `HttpContext`okumaya çalış.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Bunu yapın:** Aşağıdaki örnek:

* İstek sırasında arka plan görevinde gereken verileri kopyalar.
* Kumandadan hiçbir şeye atıfta bulunmuyor.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Arka plan görevleri barındırılan hizmetler olarak uygulanmalıdır. Daha fazla bilgi için [barındırılan hizmetlerle arka plan görevlerine](xref:fundamentals/host/hosted-services)bakın.

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Arka plan iş parçacıklarıüzerindeki denetleyicilere enjekte edilen hizmetleri yakalamayın

**Bunu yapmayın:** Aşağıdaki örnek, bir kapatmanın `DbContext` `Controller` eylem parametresinden capturing olduğunu gösterir. Bu kötü bir uygulama.  Çalışma öğesi istek kapsamının dışında çalışabilir. İstek `ContosoDbContext` kapsamına girer ve bu da `ObjectDisposedException`bir .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Bunu yapın:** Aşağıdaki örnek:

* Arka plan <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> çalışma öğesinde bir kapsam oluşturmak için bir ekleme. `IServiceScopeFactory`bir singleton olduğunu.
* Arka plan iş parçacığında yeni bir bağımlılık enjeksiyon kapsamı oluşturur.
* Kumandadan hiçbir şeye atıfta bulunmuyor.
* Gelen istekten `ContosoDbContext` geleni yakalamaz.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Aşağıdaki vurgulanan kod:

* Arka plan çalışmasının ömrü için bir kapsam oluşturur ve hizmetleri ondan çözer.
* Doğru `ContosoDbContext` kapsamdakullanır.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Yanıt gövdesi başladıktan sonra durum kodunu veya üstbilgileri değiştirmeyin

ASP.NET Core HTTP yanıt gövdesini arabelleğe almaz. Yanıt ilk kez yazılır:

* Üstbilgi, gövdenin bu parçasıyla birlikte istemciye gönderilir.
* Yanıt üstbilgilerini değiştirmek artık mümkün değil.

**Bunu yapmayın:** Aşağıdaki kod, yanıt zaten başladıktan sonra yanıt üstleri eklemeye çalışır:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

Önceki kodda, `context.Response.Headers["test"] = "test value";` yanıta yazılmışsa `next()` bir özel durum oluşturur.

**Bunu yapın:** Aşağıdaki örnek, üstbilgileri değiştirmeden önce HTTP yanıtının başlatılıp başlaolmadığını denetler.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Bunu yapın:** Aşağıdaki örnek, `HttpResponse.OnStarting` yanıt üstbilgisi istemciye atılmadan önce üstbilgi ayarlamak için kullanır.

Yanıtın başlatılmadığını denetleme, yanıt üstbilgisi yazılmadan hemen önce çağrılacak bir geri aramanın kaydedilmesine izin verir. Yanıtın başlatılmadığını denetleme:

* Üstbilgileri tam zamanında eklenebilir veya geçersiz kılma olanağı sağlar.
* Boru hattında bir sonraki ara yazılım bilgisi gerektirmez.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Yanıt gövdesine yazmaya başladıysanız sonraki() aramayın

Bileşenler yalnızca yanıtı işlemeleri ve işlemeleri mümkünse çağrılmayı bekler.
