---
title: ASP.NET Çekirdeğine GirişBlazor
author: guardrex
description: ASP.NET Core Blazoruygulamasında .NET ile etkileşimli istemci tarafı web UI oluşturmanın bir yolu olan ASP.NET Core'u keşfedin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 6d2e95cd2ec92f97a97cb558fb39e4540450c766
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405950"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a>ASP.NET Çekirdeğine GirişBlazor

Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)

*Hoş Blazorgeldiniz!*

Blazoris a framework for building interactive client-side web UI with .NET:

* JavaScript yerine C# kullanarak zengin etkileşimli web'ler oluşturun.
* .NET'te yazılı sunucu ve istemci tarafı uygulama mantığını paylaşın.
* Mobil tarayıcılar da dahil olmak üzere geniş tarayıcı desteği için UI'yi HTML ve CSS olarak işleyin.
* [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)gibi modern barındırma platformları ile entegre edin.

İstemci tarafı web geliştirme için .NET'i kullanmak aşağıdaki avantajları sunar:

* JavaScript yerine C# kodu yazın.
* .NET kitaplıklarının mevcut .NET ekosisteminden yararlanın.
* Uygulama mantığını sunucu ve istemci arasında paylaşın.
* Yararlanın . NET'in performansı, güvenilirliği ve güvenliği.
* Windows, Linux ve macOS'ta Visual Studio ile üretken kalın.
* Kararlı, zengin özelliklere sahip ve kullanımı kolay ortak bir dil, çerçeve ve araç kümesi oluşturun.

## <a name="components"></a>Bileşenler

Blazoruygulamalar *bileşenleri*ne lerdir. Bir Blazor bileşen, sayfa, iletişim kutusu veya veri giriş formu gibi ui'nin bir öğesidir.

Bileşenler .NET derlemeleri yerleşik .NET sınıfları şunlardır:

* Esnek UI oluşturma mantığını tanımlayın.
* Kullanıcı olaylarını işleyebilir.
* Iç içe ve yeniden kullanılabilir.
* [Razor sınıf kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.

Bileşen sınıfı genellikle *.razor* dosya uzantısı olan [bir Jilet](xref:mvc/views/razor) biçimlendirme sayfası biçiminde yazılır. Bileşenler Blazor resmen *Razor bileşenleri*olarak adlandırılır. Razor, HTML biçimlendirmesini geliştirici üretkenliği için tasarlanmış C# koduyla birleştirmek için bir sözdizimidir. Razor, [IntelliSense](/visualstudio/ide/using-intellisense) desteğiyle aynı dosyada HTML biçimlendirmesi ve C# arasında geçiş yapmanızı sağlar. Razor Pages ve MVC de Razor kullanın. Bir istek/yanıt modeli etrafında oluşturulmuş Olan Razor Pages ve MVC'nin aksine, bileşenler özellikle istemci tarafındaki Kullanıcı Arabirimi mantığı ve kompozisyonu için kullanılır.

Aşağıdaki Jilet biçimlendirmesi, başka bir bileşenin içinde iç içe yapabilen bir bileşeni *(Dialog.razor)* gösterir:

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

İletişim kutusunun gövde içeriği`ChildContent`( )`Title`ve başlık ( ) kullanıcı ui'sinde bu bileşeni kullanan bileşen tarafından sağlanır. `OnYes`düğmenin olayı tarafından tetiklenen bir `onclick` C# yöntemidir.

BlazorUI kompozisyonu için doğal HTML etiketleri kullanır. HTML öğeleri bileşenleri belirtir ve etiketin öznitelikleri değerleri bir bileşenin özelliklerine geçirir.

Aşağıdaki örnekte, `Index` bileşen `Dialog` bileşeni kullanır. `ChildContent`ve `Title` öğenin öznitelikleri ve `<Dialog>` içeriği tarafından ayarlanır.

*Index.razor*:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

İletişim, üst öğeye *(Index.razor)* bir tarayıcıda erişildiğinde işlenir:

![Tarayıcıda işlenen iletişim bileşeni](index/_static/dialog.png)

Bu bileşen uygulamada kullanıldığında, [Visual Studio](/visualstudio/ide/using-intellisense) ve Visual [Studio Code'daki](https://code.visualstudio.com/docs/editor/intellisense) IntelliSense sözdizimi ve parametre tamamlama ile geliştirmeyi hızlandırAr.

Bileşenler, kullanıcı arasını esnek ve verimli bir şekilde güncelleştirmek için kullanılan *render ağacı*olarak adlandırılan tarayıcının Belge Nesnesi Modeli'nin (DOM) bellek içi gösterimine dönüşür.

## <a name="opno-locblazor-webassembly"></a>BlazorWebAssembly

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorWebAssembly, .NET ile etkileşimli istemci tarafı web uygulamaları oluşturmak için tek sayfalık bir uygulama çerçevesidir. BlazorWebAssembly eklentileri veya kod transpilasyonu olmadan açık web standartları kullanır ve mobil tarayıcılar da dahil olmak üzere tüm modern web tarayıcılarında çalışır.

Web tarayıcılarında .NET kodunun çalıştırılmasının [WebAssembly](https://webassembly.org) (kısaltılmış *wasm)* tarafından yürütülmesi mümkün kılındı. WebAssembly, hızlı indirme ve maksimum yürütme hızı için optimize edilmiş kompakt bir bytecode biçimidir. WebAssembly açık bir web standardıdır ve eklentileri olmadan web tarayıcılarında desteklenir.

WebAssembly kodu *JavaScript birlikte çalışabilirlik* (veya *JavaScript interop)* olarak adlandırılan JavaScript üzerinden tarayıcının tam işlevselliğine erişebilirsiniz. .NET kodu tarayıcıda WebAssembly üzerinden yürütülen sandbox istemci makineüzerinde kötü amaçlı eylemlere karşı sağladığı korumaları ile tarayıcının JavaScript sandbox çalışır.

![BlazorWebAssembly, WebAssembly ile tarayıcıda .NET kodunu çalıştırın.](index/_static/blazor-webassembly.png)

Bir Blazor WebAssembly uygulaması bir tarayıcıda oluşturulup çalıştırıldığında:

* C# kod dosyaları ve Razor dosyaları .NET derlemeleri halinde derlenir.
* Derlemeler ve .NET çalışma süresi tarayıcıya indirilir.
* BlazorWebAssembly .NET çalışma saatini yakalar ve uygulamanın derlemelerini yüklemek için çalışma süresini yapılandırır. Blazor WebAssembly çalışma süresi, DOM manipülasyonu ve tarayıcı API çağrılarını işlemek için JavaScript interop'u kullanır.

Yayınlanan uygulamanın boyutu, *yük boyutu,* bir uygulamanın kullanılabilirliği için kritik bir performans faktörüdür. Büyük bir uygulamanın tarayıcıya indirilen kullanıcı deneyimini azaltan nispeten uzun bir zaman alır. BlazorWebAssembly, indirme sürelerini azaltmak için yük boyutunu optimize eder:

* Kullanılmayan [kod, Ara Dil (IL) Bağlantı Aracı](xref:host-and-deploy/blazor/configure-linker)tarafından yayınlandığında uygulamadan çıkarılır.
* HTTP yanıtları sıkıştırılır.
* .NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.

## <a name="opno-locblazor-server"></a>BlazorSunucu

Blazorui güncelleştirmelerinin nasıl uygulandığından bileşen oluşturma mantığını ayırır. BlazorSunucu, ASP.NET Core uygulamasında Razor bileşenlerini sunucuda barındırma desteği sağlar. UI güncelleştirmeleri bir [SignalR](xref:signalr/introduction) bağlantı üzerinden işlenir.

Çalışma zamanı, tarayıcıdan sunucuya UI olayları göndermeyi işler ve bileşenleri çalıştırdıktan sonra sunucu tarafından gönderilen UI güncelleştirmelerini tarayıcıya uygular.

Sunucu tarafından Blazor tarayıcıyla iletişim kurmak için kullanılan bağlantı, JavaScript interop çağrılarını işlemek için de kullanılır.

![BlazorSunucu sunucuda .NET kodunu çalıştırıyor ve bağlantı SignalR üzerinden istemcideki Belge Nesnesi Modeli ile etkileşime giriyor](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>JavaScript ile birlikte çalışma

Üçüncü taraf JavaScript kitaplıkları ve tarayıcı API'lerine erişim gerektiren uygulamalar için bileşenler JavaScript ile birlikte çalışır. Bileşenler, JavaScript'in kullanabildiği herhangi bir kitaplık veya API'yi kullanabilir. C# kodu JavaScript koduna çağrıyapabilir ve JavaScript kodu C# koduna çağrıyapabilir. Daha fazla bilgi için aşağıdaki makalelere bakın:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a>Kod paylaşımı ve .NET Standardı

Blazoruygular [.NET Standart 2.0](/dotnet/standard/net-standard). .NET Standardı, .NET uygulamalarında yaygın olan .NET API'lerinin resmi bir belirtimidir. .NET Standart sınıf kitaplıkları , .NET BlazorFramework, .NET Core, Xamarin, Mono ve Unity gibi farklı .NET platformları arasında paylaşılabilir.

Bir web tarayıcısının içinde geçerli olmayan API'ler (örneğin, dosya sistemine erişim, bir soket açma ve iş parçacığı) bir <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Ek kaynaklar

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [C# Rehberi](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Awesome Blazor ](https://github.com/AdrienTorris/awesome-blazor) topluluk bağlantıları
