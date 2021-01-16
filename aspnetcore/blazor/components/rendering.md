---
title: ASP.NET Core Blazor bileşen işleme
author: guardrex
description: RazorASP.NET Core uygulamalarda bileşen işleme hakkında bilgi edinin Blazor ve StateHasChanged çağrısının ne zaman çağrılacağını de dahil edin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: 1a4d4116b8a6d9266bbacbbdd8f20dc49b4e1db0
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253976"
---
# <a name="aspnet-core-no-locblazor-component-rendering"></a>ASP.NET Core Blazor bileşen işleme

[Steve Sanderson](https://github.com/SteveSandersonMS) tarafından

Bileşenler, ana bileşenleri tarafından bileşen hiyerarşisine İlk eklendiğinde *işlenmelidir* . Bu, bir bileşenin tamamen işlenmesi gereken tek zaman olur.

Bileşenler kendi mantığına ve kurallarına göre herhangi bir zamanda işlemeyi tercih *edebilir* .

## <a name="conventions-for-componentbase"></a>İçin kurallar `ComponentBase`

Varsayılan olarak, Razor Components ( `.razor` ), <xref:Microsoft.AspNetCore.Components.ComponentBase> aşağıdaki saatlerde rerendering tetikleme mantığını içeren temel sınıftan devralınır:

* Bir üst bileşenden güncelleştirilmiş bir parametre kümesi uyguladıktan sonra.
* Basamaklı bir parametre için güncelleştirilmiş bir değer uygulandıktan sonra.
* Bir olayın bildiriminde ve kendi olay işleyicilerinden birini çağırarak.
* Kendi yöntemine yapılan çağrıdan sonra <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .

<xref:Microsoft.AspNetCore.Components.ComponentBase>Aşağıdakilerden biri doğru olursa, parametre güncelleştirmeleri nedeniyle bu öğeden devralan bileşenler atlayın:

* Tüm parametre değerleri, bilinen sabit ilkel türlerdir (örneğin,,, `int` `string` `DateTime` ) ve önceki parametre kümesi ayarlanmasından bu yana değiştirilmez.
* Bileşenin <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> yöntemi döndürür `false` .

Hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> bkz <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender> ..

## <a name="control-the-rendering-flow"></a>İşleme akışını denetleme

Çoğu durumda, <xref:Microsoft.AspNetCore.Components.ComponentBase> kurallar bir olay oluştuktan sonra doğru bileşen alt kümesiyle sonuçlanır. Geliştiriciler, çerçeveye hangi bileşenlerin yeniden başlatılacağını ve ne zaman yeniden yeniden örnekleyeceğinizi belirten el ile mantık sağlamak için genellikle gerekli değildir. Çerçevenin kurallarının genel etkisi, bir olayı alan bileşenin kendisini tekrar tekrar harekete geçirmesidir ve bu da parametre değerleri değişmiş olabilecek alt bileşenlerin rerendering yinelemeli olarak tetikler.

Çerçevenin kurallarının performans etkileri ve bir uygulamanın bileşen hiyerarşisinin nasıl iyileştirileceği hakkında daha fazla bilgi için bkz <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> ..

## <a name="when-to-call-statehaschanged"></a>Ne zaman çağrılacağını `StateHasChanged`

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType>Yöntemi, bir işlemeyi istediğiniz zaman tetiklemeniz için izin verir. Ancak, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> gereksiz işleme maliyetlerini sağladığından, yaygın bir hata olan gereksiz bir hata çağırmamaya dikkat edin.

*Şu* durumlarda çağırmanız gerekmez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :

* <xref:Microsoft.AspNetCore.Components.ComponentBase>Birçok rutin olay işleyicisi için bir işlemeyi tetiklediği zaman uyumlu veya zaman uyumsuz olarak olayları düzenli olarak işleme.
* Genel yaşam [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) <xref:Microsoft.AspNetCore.Components.ComponentBase> döngüsü olayları için bir işlemeyi tetikleyip, veya gibi tipik yaşam döngüsü mantığını uygulama.

Ancak, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Aşağıdaki bölümlerde açıklanan durumlarda çağrı yapmak mantıklı olabilir:

* [Zaman uyumsuz bir işleyici birden çok zaman uyumsuz aşama içerir](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [Blazorİşleme ve olay işleme sistemine bir dış öğe çağrısı alma](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [Bileşeni belirli bir olay tarafından yeniden kullanılan alt ağacın dışında işlemek için](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>Zaman uyumsuz bir işleyici birden çok zaman uyumsuz aşama içerir

`Counter`Her tıklama için sayıyı dört kez güncelleştiren aşağıdaki bileşeni göz önünde bulundurun.

`Pages/Counter.razor`:

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

Görevlerin .NET 'te tanımlanmasıyla ilgili olarak bir alıcısı, <xref:System.Threading.Tasks.Task> Ara zaman uyumsuz durumlar yerine yalnızca son tamamlanmasını gözlemlemez. Bu nedenle, <xref:Microsoft.AspNetCore.Components.ComponentBase> yalnızca <xref:System.Threading.Tasks.Task> ilk kez döndürüldüğünde ve son tamamlandığında rerendering tetiklenebilir <xref:System.Threading.Tasks.Task> . Diğer ara noktalarda yeniden günlüğe eklemek için kullanılamaz. Ara noktalarda yeniden kullanmak istiyorsanız kullanın <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .

### <a name="receiving-a-call-from-something-external-to-the-no-locblazor-rendering-and-event-handling-system"></a>Blazorİşleme ve olay işleme sistemine bir dış öğe çağrısı alma

<xref:Microsoft.AspNetCore.Components.ComponentBase> yalnızca kendi yaşam döngüsü yöntemleri ve tetiklenmiş olayları hakkında bilgi sahibi Blazor . <xref:Microsoft.AspNetCore.Components.ComponentBase> kodunuzda oluşabilecek diğer olaylar hakkında bilgi sahibi değildir. Örneğin, özel bir veri deposu tarafından oluşturulan tüm C# olayları için olarak bilinmez Blazor . Bu tür olayların Kullanıcı arabirimindeki güncelleştirilmiş değerleri görüntülemesi için rerendering tetiklenmesi için kullanın <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .

Başka bir kullanım durumunda, `Counter` sayıyı düzenli bir aralıkta güncelleştirmek için tarafından kullanılan aşağıdaki bileşeni göz önünde bulundurun <xref:System.Timers.Timer?displayProperty=fullName> ve <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Kullanıcı arabirimini güncelleştirmek için çağırır.

`Pages/Counter.razor`:

```razor
@page "/counter"
@using System.Timers
@implements IDisposable

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    void IDisposable.Dispose() => timer.Dispose();
}
```

Önceki örnekte, `OnTimerCallback` çağrı <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Blazor içindeki değişiklikleri farkında olmadığından çağrılmalıdır `currentCount` . `OnTimerCallback` , Blazor yönetilen işleme akışı veya olay bildiriminin dışında çalışır.

Benzer şekilde, geri çağırma, Blazor eşitleme bağlamı dışında çağrıldığı için, içindeki mantığı, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> oluşturucunun eşitleme bağlamına taşımak üzere kaydırmak gerekir. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> yalnızca oluşturucunun eşitleme bağlamından çağrılabilir, aksi takdirde bir özel durum oluşturur. Bu, diğer kullanıcı arabirimi çerçevelerinden UI iş parçacığına sıralama ile eşdeğerdir.

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>Bir bileşeni belirli bir olay tarafından yeniden eklenen alt ağacın dışında işlemek için

Kullanıcı arabiriminiz bir bileşene bir olay gönderme, bazı durumları değiştirme ve olayı alan bir alt öğesi olmayan tamamen farklı bir bileşeni yeniden oluşturma gereksinimi içerebilir.

Bu senaryoyu ele almanın bir yolu, büyük olasılıkla bir DI hizmeti olarak birden çok bileşene eklenen bir *durum yönetimi* sınıfına sahip olur. Bir bileşen durum yöneticisinde bir yöntemi çağırdığında, durum Yöneticisi bir bağımsız bileşen tarafından alınan bir C# olayını tetikleyebilir.

Bu C# olayları, işleme işlem hattının dışında olduğundan Blazor , <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> durum yöneticisinin olaylarına yanıt olarak işlemek istediğiniz diğer bileşenlere çağrı yapın.

Bu, <xref:System.Timers.Timer?displayProperty=fullName> [önceki bölüm](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) bölümünde daha önceki bir durum ile benzerdir. Yürütme çağrı yığını genellikle oluşturucunun eşitleme bağlamında kaldığından, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> normalde gerekli değildir. <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> yalnızca mantığın, ' a çağrı yapma <xref:System.Threading.Tasks.Task.ContinueWith%2A> <xref:System.Threading.Tasks.Task> ya da bir ile bekleme gibi eşitleme bağlamını iptal eden olması durumunda <xref:System.Threading.Tasks.Task> gereklidir [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) .
