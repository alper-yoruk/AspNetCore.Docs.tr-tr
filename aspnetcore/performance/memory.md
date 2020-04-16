---
title: ASP.NET Core'da bellek yönetimi ve desenler
author: rick-anderson
description: ASP.NET Core'da belleğin nasıl yönetildiğini ve çöp toplayıcının (GC) nasıl çalıştığını öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440954"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>ASP.NET Core'da bellek yönetimi ve çöp toplama (GC)

Yazar: [Sébastien Ros](https://github.com/sebastienros) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bellek yönetimi,.NET gibi yönetilen bir çerçevede bile karmaşıktır. Bellek sorunlarını analiz etmek ve anlamak zor olabilir. Bu makalede:

* Birçok bellek *sızıntısı* ve *GC sorunları çalışmıyor* tarafından motive edildi. Bu sorunların çoğu, bellek tüketiminin .NET Core'da nasıl çalıştığını anlamamak veya nasıl ölçüldüğünü anlamamak nedeniyle kaynaklanmıştır.
* Sorunlu bellek kullanımını gösterir ve alternatif yaklaşımlar önerir.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>.NET Core'da çöp toplama (GC) nasıl çalışır?

GC, her kesimin bitişik bir bellek aralığı olduğu yığın segmentler ayırır. Yığına yerleştirilen nesneler 3 nesilden birine ayrılır: 0, 1 veya 2. Oluşturma, GC'nin artık uygulama tarafından başvurulan olmayan yönetilen nesnelerde bellek serbest bırakmaya çalıştığı sıklığını belirler. Daha düşük numaralı nesiller gc'd daha sık vardır.

Nesneler yaşamlarına göre bir nesilden diğerine taşınır. Nesneler daha uzun yaşadıkça, daha yüksek bir nesle taşınırlar. Daha önce de belirtildiği gibi, yüksek nesiller gc'd daha az sıklıkta. Kısa süreli yaşayan nesneler her zaman nesil 0 kalır. Örneğin, bir web isteğinin ömrü boyunca başvurulan nesneler kısa ömürlüdür. Uygulama düzeyi [singletons](xref:fundamentals/dependency-injection#service-lifetimes) genellikle nesil 2'ye göç.

bir ASP.NET Core uygulaması başladığında, GC:

* İlk yığın segmentleri için bazı bellek yedekler.
* Çalışma zamanı yüklendiğinde belleğin küçük bir bölümünü işler.

Önceki bellek ayırmaları performans nedenleriyle yapılır. Performans avantajı bitişik bellekteki yığın segmentlerden gelir.

### <a name="call-gccollect"></a>GC'yi ara. Toplamak

[GC'yi arıyorum. Açıkça toplayın:](xref:System.GC.Collect*)

* Core uygulamaları nın üretim ASP.NET **yapılmaması** gerekir.
* Bellek sızıntılarını araştırırken yararlıdır.
* Araştırma yaparken, GC bellek ölçülebilir böylece bellekten tüm sarkan nesneleri kaldırdı doğrular.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Bir uygulamanın bellek kullanımını analiz etme

Özel araçlar bellek kullanımını analiz yardımcı olabilir:

- Nesne başvurularını sayma
- GC'nin CPU kullanımı üzerinde ne kadar etkisi olduğunu ölçme
- Her nesil için kullanılan bellek alanını ölçme

Bellek kullanımını analiz etmek için aşağıdaki araçları kullanın:

* [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Üretim makinelerinde kullanılabilir.
* [Visual Studio hata ayıklama olmadan bellek kullanımını analiz edin](/visualstudio/profiling/memory-usage-without-debugging2)
* [Visual Studio’da bellek kullanımının profilini oluşturma](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Bellek sorunlarını algılama

Görev Yöneticisi, bir ASP.NET uygulamasının ne kadar bellek kullandığı hakkında bir fikir almak için kullanılabilir. Görev Yöneticisi bellek değeri:

* ASP.NET işlemi tarafından kullanılan bellek miktarını temsil eder.
* Uygulamanın canlı nesnelerini ve yerel bellek kullanımı gibi diğer bellek tüketicilerini içerir.

Görev Yöneticisi bellek değeri süresiz olarak artar ve hiçbir zaman düzleşirse, uygulamanın bir bellek sızıntısı vardır. Aşağıdaki bölümlerde çeşitli bellek kullanım desenleri gösterin ve açıklar.

## <a name="sample-display-memory-usage-app"></a>Örnek ekran bellek kullanım uygulaması

[MemoryLeak örnek uygulaması](https://github.com/sebastienros/memoryleak) GitHub'da kullanılabilir. MemoryLeak uygulaması:

* Uygulama için gerçek zamanlı bellek ve GC verilerini toplayan bir tanıdenetleyicisi içerir.
* Bellek ve GC verilerini görüntüleyen bir Dizin sayfası vardır. Dizin sayfası her saniye yenilenir.
* Çeşitli bellek yükü desenleri sağlayan bir API denetleyicisi içerir.
* Desteklenen bir araç değildir, ancak ASP.NET Core uygulamalarıbellek kullanım desenleri görüntülemek için kullanılabilir.

MemoryLeak çalıştırın. Ayrılan bellek, GC oluşana kadar yavaşça artar. Araç veri yakalamak için özel nesne ayırdığından bellek artar. Aşağıdaki resim, Gen 0 GC oluştuğunda MemoryLeak Index sayfasını gösterir. GRAFIK, API denetleyicisinden hiçbir API uç noktası çağrıldığından, 0 RPS (Saniyede istek) gösterir.

![önceki grafik](memory/_static/0RPS.png)

Grafik bellek kullanımı için iki değer görüntüler:

- Ayrılan: yönetilen nesneler tarafından işgal edilen bellek miktarı
- [Çalışma kümesi](/windows/win32/memory/working-set): Şu anda fiziksel bellekte ikamet eden işlemin sanal adres alanında yer alan sayfa kümesi. Gösterilen çalışma kümesi, Görev Yöneticisi'nin gösterdiği değerle aynıdır.

### <a name="transient-objects"></a>Geçici nesneler

Aşağıdaki API 10-KB String örneği oluşturur ve istemciye döndürür. Her istekte, bellekte yeni bir nesne ayrılır ve yanıta yazılır. Dizeleri .NET'te UTF-16 karakteri olarak depolanır, böylece her karakter bellekte 2 bayt alır.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Aşağıdaki grafik, bellek ayırmalarının GC tarafından nasıl etkilendiğini göstermek için nispeten küçük bir yükle oluşturulur.

![önceki grafik](memory/_static/bigstring.png)

Önceki grafik tesniye:

* 4K RPS (İstekler saniyede).
* Nesil 0 GC koleksiyonları yaklaşık her iki saniyede bir oluşur.
* Çalışma seti yaklaşık 500 MB sabittir.
* CPU%12'dir.
* Bellek tüketimi ve serbest bırakılması (GC ile) kararlıdır.

Aşağıdaki grafik, makine tarafından işlenebilen maksimum iş lenme noktasında alınır.

![önceki grafik](memory/_static/bigstring2.png)

Önceki grafik tesniye:

* 22K RPS
* Nesil 0 GC koleksiyonları saniyede birkaç kez oluşur.
* Uygulama saniyede önemli ölçüde daha fazla bellek ayırdığı için nesil 1 koleksiyonları tetiklenir.
* Çalışma seti yaklaşık 500 MB sabittir.
* CPU %33'tür.
* Bellek tüketimi ve serbest bırakılması (GC ile) kararlıdır.
* CPU (%33) aşırı kullanılmadığından, çöp toplama çok sayıda ayırmaya yetişebilir.

### <a name="workstation-gc-vs-server-gc"></a>İş İstasyonu GC vs Sunucu GC

.NET Çöp Toplayıcı'nın iki farklı modu vardır:

* **Workstation GC**: Masaüstü için optimize edin.
* **Sunucu GC**. ASP.NET Core uygulamaları için varsayılan GC. Sunucu için optimize edin.

GC modu, proje dosyasında veya yayınlanan uygulamanın *runtimeconfig.json* dosyasında açıkça ayarlanabilir. Aşağıdaki biçimlendirme proje `ServerGarbageCollection` dosyasındaki ayarı gösterir:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Proje `ServerGarbageCollection` dosyasında yapılan değiştirme, uygulamanın yeniden oluşturulmasını gerektirir.

**Not:** Sunucu çöp toplama tek bir çekirdekli makinelerde **kullanılamaz.** Daha fazla bilgi için bkz. <xref:System.Runtime.GCSettings.IsServerGC>.

Aşağıdaki resimde Workstation GC kullanarak bir 5K RPS altında bellek profili gösterilmektedir.

![önceki grafik](memory/_static/workstation.png)

Bu grafik ve sunucu sürümü arasındaki farklar önemlidir:

- Çalışma seti 500 MB'dan 70 MB'a düşer.
- GC, her iki saniyede bir değil, saniyede birden çok kez nesil 0 koleksiyonu yapar.
- GC 300 MB'dan 10 MB'a düşer.

Tipik bir web sunucusu ortamında, CPU kullanımı bellekten daha önemlidir, bu nedenle Server GC daha iyidir. Bellek kullanımı yüksekse ve CPU kullanımı nispeten düşükse, Workstation GC daha fazla performans olabilir. Örneğin, bellek kıt olduğu çeşitli web uygulamaları barındırma yüksek yoğunluklu.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>Docker ve küçük kaplar kullanarak GC

Tek bir makinede birden çok kapsayıcı uygulama çalışırken, Workstation GC Server GC'den daha preformant olabilir. Daha fazla bilgi için, [Küçük Bir Kapsayıcı da Server GC ile çalışan](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) ve Küçük Bir Kapsayıcı Senaryo Bölüm 1 Sunucu GC ile çalışan - [GC Yığını için Sabit Limit](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)bakın.

### <a name="persistent-object-references"></a>Kalıcı nesne başvuruları

GC başvurulan nesneleri serbest kılamaz. Başvurulan ancak artık gerekk olmayan nesneler bellek sızıntısına neden olur. Uygulama sık sık nesneleri ayırır ve artık ihtiyaç duyulmadıktan sonra bunları serbest bırakmazsa, bellek kullanımı zaman içinde artar.

Aşağıdaki API 10-KB String örneği oluşturur ve istemciye döndürür. Önceki örnekteki fark, bu örneğin statik bir üye tarafından başvurulsa da, bu da hiçbir zaman koleksiyon için kullanılamadığı anlamına gelir.

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
* Sık yapılan aramalarda, işlem bir `OutOfMemory` istisna dışında çökene kadar uygulama belleği nin artmasına neden olur.

![önceki grafik](memory/_static/eternal.png)

Önceki resimde:

* Uç noktayı `/api/staticstring` test eden yük bellekte doğrusal bir artışa neden olur.
* GC, bellek basıncı arttıkça, nesil 2 koleksiyonunu arayarak belleği serbest tutmaya çalışır.
* GC, sızdırılan belleği kurtaramaz. Tahsis ve çalışma seti zamanla artar.

Önbelleğe alma gibi bazı senaryolar, bellek basıncı onları serbest bırakılmaya zorlayana kadar nesne başvurularının tutulmasını gerektirir. Sınıf <xref:System.WeakReference> önbelleğe alma kodu bu tür için kullanılabilir. Bir `WeakReference` nesne bellek basınçları altında toplanır. Kullanımların <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> varsayılan `WeakReference`uygulaması.

### <a name="native-memory"></a>Yerel bellek

Bazı .NET Core nesneleri yerel belleğe dayanır. Yerel bellek GC tarafından **toplanabilir.** Yerel belleği kullanan .NET nesnesi, yerel kodu kullanarak onu serbest etmelidir.

.NET, <xref:System.IDisposable> geliştiricilerin yerel belleği serbest bırakmasına izin veren arabirimi sağlar. <xref:System.IDisposable.Dispose*> Çağrılmasa bile, doğru uygulanan `Dispose` sınıflar [sonlandırıcı](/dotnet/csharp/programming-guide/classes-and-structs/destructors) çalıştığında çağırır.

Aşağıdaki kodu inceleyin:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) yönetilen bir sınıftır, bu nedenle herhangi bir örnek isteğin sonunda toplanır.

Aşağıdaki resim, `fileprovider` API'yi sürekli olarak çağırırken bellek profilini gösterir.

![önceki grafik](memory/_static/fileprovider.png)

Önceki grafik, bellek kullanımını artırmaya devam ettikçe, bu sınıfın uygulanmasıyla ilgili bariz bir sorun gösterir. Bu, [bu sorunda](https://github.com/dotnet/aspnetcore/issues/3110)izlenen bilinen bir sorundur.

Aynı sızıntı aşağıdakilerden biri tarafından, kullanıcı kodunda olabilir:

* Sınıfı doğru bırakmamak.
* Atılması gereken bağımlı `Dispose`nesnelerin yöntemini çağırmayı unutma.

### <a name="large-objects-heap"></a>Büyük nesneler yığını

Sık bellek ayırma/boş döngüleri, özellikle büyük bellek parçaları ayırırken belleği parçalayabilir. Nesneler bitişik bellek bloklarında ayrılır. Parçalanmayı azaltmak için, GC belleği serbest ettiğinde, parçalamaya çalışır. Bu işleme **sıkıştırma**denir. Sıkıştırma nesneleri hareketli içerir. Büyük nesneleri taşımak bir performans cezası uygular. Bu nedenle GC, [büyük nesne yığını](/dotnet/standard/garbage-collection/large-object-heap) (LOH) adı verilen _büyük_ nesneler için özel bir bellek bölgesi oluşturur. 85.000 bayttan (yaklaşık 83 KB) büyük nesneler şunlardır:

* LOH'a yerleştirildi.
* Sıkıştırılmış değil.
* Nesil 2 GC'ler sırasında toplanır.

LOH dolduğunda, GC bir nesil 2 koleksiyonu tetikler. Nesil 2 koleksiyonları:

* Doğal olarak yavaşlar.
* Ayrıca, diğer tüm nesillerde bir koleksiyonu tetikleme maliyetine de neden olabilir.

Aşağıdaki kod hemen LOH kompakt:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

LOH sıkıştırma hakkında bilgi için bkz. <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>

.NET Core 3.0 ve sonrası kullanan kaplarda LOH otomatik olarak sıkıştırılır.

Bu davranışı gösteren aşağıdaki API:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Aşağıdaki grafik, maksimum yük altında `/api/loh/84975` bitiş noktasını çağırmanın bellek profilini gösterir:

![önceki grafik](memory/_static/loh1.png)

Aşağıdaki grafik, `/api/loh/84976` bitiş noktasını çağırmanın bellek profilini gösterir ve yalnızca bir *bayt daha*ayırır:

![önceki grafik](memory/_static/loh2.png)

Not: `byte[]` Yapının tepede baytları vardır. Bu yüzden 84.976 bayt 85.000 limitini tetikler.

Önceki iki grafiğin karşılaştırılması:

* Çalışma kümesi her iki senaryo için de benzer, yaklaşık 450 MB.
* Altında LOH istekleri (84.975 bayt) çoğunlukla nesil 0 koleksiyonları gösterir.
* Over LOH istekleri sürekli nesil 2 koleksiyonları oluşturur. Generation 2 koleksiyonları pahalıdır. Daha fazla CPU gereklidir ve iş çıktısı neredeyse %50 düşer.

Geçici büyük nesneler özellikle sorunludur, çünkü gen2 GC'lere neden olurlar.

Maksimum performans için, büyük nesne kullanımı en aza indirilmelidir. Mümkünse, büyük nesneleri bölün. Örneğin, [Core'ASP.NET'daki Yanıt Önbelleğe Alma](xref:performance/caching/response) ara yazılımı önbellek girişlerini 85.000 bayttan daha az bloklara böler.

Aşağıdaki bağlantılar, nesneleri LOH sınırının altında tutmak için ASP.NET Core yaklaşımını gösterir:

* [YanıtCaching/Streams/StreamUtilities.cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [YanıtCaching/MemoryResponseCache.cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Daha fazla bilgi için bkz.

* [Büyük Nesne Yığını Ortaya Çıkarıldı](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Büyük nesne yığını](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>httpİsteC

Yanlış kullanmak <xref:System.Net.Http.HttpClient> kaynak sızıntısına neden olabilir. Veritabanı bağlantıları, soketler, dosya tutamaçları vb. gibi sistem kaynakları:

* Hafızadan daha az.
* Sızdırıldığında bellekten daha sorunludur.

Deneyimli .NET geliştiricileri <xref:System.IDisposable.Dispose*> uygulayan <xref:System.IDisposable>nesneleri aramayı bilir. Genellikle zamansız bellek veya `IDisposable` sızdırılan sistem kaynakları yla sonuçlanan nesneleri atmaz.

`HttpClient``IDisposable`uygular, ancak her çağrıüzerine **atılmamalıdır.** Bunun `HttpClient` yerine, yeniden kullanılmalıdır.

Aşağıdaki bitiş noktası, her istekte `HttpClient` yeni bir örnek oluşturur ve bertaraf eder:

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

Yük altında, aşağıdaki hata iletileri günlüğe kaydedilir:

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

`HttpClient` Örnekler elden çıkarılmış olsa bile, gerçek ağ bağlantısının işletim sistemi tarafından serbest bırakılması biraz zaman alır. Sürekli olarak yeni bağlantılar oluşturarak, _bağlantı noktalarının tükenmesi_ oluşur. Her istemci bağlantısı kendi istemci bağlantı noktasını gerektirir.

Bağlantı noktası yorgunluğunu önlemenin bir yolu `HttpClient` da aynı örneği yeniden kullanmaktır:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

Örnek, `HttpClient` uygulama durduğunda serbest bırakılır. Bu örnek, her tek kullanımlık kaynağın her kullanımdan sonra elden çıkarılmaması gerektiğini göstermektedir.

Bir `HttpClient` örneğin kullanım ömrünü daha iyi bir şekilde işlemek için aşağıdakilere bakın:

* [Httpİste ve yaşam boyu yönetim](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [HTTPClient fabrika günlüğü](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Nesne havuzu

Önceki örnek, `HttpClient` örneğin nasıl statik yapılabilir ve tüm istekler tarafından yeniden gösterilebilir gösterdi. Yeniden kullanım, kaynakların tükenmesini önler.

Nesne havuzu:

* Yeniden kullanım deseni kullanır.
* Oluşturulması pahalı nesneler için tasarlanmıştır.

Havuz, iş parçacıkları arasında rezerve edilebilen ve serbest bırakılabilen önceden başlatılanmış nesneler topluluğudur. Havuzlar, sınırlar, önceden tanımlanmış boyutlar veya büyüme hızı gibi ayırma kuralları tanımlayabilir.

NuGet paketi [Microsoft.Extensions.ObjectPool,](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) bu tür havuzları yönetmeye yardımcı olan sınıflar içerir.

Aşağıdaki API bitiş noktası, her `byte` istekte rasgele sayılarla dolu bir arabellek anında gerçekleşir:

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

Aşağıdaki grafik ekranı önceki API'yi orta düzeyde yükile çağırarak gösterir:

![önceki grafik](memory/_static/array.png)

Önceki grafikte, nesil 0 koleksiyonları saniyede yaklaşık bir kez gerçekleşir.

Önceki kod [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1)kullanarak `byte` arabellek havuzlama tarafından optimize edilebilir. Statik bir örnek istekler arasında yeniden kullanılır.

Bu yaklaşımda farklı olan, birleştirilmiş bir nesnenin API'den döndürülür olmasıdır. Bu şu anlama gelir:

* Yöntemden döner dönmez nesne sizin denetiminizde değildir.
* Nesneyi serbest bırakamazsın.

Nesnenin bertarafını ayarlamak için:

* Tek kullanımlık bir nesnede birleştirilmiş diziyi kapsülle.
* Havuza toplanan nesneyi [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)ile kaydedin.

`RegisterForDispose`yalnızca HTTP isteği `Dispose`tamamlandığında serbest bırakılması için hedef nesneyi arama yı önemseyecektir.

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

Havuza ait olmayan sürümle aynı yükün uygulanması aşağıdaki grafikte sonuçlanır:

![önceki grafik](memory/_static/pooledarray.png)

Ana fark bayt ayrılır ve sonuç olarak çok daha az nesil 0 koleksiyonları.

## <a name="additional-resources"></a>Ek kaynaklar

* [Çöp Toplama](/dotnet/standard/garbage-collection/)
* [Eşzamanlı Görselleştirici ile farklı GC modlarını anlama](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Büyük Nesne Yığını Ortaya Çıkarıldı](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Büyük nesne yığını](/dotnet/standard/garbage-collection/large-object-heap)
