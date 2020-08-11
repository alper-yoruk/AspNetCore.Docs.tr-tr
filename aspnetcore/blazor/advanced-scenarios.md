---
title: ASP.NET Core Blazor Gelişmiş senaryolar
author: guardrex
description: "' Deki gelişmiş senaryolar Blazor , El Ile RenderTreeBuilder mantığını bir uygulamaya nasıl dahil leyeceğinizi öğrenin."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 4bd73acd821a8791d7f6cc93545edc2e39a6f2c7
ms.sourcegitcommit: 68d03d1aee8906b53bda66f8f1e0747efc3007e6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88051790"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a>ASP.NET Core Blazor Gelişmiş senaryolar

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

## <a name="no-locblazor-server-circuit-handler"></a>Blazor Serverdevre işleyici

Blazor Serverkodun bir Kullanıcı devresi durumunda değişiklikler üzerinde kod çalıştırmaya izin veren bir *devhandler*tanımlamasına olanak tanır. Devre işleyici, `CircuitHandler` uygulamanın hizmet kapsayıcısındaki sınıfından türeterek ve kayıt işleminden uygulanır. Bir devre işleyicinin aşağıdaki örneği açık SignalR bağlantıları izler:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

Devre işleyicileri DI kullanılarak kaydedilir. Kapsamlı örnekler, bir devrenin örneği başına oluşturulur. `TrackingCircuitHandler`Önceki örnekte kullanılarak, tüm devrelerin durumu izlenmelidir çünkü bir tek hizmet oluşturulur:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Özel bir devre işleyicisindeki Yöntemler işlenmeyen bir özel durum oluşturuyorsam, özel durum devre için önemli olur Blazor Server . Bir işleyicinin kodundaki veya yöntemleri çağrılan özel durumlara tolerans sağlamak için kodu bir veya daha fazla [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) deyimde hata işleme ve günlüğe kaydetme ile sarın.

Bir kullanıcının bağlantısı kesilmediği ve Framework devre durumunu temizlemede bir devre dışı bırakıldığında, çerçeve devre dışı bırakıldı. Kapsamı elden atılırken, uygulayan hiçbir devre kapsamlı dı hizmeti yok <xref:System.IDisposable?displayProperty=fullName> . Herhangi bir DI hizmeti, elden çıkarma sırasında işlenmeyen bir özel durum oluşturursa, çerçeve özel durumu günlüğe kaydeder.

## <a name="manual-rendertreebuilder-logic"></a>El ile RenderTreeBuilder mantığı

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>bileşenleri ve öğeleri düzenlemek için yöntemler sağlar. bu sayede bileşenleri C# kodunda el ile oluşturma da dahil olmak üzere.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>Bileşenlerini oluşturmak için kullanımı gelişmiş bir senaryodur. Hatalı biçimlendirilmiş bir bileşen (örneğin, kapatılmamış bir biçimlendirme etiketi) tanımsız davranışa neden olabilir.

Aşağıdaki `PetDetails` bileşeni, başka bir bileşende el ile yerleşik olarak kullanılabilecek şekilde göz önünde bulundurun:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Aşağıdaki örnekte, `CreateComponent` yöntemindeki döngü üç `PetDetails` bileşen oluşturur. <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>Sıra numarası olan yöntemlerde, sıra numaraları kaynak kodu satır numaralarıdır. BlazorFark algoritması, ayrı çağrı etkinleştirmeleri değil ayrı kod satırlarına karşılık gelen sıra numaralarına dayanır. Yöntemler içeren bir bileşen oluştururken <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> , dizi numaralarına yönelik bağımsız değişkenleri kod olarak kodlayın. **Sıra numarasını oluşturmak için bir hesaplama veya sayaç kullanmak kötü performansa neden olabilir.** Daha fazla bilgi için bkz. [kod satırı numaralarıyla Ilgili sıra numaraları ve yürütme sırası çalışmıyor](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) bölümü.

`BuiltContent`bileşeninde

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> İçindeki türler, <xref:Microsoft.AspNetCore.Components.RenderTree> işleme işlemlerinin *sonuçlarının* işlenmesine izin verir. Bunlar, çerçeve uygulamasının iç ayrıntılardır Blazor . Bu türlerin *dengesizleşilmesi* ve gelecekteki sürümlerde değişikliğe tabi olması gerekir.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Sıra numaraları, kod satırı numaralarıyla ilgilidir ve yürütme sırası değildir

