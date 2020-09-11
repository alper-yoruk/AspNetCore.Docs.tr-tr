---
title: ASP.NET Core Blazor WebAssembly performans en iyi yöntemleri
author: pranavkm
description: ASP.NET Core uygulamalarında performansı artırma Blazor WebAssembly ve sık karşılaşılan performans sorunlarından kaçınmaya yönelik ipuçları.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 91d0eb7b4910d1cf19b179372546afa63cd3f9c1
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009602"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>ASP.NET Core Blazor WebAssembly performans en iyi yöntemleri

By [Pranav Krishnamoorthy](https://github.com/pranavkm)

Bu makalede ASP.NET Core Blazor WebAssembly performans en iyi uygulamalarına yönelik yönergeler sunulmaktadır.

## <a name="avoid-unnecessary-component-renders"></a>Gereksiz bileşen işlediğini önleyin

Blazor, bir bileşenin, bileşenin değiştirmediğinden rerendering bir bileşeni dağıtma algoritması bir bileşeni engeller. <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType>Bileşen işleme üzerinde ayrıntılı denetim için geçersiz kılın.

İlk işleme sonrasında hiçbir şekilde hiçbir değişiklik olmayan bir UI bileşeni yazıyorsanız, şunu <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> döndürecek şekilde yapılandırın `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Çoğu uygulama ayrıntılı denetim gerektirmez, ancak bir <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Kullanıcı arabirimi olayına yanıt veren bir bileşeni seçmeli olarak işlemek için kullanılabilir. Kullanmak <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> çok sayıda bileşenin işlendiği senaryolarda de önemli olabilir. Kılavuzdaki kılavuz <xref:Microsoft.AspNetCore.Components.EventCallback> çağrılarının bir hücresinde tek bir bileşende kullanıldığı bir kılavuz düşünün <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> . Çağırma <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> , her alt bileşenin yeniden işlenmesine neden olur. Yalnızca az sayıda hücre rerendering gerektiriyorsa, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> gereksiz sıra performans cezasından kaçınmak için kullanın.

Aşağıdaki örnekte:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> geçersiz kılınır ve <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Bu, bileşen yüklendiğinde başlangıçta olan değerine ayarlanır `false` .
* Düğme seçildiğinde, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> olarak ayarlanır ve `true` bileşen güncelleştirilmiş ile yeniden başlamaya zorlar `currentCount` .
* Rerendering hemen sonra, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> `false` düğmenin bir sonraki seçilene kadar daha fazla rerendering 'yi engellemek için, geri değerini olarak ayarlayın.

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Yeniden kullanılabilir parçaları sanallaştırın

Bileşenler, kod ve biçimlendirme için yeniden kullanılabilir parçalar üretmede kullanışlı bir yaklaşım sunar. Genel olarak, uygulamanın gereksinimleriyle en iyi şekilde uyum sağlayan ayrı bileşenleri yazmayı öneririz. Bir desteklenmediği uyarısıyla, her ek alt bileşen bir üst bileşeni işlemek için gereken toplam süreye katkıda bulunur. Çoğu uygulama için ek yük gözardı edilebilir değildir. Çok sayıda bileşen üreten uygulamalar, işlenen bileşenlerin sayısını sınırlama gibi işlem yükünü azaltmak için stratejileri kullanmayı göz önünde bulundurmalıdır.

Örneğin, bileşenleri içeren yüzlerce satırı işleyen bir kılavuz veya liste, işlemcinin işlenmesi için yoğun bir işlemdir. Belirli bir zamanda bileşenlerin yalnızca bir alt kümesinin işlenmesi için bir kılavuz veya liste düzeninin sanallaştırılmasını düşünün. Bileşen alt kümesi işleme örneği için örnek uygulamada aşağıdaki bileşenlere bakın [ `Virtualization` (ASPNET/Samples GitHub deposu)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): <xref:Microsoft.AspNetCore.Components.ComponentBase> Kullanıcı kaydırması temelinde bir hava durumu veri satırı kümesini işlemek Için uygulayan C# dilinde yazılmış bir bileşen.
* `FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): tek seferde `Virtualize` 25 ' lik Hava durumu verilerini göstermek için bileşeni kullanır.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Verileri sıralamak için JavaScript birlikte çalışabiliri önleyin

Blazor WebAssembly' De, bir JavaScript (js) birlikte çalışma çağrısının WebAssembly-js sınırını geçmesi gerekir. İki bağlamda içeriğin serileştirilmesi ve serisini kaldırma, uygulama için işleme yükünü oluşturur. Sık kullanılan JS birlikte çalışma çağrıları performansı olumsuz etkiler. Verilerin sınır genelinde sıralanmasını azaltmak için, uygulamanın birçok küçük yükü tek bir büyük yük içinde birleştirip birleştireceğini, WebAssembly ve JS arasında yüksek hacimli bağlam geçişi yapmaktan kaçının.

