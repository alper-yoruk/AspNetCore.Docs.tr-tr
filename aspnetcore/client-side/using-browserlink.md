---
title: ASP.NET Core tarayıcı bağlantısı
author: ncarandini
description: Tarayıcı bağlantısının, geliştirme ortamını bir veya daha fazla Web tarayıcısına bağlayan bir Visual Studio özelliği olduğunu açıklar.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: ab4ca78fa50768ff66536608a7cf03e73aecf73a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628826"
---
# <a name="browser-link-in-aspnet-core"></a>ASP.NET Core tarayıcı bağlantısı

, [Nicolò Carandini](https://github.com/ncarandini), [Mike, son](https://github.com/MikeWasson)ve [Tom Dykstra](https://github.com/tdykstra) tarafından

Tarayıcı bağlantısı, bir Visual Studio özelliğidir. Geliştirme ortamı ile bir veya daha fazla Web tarayıcısı arasında bir iletişim kanalı oluşturur. Tarayıcı bağlantısını, Web uygulamanızı birden çok tarayıcıda tek seferde yenilemek için kullanabilirsiniz. Bu, tarayıcılar arası testler için kullanışlıdır.

## <a name="browser-link-setup"></a>Tarayıcı bağlantısı kurulumu

::: moniker range=">= aspnetcore-3.0"

[Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketini projenize ekleyin. ASP.NET Core Razor sayfaları veya MVC projeleri için, Razor ' de açıklandığı gibi (*. cshtml*) dosyaları için çalışma zamanı derlemesini de etkinleştirin <xref:mvc/views/view-compilation> . Razor söz dizimi değişiklikleri yalnızca çalışma zamanı derlemesi etkinleştirildiğinde uygulanır.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

ASP.NET Core 2,0 projesi ASP.NET Core 2,1 ' e dönüştürülürken ve [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e geçiş yaparken, tarayıcı bağlantısı Işlevselliği için [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketini yükler. ASP.NET Core 2,1 proje şablonları, `Microsoft.AspNetCore.App` metapackage 'i varsayılan olarak kullanır.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2,0 **Web uygulaması**, **Empty**ve **Web API** proje şablonları, [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)Için bir paket başvurusu içeren [Microsoft. aspnetcore. All meta paketini](xref:fundamentals/metapackage)kullanır. Bu nedenle, `Microsoft.AspNetCore.All` metapackage 'in kullanılması tarayıcı bağlantısının kullanılabilir olmasını sağlamak için başka bir eylem gerektirmez.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1. x **Web uygulaması** proje şablonunda, [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketi için bir paket başvurusu vardır. Diğer proje türleri için bir paket başvurusu eklemenizi gerektirir `Microsoft.VisualStudio.Web.BrowserLink` .

::: moniker-end

### <a name="configuration"></a>Yapılandırma

`UseBrowserLink` `Startup.Configure` Yöntemi çağırın:

```csharp
app.UseBrowserLink();
```

`UseBrowserLink`Çağrı genellikle `if` geliştirme ortamında tarayıcının bağlantısını sağlayan bir bloğun içine yerleştirilir. Örnek:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Tarayıcı bağlantısı kullanma

Bir ASP.NET Core projesi açıkken, Visual Studio **hata ayıklama hedefi** araç çubuğu denetiminin yanında tarayıcı bağlantısı araç çubuğu denetimini gösterir:

![Tarayıcı bağlantısı açılan menüsü](using-browserlink/_static/browserLink-dropdown-menu.png)

Tarayıcı bağlantısı araç çubuğu denetiminden şunları yapabilirsiniz:

* Web uygulamasını aynı anda birkaç tarayıcıda yenileyin.
* **Tarayıcı bağlantısı panosunu**açın.
* **Tarayıcı bağlantısını**etkinleştirin veya devre dışı bırakın. Note: tarayıcı bağlantısı, Visual Studio 'da varsayılan olarak devre dışıdır.
* [CSS otomatik eşitlemesini](#enable-or-disable-css-auto-sync)etkinleştirin veya devre dışı bırakın.

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Aynı anda birkaç tarayıcıda Web uygulamasını yenileyin

Projeyi başlatırken başlatılacak tek bir Web tarayıcısı seçmek için, **Hata Ayıkla hedef** araç çubuğu denetimindeki açılan menüyü kullanın:

![F5 açılır menüsü](using-browserlink/_static/debug-target-dropdown-menu.png)

Aynı anda birden çok tarayıcı açmak için, aynı açılan listeden **... öğesine gidin** ' i seçin. İstediğiniz tarayıcıları seçmek için <kbd>CTRL</kbd> tuşunu basılı tutarak, ardından da **Araştır**' a tıklayın:

![Birçok tarayıcıyı aynı anda aç](using-browserlink/_static/open-many-browsers-at-once.png)

Aşağıdaki ekran görüntüsünde, dizin görünümü açık ve iki açık tarayıcıyla Visual Studio gösterilmektedir:

![İki tarayıcıyla Eşitle örneği](using-browserlink/_static/sync-with-two-browsers-example.png)

Projeye bağlı tarayıcıları görmek için tarayıcı bağlantısı araç çubuğu denetiminin üzerine gelin:

![Üzerine gelme İpucu](using-browserlink/_static/hoover-tip.png)

Dizin görünümünü değiştirin ve tarayıcı bağlantısı yenileme düğmesine tıkladığınızda tüm bağlı tarayıcılar güncelleştirilir:

![tarayıcılar-değişikliklere karşı eşitleme](using-browserlink/_static/browsers-sync-to-changes.png)

Tarayıcı bağlantısı, Visual Studio dışından başlamış ve uygulama URL 'sine gidebileceğiniz tarayıcılarla de kullanılabilir.

### <a name="the-browser-link-dashboard"></a>Tarayıcı bağlantısı panosu

Tarayıcı bağlantısı açılan menüsünden **tarayıcı bağlantısı pano** penceresini açın ve açık tarayıcılarla bağlantıyı yönetin:

![Açık-browserslink-Pano](using-browserlink/_static/open-browserlink-dashboard.png)

Hiçbir tarayıcı bağlı değilse, **Tarayıcıda görüntüle** bağlantısını seçerek hata ayıklama olmayan bir oturum başlatabilirsiniz:

![browserlink-Pano-bağlantı yok](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Aksi halde, bağlı tarayıcılar her bir tarayıcının gösterdiği sayfanın yoluyla gösterilir:

![browserlink-Pano-iki bağlantı](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Yalnızca bu tarayıcıyı yenilemek için tek bir tarayıcı adına de tıklayabilirsiniz.

### <a name="enable-or-disable-browser-link"></a>Tarayıcı bağlantısını etkinleştir veya devre dışı bırak

Tarayıcı bağlantısını devre dışı bıraktıktan sonra yeniden etkinleştirdiğinizde, bunları yeniden bağlamak için tarayıcıları yenilemeniz gerekir.

### <a name="enable-or-disable-css-auto-sync"></a>CSS otomatik eşitlemesini etkinleştir veya devre dışı bırak

CSS otomatik eşitleme etkinleştirildiğinde, CSS dosyalarında herhangi bir değişiklik yaptığınızda bağlantılı tarayıcılar otomatik olarak yenilenir.

## <a name="how-it-works"></a>Nasıl çalışır?

Tarayıcı bağlantısı [SignalR](xref:signalr/introduction) , Visual Studio ile tarayıcı arasında bir iletişim kanalı oluşturmak için kullanır. Tarayıcı bağlantısı etkinleştirildiğinde, Visual Studio SignalR birden çok istemcinin (tarayıcının) bağlanabileceği bir sunucu gibi davranır. Tarayıcı bağlantısı ayrıca ASP.NET Core isteği ardışık düzenine bir ara yazılım bileşeni kaydeder. Bu bileşen `<script>` , sunucudan her sayfa isteğine özel başvurular çıkarır. Tarayıcıda **Görünüm kaynağı** ' nı seçerek komut dosyası başvurularını görebilir ve etiket içeriğinin sonuna kadar kaydırma yapabilirsiniz `<body>` :

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Kaynak dosyalarınız değiştirilmez. Ara yazılım bileşeni, betik başvurularını dinamik olarak çıkarır.

Tarayıcı tarafı kodu tüm JavaScript olduğundan, SignalR tarayıcı eklentisi gerekmeden desteklenen tüm tarayıcılarda çalışıyor olur.