Razorbileşen dosyaları ( `.razor` ) her zaman derlenir. Derleme adımı, çalışma zamanında uygulama performansını geliştiren bilgileri eklemek için kullanılabilir olduğundan, kod yorumlanması üzerinde olası bir avantajdır.

Bu geliştirmelerin önemli bir örneği, *sıra numaraları*içerir. Sıra numaraları, hangi çıkışların ayrı ve sıralı kod satırlarından geldiğini çalışma zamanına işaret ediyor. Çalışma zamanı, doğrusal bir zamanda, genel ağaç farkı algoritması için genellikle mümkün olandan çok daha hızlı olan etkili ağaç SLA 'ları oluşturmak için bu bilgileri kullanır.

Aşağıdaki Razor bileşen () dosyasını göz önünde bulundurun `.razor` :

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Yukarıdaki kod, aşağıdakine benzer şekilde derlenir:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Kod ilk kez çalıştırıldığında, `someFlag` `true` Oluşturucu şunları alır:

| Sequence | Tür      | Veriler   |
| :------: | --------- | :----: |
| 0        | Metin düğümü | Birinci  |
| 1        | Metin düğümü | Second |

Olduğunu düşünün `someFlag` `false` ve biçimlendirme yeniden işlenir. Bu kez, Oluşturucu şunları alır:

| Sequence | Tür       | Veriler   |
| :------: | ---------- | :----: |
| 1        | Metin düğümü  | Second |

Çalışma zamanı bir fark gerçekleştirdiğinde, sıradaki öğenin `0` kaldırıldığını görür, bu nedenle aşağıdaki önemsiz *düzenleme betiğini*oluşturur:

* İlk metin düğümünü kaldırın.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Program aracılığıyla sıra numaraları oluşturma sorunu

Aşağıdaki işleme Ağacı Oluşturucusu mantığını yazmanız yerine düşünün:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Şimdi ilk çıktı:

| Sequence | Tür      | Veriler   |
| :------: | --------- | :----: |
| 0        | Metin düğümü | Birinci  |
| 1        | Metin düğümü | Second |

Bu sonuç önceki bir durum ile aynıdır, bu nedenle olumsuz bir sorun yoktur. `someFlag``false`ikinci işleme ve çıktı:

| Sequence | Tür      | Veriler   |
| :------: | --------- | ------ |
| 0        | Metin düğümü | Second |

Bu kez, fark algoritması *iki* değişikliğin oluştuğunu görür ve algoritma aşağıdaki düzenleme betiğini üretir:

* İlk metin düğümünün değerini olarak değiştirin `Second` .
* İkinci metin düğümünü kaldırın.

Sıra numaralarının oluşturulması, `if/else` dal ve döngülerin orijinal kodda bulunduğu yer hakkındaki tüm yararlı bilgileri kaybetti. Bu, daha önce olduğu gibi bir fark **ile iki kez** sonuçlanır.

Bu, önemsiz bir örnektir. Karmaşık ve derin iç içe yapıları ve özellikle döngülerle daha gerçekçi durumlarda performans maliyeti genellikle daha yüksektir. Hangi döngü bloklarının veya dallarının eklendiğini veya kaldırıldığını hemen belirlemek yerine, fark algoritmasının işleme ağaçlarına göre belirlenmesi gerekir. Bu genellikle, fark algoritması, eski ve yeni yapıların birbirleriyle nasıl ilişkilendirileceğiyle ilgili bilgi sahibi olduğu için daha uzun düzenleme betikleri oluşturma ile sonuçlanır.

### <a name="guidance-and-conclusions"></a>Kılavuz ve ekibinizle

* Sıra numaraları dinamik olarak oluşturulursa uygulama performansı de vardır.
* Altyapı, derleme zamanında yakalanmadığı takdirde gerekli bilgiler bulunmadığından, çalışma zamanında kendi sıra numaralarını otomatik olarak oluşturamaz.
* El ile uygulanan mantık uzun blokları yazmayın <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> . Dosyaları tercih edin `.razor` ve derleyicinin sıra numaralarıyla uğraşmak için izin verin. El ile <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> mantığın olmaması durumunda, uzun kod bloklarını çağrılarında kaydırılmış küçük parçalara ayırın <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> . Her bölge kendi ayrı dizi numaralarına sahiptir, bu nedenle her bölge içinde sıfırdan (veya herhangi bir rastgele sayıdan) yeniden başlatabilirsiniz.
* Dizi numaraları sabit kodluysa, fark algoritması yalnızca değer değerinde sıra numaralarının artırılmasını gerektirir. İlk değer ve boşluklar ilgisiz. Tek bir seçenek, kod satırı numarasını sıra numarası olarak kullanmak veya sıfırdan başlayıp bir ya da yüzlerce (ya da tercih edilen aralığa) artırmak için kullanılır. 
* Blazorsıra numaralarını kullanır, diğer ağaç dağıtma Kullanıcı arabirimi çerçeveleri bunları kullanmaz. Dizi numaraları kullanıldığında, yayılma işlemi daha hızlı bir şekilde yapılır ve Blazor geliştiricilerin yazma dosyaları için otomatik olarak sıra numaralarıyla ilgilenen bir derleme adımının avantajına sahiptir `.razor` .

