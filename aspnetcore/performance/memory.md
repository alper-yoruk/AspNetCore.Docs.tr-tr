---
title: ASP.NET Core bellek yönetimi ve desenleri
author: rick-anderson
description: ASP.NET Core ' de belleğin nasıl yönetildiğini ve çöp toplayıcı 'nın (GC) nasıl çalıştığını öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- appsettings.json
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
uid: performance/memory
ms.openlocfilehash: 6d2a89ec7c64728bc585ad235293f2277f9a66f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061489"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>ASP.NET Core 'de bellek yönetimi ve çöp toplama (GC)

[Sébastien Ros](https://github.com/sebastienros) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

Bellek yönetimi, .NET gibi yönetilen bir çerçevede bile karmaşıktır. Bellek sorunlarını analiz etmek ve anlamak zor olabilir. Bu makalede:

* , Çok fazla *bellek sızıntısı* ve *GC çalışmasız* sorunlar tarafından tetiklendi. Bu sorunların çoğu, bellek tüketiminin .NET Core 'da nasıl çalıştığını Anlamamasından veya nasıl ölçüleceğini anlayamadığında oluşur.
* Sorunlu bellek kullanımını gösterir ve alternatif yaklaşımlar önerir.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Çöp toplama (GC) .NET Core 'da nasıl kullanılır

GC, her segmentin bitişik bellek aralığı olduğu yığın kesimlerini ayırır. Yığına yerleştirilmiş nesneler 3 nesilden birine kategorize edilir: 0, 1 veya 2. Oluşturma, GC 'nin, uygulama tarafından artık başvurulmayan yönetilen nesneler üzerinde bellek serbest bırakmaya yönelik sıklığı belirler. Daha düşük numaralandırılmış nesiller GC daha sık kullanılır.

Nesneler, yaşam sürelerinin ömrü temelinde bir neslin diğerine taşınır. Nesneler daha uzun bir süre içinde yaşlılarsa daha yüksek bir oluşturmaya taşınır. Daha önce belirtildiği gibi, daha yüksek neslikler GC 'yi daha düşüktür. Kısa vadeli süreli nesneler her zaman nesil 0 ' da kalır. Örneğin, bir Web isteğinin ömrü boyunca başvurulan nesneler kısa ömürlü değildir. Uygulama düzeyi [tekton](xref:fundamentals/dependency-injection#service-lifetimes) genellikle 2. nesil 'e geçirilir.

ASP.NET Core bir uygulama başlatıldığında GC:

* İlk yığın kesimleri için bazı belleği ayırır.
* Çalışma zamanı yüklenirken belleğin küçük bir bölümünü kaydeder.

Önceki bellek ayırmaları performans nedenleriyle yapılır. Performans avantajı, ardışık bellekteki yığın kesimlerinden gelir.

### <a name="call-gccollect"></a>GC 'yi çağırın. Topladıktan

[GC çağrılıyor. Açıkça topla](xref:System.GC.Collect*) :

* , Üretim ASP.NET Core uygulamaları tarafından **yapılmamalıdır.**
* Bellek sızıntılarını araştırırken faydalıdır.
* İnceleme yaparken, GC 'nin tüm sallaştırılmış nesneleri bellekten kaldırdığını doğrular, böylece bellek ölçülenebilir.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Uygulamanın bellek kullanımını analiz etme

Adanmış araçlar bellek kullanımının analiz edilmesine yardımcı olabilir:

- Nesne başvurularını sayma
- GC 'nin CPU kullanımında ne kadar etkili olduğunu ölçme
- Her nesil için kullanılan bellek alanını ölçme

Bellek kullanımını çözümlemek için aşağıdaki araçları kullanın:

* [DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace): üretim makinelerinde kullanılabilir.
* [Visual Studio hata ayıklayıcısı olmadan bellek kullanımını analiz etme](/visualstudio/profiling/memory-usage-without-debugging2)
* [Visual Studio’da bellek kullanımının profilini oluşturma](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Bellek sorunlarını algılama

Görev Yöneticisi, bir ASP.NET uygulamasının ne kadar bellek kullandığını gösteren bir fikir almak için kullanılabilir. Görev Yöneticisi bellek değeri:

* ASP.NET işlemi tarafından kullanılan bellek miktarını temsil eder.
* Uygulamanın oturma nesnelerini ve yerel bellek kullanımı gibi diğer bellek tüketicilerini içerir.

Görev Yöneticisi bellek değeri sonsuza kadar artıyorsa ve hiçbir şekilde düzermez, uygulamanın bellek sızıntısı vardır. Aşağıdaki bölümlerde, çeşitli bellek kullanımı desenleri gösterilmektedir ve açıklanmaktadır.

## <a name="sample-display-memory-usage-app"></a>Örnek görüntüleme belleği kullanım uygulaması

[Memoryleak örnek uygulaması](https://github.com/sebastienros/memoryleak) GitHub ' da kullanılabilir. MemoryLeak uygulaması:

* , Uygulamanın gerçek zamanlı bellek ve GC verilerini toplayan bir tanılama denetleyicisi içerir.
* , Bellek ve GC verilerini görüntüleyen bir dizin sayfasına sahiptir. Dizin sayfası her saniye yenilenir.
* Çeşitli bellek yük desenleri sağlayan bir API denetleyicisi içerir.
* Desteklenen bir araç değildir, ancak ASP.NET Core uygulamaların bellek kullanım düzenlerini göstermek için kullanılabilir.

MemoryLeak 'yi çalıştırın. Ayrılan bellek, GC gerçekleşene kadar yavaş artar. Araç, verileri yakalamak için özel nesne ayırdığından bellek artar. Aşağıdaki görüntüde bir gen 0 GC gerçekleştiğinde MemoryLeak Dizin sayfası gösterilmektedir. API denetleyicisinden API uç noktası çağrılmadığından grafik 0 RPS (saniye başına Istek) gösterir.

![önceki grafik](memory/_static/0RPS.png)

Grafik, bellek kullanımı için iki değer görüntüler:

- Ayrılan: yönetilen nesnelerin kapladığı bellek miktarı
- [Çalışma kümesi](/windows/win32/memory/working-set): Şu anda fiziksel bellekte bulunan işlemin sanal adres alanındaki sayfa kümesi. Gösterilen çalışma kümesi, Görev Yöneticisi 'nin görüntülediği değerdir.

### <a name="transient-objects"></a>Geçici nesneler

Aşağıdaki API 10 KB 'lik bir dize örneği oluşturur ve istemciye döndürür. Her istekte, bellekte yeni bir nesne ayrılır ve yanıta yazılır. Dizeler .NET 'te UTF-16 karakter olarak depolanır, böylece her karakter bellekte 2 bayt sürer.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Aşağıdaki grafik, ' de, bellek ayırmalarının GC tarafından nasıl etkilendiğini göstermek için görece küçük bir yük ile oluşturulmuştur.

![önceki grafik](memory/_static/bigstring.png)

Yukarıdaki grafik şunları gösterir:

* 4K RPS (saniye başına Istek).
* Nesil 0 GC koleksiyonları her iki saniyede bir gerçekleşir.
* Çalışma kümesi yaklaşık 500 MB 'tan sabittir.
* CPU %12 ' dir.
* Bellek tüketimi ve sürümü (GC aracılığıyla) kararlı bir şekilde yapılır.

Aşağıdaki grafik, makine tarafından işlenebilen en fazla aktarım hızı üzerinden alınır.

![önceki grafik](memory/_static/bigstring2.png)

Yukarıdaki grafik şunları gösterir:

* 22K RPS
* Nesil 0 GC koleksiyonları saniye başına birkaç kez gerçekleşir.
* 1. nesil koleksiyonlar, uygulama saniye başına önemli ölçüde daha fazla bellek ayırdığından tetiklenir.
* Çalışma kümesi yaklaşık 500 MB 'tan sabittir.
* CPU %33 ' dir.
* Bellek tüketimi ve sürümü (GC aracılığıyla) kararlı bir şekilde yapılır.
* CPU (%33) aşırı kullanılmaz, bu nedenle çöp toplama işlemi yüksek sayıda ayırma ile devam edebilir.

### <a name="workstation-gc-vs-server-gc"></a>Workstation GC vs. Server GC

.NET atık toplayıcısı 'nda iki farklı mod vardır:

* **Iş Istasyonu GC** : Masaüstü için iyileştirildi.
* **Sunucu GC** . ASP.NET Core uygulamalar için varsayılan GC. Sunucu için iyileştirildi.

GC modu, proje dosyasında veya yayımlanan uygulamanın dosyası *runtimeconfig.js* açık olarak ayarlanabilir. Aşağıdaki biçimlendirme `ServerGarbageCollection` Proje dosyasındaki ayarı gösterir:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

`ServerGarbageCollection`Proje dosyasında değiştirme uygulamanın yeniden oluşturulmasını gerektirir.

**Note:** Sunucu çöp toplama, tek çekirdekli **makinelerde kullanılamaz.** Daha fazla bilgi için bkz. <xref:System.Runtime.GCSettings.IsServerGC>.

Aşağıdaki görüntüde, Workstation GC kullanarak bir 5K RPS altındaki bellek profili gösterilmektedir.

![önceki grafik](memory/_static/workstation.png)

Bu grafik ve sunucu sürümü arasındaki farklar önemlidir:

- Çalışma kümesi 500 MB ile 70 MB arasında bırakılır.
- GC, her iki saniyede yerine saniyede birden çok kez 1. nesil koleksiyonu yapar.
- GC 300 MB 'den 10 MB 'a düşün.

Tipik bir Web sunucusu ortamında CPU kullanımı bellekten daha önemlidir, bu nedenle sunucu GC daha iyidir. Bellek kullanımı yüksekse ve CPU kullanımı nispeten düşükse, Iş Istasyonu GC daha iyi olabilir. Örneğin, belleğin scarce olduğu çeşitli Web uygulamalarını barındıran yüksek yoğunluklu.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>Docker ve küçük kapsayıcılar kullanılarak GC

Bir makinede birden çok Kapsayıcılı uygulama çalıştığında, Iş Istasyonu GC sunucu GC 'den daha preformant olabilir. Daha fazla bilgi için, bkz. [bir küçük kapsayıcıda Server GC Ile çalıştırma](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) ve [küçük bir KAPSAYıCı senaryosunda sunucu GC ile bırlıkte çalıştırma-GC yığını için sabit sınır](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).

### <a name="persistent-object-references"></a>Kalıcı nesne başvuruları

GC, başvurulan nesneleri serbest olamaz. Başvurulan ancak artık gerekli olmayan nesneler bellek sızıntısına neden olur. Uygulama genellikle nesneleri ayırırsa ve artık gerekli olmadıklarında bunları boşaltamazsa, bellek kullanımı zaman içinde artar.

Aşağıdaki API 10 KB 'lik bir dize örneği oluşturur ve istemciye döndürür. Önceki örnekle aradaki fark, bu örneğe statik bir üye tarafından başvuruluyorsa, yani koleksiyon için hiçbir şekilde kullanılamaz.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

Yukarıdaki kod:

* Tipik bir bellek sızıntısı örneğidir.
* Sık yapılan çağrılar sayesinde, işlem bir özel durumla çökene kadar uygulama belleğinin artmasına neden olur `OutOfMemory` .

![önceki grafik](memory/_static/eternal.png)

Önceki görüntüde:

* Yük testi, `/api/staticstring` uç nokta bellekte doğrusal artışa neden olur.
* GC, bellek baskısı arttıkça 2. nesil bir koleksiyon çağırarak belleği boşaltmaya çalışır.
* GC, sızdırılan belleği serbest olamaz. Ayrılan ve çalışma kümesi zaman ile artar.

Önbelleğe alma gibi bazı senaryolar, bellek baskısı serbest bırakılana kadar nesne başvurularının tutulmasını gerektirir. <xref:System.WeakReference>Sınıfı bu tür bir önbelleğe alma kodu için kullanılabilir. Bir `WeakReference` nesne, bellek baskılarına altında toplanır. Varsayılan <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> kullanım uygulamasıdır `WeakReference` .

### <a name="native-memory"></a>Yerel bellek

Bazı .NET Core nesneleri yerel belleğe bağımlıdır. Yerel bellek GC tarafından **toplanamaz.** Yerel bellek kullanan .NET nesnesi yerel kod kullanarak onu serbest vermelidir.

.NET, <xref:System.IDisposable> geliştiricilerin yerel bellek yayınlamasına izin vermek için arabirim sağlar. <xref:System.IDisposable.Dispose*>Çağrılmasa bile, Sonlandırıcı çalıştırıldığında doğru uygulanmış sınıflar çağrısı `Dispose` . [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors)

Aşağıdaki kodu inceleyin:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[Physicalfileprovider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) yönetilen bir sınıftır, bu nedenle isteğin sonunda herhangi bir örnek toplanacaktır.

Aşağıdaki görüntüde, API 'yi sürekli çağırırken bellek profili gösterilmektedir `fileprovider` .

![önceki grafik](memory/_static/fileprovider.png)

Yukarıdaki grafikte, bu sınıfın uygulanmasıyla ilgili olarak, bellek kullanımının artmasının devam eden belirgin bir sorun gösterilmektedir. Bu, [Bu sorun](https://github.com/dotnet/aspnetcore/issues/3110)içinde izlenmekte olan bilinen bir sorundur.

Kullanıcı kodunda, aşağıdakilerden biri ile aynı sızıntı gerçekleşecektir:

* Sınıf doğru şekilde serbest bırakılmıyor.
* Atılan bağımlı nesnelerin yöntemini çağırmak için foralıtıon `Dispose` .

### <a name="large-objects-heap"></a>Büyük nesne yığını

Sık bellek ayırma/ücretsiz döngüler, özellikle büyük bellek öbekleri ayrılırken belleği parçalara ayırıyor. Nesneler bitişik bellek bloklarına ayrılır. Parçalanmayı azaltmak için, GC belleği serbest bırakır, birleştirmeyi dener. Bu işleme **sıkıştırma** adı verilir. Sıkıştırma, nesneleri taşımayı içerir. Büyük nesnelerin taşınması, bir performans cezası getirir. Bu nedenle GC, büyük [nesne yığını](/dotnet/standard/garbage-collection/large-object-heap) (LOH) olarak adlandırılan _büyük_ nesneler için özel bir bellek bölgesi oluşturur. 85.000 bayttan (yaklaşık 83 KB) büyük olan nesneler şunlardır:

* LOH 'ye yerleştirildi.
* Düzenlenmedi.
* 2. nesil oluşturma sırasında toplanır.

LOH dolduğunda GC, 2. nesil bir koleksiyon tetikleyecektir. 2. nesil Koleksiyonlar:

* Doğal olarak yavaştır.
* Ayrıca, diğer tüm neslerde bir koleksiyonun tetiklenmesi için de ücret uygulanır.

Aşağıdaki kod, LOH 'yi hemen sıkıştırır:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>LOH 'yi düzenleme hakkında bilgi için bkz..

.NET Core 3,0 ve üstünü kullanan kapsayıcılarda LOH otomatik olarak sıkıştırılır.

Bu davranışı gösteren aşağıdaki API:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Aşağıdaki grafikte `/api/loh/84975` , en fazla yük altında uç nokta çağırma bellek profili gösterilmektedir:

![önceki grafik](memory/_static/loh1.png)

Aşağıdaki grafik, uç noktayı çağırmanın bellek profilini gösterir `/api/loh/84976` ve *yalnızca bir bayt daha* ayırarak aşağıda verilmiştir:

![önceki grafik](memory/_static/loh2.png)

Note: `byte[]` yapıda ek yük baytları vardır. Bu nedenle 84.976 bayt 85.000 limitini tetikler.

Önceki iki grafik karşılaştırılıyor:

* Çalışma kümesi, yaklaşık 450 MB olmak üzere her iki senaryo için de benzerdir.
* Altındaki LOH istekleri (84.975 bayt), çoğunlukla nesil 0 koleksiyonlarını gösterir.
* Over LOH istekleri, sabit 2. nesil koleksiyonlar üretir. 2. nesil koleksiyonlar pahalıdır. Daha fazla CPU gerekir ve verimlilik neredeyse %50 ' a düşyordu.

Geçici büyük nesneler özellikle sorunlu olduğundan Gen2 GCs 'ye neden olur.

En yüksek performans için büyük nesne kullanımı küçültülmüş olmalıdır. Mümkünse, büyük nesneleri ayırın. Örneğin, [yanıt önbelleğe alma](xref:performance/caching/response) ara yazılımı ASP.NET Core önbellek girişlerini 85.000 bayttan daha az blok halinde ayırır.

Aşağıdaki bağlantılarda, LOH sınırı altına nesneleri tutma ASP.NET Core yaklaşımı gösterilmektedir:

* [ResponseCaching/Streams/Streammutilities. cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache. cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Daha fazla bilgi için bkz.

* [Büyük nesne yığını kapsanmamış](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Büyük nesne yığını](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

Yanlış kullanımı <xref:System.Net.Http.HttpClient> , kaynak sızıntısına neden olabilir. Veritabanı bağlantıları, yuvalar, dosya tutamaçları vb. gibi sistem kaynakları:

* , Bellekten daha fazla.
* Bellekten sızmış olduğunda daha sorunlu sorun vardır.

Deneyimli .NET geliştiricileri <xref:System.IDisposable.Dispose*> , uygulayan nesneler üzerinde arama gerektiğini bilir <xref:System.IDisposable> . Genellikle, elde edilen nesneleri atan `IDisposable` bellek ya da sızdırılan sistem kaynakları ile ilgili olarak elden atma.

`HttpClient`uygular `IDisposable` , ancak her **not** çağrıdan çıkarılmamalıdır. Bunun yerine yeniden kullanılmalıdır `HttpClient` .

Aşağıdaki uç nokta her istekte yeni bir örnek oluşturur ve atar  `HttpClient` :

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

Yük altında aşağıdaki hata iletileri günlüğe kaydedilir:

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

`HttpClient`Örnekler atılsa da, gerçek ağ bağlantısının işletim sistemi tarafından yayımlanması zaman alır. Sürekli olarak yeni bağlantılar oluşturarak _bağlantı noktaları tükenmesi_ oluşur. Her istemci bağlantısı kendi istemci bağlantı noktasını gerektirir.

Bağlantı noktası tükenmesi 'ni önlemenin bir yolu aynı örneği yeniden kullanmaktır `HttpClient` :

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

`HttpClient`Uygulama durdurulduğunda örnek serbest bırakılır. Bu örnek her bir atılabilir kaynağının her bir kullanım sonrasında atılmamalıdır.

Bir örneğin yaşam süresini işlemenin daha iyi bir yolu için aşağıdakilere bakın `HttpClient` :

* [HttpClient ve ömür yönetimi](../fundamentals/http-requests.md#httpclient-and-lifetime-management)
* [HTTPClient Factory blogu](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Nesne havuzu oluşturma

Önceki örnek, `HttpClient` Örneğin tüm istekler tarafından nasıl statik hale getirilebilir ve yeniden kullanılabileceğini gösterdi. Yeniden kullanım, kaynak dışı çalışmayı önler.

Nesne havuzu:

* Yeniden kullanım modelini kullanır.
* , Oluşturulması pahalı olan nesneler için tasarlanmıştır.

Havuz, iş parçacıkları genelinde ayrılmaları ve yayımlanmaları önceden başlatılmış nesnelerden oluşan bir koleksiyondur. Havuzlar sınırlar, önceden tanımlanmış boyutlar veya büyüme oranı gibi ayırma kuralları tanımlayabilir.

[Microsoft. Extensions. ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) NuGet paketi, bu tür havuzları yönetmeye yardımcı olan sınıflar içerir.

Aşağıdaki API uç noktası `byte` her istekte rastgele sayılarla doldurulmuş bir arabellek başlatır:

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

Aşağıdaki grafik, önceki API 'nin orta yük ile çağrılmasını gösterir:

![önceki grafik](memory/_static/array.png)

Yukarıdaki grafikte, nesil 0 toplamaları yaklaşık olarak saniyede bir kez gerçekleşir.

Önceki kod, `byte` [arraypool \<T> ](xref:System.Buffers.ArrayPool`1)kullanılarak arabelleği havuzlayarak iyileştirilebilir. Bir statik örnek istekler arasında yeniden kullanılır.

Bu yaklaşımla farklı olan özellikler, havuza alınmış bir nesnenin API 'den döndürüldüğü şeydir. Bunun anlamı:

* Yöntemi, yönteminden geri döndükten hemen sonra Denetim dışındadır.
* Nesneyi serbest bırakamıyoruz.

Nesnenin elden çıkarılmasını ayarlamak için:

* Havuza alınmış diziyi bir atılabilir nesnesinde yalıtma.
* Havuza alınmış nesneyi [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)ile kaydedin.

`RegisterForDispose` , `Dispose` yalnızca http isteği tamamlandığında serbest bırakılacak şekilde hedef nesneye çağrı yapılır.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

Havuza alınmamış sürüm olarak aynı yükün uygulanması aşağıdaki grafiğe neden olur:

![önceki grafik](memory/_static/pooledarray.png)

Ana fark ayrılan bayttır ve çok daha az nesil 0 koleksiyonu olarak.

## <a name="additional-resources"></a>Ek kaynaklar

* [Çöp toplama](/dotnet/standard/garbage-collection/)
* [Eşzamanlılık görselleştiricisi ile farklı GC modlarını anlama](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Büyük nesne yığını kapsanmamış](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Büyük nesne yığını](/dotnet/standard/garbage-collection/large-object-heap)