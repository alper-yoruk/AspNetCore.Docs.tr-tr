---
title: ASP.NET Core girişBlazor
author: guardrex
description: ASP.NET Core uygulamasında Blazor.NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için bir yol ASP.NET Core keşfedelim.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: ced3e2cc0428fccf6f0b2eba7a3f045e07002234
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771956"
---
# <a name="introduction-to-aspnet-core-blazor"></a>ASP.NET Core girişBlazor

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

*Hoş geldiniz Blazor!*

Blazor, .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmaya yönelik bir çerçevedir:

* JavaScript yerine C# kullanarak zengin etkileşimli uo 'lar oluşturun.
* .NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.
* Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.
* [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)gibi modern barındırma platformlarıyla tümleştirin.

İstemci tarafı web geliştirme için .NET kullanmak aşağıdaki avantajları sunar:

* JavaScript yerine C# dilinde kod yazın.
* .NET kitaplıklarının mevcut .NET ekosisteminden yararlanın.
* Sunucu ve istemci arasında uygulama mantığını paylaşma.
* Avantajı. NET ' in performans, güvenilirlik ve güvenlik.
* Windows, Linux ve macOS 'ta Visual Studio ile üretken olun.
* Kararlı, özellik açısından zengin ve kullanımı kolay olan ortak diller, çerçeveler ve araçlar kümesi oluşturun.

## <a name="components"></a>Bileşenler

Blazoruygulamalar *bileşenleri*temel alır. İçindeki Blazor bir bileşeni, bir sayfa, iletişim veya veri girişi formu gibi bir kullanıcı arabirimi öğesidir.

Bileşenler, .NET Derlemeleriyle yerleşik olarak bulunan .NET sınıflarıdır:

* Esnek kullanıcı arabirimi işleme mantığını tanımlayın.
* Kullanıcı olaylarını işleyin.
* İç içe ve yeniden kullanılabilir olabilir.
* , [ Razor Sınıf kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.

Bileşen sınıfı genellikle [Razor](xref:mvc/views/razor) *. Razor* dosya uzantısına sahip bir biçimlendirme sayfası biçiminde yazılır. İçindeki Blazor bileşenler, resmi olarak * Razor bileşen*olarak adlandırılır. Razor, HTML işaretlemesini geliştirici üretkenliği için tasarlanan C# kodu ile birleştirmek için bir sözdizimidir. Razor[IntelliSense](/visualstudio/ide/using-intellisense) desteğiyle aynı dosyada HTML Işaretlemesi ile C# arasında geçiş yapmanıza olanak sağlar. RazorSayfalar ve MVC de kullanır Razor. Bir Razor istek/yanıt modeli etrafında oluşturulan SAYFALARıN ve MVC 'nin aksine, bileşenler özellikle ISTEMCI tarafı UI mantığı ve bileşimi için kullanılır.

Aşağıdaki Razor biçimlendirme, başka bir bileşen içinde iç içe yerleştirilebileceğini bir bileşeni (*iletişim kutusu. Razor*) gösterir:

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

İletişim kutusunun gövde içeriği (`ChildContent`) ve başlığı (`Title`), bu bileşeni Kullanıcı arabiriminde kullanan bileşen tarafından sağlanır. `OnYes`, düğme `onclick` olayının tetiklediği bir C# yöntemidir.

BlazorUI bileşimi için doğal HTML etiketleri kullanır. HTML öğeleri, bileşenleri belirtir ve bir etiketin öznitelikleri değerleri bir bileşenin özelliklerine iletir.

Aşağıdaki örnekte `Index` bileşen `Dialog` bileşeni kullanır. `ChildContent`ve `Title` `<Dialog>` öğesi öznitelikleri ve içeriği tarafından ayarlanır.

*Index. Razor*:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Üst öğeye (*Index. Razor*) bir tarayıcıda erişildiğinde iletişim kutusu işlenir:

![Tarayıcıda işlenen iletişim kutusu bileşeni](index/_static/dialog.png)

Bu bileşen uygulamada kullanıldığında, [Visual Studio](/visualstudio/ide/using-intellisense) 'da ıntellisense ve [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) , sözdizimi ve parametre tamammasıyla geliştirmeyi hızlandırır.

Bileşenler, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılan bir *işleme ağacı*adlı, tarayıcı belge nesne MODELI (DOM) ' ın bellek içi gösterimine işlenir.

## <a name="blazor-webassembly"></a>BlazorWebAssembly

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorWebAssembly, .NET ile etkileşimli istemci tarafı Web uygulamaları oluşturmaya yönelik tek sayfalı bir uygulama çerçevesidir. BlazorWebAssembly, eklentiler veya Code transpilation olmadan açık Web standartları kullanır ve mobil tarayıcılar dahil tüm modern web tarayıcılarında kullanılabilir.

Web tarayıcıları içinde .NET kodu çalıştırmak, [Webassembly](https://webassembly.org) *(kısaltılmış)* tarafından mümkün hale getirilir. WebAssembly hızlı indirme ve en yüksek yürütme hızı için iyileştirilmiş bir sıkıştırma kodu biçimidir. WebAssembly, açık bir web standardıdır ve eklentileri olmayan Web tarayıcılarında desteklenir.

WebAssembly Code, JavaScript ile *birlikte çalışabilirlik* (veya *JavaScript birlikte çalışma*) olarak adlandırılan JavaScript aracılığıyla tarayıcının tüm işlevlerine erişebilir. Tarayıcıda WebAssembly aracılığıyla yürütülen .NET kodu, sanal makinenin istemci makinesindeki kötü amaçlı eylemlere karşı sağladığı korumalar ile tarayıcının JavaScript korumalı alanında çalışır.

![BlazorWebAssembly, WebAssembly ile tarayıcıda .NET kodu çalıştırır.](index/_static/blazor-webassembly.png)

Bir Blazor weelsembly uygulaması bir tarayıcıda oluşturulup çalıştırıldığında:

* C# kod dosyaları ve Razor dosyaları .net Derlemeleriyle derlenir.
* Derlemeler ve .NET çalışma zamanı tarayıcıya indirilir.
* BlazorWebAssembly, .NET çalışma zamanı önyükleme ve çalışma zamanını uygulamanın derlemelerini yükleyecek şekilde yapılandırır. Webassembly çalışma zamanı, Blazor DOM işleme ve tarayıcı API çağrılarını Işlemek için JavaScript birlikte çalışabilirliği kullanır.

Yayınlanan uygulamanın boyutu, *Yük boyutu*, uygulamanın useyeteneğinin önemli bir performans etkendir. Büyük bir uygulamanın tarayıcıya indirmesi oldukça uzun sürer ve bu da Kullanıcı deneyimini azaltabilecek. BlazorWebAssembly indirme sürelerini azaltmak için yük boyutunu iyileştirir:

* Kullanılmayan kod, [ara dil (IL) bağlayıcı](xref:host-and-deploy/blazor/configure-linker)tarafından yayımlandığında uygulamadan çıkarılır.
* HTTP yanıtları sıkıştırılır.
* .NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.

## <a name="blazor-server"></a>BlazorServer

BlazorKullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır. BlazorSunucu, bir ASP.NET Core uygulamasındaki Razor sunucuda bileşenleri barındırmak için destek sağlar. Kullanıcı Arabirimi güncelleştirmeleri bir [SignalR](xref:signalr/introduction) bağlantı üzerinden işlenir.

Çalışma zamanı, tarayıcıdan sunucuya kullanıcı arabirimi olayları göndermeyi ve bileşenleri çalıştırdıktan sonra sunucu tarafından tarayıcıya geri gönderilen Kullanıcı arabirimi güncelleştirmelerini uygular.

Sunucu tarafından Blazor tarayıcıyla iletişim kurmak için kullanılan bağlantı, JavaScript birlikte çalışma çağrılarını işlemek için de kullanılır.

![BlazorSunucu, sunucuda .NET kodu çalıştırır ve bir SignalR bağlantı üzerinden istemcideki belge nesne modeli etkileşime girer](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>JavaScript ile birlikte çalışma

Üçüncü taraf JavaScript kitaplıklarını ve tarayıcı API 'Lerine erişimi gerektiren uygulamalar için, bileşenler JavaScript ile birlikte çalışır. Bileşenler, JavaScript 'in kullanabileceği herhangi bir kitaplığı veya API kullanma yeteneğine sahiptir. C# kodu JavaScript kodunu çağırabilir ve JavaScript kodu C# koduna çağrı yapabilir. Daha fazla bilgi için aşağıdaki makalelere bakın:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a>Kod paylaşımı ve .NET Standard

Blazor[2,0 .NET Standard](/dotnet/standard/net-standard)uygular. .NET Standard, .NET uygulamaları genelinde ortak olan .NET API 'lerinin resmi bir belirtimidir. .NET Standard sınıf kitaplıkları Blazor, .NET Framework, .NET Core, Xamarin, mono ve Unity gibi farklı .net platformları arasında paylaşılabilir.

Bir Web tarayıcısı içinde geçerli olmayan API 'Ler (örneğin, dosya sistemine erişmek, bir yuva açmak ve iş parçacığı açmak) bir <xref:System.PlatformNotSupportedException>oluşturur.

## <a name="additional-resources"></a>Ek kaynaklar

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [C# Kılavuzu](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Başar Blazor ](https://github.com/AdrienTorris/awesome-blazor) topluluk bağlantıları