## <a name="use-systemtextjson"></a>Üzerinde System.Text.Jskullan

Blazor' nin JS birlikte çalışma uygulamasının <xref:System.Text.Json> , düşük bellek ayırması olan yüksek performanslı BIR JSON serileştirme kitaplığı olan ' i kullanır. Kullanmak <xref:System.Text.Json> , bir veya daha fazla ALTERNATIF JSON kitaplığı ekleyerek ek uygulama yükü boyutuna neden olmaz.

Geçiş Kılavuzu için, bkz. ' [den `Newtonsoft.Json` `System.Text.Json` ' ye geçiş ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Uygun yerlerde eşzamanlı ve unmarshalled JS birlikte çalışma API 'Lerini kullanın

Blazor WebAssembly , <xref:Microsoft.JSInterop.IJSRuntime> uygulamalar için kullanılabilen tek sürümün iki ek sürümünü sunar Blazor Server :

* <xref:Microsoft.JSInterop.IJSInProcessRuntime> , zaman uyumsuz sürümlerden daha az ek yük olan, JS birlikte çalışabilirlik çağrılarının zaman uyumlu olarak yüklenmesine izin verir:

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> sıradan olmayan JS birlikte çalışabilirlik çağrılarına izin verir:

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > Kullanırken <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> , JS birlikte çalışma yaklaşımının en az ek yükü olsa da, bu API 'lerle etkileşimde bulunmak için gereken JavaScript API 'leri Şu anda açıklanmamıştır ve gelecekteki sürümlerde yapılan değişikliklere tabidir.

## <a name="reduce-app-size"></a>Uygulama boyutunu azalt

### <a name="intermediate-language-il-linking"></a>Ara dil (IL) bağlama

Uygulamanın [bağlanması Blazor WebAssembly ](xref:blazor/host-and-deploy/configure-linker) , uygulamanın ikili dosyalarında kullanılmayan kodu kırparak uygulamanın boyutunu azaltır. Varsayılan olarak, bağlayıcı yalnızca yapılandırmada oluşturulurken etkin olur `Release` . Bu özellikten yararlanmak için, [`dotnet publish`](/dotnet/core/tools/dotnet-publish) [-c |--yapılandırma](/dotnet/core/tools/dotnet-publish#options) seçeneği olarak ayarlanan komutunu kullanarak uygulamayı dağıtım için yayımlayın `Release` :

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a>Yavaş yük derlemeleri

Derlemeler bir rota için gerektiğinde çalışma zamanında derlemeleri yükleyin. Daha fazla bilgi için bkz. <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Sıkıştırma

Bir Blazor WebAssembly uygulama yayımlandığında, çıkış sırasında uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması için ek yükü kaldırmak üzere çıkış sırasında statik olarak sıkıştırılır. Blazor , içerik eklemek için sunucuyu kullanır ve statik olarak sıkıştırılan dosyaları sunar.

Bir uygulama dağıtıldıktan sonra uygulamanın sıkıştırılmış dosyalara hizmet ettiğini doğrulayın. Tarayıcının Geliştirici Araçları ağ sekmesini inceleyin ve dosyaların veya ile birlikte sunulduğunu doğrulayın `Content-Encoding: br` `Content-Encoding: gz` . Ana bilgisayar sıkıştırılmış dosyalara hizmet vermemişse, içindeki yönergeleri izleyin <xref:blazor/host-and-deploy/webassembly#compression> .

### <a name="disable-unused-features"></a>Kullanılmayan özellikleri devre dışı bırak

Blazor WebAssemblyçalışma zamanı, uygulamanın daha küçük bir yük boyutu için gerektirmemesi durumunda devre dışı bırakılabilen aşağıdaki .NET özelliklerini içerir:

* Saat dilimi bilgilerinin doğru olması için bir veri dosyası dahildir. Uygulama bu özelliği gerektirmiyorsa, `BlazorEnableTimeZoneSupport` uygulamanın proje dosyasındaki MSBuild özelliğini şu şekilde ayarlayarak devre dışı bırakmayı göz önünde bulundurun `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* Varsayılan olarak, Blazor WebAssembly Kullanıcı kültürünün tarih ve para birimi gibi değerleri görüntülemesi için gereken Genelleştirme kaynaklarını taşır. Uygulama yerelleştirmeyi gerektirmiyorsa, uygulamayı kültürü temel alan sabit kültürü destekleyecek şekilde yapılandırabilirsiniz `en-US` :

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```
::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Doğru çalışma gibi API 'Leri oluşturmak için harmanlama bilgileri eklenmiştir <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> . Uygulamanın harmanlama verileri gerektirmediğinden emin değilseniz, `BlazorWebAssemblyPreserveCollationData` uygulamanın proje dosyasındaki MSBuild özelliğini şu şekilde ayarlayarak devre dışı bırakmayı göz önünde bulundurun `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
