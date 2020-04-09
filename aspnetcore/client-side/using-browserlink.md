---
title: ASP.NET Core Tarayıcı Bağlantısı
author: ncarandini
description: Tarayıcı Bağlantısının, geliştirme ortamını bir veya daha fazla web tarayıcısıyla ilişkilendiren bir Visual Studio özelliği olduğunu açıklar.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658853"
---
# <a name="browser-link-in-aspnet-core"></a>ASP.NET Core Tarayıcı Bağlantısı

Yazar: [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), ve [Tom Dykstra](https://github.com/tdykstra)

Browser Link bir Visual Studio özelliğidir. Geliştirme ortamı ile bir veya daha fazla web tarayıcısı arasında bir iletişim kanalı oluşturur. Tarayıcı Bağlantısı'nı kullanarak web uygulamanızı aynı anda birden fazla tarayıcıda yenileyebilirsiniz, bu da tarayıcılar arası testler için yararlıdır.

## <a name="browser-link-setup"></a>Tarayıcı Bağlantısı kurulumu

::: moniker range=">= aspnetcore-3.0"

[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketini projenize ekleyin. Core Razor Pages veya MVC projeleri ASP.NET için, aynı zamanda Razor (*.cshtml*) dosyalarının çalışma zamanı derlemesini de <xref:mvc/views/view-compilation>sağlar. Jilet sözdizimi değişiklikleri yalnızca çalışma zamanı derlemesi etkinleştirildiğinde uygulanır.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

core 2.1ASP.NET bir ASP.NET Core 2.0 projesini dönüştürüp [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)geçerken, Tarayıcı Bağlantısı işlevi için [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketini yükleyin. Core 2.1 proje şablonlarının `Microsoft.AspNetCore.App` ASP.NET varsayılan olarak meta paketi kullanır.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 **Web Application**, **Boş**ve **Web API** proje şablonları [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)kullanın , [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)için bir paket referans içerir. Bu nedenle, `Microsoft.AspNetCore.All` metapaketi kullanmak, Tarayıcı Bağlantısını kullanıma açmak için başka bir işlem gerektirmez.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x **Web Application** proje şablonu [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketi için bir paket başvurusu vardır. Diğer proje türleri için bir paket `Microsoft.VisualStudio.Web.BrowserLink`başvurusu eklemenizi gerektirir.

::: moniker-end

### <a name="configuration"></a>Yapılandırma

`Startup.Configure` Yöntemde arama: `UseBrowserLink`

```csharp
app.UseBrowserLink();
```

Arama `UseBrowserLink` genellikle yalnızca Geliştirme `if` ortamında Tarayıcı Bağlantısı sağlayan bir blok içinde yerleştirilir. Örneğin:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Tarayıcı Bağlantısı nasıl kullanılır?

ASP.NET Core projeniz açık olduğunda, Visual Studio **Hata Ayıklama Hedef** araç çubuğu denetiminin yanındaki Tarayıcı Bağlantısı araç çubuğu denetimini gösterir:

![Tarayıcı Bağlantısı açılır menüsü](using-browserlink/_static/browserLink-dropdown-menu.png)

Browser Link araç çubuğu denetiminden şunları yapabilirsiniz:

* Web uygulamasını aynı anda birden fazla tarayıcıda yenileyin.
* Tarayıcı **Bağlantı Panosunu**açın.
* **Tarayıcı Bağlantısını**etkinleştirme veya devre dışı Not: Visual Studio'da Tarayıcı Bağlantısı varsayılan olarak devre dışı bırakılır.
* [CSS Otomatik Eşitleme'yi](#enable-or-disable-css-auto-sync)etkinleştirme veya devre dışı.

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Web uygulamasını aynı anda birden fazla tarayıcıda yenileyin

Projeyi başlatırken başlatılabilmek için tek bir web tarayıcısı seçmek için **Hata Ayıklama Hedef** araç çubuğu denetiminde açılır menüyü kullanın:

![F5 açılır menü](using-browserlink/_static/debug-target-dropdown-menu.png)

Aynı anda birden çok tarayıcı açmak için, aynı açılır yerden **Gözat'ı...** seçin. İstediğiniz tarayıcıları seçmek için <kbd>Ctrl</kbd> tuşuna basılı tutun ve ardından **Gözat'ı**tıklatın:

![Aynı anda birçok tarayıcı açın](using-browserlink/_static/open-many-browsers-at-once.png)

Aşağıdaki ekran görüntüsü, Index görünümü açık ve iki açık tarayıcısı olan Visual Studio'yu gösterir:

![İki tarayıcı örneğiyle eşitleme](using-browserlink/_static/sync-with-two-browsers-example.png)

Projeye bağlı tarayıcıları görmek için Tarayıcı Bağlantısı araç çubuğu denetiminin üzerine tıklayın:

![Hover ucu](using-browserlink/_static/hoover-tip.png)

Dizin görünümünü değiştirin ve Tarayıcı Bağlantısı yenileme düğmesini tıklattığınızda bağlı tüm tarayıcılar güncelleştirilir:

![tarayıcılar-eşitleme-değişiklikler](using-browserlink/_static/browsers-sync-to-changes.png)

Tarayıcı Bağlantısı ayrıca Visual Studio'nun dışından başlattığınız ve uygulama URL'sine gidin tarayıcılarla da çalışır.

### <a name="the-browser-link-dashboard"></a>Tarayıcı Bağlantı Panosu

Açık tarayıcılarla bağlantıyı yönetmek için Tarayıcı Bağlantısı açılır menüsünden **Tarayıcı Bağlantısı Panosu** penceresini açın:

![açık tarayıcılarbağlantı panosu](using-browserlink/_static/open-browserlink-dashboard.png)

Tarayıcı bağlı değilse, **Tarayıcıda Görüntüle** bağlantısını seçerek hata ayıklamayan bir oturum başlatabilirsiniz:

![browserlink-pano-bağlantı yok](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Aksi takdirde, bağlı tarayıcılar her tarayıcının gösterdiği sayfaya giden yol ile gösterilir:

![browserlink-pano-iki bağlantı](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Ayrıca, yalnızca tarayıcıyı yenilemek için tek bir tarayıcı adını tıklatabilirsiniz.

### <a name="enable-or-disable-browser-link"></a>Tarayıcı Bağlantısını etkinleştirme veya devre dışı

Tarayıcı Bağlantısını devre dışı ettikten sonra yeniden etkinleştirdiğinizde, tarayıcıları yeniden bağlamak için tarayıcıları yenilemeniz gerekir.

### <a name="enable-or-disable-css-auto-sync"></a>CSS Otomatik Eşitleme'yi etkinleştirme veya devre dışı

CSS Otomatik Eşitleme etkinleştirildiğinde, CSS dosyalarında herhangi bir değişiklik yaptığınızda bağlı tarayıcılar otomatik olarak yenilenir.

## <a name="how-it-works"></a>Nasıl çalışır?

Browser Link, Visual Studio ile tarayıcı arasında bir iletişim kanalı oluşturmak için kullanır. [SignalR](xref:signalr/introduction) Tarayıcı Bağlantısı etkinleştirildiğinde, Visual Studio SignalR birden çok istemcinin (tarayıcıların) bağlanabileceği bir sunucu gibi davranır. Browser Link ayrıca ASP.NET Core istek ardışık alanında bir ara yazılım bileşeni kaydeder. Bu bileşen, `<script>` sunucudan gelen her sayfa isteğine özel başvurular enjekte eder. Tarayıcıda Kaynak Görüntüle'yi **View source** seçerek ve `<body>` etiket içeriğinin sonuna kaydırarak komut dosyası başvurularını görebilirsiniz:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Kaynak dosyalarınız değiştirilmedi. Ara yazılım bileşeni, komut dosyası başvurularını dinamik olarak enjekte eder.

Tarayıcı tarafındaki kodun tümü JavaScript olduğundan, tarayıcı eklentisi gerektirmeden SignalR destekleyen tüm tarayıcılarda çalışır.
