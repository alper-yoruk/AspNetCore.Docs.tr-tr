---
title: ASP.NET Core performans En Iyi yöntemleri
author: mjrousos
description: ASP.NET Core uygulamalarında performansı artırma ve sık karşılaşılan performans sorunlarından kaçınmaya yönelik ipuçları.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 15f3ce5a8e8d47ac567acaadcdc4bf8ba738b2ff
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408181"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Core performans En Iyi yöntemleri

, [Mike Rousos](https://github.com/mjrousos) tarafından

Bu makalede, ASP.NET Core ile performans en iyi uygulamalarına yönelik yönergeler sunulmaktadır.

## <a name="cache-aggressively"></a>Önbellek kararlılığı

Önbelleğe alma, bu belgenin çeşitli bölümlerinde ele alınmıştır. Daha fazla bilgi için bkz. <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Etkin kod yollarını anlayın

Bu belgede, sık kullanılan bir *kod yolu* , genellikle çağrılan ve yürütme süresinin çoğunun gerçekleştiği bir kod yolu olarak tanımlanır. Sık kullanılan kod yolları genellikle uygulama ölçeğini ve performansını sınırlar ve bu belgenin çeşitli bölümlerinde ele alınmıştır.

## <a name="avoid-blocking-calls"></a>Çağrı engellemeyi önleyin

ASP.NET Core uygulamalar aynı anda birçok isteği işleyecek şekilde tasarlanmalıdır. Zaman uyumsuz API 'Ler, blok çağrılarını beklemeden binlerce eşzamanlı isteği işlemek için küçük bir iş parçacığı havuzuna izin verir. Uzun süre çalışan bir zaman uyumlu görevin tamamlanmasını beklemek yerine, iş parçacığı başka bir istek üzerinde çalışabilir.

ASP.NET Core uygulamalarda yaygın bir performans sorunu, zaman uyumsuz olabilecek çağrıları engelliyor. Birçok zaman uyumlu engelleme çağrısı, [Iş parçacığı havuzu](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) ve azaltılmış yanıt sürelerinin oluşmasına yol açabilir.

**Şunları yapın**:

* [Task. Wait](/dotnet/api/system.threading.tasks.task.wait) veya [Task. Result](/dotnet/api/system.threading.tasks.task-1.result)çağırarak zaman uyumsuz yürütmeyi engelleyin.
* Ortak kod yollarındaki kilitleri alın. ASP.NET Core uygulamalar, kodu paralel olarak çalıştırmak için tasarlanmış olduğunda en iyi performansı sağlar.
* [Task. Run](/dotnet/api/system.threading.tasks.task.run) çağırın ve hemen bekler. ASP.NET Core, uygulama kodunu normal Iş parçacığı havuzu iş parçacıklarında zaten çalıştırıyor, bu nedenle görevi çağırıyor. yalnızca ek gereksiz Iş parçacığı havuzu zamanlaması ile sonuçları çalıştırın. Zamanlanan kod bir iş parçacığını engelleyebilse bile, Task. Run bunu engellemez.

**Şunları yapın**:

* [Etkin kod yollarını](#understand-hot-code-paths) zaman uyumsuz yapın.
* Zaman uyumsuz bir API kullanılabiliyorsa veri erişimi, g/ç ve uzun süre çalışan işlem API 'Lerini çağrı zaman uyumsuz olarak çağırın. Bir synchronus API 'SI zaman uyumsuz yapmak için [Task. Run](/dotnet/api/system.threading.tasks.task.run) **kullanmayın.**
* Denetleyici/ Razor sayfa eylemlerini zaman uyumsuz yapın. [Zaman uyumsuz/await](/dotnet/csharp/programming-guide/concepts/async/) desenlerinden faydalanmak için tüm çağrı yığını zaman uyumsuzdur.

[Iş parçacığı havuzuna](/windows/desktop/procthread/thread-pools)sık sık eklenen iş parçacıklarını bulmak Için [PerfView](https://github.com/Microsoft/perfview)gibi bir profil oluşturucu kullanılabilir. `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`Olay, iş parçacığı havuzuna eklenen bir iş parçacığını gösterir. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>Büyük nesne ayırmalarını en aza indir

[.NET Core atık toplayıcısı](/dotnet/standard/garbage-collection/) , ASP.NET Core uygulamalarda otomatik olarak bellek ayırmayı ve serbest bırakma işlemini yönetir. Otomatik atık toplama işlemi, geliştiricilerin belleğin nasıl veya ne zaman boşaltılana ilişkin endişelenmek zorunda olmadığı anlamına gelir. Ancak, başvurulmayan nesnelerin temizlenmesi CPU süresi alırsa, geliştiricilerin [etkin kod yollarındaki](#understand-hot-code-paths)nesneleri ayırmayı en aza indirmeleri gerekir. Çöp toplama özellikle büyük nesneler üzerinde pahalıdır (> 85 K bayt). Büyük nesneler [büyük nesne yığınında](/dotnet/standard/garbage-collection/large-object-heap) depolanır ve temizlemek için tam (2. nesil) çöp toplama gerektirir. Nesil 0 ve 1. nesil koleksiyonlarının aksine, 2. nesil bir koleksiyon, uygulama yürütmenin geçici olarak askıya alınmasını gerektirir. Büyük nesnelerin sık aralıklarla ayrılması ve ayrılması, tutarsız performansa neden olabilir.

Öneri

* Sık kullanılan büyük nesneleri önbelleğe **almayı düşünün.** Büyük nesnelerin önbelleğe alınması pahalı ayırmaları önler.
* Büyük dizileri depolamak için [Arraypool \<T> ](/dotnet/api/system.buffers.arraypool-1) kullanarak havuz arabellekleri **yapın** .
* [Sık erişimli kod yollarında](#understand-hot-code-paths)çok sayıda, kısa süreli büyük **nesneler ayırmayın** .

Yukarıdaki gibi bellek sorunları, [PerfView](https://github.com/Microsoft/perfview) ve İnceleme içindeki çöp toplama (GC) istatistiklerini inceleyerek tanılanabilir:

* Çöp toplama duraklatma süresi.
* Çöp toplama işlemi için işlemci zamanının yüzde kaçına harcanması.
* Kaç çöp toplama 0, 1 ve 2. nesil.

Daha fazla bilgi için bkz. [çöp toplama ve performans](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Veri erişimini ve g/ç 'yi iyileştirme

Veri deposuna ve diğer uzak hizmetlere sahip etkileşimler genellikle ASP.NET Core uygulamasının en yavaş parçalarından oluşur. Verileri etkili bir şekilde okumak ve yazmak iyi bir performans için önemlidir.

Öneri

* Tüm veri erişim API 'Lerini zaman uyumsuz **olarak çağırın.**
* Gerekenden daha fazla **veri alınamaz.** Yalnızca geçerli HTTP isteği için gerekli olan verileri döndürmek için sorgular yazın.
* Güncel olmayan veriler kabul edilebilir ise, bir veritabanından veya uzak hizmetten alınan sık erişilen verileri önbelleğe **almayı düşünün.** Senaryoya bağlı olarak, bir [MemoryCache](xref:performance/caching/memory) veya [DistributedCache](xref:performance/caching/distributed)kullanın. Daha fazla bilgi için bkz. <xref:performance/caching/response>.
* Ağ gidiş dönüşlerini **en aza** indirir. Amaç, birkaç çağrı yerine, gerekli verileri tek bir çağrıda almak olur.
* Salt okuma amacıyla verilere erişirken Entity Framework Core [izleme sorguları](/ef/core/querying/tracking#no-tracking-queries) **kullanmayın.** EF Core, hiçbir izleme sorgusunun sonuçlarını daha verimli bir şekilde döndürebilir.
* **Filtrenin** `.Where` veritabanı tarafından GERÇEKLEŞTIRILMESI için, LINQ sorgularını filtreleyin ve toplayın ( `.Select` Örneğin, veya `.Sum` deyimleri ile).
* EF Core, istemci üzerindeki bazı sorgu işleçlerini çözdüğünü, bu da verimsiz sorgu yürütmeye neden **olabileceğini göz önünde** bulundurun. Daha fazla bilgi için bkz. [istemci değerlendirmesi performans sorunları](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* Koleksiyonlar üzerinde İzdüşüm sorguları kullanmayın ve bu, "N + 1" SQL sorgularının **yürütülmeleriyle** sonuçlanabilir. Daha fazla bilgi için bkz. [bağıntılı alt sorguları iyileştirme](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Yüksek ölçekli uygulamalarda performansı iyileştirebilecek yaklaşımlar için bkz. [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) :

* [DbContext havuzu](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Açıkça derlenmiş sorgular](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Kod tabanını çalıştırmadan önce, önceki yüksek performanslı yaklaşımların etkisini ölçmenizi öneririz. Derlenmiş sorguların ek karmaşıklığı performans iyileştirmesini engelleyebilir.

Sorgu sorunları, [Application Insights](/azure/application-insights/app-insights-overview) veya profil oluşturma araçlarıyla verilere erişirken harcanan süreyi inceleyerek algılanabilir. Çoğu veritabanı Ayrıca, sık çalıştırılan sorgularla ilgili istatistikleri de kullanılabilir hale getirir.

## <a name="pool-http-connections-with-httpclientfactory"></a>HttpClientFactory ile HTTP bağlantılarını havuz

[HttpClient](/dotnet/api/system.net.http.httpclient) arabirimini uyguluyor olsa da `IDisposable` , yeniden kullanım için tasarlanmıştır. Kapalı `HttpClient` örnekler, yuvaları `TIME_WAIT` kısa bir süre için durumunda açık bırakır. Nesneleri oluşturan ve kaldırdık bir kod yolu `HttpClient` sık sık kullanılırsa, uygulama kullanılabilir yuvaları tüketebilir. [Httpclientfactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) , bu soruna çözüm olarak ASP.NET Core 2,1 ' de tanıtılmıştı. Performansı ve güvenilirliği iyileştirmek için havuz HTTP bağlantılarını işler.

Öneri

* Örnekleri doğrudan **oluşturma ve** atma `HttpClient` .
* **Do** Örnek almak Için [httpclientfactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) kullanın `HttpClient` . Daha fazla bilgi için bkz. [Esnek http isteklerini uygulamak Için HttpClientFactory kullanma](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Ortak kod yollarını hızlı tutun

Tüm kodunuzun hızlı olmasını istiyorsunuz. Yaygın olarak çağrılan kod yolları en kritik öneme sahiptir. Bu güncelleştirmeler şunlardır:

* Uygulamanın istek işleme ardışık düzeninde bulunan ara yazılım bileşenleri, özellikle de ara yazılım ardışık düzende çalışır. Bu bileşenlerin performansı üzerinde büyük bir etkisi vardır.
* Her istek için veya istek başına birden çok kez yürütülen kod. Örneğin, özel günlük kaydı, yetkilendirme işleyicileri veya geçici Hizmetleri başlatma.

Öneri

* Uzun süre çalışan görevlerle özel ara yazılım **bileşenleri kullanmayın.**
* [Etkin kod yollarını](#understand-hot-code-paths)belirlemek Için, [Visual Studio tanılama araçları](/visualstudio/profiling/profiling-feature-tour) veya [PerfView](https://github.com/Microsoft/perfview)gibi performans profil oluşturma **araçlarını kullanın.**

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Uzun süre çalışan görevleri http isteklerinin dışında Tamam

ASP.NET Core uygulamasına yönelik çoğu istek, gerekli Hizmetleri çağıran ve HTTP yanıtı döndüren bir denetleyici veya sayfa modeli tarafından işlenebilir. Uzun süre çalışan görevleri içeren bazı istekler için, tüm istek-yanıt sürecini zaman uyumsuz hale getirmek daha iyidir.

Öneri

* Olağan HTTP istek işlemenin bir parçası olarak uzun süre çalışan görevlerin **tamamlanmasını beklememe** .
* [Arka plan hizmetleri](xref:fundamentals/host/hosted-services) ile uzun süreli istekleri işlemeyi veya bir [Azure işlevi](/azure/azure-functions/)ile işlem dışı **bırakmayı düşünün.** İşlem dışı iş tamamlama, özellikle CPU yoğun görevler için faydalıdır.
* İstemcilerle zaman uyumsuz iletişim kurmak için gibi gerçek zamanlı iletişim **seçenekleri kullanın** [SignalR](xref:signalr/introduction) .

## <a name="minify-client-assets"></a>İstemci varlıklarını küçültmeye yönelik

Karmaşık ön uçları olan ASP.NET Core uygulamalar sıklıkla birçok JavaScript, CSS veya görüntü dosyası sunar. İlk yük isteklerinin performansı şu şekilde geliştirilebilir:

* Birden çok dosyayı bir içinde birleştiren paketleme.
* Boşluk ve açıklamaları kaldırarak dosyaların boyutunu azaltan minifying.

Öneri

* ASP.NET Core, istemci varlıklarını paketleme ve küçültmeye yönelik [yerleşik desteğini](xref:client-side/bundling-and-minification) **kullanın.**
* Karmaşık istemci varlık yönetimi için [WebPack](https://webpack.js.org/)gibi diğer üçüncü taraf **araçları göz önünde** bulundurun.

## <a name="compress-responses"></a>Yanıtları sıkıştır

 Yanıt boyutunu azaltmak genellikle önemli ölçüde önemli ölçüde bir uygulamanın yanıt hızını artırır. Yük boyutlarını azaltmanın bir yolu, uygulamanın yanıtlarını sıkıştırmaktır. Daha fazla bilgi için bkz. [Yanıt sıkıştırması](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>En son ASP.NET Core sürümü kullan

ASP.NET Core her yeni sürümü performans iyileştirmeleri içerir. .NET Core ve ASP.NET Core iyileştirmeler, daha yeni sürümlerin genellikle eski sürümlerin genel olarak gerçekleştirdiği anlamına gelir. Örneğin, .NET Core 2,1, derlenmiş normal ifadeler ve [benefitted 'dan gelen \<T> ](https://msdn.microsoft.com/magazine/mt814808.aspx)destek eklendi. ASP.NET Core 2,2 HTTP/2 desteği eklendi. ASP.NET Core 3,0, bellek kullanımını azaltan ve üretilen işi geliştiren [birçok geliştirme ekler](xref:aspnetcore-3.0) . Performans bir önceliktir, ASP.NET Core güncel sürümüne yükseltmeyi göz önünde bulundurun.

## <a name="minimize-exceptions"></a>Özel durumları Küçült

Özel durumlar nadir olmalıdır. Özel durumları oluşturma ve yakalama, diğer kod akışı desenlerine göre yavaş olur. Bu nedenle, normal program akışını denetlemek için özel durumlar kullanılmamalıdır.

Öneri

* Özel durumları, özellikle de [sık erişimli kod yollarında](#understand-hot-code-paths)normal program akışının bir yolu olarak oluşturma veya **yakalama kullanmayın.**
* Özel duruma neden olacak koşulları tespit etmek ve işlemek için uygulamaya **mantığı dahil edin** .
* Olağan dışı veya beklenmedik koşullarda özel **durumlar oluşturun veya** yakalayın.

Application Insights gibi uygulama tanılama araçları, bir uygulamadaki performansı etkileyebilecek ortak özel durumları belirlemesine yardımcı olabilir.

## <a name="performance-and-reliability"></a>Performans ve güvenilirlik

Aşağıdaki bölümlerde performans ipuçları ve bilinen güvenilirlik sorunları ve çözümleri sağlanmaktadır.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>HttpRequest/HttpResponse gövdesinde zaman uyumlu okuma veya yazma yapmaktan kaçının

ASP.NET Core tüm g/ç zaman uyumsuzdur. Sunucular `Stream` , hem zaman uyumlu hem de zaman uyumsuz aşırı yüklemeleri olan arabirimini uygular. İş parçacığı havuzu iş parçacıklarını engellemeyi önlemek için zaman uyumsuz olanlar tercih edilmelidir. İş parçacıklarını engelleme, iş parçacığı havuzunda ortaya çıkmasına neden olabilir.

Bunu **yapın:** Aşağıdaki örnek öğesini kullanır <xref:System.IO.StreamReader.ReadToEnd*> . Sonuç için beklemek üzere geçerli iş parçacığını engeller. Bu, [zaman uyumsuz olarak eşitleme](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)örneğidir.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

Yukarıdaki kodda, `Get` http istek gövdesinin tamamını belleğe eşzamanlı olarak okur. İstemci yavaş karşıya yüklendikten sonra, uygulama zaman uyumsuz olarak eşitlenir. Kestrel zaman uyumlu **okumaları desteklemediğinden,** uygulama zaman uyumsuz olarak eşitlenir.

**Bunu yapın:** Aşağıdaki örnek, <xref:System.IO.StreamReader.ReadToEndAsync*> okurken iş parçacığını engellemez.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Yukarıdaki kod, HTTP istek gövdesinin tamamını belleğe zaman uyumsuz olarak okur.

> [!WARNING]
> İstek büyükse HTTP istek gövdesinin tamamını belleğe okumak bellek yetersiz (OOM) koşuluna yol açabilir. OOM, hizmet reddine neden olabilir.  Daha fazla bilgi için, bu belgedeki [büyük istek gövdelerini veya Yanıt gövdelerinin belleğe okunmasını önleyin](#arlb) .

**Bunu yapın:** Aşağıdaki örnek, arabelleğe alınmamış bir istek gövdesi kullanılarak tamamen zaman uyumsuzdur:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Yukarıdaki kod, istek gövdesini bir C# nesnesine zaman uyumsuz olarak serileştirir.

## <a name="prefer-readformasync-over-requestform"></a>Istek üzerinde ReadFormAsync tercih et. form

`HttpContext.Request.ReadFormAsync`Yerine kullanın `HttpContext.Request.Form` .
`HttpContext.Request.Form`yalnızca aşağıdaki koşullara göre güvenle okunabilir:

* Form, bir çağrısıyla okundu `ReadFormAsync` ve
* Önbelleğe alınan form değeri şu kullanılarak okunmakta`HttpContext.Request.Form`

Bunu **yapın:** Aşağıdaki örnek kullanılmıştır `HttpContext.Request.Form` .  `HttpContext.Request.Form`, [zaman uyumsuz olarak eşitleme](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) kullanır ve iş parçacığı havuzunda ortaya çıkmasına neden olabilir.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Bunu yapın:** Aşağıdaki örnek, `HttpContext.Request.ReadFormAsync` form gövdesini zaman uyumsuz olarak okumak için kullanır.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Büyük istek gövdelerini veya yanıt gövdelerini belleğe okumaktan kaçının

.NET ' te, 85 KB 'den büyük olan her nesne ayırması büyük nesne yığınında ([Loh](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) sona erer. Büyük nesneler iki şekilde pahalıdır:

* Yeni ayrılan büyük bir nesne için belleğin temizlenmesi gerektiğinden, ayırma maliyeti yüksektir. CLR, tüm yeni ayrılmış nesneler için belleğin temizlenmiş olmasını garanti eder.
* LOH, yığının geri kalanı ile toplanır. LOH, tam [atık toplama](/dotnet/standard/garbage-collection/fundamentals) veya [Gen2 koleksiyonu](/dotnet/standard/garbage-collection/fundamentals#generations)gerektirir.

Bu [blog gönderisi](https://adamsitnik.com/Array-Pool/#the-problem) succinctly sorununu açıklar:

> Büyük bir nesne ayrıldığında, Gen 2 nesnesi olarak işaretlenir. Küçük nesneler için Gen 0 değildir. Sonuçlar LOH 'de bellek tükeniyorsa, GC yalnızca LOH değil, yönetilen yığının tamamını temizler. Bu nedenle, LOH dahil olmak üzere Gen 0, Gen 1 ve Gen 2 ' yi temizler. Bu, tam atık toplama olarak adlandırılır ve en çok kullanılan çöp toplamadır. Birçok uygulama için kabul edilebilir. Ancak, ortalama bir web isteğini işlemek için çok büyük bellek arabelleklerinin (bir yuvadan okunan, sıkıştırmayı açık olan JSON & daha fazla kod çözme) gerekli olduğu yüksek performanslı Web sunucuları için kesinlikle değildir.

Büyük bir isteği veya Yanıt gövdesini tek bir veya ' a bir veya daha fazla depolama `byte[]` `string` :

* LOH 'de hızlı bir şekilde boş alan tükenmenize neden olabilir.
* Çalıştıran tam GC 'Ler nedeniyle uygulama için performans sorunlarına neden olabilir.

## <a name="working-with-a-synchronous-data-processing-api"></a>Zaman uyumlu veri işleme API 'SI ile çalışma

Yalnızca zaman uyumlu okuma ve yazma işlemlerini destekleyen bir serileştirici/devre dışı bırakma kullanılırken (örneğin, [JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* Verileri seri hale getirici/devre dışı serileştiriciye geçirmeden önce zaman uyumsuz olarak belleğe arabelleğe ın.

> [!WARNING]
> İstek büyükse, bellek yetersiz (OOM) koşuluna yol açabilir. OOM, hizmet reddine neden olabilir.  Daha fazla bilgi için, bu belgedeki [büyük istek gövdelerini veya Yanıt gövdelerinin belleğe okunmasını önleyin](#arlb) .

ASP.NET Core 3,0, <xref:System.Text.Json> JSON serileştirme için varsayılan olarak kullanır. <xref:System.Text.Json>:

* JSON 'yi zaman uyumsuz olarak okur ve yazar.
* UTF-8 metni için iyileştirilmiştir.
* Genellikle daha yüksek performans `Newtonsoft.Json` .

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Bir alanda ıhttpcontextaccessor. HttpContext depolamayın

[Ihttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) , `HttpContext` istek iş parçacığından erişildiğinde etkin isteğin ' i döndürür. , `IHttpContextAccessor.HttpContext` Bir **not** alan veya değişkende depolanmamalıdır.

Bunu **yapın:** Aşağıdaki örnek `HttpContext` öğesini bir alanında depolar ve daha sonra kullanmaya çalışır.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Yukarıdaki kod, oluşturucuda genellikle null veya yanlış yakalar `HttpContext` .

**Bunu yapın:** Aşağıdaki örnek:

* <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>Alanını bir alanında depolar.
* `HttpContext`Alanı doğru zamanda kullanır ve kontrol eder `null` .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Birden çok iş parçacığından HttpContext 'e erişme

`HttpContext`, iş parçacığı açısından güvenli *değildir* . `HttpContext`Paralel olarak birden çok iş parçacığından erişilmesi, askıda kalma, kilitlenme ve veri bozulması gibi tanımsız davranışlara neden olabilir.

Bunu **yapın:** Aşağıdaki örnek üç paralel istek yapar ve giden HTTP isteğinden önce ve sonra gelen istek yolunu günlüğe kaydeder. İstek yoluna, potansiyel olarak paralel olarak birden çok iş parçacığından erişilir.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Bunu yapın:** Aşağıdaki örnek, üç paralel isteği yapmadan önce gelen istekten tüm verileri kopyalar.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>İstek tamamlandıktan sonra HttpContext 'i kullanma

`HttpContext`yalnızca ASP.NET Core işlem hattında etkin bir HTTP isteği olduğu sürece geçerlidir. Tüm ASP.NET Core işlem hattı, her isteği yürüten zaman uyumsuz temsilciler zinciridir. `Task`Bu zincirden döndürülen işlem tamamlandığında, geri dönüştürülür `HttpContext` .

Bunu **yapın:** Aşağıdaki örnek, `async void` ilk kez ULAŞıLDıĞıNDA http isteğinin tamamlanmasını sağlar `await` :

* ASP.NET Core uygulamalarda bu **her zaman** hatalı bir uygulamadır.
* `HttpResponse`Http isteği tamamlandıktan sonra öğesine erişir.
* İşlemi çöker.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Bunu yapın:** Aşağıdaki örnek çerçevesine bir döndürür `Task` , bu nedenle http isteği eylem tamamlanana kadar tamamlanmaz.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Arka plan iş parçacıklarında HttpContext 'i yakalama

Bunu **yapın:** Aşağıdaki örnekte, bir kapanışın özelliğinden yakalanırken bir kapanış gösterilmektedir `HttpContext` `Controller` . Bu kötü bir uygulamadır çünkü iş öğesi şu şekilde olabilir:

* İstek kapsamının dışında çalıştırın.
* Yanlış okuma girişimi `HttpContext` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Bunu yapın:** Aşağıdaki örnek:

* İstek sırasında arka plan görevinde gereken verileri kopyalar.
* Denetleyiciden hiçbir şeye başvurmuyor.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Arka plan görevleri barındırılan hizmet olarak uygulanmalıdır. Daha fazla bilgi için bkz. [barındırılan hizmetlerle arka plan görevleri](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Arka plan iş parçacıklarında denetleyicilere eklenen Hizmetleri yakalama

Bunu **yapın:** Aşağıdaki örnek, bir kapanışın `DbContext` `Controller` eylem parametresinden yakalama işlemini gösterir. Bu kötü bir uygulamadır.  İş öğesi, istek kapsamı dışında çalıştırılabilir. , `ContosoDbContext` İstek kapsamına alınır ve buna yol açar `ObjectDisposedException` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Bunu yapın:** Aşağıdaki örnek:

* <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>Arka plan iş öğesinde bir kapsam oluşturmak için bir oluşturur. `IServiceScopeFactory`tek bir.
* Arka plan iş parçacığında yeni bir bağımlılık ekleme kapsamı oluşturur.
* Denetleyiciden hiçbir şeye başvurmuyor.
* `ContosoDbContext`Gelen istekten yakalanmaz.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Aşağıdaki vurgulanan kod:

* Arka plan işleminin yaşam süresi boyunca bir kapsam oluşturur ve Hizmetleri bundan çözer.
* `ContosoDbContext`Doğru kapsamdan kullanır.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Yanıt gövdesi başlatıldıktan sonra durum kodunu veya başlıkları değiştirmeyin

ASP.NET Core HTTP yanıt gövdesini arabelleğe almaz. Yanıtın ilk yazıldığı zaman:

* Üst bilgiler, bu gövdenin öbek ile birlikte gönderilir.
* Artık yanıt üst bilgilerini değiştirmek mümkün değildir.

Bunu **yapın:** Aşağıdaki kod, yanıt önceden başlatıldıktan sonra yanıt üst bilgileri eklemeye çalışır:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

Önceki kodda, `context.Response.Headers["test"] = "test value";` yanıta yazılmışsa bir özel durum oluşturur `next()` .

**Bunu yapın:** Aşağıdaki örnek, üst bilgileri değiştirmeden önce HTTP yanıtının başlatılıp başlatılmadığını denetler.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Bunu yapın:** Aşağıdaki örnek, `HttpResponse.OnStarting` yanıt üst bilgileri istemciye temizlenmeden önce üst bilgileri ayarlamak için kullanır.

Yanıtın başlatılmamış olup olmadığı denetleniyor yanıt üst bilgileri yazılmadan önce çağrılacak geri aramanın kaydedilmesini sağlar. Yanıtın başlatılmamış olup olmadığı denetleniyor:

* Başlıkları tam zamanında ekleme veya geçersiz kılma olanağı sağlar.
* İşlem hattındaki bir sonraki ara yazılım hakkında bilgi gerektirmez.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Yanıt gövdesine yazmaya başladıysanız ileri () çağrısı yapın

Bileşenler yalnızca yanıtı işlemek ve işlemek için mümkünse çağrılabilir.
