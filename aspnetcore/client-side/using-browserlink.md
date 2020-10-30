---
title: ASP.NET Core tarayıcı bağlantısı
author: ncarandini
description: Tarayıcı bağlantısının, geliştirme ortamını bir veya daha fazla Web tarayıcısına bağlayan bir Visual Studio özelliği olduğunu açıklar.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054495"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="68164-103">ASP.NET Core tarayıcı bağlantısı</span><span class="sxs-lookup"><span data-stu-id="68164-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="68164-104">, [Nicolò Carandini](https://github.com/ncarandini), [Mike, son](https://github.com/MikeWasson)ve [Tom Dykstra](https://github.com/tdykstra) tarafından</span><span class="sxs-lookup"><span data-stu-id="68164-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="68164-105">Tarayıcı bağlantısı, bir Visual Studio özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="68164-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="68164-106">Geliştirme ortamı ile bir veya daha fazla Web tarayıcısı arasında bir iletişim kanalı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="68164-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="68164-107">Tarayıcı bağlantısını, Web uygulamanızı birden çok tarayıcıda tek seferde yenilemek için kullanabilirsiniz. Bu, tarayıcılar arası testler için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="68164-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="68164-108">Tarayıcı bağlantısı kurulumu</span><span class="sxs-lookup"><span data-stu-id="68164-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68164-109">[Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketini projenize ekleyin.</span><span class="sxs-lookup"><span data-stu-id="68164-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="68164-110">ASP.NET Core :::no-loc(Razor)::: sayfaları veya MVC projeleri için, :::no-loc(Razor)::: ' de açıklandığı gibi ( *. cshtml* ) dosyaları için çalışma zamanı derlemesini de etkinleştirin <xref:mvc/views/view-compilation> .</span><span class="sxs-lookup"><span data-stu-id="68164-110">For ASP.NET Core :::no-loc(Razor)::: Pages or MVC projects, also enable runtime compilation of :::no-loc(Razor)::: ( *.cshtml* ) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="68164-111">:::no-loc(Razor)::: söz dizimi değişiklikleri yalnızca çalışma zamanı derlemesi etkinleştirildiğinde uygulanır.</span><span class="sxs-lookup"><span data-stu-id="68164-111">:::no-loc(Razor)::: syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="68164-112">ASP.NET Core 2,0 projesi ASP.NET Core 2,1 ' e dönüştürülürken ve [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e geçiş yaparken, tarayıcı bağlantısı Işlevselliği için [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="68164-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="68164-113">ASP.NET Core 2,1 proje şablonları, `Microsoft.AspNetCore.App` metapackage 'i varsayılan olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="68164-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="68164-114">ASP.NET Core 2,0 **Web uygulaması** , **Empty** ve **Web API** proje şablonları, [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)Için bir paket başvurusu içeren [Microsoft. aspnetcore. All meta paketini](xref:fundamentals/metapackage)kullanır.</span><span class="sxs-lookup"><span data-stu-id="68164-114">The ASP.NET Core 2.0 **Web Application** , **Empty** , and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="68164-115">Bu nedenle, `Microsoft.AspNetCore.All` metapackage 'in kullanılması tarayıcı bağlantısının kullanılabilir olmasını sağlamak için başka bir eylem gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="68164-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="68164-116">ASP.NET Core 1. x **Web uygulaması** proje şablonunda, [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketi için bir paket başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="68164-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="68164-117">Diğer proje türleri için bir paket başvurusu eklemenizi gerektirir `Microsoft.VisualStudio.Web.BrowserLink` .</span><span class="sxs-lookup"><span data-stu-id="68164-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="68164-118">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="68164-118">Configuration</span></span>

<span data-ttu-id="68164-119">`UseBrowserLink` `Startup.Configure` Yöntemi çağırın:</span><span class="sxs-lookup"><span data-stu-id="68164-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="68164-120">`UseBrowserLink`Çağrı genellikle `if` geliştirme ortamında tarayıcının bağlantısını sağlayan bir bloğun içine yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="68164-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="68164-121">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="68164-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="68164-122">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="68164-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="68164-123">Tarayıcı bağlantısı kullanma</span><span class="sxs-lookup"><span data-stu-id="68164-123">How to use Browser Link</span></span>

<span data-ttu-id="68164-124">Bir ASP.NET Core projesi açıkken, Visual Studio **hata ayıklama hedefi** araç çubuğu denetiminin yanında tarayıcı bağlantısı araç çubuğu denetimini gösterir:</span><span class="sxs-lookup"><span data-stu-id="68164-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Tarayıcı bağlantısı açılan menüsü](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="68164-126">Tarayıcı bağlantısı araç çubuğu denetiminden şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="68164-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="68164-127">Web uygulamasını aynı anda birkaç tarayıcıda yenileyin.</span><span class="sxs-lookup"><span data-stu-id="68164-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="68164-128">**Tarayıcı bağlantısı panosunu** açın.</span><span class="sxs-lookup"><span data-stu-id="68164-128">Open the **Browser Link Dashboard** .</span></span>
* <span data-ttu-id="68164-129">**Tarayıcı bağlantısını** etkinleştirin veya devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="68164-129">Enable or disable **Browser Link** .</span></span> <span data-ttu-id="68164-130">Note: tarayıcı bağlantısı, Visual Studio 'da varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="68164-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="68164-131">[CSS otomatik eşitlemesini](#enable-or-disable-css-auto-sync)etkinleştirin veya devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="68164-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="68164-132">Aynı anda birkaç tarayıcıda Web uygulamasını yenileyin</span><span class="sxs-lookup"><span data-stu-id="68164-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="68164-133">Projeyi başlatırken başlatılacak tek bir Web tarayıcısı seçmek için, **Hata Ayıkla hedef** araç çubuğu denetimindeki açılan menüyü kullanın:</span><span class="sxs-lookup"><span data-stu-id="68164-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 açılır menüsü](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="68164-135">Aynı anda birden çok tarayıcı açmak için, aynı açılan listeden **... öğesine gidin** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="68164-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="68164-136">İstediğiniz tarayıcıları seçmek için <kbd>CTRL</kbd> tuşunu basılı tutarak, ardından da **Araştır** ' a tıklayın:</span><span class="sxs-lookup"><span data-stu-id="68164-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse** :</span></span>

![Birçok tarayıcıyı aynı anda aç](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="68164-138">Aşağıdaki ekran görüntüsünde, dizin görünümü açık ve iki açık tarayıcıyla Visual Studio gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="68164-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![İki tarayıcıyla Eşitle örneği](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="68164-140">Projeye bağlı tarayıcıları görmek için tarayıcı bağlantısı araç çubuğu denetiminin üzerine gelin:</span><span class="sxs-lookup"><span data-stu-id="68164-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Üzerine gelme İpucu](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="68164-142">Dizin görünümünü değiştirin ve tarayıcı bağlantısı yenileme düğmesine tıkladığınızda tüm bağlı tarayıcılar güncelleştirilir:</span><span class="sxs-lookup"><span data-stu-id="68164-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![tarayıcılar-değişikliklere karşı eşitleme](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="68164-144">Tarayıcı bağlantısı, Visual Studio dışından başlamış ve uygulama URL 'sine gidebileceğiniz tarayıcılarla de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="68164-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="68164-145">Tarayıcı bağlantısı panosu</span><span class="sxs-lookup"><span data-stu-id="68164-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="68164-146">Tarayıcı bağlantısı açılan menüsünden **tarayıcı bağlantısı pano** penceresini açın ve açık tarayıcılarla bağlantıyı yönetin:</span><span class="sxs-lookup"><span data-stu-id="68164-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![Açık-browserslink-Pano](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="68164-148">Hiçbir tarayıcı bağlı değilse, **Tarayıcıda görüntüle** bağlantısını seçerek hata ayıklama olmayan bir oturum başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="68164-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-Pano-bağlantı yok](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="68164-150">Aksi halde, bağlı tarayıcılar her bir tarayıcının gösterdiği sayfanın yoluyla gösterilir:</span><span class="sxs-lookup"><span data-stu-id="68164-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-Pano-iki bağlantı](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="68164-152">Yalnızca bu tarayıcıyı yenilemek için tek bir tarayıcı adına de tıklayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="68164-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="68164-153">Tarayıcı bağlantısını etkinleştir veya devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="68164-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="68164-154">Tarayıcı bağlantısını devre dışı bıraktıktan sonra yeniden etkinleştirdiğinizde, bunları yeniden bağlamak için tarayıcıları yenilemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="68164-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="68164-155">CSS otomatik eşitlemesini etkinleştir veya devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="68164-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="68164-156">CSS otomatik eşitleme etkinleştirildiğinde, CSS dosyalarında herhangi bir değişiklik yaptığınızda bağlantılı tarayıcılar otomatik olarak yenilenir.</span><span class="sxs-lookup"><span data-stu-id="68164-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="68164-157">Nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="68164-157">How it works</span></span>

<span data-ttu-id="68164-158">Tarayıcı bağlantısı [:::no-loc(SignalR):::](xref:signalr/introduction) , Visual Studio ile tarayıcı arasında bir iletişim kanalı oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="68164-158">Browser Link uses [:::no-loc(SignalR):::](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="68164-159">Tarayıcı bağlantısı etkinleştirildiğinde, Visual Studio :::no-loc(SignalR)::: birden çok istemcinin (tarayıcının) bağlanabileceği bir sunucu gibi davranır.</span><span class="sxs-lookup"><span data-stu-id="68164-159">When Browser Link is enabled, Visual Studio acts as a :::no-loc(SignalR)::: server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="68164-160">Tarayıcı bağlantısı ayrıca ASP.NET Core isteği ardışık düzenine bir ara yazılım bileşeni kaydeder.</span><span class="sxs-lookup"><span data-stu-id="68164-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="68164-161">Bu bileşen `<script>` , sunucudan her sayfa isteğine özel başvurular çıkarır.</span><span class="sxs-lookup"><span data-stu-id="68164-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="68164-162">Tarayıcıda **Görünüm kaynağı** ' nı seçerek komut dosyası başvurularını görebilir ve etiket içeriğinin sonuna kadar kaydırma yapabilirsiniz `<body>` :</span><span class="sxs-lookup"><span data-stu-id="68164-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="68164-163">Kaynak dosyalarınız değiştirilmez.</span><span class="sxs-lookup"><span data-stu-id="68164-163">Your source files aren't modified.</span></span> <span data-ttu-id="68164-164">Ara yazılım bileşeni, betik başvurularını dinamik olarak çıkarır.</span><span class="sxs-lookup"><span data-stu-id="68164-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="68164-165">Tarayıcı tarafı kodu tüm JavaScript olduğundan, :::no-loc(SignalR)::: tarayıcı eklentisi gerekmeden desteklenen tüm tarayıcılarda çalışıyor olur.</span><span class="sxs-lookup"><span data-stu-id="68164-165">Because the browser-side code is all JavaScript, it works on all browsers that :::no-loc(SignalR)::: supports without requiring a browser plug-in.</span></span>
