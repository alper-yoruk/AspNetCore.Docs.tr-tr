---
title: ASP.NET Core 'de geç yük derlemeleri Blazor WebAssembly
author: guardrex
description: ASP.NET Core uygulamalarında derlemelerin nasıl yavaş yükleneceğini öğrenin Blazor WebAssembly .
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 46f98080ad40f614f9cb1af2190f263d205c1016
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865154"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core 'de geç yük derlemeleri Blazor WebAssembly

By [Safia Abdalla](https://safia.rocks) ve [Luke Latham](https://github.com/guardrex)

Blazor WebAssembly Uygulama başlatma performansı, bazı uygulama derlemelerinin yüklenmesi gerekene kadar artırılabilir ve bu da *yavaş yükleme*olarak adlandırılır. Örneğin, yalnızca tek bir bileşeni işlemek için kullanılan derlemeler yalnızca Kullanıcı o bileşene gittiğinde yüklenecek şekilde ayarlanabilir. Yüklendikten sonra derlemeler istemci tarafında önbelleğe alınır ve gelecekteki tüm gezinmeler için kullanılabilir.

Blazoryavaş yükleme özelliği, Kullanıcı belirli bir rotaya geçtiğinde çalışma zamanı sırasında derlemeleri yükleyen yavaş yükleme için uygulama derlemelerini işaretlemenizi sağlar. Özelliği proje dosyasındaki değişikliklerden ve uygulamanın yönlendiricisinde yapılan değişikliklerle oluşur.

> [!NOTE]
> Blazor ServerDerlemeler bir uygulamadaki istemciye indirilmediğinden derleme yavaş yüklemesi uygulama avantajına sahip değildir Blazor Server .

## <a name="project-file"></a>Proje dosyası

Öğeyi kullanarak uygulamanın proje dosyasında () yavaş yükleme için derlemeleri işaretleyin `.csproj` `BlazorWebAssemblyLazyLoad` . Uzantı olmadan derleme adını kullanın `.dll` . BlazorFramework, bu öğe grubu tarafından belirtilen derlemelerin uygulama başlatma sırasında yüklenmesine engel olur. Aşağıdaki örnek, yavaş yükleme için büyük bir özel derlemeyi ( `GrantImaharaRobotControls.dll` ) işaretler. Yavaş yükleme için işaretlenen bir derlemenin bağımlılıkları varsa, proje dosyasında da yavaş yükleme için işaretlenmelidir.

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

Yalnızca uygulama tarafından kullanılan derlemelerin geç yüklü olması olabilir. Bağlayıcı, yayımlanan çıktısından kullanılmayan derlemeleri şeritleri.

> [!NOTE]
> .NET 5 sürüm adayı 1 ' de (RC1) veya sonraki sürümlerde, bu, PARÇAAL-Eylül 'de yayımlanacak, derleme adı şu uzantıyı gerektirir `.dll` :
>
> ```xml
> <ItemGroup>
>  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
> </ItemGroup>
> ```

## <a name="router-component"></a>`Router` bileşeni

Blazor`Router`bileşeni, hangi derlemelerin Blazor yönlendirilebilir bileşenleri arayacağını belirler. `Router`Bileşen ayrıca, kullanıcının gittiği yol için bileşenin işlenmesinden de sorumludur. `Router`Bileşeni, `OnNavigateAsync` yavaş yükleme ile birlikte kullanılabilecek bir özelliği destekler.

Uygulamanın `Router` bileşeninde ( `App.razor` ):

* Bir `OnNavigateAsync` geri çağırma ekleyin. `OnNavigateAsync`İşleyici, Kullanıcı şunları yaparken çağrılır:
  * Doğrudan tarayıcısından gezinerek bir yolu ilk kez ziyaret edin.
  * Bağlantı veya çağırma kullanarak yeni bir yola gider <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> .
* Geç yüklenmiş derlemeler yönlendirilebilir bileşenler içeriyorsa, bileşene bir [liste](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (örneğin, adlandırılmış `lazyLoadedAssemblies` ) ekleyin. Derlemeler <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> yönlendirilebilir bileşenleri içermesi durumunda derlemeler koleksiyona geri geçirilir. Çerçeve, derlemeleri rotalar için arar ve yeni yollar bulunursa rota koleksiyonunu günceller.

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

`OnNavigateAsync`Geri çağırma işlenmeyen bir özel durum oluşturursa, [ Blazor hata Kullanıcı arabirimi](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) çağrılır.

### <a name="assembly-load-logic-in-onnavigateasync"></a>İçindeki derleme yükleme mantığı `OnNavigateAsync`

`OnNavigateAsync``NavigationContext`, hedef yol ( `Path` ) ve iptal belirteci () dahil olmak üzere geçerli zaman uyumsuz gezinti olayı hakkında bilgi sağlayan bir parametreye sahiptir `CancellationToken` :

* `Path`Özelliği, kullanıcının, gibi uygulamanın temel yoluna göre hedef yoludur `/robot` .
* , `CancellationToken` Zaman uyumsuz görevin iptalini gözlemlemek için kullanılabilir. `OnNavigateAsync` Kullanıcı farklı bir sayfaya gittiğinde, çalışmakta olan gezinti görevini otomatik olarak iptal eder.

İçinde `OnNavigateAsync` , yüklenecek derlemeleri belirleme mantığını uygulayın. Seçeneklere şunlar dahildir:

* Yöntemin içindeki koşullu denetimler `OnNavigateAsync` .
* Bileşene eklenen ya da blok içinde uygulanan bir arama tablosu [`@code`](xref:mvc/views/razor#code) .

`LazyAssemblyLoader` , derlemeleri yüklemek için çerçeve tarafından sunulan tek bir hizmettir. `LazyAssemblyLoader` `Router` Bileşene ekle:

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

, `LazyAssemblyLoader` Aşağıdakileri sağlayan `LoadAssembliesAsync` yöntemini sağlar:

* Bir ağ çağrısı aracılığıyla derlemeleri getirmek için JS birlikte çalışabilirliği kullanır.
* Derlemeleri tarayıcıda WebAssembly üzerinde yürüten çalışma zamanına yükler.

> [!NOTE]
> Framework 'ün yavaş yükleme uygulamasının sunucusunda prerendering desteği vardır. Prerendering sırasında, yavaş yükleme için işaretlenenler de dahil olmak üzere tüm derlemelerin yüklendiği varsayılır.

### <a name="user-interaction-with-navigating-content"></a>İçerikle Kullanıcı etkileşimi `<Navigating>`

Birkaç saniye sürebilen derlemeler yüklenirken, `Router` bileşen kullanıcıya bir sayfa geçişinin oluştuğunu gösterebilir:

* [`@using`](xref:mvc/views/razor#using)Ad alanı için bir yönerge ekleyin <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> .
* `<Navigating>`Sayfa geçişi olayları sırasında görüntülenecek işaretlerle bileşene bir etiket ekleyin.

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a>Üzerinde iptalleri işle `OnNavigateAsync`

`NavigationContext`Geri çağırmaya geçirilen nesne, `OnNavigateAsync` `CancellationToken` Yeni bir gezinti olayı gerçekleştiğinde ayarlanmış bir içerir. `OnNavigateAsync`Bu iptal belirteci, `OnNavigateAsync` geri çağırma işlemini eski bir gezinmede çalıştırmaya devam etmeden kaçınmak üzere ayarlandığında, geri çağırma işlemi throw olmalıdır.

Bir kullanıcı yönlendirme A 'ya gider ve sonra B 'ye yönlendirmek için, uygulama `OnNavigateAsync` a yolu için geri çağırma çalıştırmaya devam edememelidir:

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> ' Deki iptal belirteci `NavigationContext` iptal edildiğinde, bir bileşeni önceki bir gezinmede işlemek gibi istenmeden davranışa neden olabilir.

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a>`OnNavigateAsync` olaylar ve yeniden adlandırılmış derleme dosyaları

Kaynak yükleyicisi, dosyasında tanımlanan derleme adlarını kullanır `blazor.boot.json` . [Derlemeler yeniden adlandırılırsa](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), yöntemlerde kullanılan derleme adları `OnNavigateAsync` ve dosyadaki derleme adları `blazor.boot.json` eşitlenmemiş.

Bunu düzeltmek için:

* Kullanılacak derleme adlarını belirlerken uygulamanın üretim ortamında çalışıp çalışmadığını denetleyin.
* Yeniden adlandırılmış derleme adlarını ayrı bir dosyada depolayın ve bu dosyadan okuyarak, ve yöntemlerinde hangi derleme adının kullanılacağını saptayın `LazyLoadAssemblyService` `OnNavigateAsync` .

### <a name="complete-example"></a>Örnek Tamam

Aşağıdaki tamamlanmış `Router` Bileşen, `GrantImaharaRobotControls.dll` Kullanıcı öğesine gittiğinde derlemeyi yüklemeyi gösterir `/robot` . Sayfa geçişleri sırasında, kullanıcıya biçimlendirilmiş bir ileti görüntülenir.

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a>Sorun giderme

* Beklenmeyen işleme gerçekleşirse (örneğin, önceki bir gezinmede bir bileşen işlendiğinde), iptal belirteci ayarlanmışsa kodun bulunduğunu onaylayın.
* Uygulama başlangıcında derlemeler hala yüklenirse, derlemenin proje dosyasında yavaş yüklenmiş olarak işaretlendiğinden emin olun.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/webassembly-performance-best-practices>
