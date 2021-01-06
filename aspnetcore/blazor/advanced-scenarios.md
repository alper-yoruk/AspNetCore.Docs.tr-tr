---
title: ASP.NET Core Blazor Gelişmiş senaryolar
author: guardrex
description: "' Deki gelişmiş senaryolar Blazor , El Ile RenderTreeBuilder mantığını bir uygulamaya nasıl dahil leyeceğinizi öğrenin."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: 95714b3c0d21d3b348a9a8a984e2a42e7708499e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056562"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a>ASP.NET Core Blazor Gelişmiş senaryolar

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

## <a name="no-locblazor-server-circuit-handler"></a>Blazor Server devre işleyici

Blazor Server kodun bir Kullanıcı devresi durumunda değişiklikler üzerinde kod çalıştırmaya izin veren bir *devhandler* tanımlamasına olanak tanır. Devre işleyici, `CircuitHandler` uygulamanın hizmet kapsayıcısındaki sınıfından türeterek ve kayıt işleminden uygulanır. Bir devre işleyicinin aşağıdaki örneği açık SignalR bağlantıları izler:

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

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> bileşenleri ve öğeleri düzenlemek için yöntemler sağlar. bu sayede bileşenleri C# kodunda el ile oluşturma da dahil olmak üzere.

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

`BuiltContent` bileşeninde

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

Razor bileşen dosyaları ( `.razor` ) her zaman derlenir. Derleme adımı, çalışma zamanında uygulama performansını geliştiren bilgileri eklemek için kullanılabilir olduğundan, kod yorumlanması üzerinde olası bir avantajdır.

Bu geliştirmelerin önemli bir örneği, *sıra numaraları* içerir. Sıra numaraları, hangi çıkışların ayrı ve sıralı kod satırlarından geldiğini çalışma zamanına işaret ediyor. Çalışma zamanı, doğrusal bir zamanda, genel ağaç farkı algoritması için genellikle mümkün olandan çok daha hızlı olan etkili ağaç SLA 'ları oluşturmak için bu bilgileri kullanır.

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

Çalışma zamanı bir fark gerçekleştirdiğinde, sıradaki öğenin `0` kaldırıldığını görür, bu nedenle aşağıdaki önemsiz *düzenleme betiğini* oluşturur:

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
* Blazor sıra numaralarını kullanır, diğer ağaç dağıtma Kullanıcı arabirimi çerçeveleri bunları kullanmaz. Dizi numaraları kullanıldığında, yayılma işlemi daha hızlı bir şekilde yapılır ve Blazor geliştiricilerin yazma dosyaları için otomatik olarak sıra numaralarıyla ilgilenen bir derleme adımının avantajına sahiptir `.razor` .
