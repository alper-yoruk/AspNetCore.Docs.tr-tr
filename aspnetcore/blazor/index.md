---
title: ASP.NET Core girişBlazor
author: guardrex
description: ASP.NET Core uygulamasında Blazor .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için bir yol ASP.NET Core keşfedelim.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 06/19/2020
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
uid: blazor/index
ms.openlocfilehash: ad543087243658f09a23e4f6d957d0c6aa77b361
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014184"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a>ASP.NET Core girişBlazor

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

*Hoş geldiniz Blazor !*

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

Blazoruygulamalar *bileşenleri*temel alır. İçindeki bir bileşeni Blazor , bir sayfa, iletişim veya veri girişi formu gibi bir kullanıcı arabirimi öğesidir.

Bileşenler, .NET Derlemeleriyle yerleşik olarak bulunan .NET sınıflarıdır:

* Esnek kullanıcı arabirimi işleme mantığını tanımlayın.
* Kullanıcı olaylarını işleyin.
* İç içe ve yeniden kullanılabilir olabilir.
* , [ Razor Sınıf kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.

Bileşen sınıfı, genellikle [Razor](xref:mvc/views/razor) bir dosya uzantısına sahip bir biçimlendirme sayfası biçiminde yazılır `.razor` . İçindeki bileşenler Blazor , resmi olarak * Razor bileşen*olarak adlandırılır. Razor, HTML işaretlemesini geliştirici üretkenliği için tasarlanan C# kodu ile birleştirmek için bir sözdizimidir. Razor[IntelliSense](/visualstudio/ide/using-intellisense) desteğiyle aynı dosyada HTML Işaretlemesi ile C# arasında geçiş yapmanıza olanak sağlar. RazorSayfalar ve MVC de kullanır Razor . RazorBir istek/yanıt modeli etrafında oluşturulan sayfaların ve MVC 'nin aksine, bileşenler özellikle istemci tarafı UI mantığı ve bileşimi için kullanılır.

Aşağıdaki Razor biçimlendirme, `Dialog.razor` başka bir bileşen içinde iç içe yerleştirilebileceğini bir bileşeni () gösterir:

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

İletişim kutusunun gövde içeriği ( `ChildContent` ) ve başlığı ( `Title` ), bu bileşeni Kullanıcı arabiriminde kullanan bileşen tarafından sağlanır. `OnYes`, düğme olayının tetiklediği bir C# yöntemidir `onclick` .

BlazorUI bileşimi için doğal HTML etiketleri kullanır. HTML öğeleri, bileşenleri belirtir ve bir etiketin öznitelikleri değerleri bir bileşenin özelliklerine iletir.

Aşağıdaki örnekte `Index` bileşen `Dialog` bileşeni kullanır. `ChildContent`ve `Title` öğesi öznitelikleri ve içeriği tarafından ayarlanır `<Dialog>` .

`Pages/Index.razor`:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Üst öğeye ( `Pages/Index.razor` ) bir tarayıcıda erişildiğinde iletişim kutusu işlenir:

![Tarayıcıda işlenen iletişim kutusu bileşeni](index/_static/dialog.png)

Bu bileşen uygulamada kullanıldığında, [Visual Studio](/visualstudio/ide/using-intellisense) 'da ıntellisense ve [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) , sözdizimi ve parametre tamammasıyla geliştirmeyi hızlandırır.

Bileşenler, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılan bir *işleme ağacı*adlı, tarayıcı belge nesne MODELI (DOM) ' ın bellek içi gösterimine işlenir.

## Blazor WebAssembly

Blazor WebAssembly, .NET ile etkileşimli istemci tarafı Web uygulamaları oluşturmaya yönelik tek sayfalı bir uygulama çerçevesidir. Blazor WebAssembly, eklentiler veya kod transpilation olmadan açık Web standartları kullanır ve mobil tarayıcılar dahil tüm modern web tarayıcılarında kullanılabilir.

Web tarayıcıları içinde .NET kodu çalıştırmak, [Webassembly](https://webassembly.org) (kısaltılmış) tarafından mümkün hale getirilir `wasm` . WebAssembly hızlı indirme ve en yüksek yürütme hızı için iyileştirilmiş bir sıkıştırma kodu biçimidir. WebAssembly, açık bir web standardıdır ve eklentileri olmayan Web tarayıcılarında desteklenir.

WebAssembly Code, JavaScript ile *birlikte çalışabilirlik* (veya *JavaScript birlikte çalışma*) olarak adlandırılan JavaScript aracılığıyla tarayıcının tüm işlevlerine erişebilir. Tarayıcıda WebAssembly aracılığıyla yürütülen .NET kodu, sanal makinenin istemci makinesindeki kötü amaçlı eylemlere karşı sağladığı korumalar ile tarayıcının JavaScript korumalı alanında çalışır.

![::: No-Loc (Blazor WebAssembly)::: .NET kodunu WebAssembly ile tarayıcıda çalıştırır.](index/_static/blazor-webassembly.png)

Bir Blazor WebAssembly uygulama bir tarayıcıda oluşturulup çalıştırıldığında:

* C# kod dosyaları ve Razor dosyaları .net Derlemeleriyle derlenir.
* Derlemeler ve .NET çalışma zamanı tarayıcıya indirilir.
* Blazor WebAssembly.NET çalışma zamanı önyükleme ve çalışma zamanını uygulamanın derlemelerini yükleyecek şekilde yapılandırır. Blazor WebAssemblyÇalışma zamanı, DOM işleme ve tarayıcı API çağrılarını işlemek Için JavaScript birlikte çalışabilirliği kullanır.

Yayınlanan uygulamanın boyutu, *Yük boyutu*, uygulamanın useyeteneğinin önemli bir performans etkendir. Büyük bir uygulamanın tarayıcıya indirmesi oldukça uzun sürer ve bu da Kullanıcı deneyimini azaltabilecek. Blazor WebAssemblyyükleme sürelerini azaltmak için yük boyutunu iyileştirir:

* Kullanılmayan kod, [ara dil (IL) bağlayıcı](xref:blazor/host-and-deploy/configure-linker)tarafından yayımlandığında uygulamadan çıkarılır.
* HTTP yanıtları sıkıştırılır.
* .NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.

## Blazor Server

BlazorKullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır. Blazor ServerASP.NET Core uygulamasında sunucuda barındırma bileşenleri için destek sağlar Razor . Kullanıcı Arabirimi güncelleştirmeleri bir bağlantı üzerinden işlenir [SignalR](xref:signalr/introduction) .

Çalışma zamanı, tarayıcıdan sunucuya kullanıcı arabirimi olayları göndermeyi ve bileşenleri çalıştırdıktan sonra sunucu tarafından tarayıcıya geri gönderilen Kullanıcı arabirimi güncelleştirmelerini uygular.

Blazor ServerTarayıcıyla iletişim kurmak için tarafından kullanılan bağlantı, JavaScript birlikte çalışma çağrılarını işlemek için de kullanılır.

![::: No-Loc (Blazor Server)::: sunucuda .NET kodu çalıştırır ve şu şekilde istemcisinde Belge Nesne Modeli:: No-Loc (SignalR)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>JavaScript ile birlikte çalışma

Üçüncü taraf JavaScript kitaplıklarını ve tarayıcı API 'Lerine erişimi gerektiren uygulamalar için, bileşenler JavaScript ile birlikte çalışır. Bileşenler, JavaScript 'in kullanabileceği herhangi bir kitaplığı veya API kullanma yeteneğine sahiptir. C# kodu JavaScript kodunu çağırabilir ve JavaScript kodu C# koduna çağrı yapabilir. Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a>Kod paylaşımı ve .NET Standard

Blazor[.NET Standard 2,1](/dotnet/standard/net-standard)uygular ve bu sayede Blazor Projeler .NET Standard 2,1 veya daha önceki belirtimlere uygun kitaplıklara başvurabilir. .NET Standard, .NET uygulamaları genelinde ortak olan .NET API 'lerinin resmi bir belirtimidir. .NET Standard sınıf kitaplıkları Blazor , .NET Framework, .NET Core, Xamarin, mono ve Unity gibi farklı .net platformları arasında paylaşılabilir.

Bir Web tarayıcısı içinde geçerli olmayan API 'Ler (örneğin, dosya sistemine erişmek, bir yuva açmak ve iş parçacığı açmak) bir oluşturur <xref:System.PlatformNotSupportedException> .

## <a name="additional-resources"></a>Ek kaynaklar

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [C# Kılavuzu](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Başar Blazor ](https://github.com/AdrienTorris/awesome-blazor) Topluluk bağlantıları
