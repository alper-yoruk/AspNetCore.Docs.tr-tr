---
title: ASP.NET Blazor Core gelişmiş senaryoları
author: guardrex
description: Manuel RenderTreeBuilder Blazormantığını bir uygulamaya nasıl dahil edebilirsiniz dahil olmak üzere gelişmiş senaryolar hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659455"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>core Blazor gelişmiş senaryoları ASP.NET

Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Blazor Server devre işleyicisi

Blazor Server, kodun bir *devre işleyicisini*tanımlamasına izin verir, bu da kullanıcının devresinin durumuna yapılan değişikliklerde kod çalışmasını sağlar. Bir devre işleyicisi, sınıfın `CircuitHandler` uygulamanın hizmet konteynerine kaydedilmesi yle uygulanır. Bir devre işleyicisi aşağıdaki örnek açık SignalR bağlantıları izler:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

Devre işleyicileri DI kullanılarak kaydedilir. Kapsamlı örnekler, bir devrenin örneğine göre oluşturulur. Önceki `TrackingCircuitHandler` örnekte kullanarak, tüm devrelerin durumu izlenmesi gerektiğinden, singleton hizmeti oluşturulur:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Özel bir devre işleyicisinin yöntemleri işlenmemiş bir özel durum atarsa, özel durum Blazor Server devresi için ölümcüldür. İşleyicinin kodundaki veya çağrılan yöntemlerdeki özel durumları tolere etmek için, kodu hata işleme ve günlüğe kaydetme içeren bir veya daha fazla [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimine sarın.

Bir kullanıcı bağlantısı kesildiği nden ve çerçeve devre durumunu temizlediği için bir devre sona erdiğinde, çerçeve devrenin DI kapsamını ortadan kaldırmıştır. Kapsamı atmak, devreye sahip tüm devre kapsamlı <xref:System.IDisposable?displayProperty=fullName>DI hizmetlerini ortadan çıkarır. Herhangi bir DI hizmeti imha sırasında işlenmemiş bir özel durum atarsa, çerçeve özel durumu kaydeder.

## <a name="manual-rendertreebuilder-logic"></a>Manuel RenderTreeBuilder mantığı

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`c# kodunda bileşenleri el ile oluşturmak da dahil olmak üzere bileşenleri ve öğeleri işlemek için yöntemler sağlar.

> [!NOTE]
> Bileşenleri `RenderTreeBuilder` oluşturmak için kullanımı gelişmiş bir senaryodur. Hatalı biçimlendirilmiş bir bileşen (örneğin, kapalı biçimlendirme etiketi) tanımlanmamış davranışlara neden olabilir.

Başka bir `PetDetails` bileşende el ile oluşturulabilen aşağıdaki bileşeni göz önünde bulundurun:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Aşağıdaki örnekte, yöntemdeki `CreateComponent` döngü üç `PetDetails` bileşen oluşturur. Bileşenleri `RenderTreeBuilder` oluşturmak için yöntemleri`OpenComponent` ararken ( ve `AddAttribute`), sıra numaraları kaynak kodu satır numaralarıdır. Blazor fark algoritması, farklı çağrı çağrılarına değil, farklı kod satırlarına karşılık gelen sıra numaralarına dayanır. Yöntemlerle `RenderTreeBuilder` bir bileşen oluştururken, sıra numaraları için bağımsız değişkenleri kodlayın. **Sıra numarasını oluşturmak için bir hesaplama veya sayaç kullanmak düşük performansa yol açabilir.** Daha fazla bilgi için, [Sıra numaralarının yürütme sırası bölümüyle değil, kod satır numaralarıyla ilgili](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) olduğunu görün.

`BuiltContent`Bileşen:

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
> İşleme `Microsoft.AspNetCore.Components.RenderTree` işlemlerinin *sonuçlarının* işlenmesine izin veren türler. Bunlar Blazor çerçeve uygulamasının iç detaylarıdır. Bu tür *kararsız* ve gelecekteki sürümlerde değişebilir olarak kabul edilmelidir.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Sıra numaraları, yürütme sırası ile değil, kod satır numaralarıyla ilgilidir

Jilet bileşen ilerleç dosyaları (*.razor*) her zaman derlenir. Derleme, çalışma zamanında uygulama performansını artıran bilgileri enjekte etmek için kullanılabildiği için kod yorumlamaya göre potansiyel bir avantajdır.

Bu geliştirmelerin önemli bir örneği *sıra numaralarını*içerir. Sıra numaraları, hangi çıktıların farklı ve sıralanmış kod satırlarından geldiğini çalışma zamanı olarak gösterir. Çalışma zamanı, genel bir ağaç diff algoritması için normalde mümkün olandan çok daha hızlı olan doğrusal zamanda verimli ağaç farkları oluşturmak için bu bilgileri kullanır.

Aşağıdaki Razor bileşenini (*.razor*) dosyasını göz önünde bulundurun:

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Önceki kod aşağıdaki gibi bir şey için derler:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Kod ilk kez yürütüldüğünde, `someFlag` oluşturucu `true`alır:

| Sequence | Tür      | Veriler   |
| :------: | --------- | :----: |
| 0        | Metin düğümü | İlk  |
| 1        | Metin düğümü | Saniye |

Bunun `someFlag` olduğunu `false`ve biçimlendirmenin yeniden işlenir olduğunu düşünün. Bu kez, oluşturucu alır:

| Sequence | Tür       | Veriler   |
| :------: | ---------- | :----: |
| 1        | Metin düğümü  | Saniye |

Çalışma zamanı bir diff gerçekleştirdiğinde, sıralı `0` öğenin kaldırıldığını görür, bu nedenle aşağıdaki önemsiz *edit komut dosyası*oluşturur:

* İlk metin düğümlerini kaldırın.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Programlı sıra numaraları oluşturma sorunu

Bunun yerine aşağıdaki render ağacı oluşturucu mantığı yazdı düşünün:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Şimdi, ilk çıkış:

| Sequence | Tür      | Veriler   |
| :------: | --------- | :----: |
| 0        | Metin düğümü | İlk  |
| 1        | Metin düğümü | Saniye |

Bu sonuç önceki durumla aynıdır, bu nedenle olumsuz bir sorun yoktur. `someFlag`ikinci `false` işleme ve çıktı:

| Sequence | Tür      | Veriler   |
| :------: | --------- | ------ |
| 0        | Metin düğümü | Saniye |

Bu kez, diff algoritması *iki* değişiklik oluştuğunu görür ve algoritma aşağıdaki düzenle komut dosyası oluşturur:

* İlk metin düğümünün değerini ' `Second`le değiştirin
* İkinci metin düğümlerini kaldırın.

Sıra numaralarının üretilmesi, `if/else` dalların ve döngülerin özgün kodda nerede bulunduğuna ilişkin tüm yararlı bilgileri kaybetmiştir. Bu, öncekinin **iki katı kadar bir** diff ile sonuçlanır.

Bu önemsiz bir örnektir. Karmaşık ve derin iç içe yapılarile daha gerçekçi durumlarda ve özellikle döngüler ile, performans maliyeti genellikle daha yüksektir. Hangi döngü bloklarının veya dalların eklendiğini veya kaldırıldığını hemen belirlemek yerine, diff algoritmasının render ağaçlarına derinden yeniden lanetlenmesi gerekir. Diff algoritması eski ve yeni yapıların birbiriyle nasıl ilişkili olduğu hakkında yanlış bilgilendirildiği için, bu genellikle daha uzun düzenkomut dosyaları oluşturmak zorunda sonuçlanır.

### <a name="guidance-and-conclusions"></a>Rehberlik ve sonuçlar

* Sıra numaraları dinamik olarak oluşturulursa uygulama performansı zarar görür.
* Derleme zamanında yakalanmadığı sürece gerekli bilgiler bulunmadığından, çerçeve çalışma zamanında otomatik olarak kendi sıra numaralarını oluşturamaz.
* El ile uygulanan `RenderTreeBuilder` mantığın uzun bloklarını yazmayın. .razor dosyalarını tercih *edin* ve derleyicinin sıra numaralarıyla ilgilenmesine izin verin. El ile `RenderTreeBuilder` mantıktan kaçınamıyorsanız, uzun kod bloklarını `OpenRegion` / `CloseRegion` çağrılara sarılmış daha küçük parçalara bölün. Her bölgenin kendi sıra numaraları alanı vardır, böylece her bölge içindeki sıfırdan (veya başka bir rasgele sayıdan) yeniden başlatabilirsiniz.
* Sıra numaraları kodlanmışsa, diff algoritması yalnızca sıra numaralarının değer artışını gerektirir. İlk değer ve boşluklar alakasızdır. Yasal seçeneklerden biri, kod satır numarasını sıra numarası olarak kullanmak veya sıfırdan başlayıp birler veya yüzlerce (veya tercih edilen herhangi bir aralık) ile artırmaktır. 
* Blazorsıra numaralarını kullanırken, diğer ağaç dağılayan UI çerçeveleri bunları kullanmaz. Dizi numaraları kullanıldığında dağıtma çok daha Blazor hızlıdır ve *.razor* dosyaları yazan geliştiriciler için sıra numaralarıyla otomatik olarak ilgilenen bir derleme adımının avantajına sahiptir.

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Sunucu uygulamalarında Blazor büyük veri aktarımları gerçekleştirin

Bazı senaryolarda, büyük miktarda veri JavaScript ve Blazor. Genellikle, büyük veri aktarımları oluşur:

* Tarayıcı dosya sistemi API'leri bir dosyayı yüklemek veya indirmek için kullanılır.
* Üçüncü taraf kitaplığı ile interop gereklidir.

Sunucu'da, Blazor performans sorunlarına neden olabilecek tek büyük iletilerin geçmesini önlemek için bir sınırlama vardır.

JavaScript ile Blazoraşağıdaki arasında veri aktaran kod geliştirirken aşağıdaki kılavuzu göz önünde bulundurun:

* Verileri daha küçük parçalara dilimleyin ve tüm veriler sunucu tarafından alınana kadar veri bölümlerini sırayla gönderin.
* JavaScript ve C# kodunda büyük nesneleri ayırmayın.
* Veri gönderirken veya alırken ana Web Bilgisi iş parçacığının uzun süreler için engellenmesini.
* İşlem tamamlandığında veya iptal edildiğinde tüketilen tüm bellekleri boşaltın.
* Güvenlik amaçları için aşağıdaki ek gereksinimleri uygulayın:
  * Geçirilebilen en büyük dosyayı veya veri boyutunu bildirin.
  * İstemciden sunucuya minimum yükleme oranını bildirin.
* Veriler sunucu tarafından alındıktan sonra, veriler aşağıdaki ler olabilir:
  * Tüm kesimler toplanana kadar geçici olarak bir bellek arabelleğinde saklanır.
  * Hemen tüketilir. Örneğin, veriler hemen bir veritabanında depolanabilir veya her kesim alınırken diske yazılabilir.

Aşağıdaki dosya yükleyici sınıfı istemci ile JS interop işler. Yükleyici sınıfı JS interop kullanır:

* Bir veri kesimi göndermek için istemciyi yokla.
* Yoklama süreleri dolursa işlemi iptal edin.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

Önceki örnekte:

* 'den `_maxBase64SegmentSize` hesaplanan `8192` `_maxBase64SegmentSize = _segmentSize * 4 / 3`' olarak ayarlanır.
* Düşük düzeyli .NET Core bellek yönetimi API'leri sunucudaki `_uploadedSegments`bellek bölümlerini '' de depolamak için kullanılır.
* JS interop üzerinden yükleme işlemek için bir `ReceiveFile` yöntem kullanılır:
  * Dosya boyutu JS interop üzerinden `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`bayt olarak belirlenir.
  * Alınacak segment sayısı hesaplanır ve 'de `numberOfSegments`saklanır.
  * Segmentler JS interop `for` ile bir döngü `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`içinde istenir. Tüm segmentler ama son çözme önce 8.192 bayt olmalıdır. İstemci verileri verimli bir şekilde göndermek zorunda kalır.
  * Alınan her kesim için denetimler' <xref:System.Convert.TryFromBase64String*>in '' ile çözülmesiönce gerçekleştirilir.
  * Yükleme tamamlandıktan sonra verilerle <xref:System.IO.Stream> birlikte`SegmentedStream`bir akış yeni ( ) olarak döndürülür.

Bölümlenmiş akış sınıfı, segmentlerin listesini yalnızca aranamayan <xref:System.IO.Stream>bir bölüm olarak ortaya çıkarır:

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
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

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

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

Aşağıdaki kod verileri almak için JavaScript işlevlerini uygular:

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
