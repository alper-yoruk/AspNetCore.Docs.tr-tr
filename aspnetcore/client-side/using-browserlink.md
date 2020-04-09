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
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="4bdd7-103">ASP.NET Core Tarayıcı Bağlantısı</span><span class="sxs-lookup"><span data-stu-id="4bdd7-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="4bdd7-104">Yazar: [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), ve [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="4bdd7-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="4bdd7-105">Browser Link bir Visual Studio özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="4bdd7-106">Geliştirme ortamı ile bir veya daha fazla web tarayıcısı arasında bir iletişim kanalı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="4bdd7-107">Tarayıcı Bağlantısı'nı kullanarak web uygulamanızı aynı anda birden fazla tarayıcıda yenileyebilirsiniz, bu da tarayıcılar arası testler için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="4bdd7-108">Tarayıcı Bağlantısı kurulumu</span><span class="sxs-lookup"><span data-stu-id="4bdd7-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4bdd7-109">[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketini projenize ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="4bdd7-110">Core Razor Pages veya MVC projeleri ASP.NET için, aynı zamanda Razor (*.cshtml*) dosyalarının çalışma zamanı derlemesini de <xref:mvc/views/view-compilation>sağlar.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="4bdd7-111">Jilet sözdizimi değişiklikleri yalnızca çalışma zamanı derlemesi etkinleştirildiğinde uygulanır.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="4bdd7-112">core 2.1ASP.NET bir ASP.NET Core 2.0 projesini dönüştürüp [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)geçerken, Tarayıcı Bağlantısı işlevi için [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="4bdd7-113">Core 2.1 proje şablonlarının `Microsoft.AspNetCore.App` ASP.NET varsayılan olarak meta paketi kullanır.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="4bdd7-114">ASP.NET Core 2.0 **Web Application**, **Boş**ve **Web API** proje şablonları [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)kullanın , [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)için bir paket referans içerir.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="4bdd7-115">Bu nedenle, `Microsoft.AspNetCore.All` metapaketi kullanmak, Tarayıcı Bağlantısını kullanıma açmak için başka bir işlem gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="4bdd7-116">ASP.NET Core 1.x **Web Application** proje şablonu [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) paketi için bir paket başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="4bdd7-117">Diğer proje türleri için bir paket `Microsoft.VisualStudio.Web.BrowserLink`başvurusu eklemenizi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="4bdd7-118">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4bdd7-118">Configuration</span></span>

<span data-ttu-id="4bdd7-119">`Startup.Configure` Yöntemde arama: `UseBrowserLink`</span><span class="sxs-lookup"><span data-stu-id="4bdd7-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="4bdd7-120">Arama `UseBrowserLink` genellikle yalnızca Geliştirme `if` ortamında Tarayıcı Bağlantısı sağlayan bir blok içinde yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="4bdd7-121">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="4bdd7-122">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="4bdd7-123">Tarayıcı Bağlantısı nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="4bdd7-123">How to use Browser Link</span></span>