## <a name="perform-large-data-transfers-in-no-locblazor-server-apps"></a>Uygulamalarda büyük veri aktarımları gerçekleştirme Blazor Server

Bazı senaryolarda, JavaScript ve arasında büyük miktarlarda veri aktarılmalıdır Blazor . Genellikle, büyük veri aktarımları şu durumlarda oluşur:

* Tarayıcı dosya sistemi API 'Leri bir dosyayı karşıya yüklemek veya indirmek için kullanılır.
* Üçüncü taraf kitaplığı ile birlikte çalışma gerekir.

' De Blazor Server , performans sorunlarına neden olabilecek tek büyük mesajların geçirilmesini engellemek için bir sınırlama vardır.

JavaScript arasında veri aktaran kodu geliştirirken aşağıdaki kılavuzu göz önünde bulundurun Blazor :

* Verileri daha küçük parçalara dilimleyin ve tüm veriler sunucu tarafından alınana kadar veri segmentlerini sırayla gönderin.
* JavaScript ve C# kodunda büyük nesneler ayırmayın.
* Veri gönderirken veya alırken uzun süreler için ana UI iş parçacığını engellemez.
* İşlem tamamlandığında veya iptal edildiğinde tüketilen tüm belleği boşaltın.
* Güvenlik amaçları için aşağıdaki ek gereksinimleri uygulayın:
  * Geçirilebilen en büyük dosya veya veri boyutunu bildirin.
  * İstemciden sunucuya en düşük karşıya yükleme hızını bildirin.
* Veriler, sunucu tarafından alındıktan sonra şu olabilir:
  * Tüm segmentler toplanana kadar bir bellek arabelleğinde geçici olarak depolanır.
  * Hemen tüketildi. Örneğin, veriler bir veritabanında hemen depolanabilir veya her bir segment alındığında diske yazılabilir.

Aşağıdaki dosya Yükleyici sınıfı istemcisiyle JS birlikte çalışabilirliği yönetir. Uploader sınıfı, JS birlikte çalışması için şunu kullanır:

* Veri segmenti göndermek için istemciyi yoklayın.
* Yoklama zaman aşımına uğrarsa işlemi iptal edin.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

Yukarıdaki örnekte:

* , `maxBase64SegmentSize` ' A ayarlanır `8192` , ' den hesaplanır `maxBase64SegmentSize = segmentSize * 4 / 3` .
* Düşük düzey .NET Core bellek yönetimi API 'Leri, bellek kesimlerini içindeki sunucuda depolamak için kullanılır `uploadedSegments` .
* Bir `ReceiveFile` Yöntem, JS birlikte çalışması aracılığıyla karşıya yüklemeyi işlemek için kullanılır:
  * Dosya boyutu, ile JS birlikte çalışma üzerinden bayt olarak belirlenir `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` .
  * Alacak segmentlerin sayısı ' de hesaplanıp depolanır `numberOfSegments` .
  * Kesimleri, `for` Ile js birlikte çalışma aracılığıyla bir döngüde istenir `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` . Tüm kesimler ancak son, kod çözmede önce 8.192 bayt olmalıdır. İstemci verileri verimli bir şekilde gönderilmeye zorlanır.
  * Alınan her segment için, ile kod çözme işleminden önce denetimler gerçekleştirilir <xref:System.Convert.TryFromBase64String%2A> .
  * Karşıya yükleme tamamlandıktan sonra verileri içeren bir akış New <xref:System.IO.Stream> () olarak döndürülür `SegmentedStream` .

Kesimli akış sınıfı, kesim listesini ReadOnly olmayan salt okunur olarak kullanıma sunar <xref:System.IO.Stream> :

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

Aşağıdaki kod, verileri almak için JavaScript işlevlerini uygular:

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
