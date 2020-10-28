---
title: ASP.NET Core giriş Blazor
author: guardrex
description: ASP.NET Core uygulamasında Blazor .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için bir yol ASP.NET Core keşfedelim.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: bae3e96021971e373ad743a0b52da7f69d839c40
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690592"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a>ASP.NET Core giriş Blazor

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

*Hoş geldiniz Blazor !*

Blazor , [.net](/dotnet/standard/tour)ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmaya yönelik bir çerçevedir:

* [JavaScript](https://www.javascript.com)yerine [C#](/dotnet/csharp/) kullanarak zengin etkileşimli uo 'lar oluşturun.
* .NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.
* Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.
* [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)gibi modern barındırma platformlarıyla tümleştirin.

İstemci tarafı web geliştirme için .NET kullanmak aşağıdaki avantajları sunar:

* JavaScript yerine C# dilinde kod yazın.
* [.Net kitaplıklarının](/dotnet/standard/class-libraries)mevcut .NET ekosisteminden yararlanın.
* Sunucu ve istemci arasında uygulama mantığını paylaşma.
* Avantajı. NET ' in performans, güvenilirlik ve güvenlik.
* Windows, Linux ve macOS 'ta [Visual Studio](https://visualstudio.microsoft.com) ile üretken olun.
* Kararlı, özellik açısından zengin ve kullanımı kolay olan ortak diller, çerçeveler ve araçlar kümesi oluşturun.

## <a name="components"></a>Bileşenler

Blazor uygulamalar *bileşenleri* temel alır. İçindeki bir bileşeni Blazor , bir sayfa, iletişim veya veri girişi formu gibi bir kullanıcı arabirimi öğesidir.

Bileşenler, [.NET derlemeleri](/dotnet/standard/assembly/) yerleşik olarak bulunan .NET C# sınıflarıdır:

* Esnek kullanıcı arabirimi işleme mantığını tanımlayın.
* Kullanıcı olaylarını işleyin.
* İç içe ve yeniden kullanılabilir olabilir.
* , [ Razor Sınıf kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.

Bileşen sınıfı, genellikle [Razor](xref:mvc/views/razor) bir dosya uzantısına sahip bir biçimlendirme sayfası biçiminde yazılır `.razor` . İçindeki bileşenler Blazor , resmi olarak *Razor bileşen* olarak adlandırılır. Razor , HTML işaretlemesini geliştirici üretkenliği için tasarlanan C# kodu ile birleştirmek için bir sözdizimidir. Razor Visual Studio 'da [IntelliSense](/visualstudio/ide/using-intellisense) programlama desteğiyle aynı dosyada HTML Işaretlemesi ile C# arasında geçiş yapmanıza olanak sağlar. Razor Sayfalar ve MVC de kullanır Razor . RazorBir istek/yanıt modeli etrafında oluşturulan sayfaların ve MVC 'nin aksine, bileşenler özellikle istemci tarafı UI mantığı ve bileşimi için kullanılır.

Blazor UI bileşimi için doğal HTML etiketleri kullanır. Aşağıdaki Razor biçimlendirme `Dialog.razor` bir iletişim kutusu görüntüleyen ve Kullanıcı bir düğme seçtiğinde bir olayı işleyen bir bileşeni () gösterir:

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

Yukarıdaki örnekte, `OnYes` düğme olayının tetiklediği bir C# yöntemidir `onclick` . İletişim kutusunun Text ( `ChildContent` ) ve title ( `Title` ), bu bileşeni Kullanıcı arabiriminde kullanan aşağıdaki bileşen tarafından sağlanır.

`Dialog`Bileşen, BIR HTML etiketi kullanılarak başka bir bileşen içinde iç içe geçmiş. Aşağıdaki örnekte, `Index` bileşen ( `Pages/Index.razor` ) önceki `Dialog` bileşeni kullanır. Etiketin özniteliği, `Title` bileşen özelliğine bir başlık değeri geçirir `Dialog` `Title` .  `Dialog`Bileşenin metni (), `ChildContent` öğesinin içeriği tarafından ayarlanır `<Dialog>` . `Dialog`Bileşen `Index` bileşene eklendiğinde, [Visual Studio 'da IntelliSense](/visualstudio/ide/using-intellisense) , sözdizimi ve parametre tamammasıyla geliştirmeyi hızlandırır.

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

İletişim kutusu, `Index` bileşene bir tarayıcıda erişildiğinde işlenir. Düğme Kullanıcı tarafından seçildiğinde, tarayıcının geliştirici araçları konsolunda yöntemi tarafından yazılan ileti görüntülenir `OnYes` :

![İletişim kutusu bileşeni, Dizin bileşeninin içinde iç içe geçmiş tarayıcıda işlendi. Tarayıcı geliştirici araçları konsolu, Kullanıcı Evet ' i seçtiğinde C# kodu tarafından yazılan iletiyi gösterir! düğmesine basın.](index/_static/dialog.png)

Bileşenler, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılan bir *işleme ağacı* adlı, tarayıcı [belge nesne modeli (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) ' ın bellek içi gösterimine işlenir.

## Blazor WebAssembly

Blazor WebAssembly , .NET ile etkileşimli istemci tarafı Web uygulamaları oluşturmaya yönelik [tek sayfalı uygulama (Spa) çerçevesidir](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) . Blazor WebAssembly , eklentiler olmadan açık Web standartları kullanır veya kodu diğer dillere yeniden derler. Blazor WebAssembly mobil tarayıcılar dahil tüm modern web tarayıcılarında çalışmaktadır.

Web tarayıcıları içinde .NET kodu çalıştırmak, [Webassembly](https://webassembly.org) (kısaltılmış) tarafından mümkün hale getirilir `wasm` . WebAssembly hızlı indirme ve en yüksek yürütme hızı için iyileştirilmiş bir sıkıştırma kodu biçimidir. WebAssembly, açık bir web standardıdır ve eklentileri olmayan Web tarayıcılarında desteklenir.

WebAssembly Code, JavaScript birlikte *çalışabilirliği* olarak adlandırılan JavaScript aracılığıyla tarayıcının tüm işlevlerine erişebilir, genellikle *JavaScript birlikte çalışma* veya *js birlikte çalışma* olarak kısaltılır. Tarayıcıda WebAssembly aracılığıyla yürütülen .NET kodu, sanal makinenin istemci makinesindeki kötü amaçlı eylemlere karşı sağladığı korumalar ile tarayıcının JavaScript korumalı alanında çalışır.

![::: No-Loc (Blazor WebAssembly)::: .NET kodunu WebAssembly ile tarayıcıda çalıştırır.](index/_static/blazor-webassembly.png)

Bir Blazor WebAssembly uygulama bir tarayıcıda oluşturulup çalıştırıldığında:

* C# kod dosyaları ve Razor dosyaları .net Derlemeleriyle derlenir.
* Derlemeler ve [.NET çalışma zamanı](/dotnet/framework/get-started/overview) tarayıcıya indirilir.
* Blazor WebAssembly .NET çalışma zamanı önyükleme ve çalışma zamanını uygulamanın derlemelerini yükleyecek şekilde yapılandırır. Blazor WebAssemblyÇalışma zamanı, DOM işleme ve tarayıcı API çağrılarını işlemek Için JavaScript birlikte çalışabilirliği kullanır.

Yayınlanan uygulamanın boyutu, *Yük boyutu* , uygulamanın useyeteneğinin önemli bir performans etkendir. Büyük bir uygulamanın tarayıcıya indirmesi oldukça uzun sürer ve bu da Kullanıcı deneyimini azaltabilecek. Blazor WebAssembly yükleme sürelerini azaltmak için yük boyutunu iyileştirir:

::: moniker range=">= aspnetcore-5.0"

* Kullanılmayan kod, [ara dil (IL) ayarlayıcısı](xref:blazor/host-and-deploy/configure-trimmer)tarafından yayımlandığında uygulamadan çıkarılır.
* HTTP yanıtları sıkıştırılır.
* .NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Kullanılmayan kod, [ara dil (IL) bağlayıcı](xref:blazor/host-and-deploy/configure-linker)tarafından yayımlandığında uygulamadan çıkarılır.
* HTTP yanıtları sıkıştırılır.
* .NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.

::: moniker-end

## Blazor Server

Blazor Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır. *Blazor Server* ASP.NET Core uygulamasında sunucuda barındırma bileşenleri için destek sağlar Razor . Kullanıcı Arabirimi güncelleştirmeleri bir bağlantı üzerinden işlenir [SignalR](xref:signalr/introduction) .

Çalışma zamanı tutamaçları:

* Tarayıcıdan sunucusuna kullanıcı arabirimi olayları gönderiliyor.
* Sunucu tarafından geri gönderilen işlenen bileşene Kullanıcı Arabirimi güncelleştirmeleri uygulanıyor.

Blazor ServerTarayıcıyla iletişim kurmak için tarafından kullanılan bağlantı, JavaScript birlikte çalışma çağrılarını işlemek için de kullanılır.

![::: No-Loc (Blazor Server)::: sunucuda .NET kodu çalıştırır ve şu şekilde istemcisinde Belge Nesne Modeli:: No-Loc (SignalR)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>JavaScript ile birlikte çalışma

Üçüncü taraf JavaScript kitaplıklarını ve tarayıcı API 'Lerine erişimi gerektiren uygulamalar için, bileşenler JavaScript ile birlikte çalışır. Bileşenler, JavaScript 'in kullanabileceği herhangi bir kitaplığı veya API kullanma yeteneğine sahiptir. C# kodu [JavaScript kodunu çağırabilir](xref:blazor/call-javascript-from-dotnet)ve JavaScript kodu [c# koduna çağrı](xref:blazor/call-dotnet-from-javascript)yapabilir.

## <a name="code-sharing-and-net-standard"></a>Kod paylaşımı ve .NET Standard

Blazor[.NET Standard](/dotnet/standard/net-standard) Blazor projelerin .NET Standard belirtimlerine uygun kitaplıklara başvurmalarını sağlayan .NET Standard uygular. .NET Standard, .NET uygulamaları genelinde ortak olan .NET API 'lerinin resmi bir belirtimidir. .NET Standard sınıf kitaplıkları Blazor , .NET Framework, .NET Core, Xamarin, mono ve Unity gibi farklı .net platformları arasında paylaşılabilir.

Bir Web tarayıcısı içinde geçerli olmayan API 'Ler (örneğin, dosya sistemine erişmek, bir yuva açmak ve iş parçacığı açmak) bir oluşturur <xref:System.PlatformNotSupportedException> .

## <a name="additional-resources"></a>Ek kaynaklar

* [WebAssembly](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [C# Kılavuzu](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Başar Blazor ](https://github.com/AdrienTorris/awesome-blazor) Topluluk bağlantıları