<span data-ttu-id="4bdd7-124">ASP.NET Core projeniz açık olduğunda, Visual Studio **Hata Ayıklama Hedef** araç çubuğu denetiminin yanındaki Tarayıcı Bağlantısı araç çubuğu denetimini gösterir:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Tarayıcı Bağlantısı açılır menüsü](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="4bdd7-126">Browser Link araç çubuğu denetiminden şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="4bdd7-127">Web uygulamasını aynı anda birden fazla tarayıcıda yenileyin.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="4bdd7-128">Tarayıcı **Bağlantı Panosunu**açın.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="4bdd7-129">**Tarayıcı Bağlantısını**etkinleştirme veya devre dışı</span><span class="sxs-lookup"><span data-stu-id="4bdd7-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="4bdd7-130">Not: Visual Studio'da Tarayıcı Bağlantısı varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="4bdd7-131">[CSS Otomatik Eşitleme'yi](#enable-or-disable-css-auto-sync)etkinleştirme veya devre dışı.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="4bdd7-132">Web uygulamasını aynı anda birden fazla tarayıcıda yenileyin</span><span class="sxs-lookup"><span data-stu-id="4bdd7-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="4bdd7-133">Projeyi başlatırken başlatılabilmek için tek bir web tarayıcısı seçmek için **Hata Ayıklama Hedef** araç çubuğu denetiminde açılır menüyü kullanın:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 açılır menü](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="4bdd7-135">Aynı anda birden çok tarayıcı açmak için, aynı açılır yerden **Gözat'ı...** seçin.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="4bdd7-136">İstediğiniz tarayıcıları seçmek için <kbd>Ctrl</kbd> tuşuna basılı tutun ve ardından **Gözat'ı**tıklatın:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Aynı anda birçok tarayıcı açın](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="4bdd7-138">Aşağıdaki ekran görüntüsü, Index görünümü açık ve iki açık tarayıcısı olan Visual Studio'yu gösterir:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![İki tarayıcı örneğiyle eşitleme](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="4bdd7-140">Projeye bağlı tarayıcıları görmek için Tarayıcı Bağlantısı araç çubuğu denetiminin üzerine tıklayın:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Hover ucu](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="4bdd7-142">Dizin görünümünü değiştirin ve Tarayıcı Bağlantısı yenileme düğmesini tıklattığınızda bağlı tüm tarayıcılar güncelleştirilir:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![tarayıcılar-eşitleme-değişiklikler](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="4bdd7-144">Tarayıcı Bağlantısı ayrıca Visual Studio'nun dışından başlattığınız ve uygulama URL'sine gidin tarayıcılarla da çalışır.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="4bdd7-145">Tarayıcı Bağlantı Panosu</span><span class="sxs-lookup"><span data-stu-id="4bdd7-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="4bdd7-146">Açık tarayıcılarla bağlantıyı yönetmek için Tarayıcı Bağlantısı açılır menüsünden **Tarayıcı Bağlantısı Panosu** penceresini açın:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![açık tarayıcılarbağlantı panosu](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="4bdd7-148">Tarayıcı bağlı değilse, **Tarayıcıda Görüntüle** bağlantısını seçerek hata ayıklamayan bir oturum başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-pano-bağlantı yok](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="4bdd7-150">Aksi takdirde, bağlı tarayıcılar her tarayıcının gösterdiği sayfaya giden yol ile gösterilir:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-pano-iki bağlantı](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="4bdd7-152">Ayrıca, yalnızca tarayıcıyı yenilemek için tek bir tarayıcı adını tıklatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="4bdd7-153">Tarayıcı Bağlantısını etkinleştirme veya devre dışı</span><span class="sxs-lookup"><span data-stu-id="4bdd7-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="4bdd7-154">Tarayıcı Bağlantısını devre dışı ettikten sonra yeniden etkinleştirdiğinizde, tarayıcıları yeniden bağlamak için tarayıcıları yenilemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="4bdd7-155">CSS Otomatik Eşitleme'yi etkinleştirme veya devre dışı</span><span class="sxs-lookup"><span data-stu-id="4bdd7-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="4bdd7-156">CSS Otomatik Eşitleme etkinleştirildiğinde, CSS dosyalarında herhangi bir değişiklik yaptığınızda bağlı tarayıcılar otomatik olarak yenilenir.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="4bdd7-157">Nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="4bdd7-157">How it works</span></span>

<span data-ttu-id="4bdd7-158">Browser Link, Visual Studio ile tarayıcı arasında bir iletişim kanalı oluşturmak için kullanır. [SignalR](xref:signalr/introduction)</span><span class="sxs-lookup"><span data-stu-id="4bdd7-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="4bdd7-159">Tarayıcı Bağlantısı etkinleştirildiğinde, Visual Studio SignalR birden çok istemcinin (tarayıcıların) bağlanabileceği bir sunucu gibi davranır.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="4bdd7-160">Browser Link ayrıca ASP.NET Core istek ardışık alanında bir ara yazılım bileşeni kaydeder.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="4bdd7-161">Bu bileşen, `<script>` sunucudan gelen her sayfa isteğine özel başvurular enjekte eder.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="4bdd7-162">Tarayıcıda Kaynak Görüntüle'yi **View source** seçerek ve `<body>` etiket içeriğinin sonuna kaydırarak komut dosyası başvurularını görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="4bdd7-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="4bdd7-163">Kaynak dosyalarınız değiştirilmedi.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-163">Your source files aren't modified.</span></span> <span data-ttu-id="4bdd7-164">Ara yazılım bileşeni, komut dosyası başvurularını dinamik olarak enjekte eder.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="4bdd7-165">Tarayıcı tarafındaki kodun tümü JavaScript olduğundan, tarayıcı eklentisi gerektirmeden SignalR destekleyen tüm tarayıcılarda çalışır.</span><span class="sxs-lookup"><span data-stu-id="4bdd7-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
