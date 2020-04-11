---
title: Core Razor bileşenleriASP.NET Razor Pages ve MVC uygulamalarına entegre edin
author: guardrex
description: Uygulamalardaki Blazor bileşenler ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 6efa84c550a4605bde5e1f2bca4f2d1aa4a2667b
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123359"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="ea107-103">Core Razor bileşenleriASP.NET Razor Pages ve MVC uygulamalarına entegre edin</span><span class="sxs-lookup"><span data-stu-id="ea107-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="ea107-104">Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ea107-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ea107-105">Razor bileşenleri Razor Pages ve MVC uygulamalarına entegre edilebilir.</span><span class="sxs-lookup"><span data-stu-id="ea107-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="ea107-106">Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="ea107-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="ea107-107">Uygulamayı sayfa ve görünümlerde bileşenleri kullanacak şekilde hazırlama</span><span class="sxs-lookup"><span data-stu-id="ea107-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="ea107-108">Mevcut bir Razor Pages veya MVC uygulaması, Razor bileşenlerini sayfalara ve görünümlere entegre edebilir:</span><span class="sxs-lookup"><span data-stu-id="ea107-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="ea107-109">Uygulamanın düzen dosyasında (*_Layout.cshtml):*</span><span class="sxs-lookup"><span data-stu-id="ea107-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="ea107-110">`<head>` Öğeye aşağıdaki `<base>` etiketi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea107-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="ea107-111">Yukarıdaki `href` örnekteki değer *(uygulama temel yolu)* uygulamanın kök URL yolunda bulunduğunu`/`varsayar ( ).</span><span class="sxs-lookup"><span data-stu-id="ea107-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="ea107-112">Uygulama bir alt uygulamaysa, <xref:host-and-deploy/blazor/index#app-base-path> makalenin Uygulama temel *yolu* bölümündeki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="ea107-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="ea107-113">*_Layout.cshtml* dosyası, Bir Jilet Sayfaları uygulamasındaki *Sayfalar/Paylaşılan* klasöründe veya Bir MVC *uygulamasındaki Görünümler/Paylaşılan* klasörde bulunur.</span><span class="sxs-lookup"><span data-stu-id="ea107-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="ea107-114">Kapanış `</body>` `<script>` etiketinden hemen önce *blazor.server.js* komut dosyası için bir etiket ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea107-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="ea107-115">Çerçeve uygulamaya *blazor.server.js* komut dosyası ekler.</span><span class="sxs-lookup"><span data-stu-id="ea107-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="ea107-116">Komut dosyasını uygulamaya el ile eklemenize gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="ea107-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="ea107-117">Aşağıdaki içeriği içeren projenin kök klasörüne bir *_Imports.razor* dosyası ekleyin (son ad alanını, `MyAppNamespace`uygulamanın ad alanına değiştirin):</span><span class="sxs-lookup"><span data-stu-id="ea107-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="ea107-118">Blazor `Startup.ConfigureServices`Server hizmetini kaydedin:</span><span class="sxs-lookup"><span data-stu-id="ea107-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="ea107-119">In `Startup.Configure`, Blazor Hub bitiş `app.UseEndpoints`noktasını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea107-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="ea107-120">Bileşenleri herhangi bir sayfaya veya görünüme tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="ea107-120">Integrate components into any page or view.</span></span> <span data-ttu-id="ea107-121">Daha fazla bilgi için [bir sayfa veya görünüm bölümünden Render bileşenlerine](#render-components-from-a-page-or-view) bakın.</span><span class="sxs-lookup"><span data-stu-id="ea107-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="ea107-122">Razor Pages uygulamasında routable bileşenlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="ea107-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="ea107-123">*Bu bölüm, kullanıcı isteklerinden doğrudan routable bileşenleri ekleme ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="ea107-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="ea107-124">Razor Pages uygulamalarında routable Razor bileşenlerini desteklemek için:</span><span class="sxs-lookup"><span data-stu-id="ea107-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="ea107-125">Sayfaları ve [görünümleri bölümündebileşenleri kullanmak için uygulamayı Hazırla'daki](#prepare-the-app-to-use-components-in-pages-and-views) kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="ea107-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="ea107-126">Proje köküne aşağıdaki içerikle bir *App.razor* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea107-126">Add an *App.razor* file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="ea107-127">Aşağıdaki içeriği içeren *Sayfalar* klasörüne *bir _Host.cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea107-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="ea107-128">Bileşenler, düzenleri için paylaşılan *_Layout.cshtml* dosyasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="ea107-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="ea107-129"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin: `App`</span><span class="sxs-lookup"><span data-stu-id="ea107-129"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="ea107-130">Sayfaya önceden işlenir.</span><span class="sxs-lookup"><span data-stu-id="ea107-130">Is prerendered into the page.</span></span>
   * <span data-ttu-id="ea107-131">Sayfada statik HTML olarak işlenir veya kullanıcı aracısından bir Blazor uygulamasını önyükleme için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="ea107-131">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="ea107-132">Render Modu</span><span class="sxs-lookup"><span data-stu-id="ea107-132">Render Mode</span></span> | <span data-ttu-id="ea107-133">Açıklama</span><span class="sxs-lookup"><span data-stu-id="ea107-133">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="ea107-134">`App` Bileşeni statik HTML'ye dönüştürür ve sunucu Blazor uygulaması için bir işaretçi içerir.</span><span class="sxs-lookup"><span data-stu-id="ea107-134">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="ea107-135">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ea107-135">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="ea107-136">Blazor Sunucu uygulaması için işaretleyici işler.</span><span class="sxs-lookup"><span data-stu-id="ea107-136">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="ea107-137">`App` Bileşenden gelen çıktı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="ea107-137">Output from the `App` component isn't included.</span></span> <span data-ttu-id="ea107-138">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ea107-138">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="ea107-139">`App` Bileşeni statik HTML'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ea107-139">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="ea107-140">Bileşen Etiket Yardımcısı hakkında daha fazla <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="ea107-140">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="ea107-141">*_Host.cshtml* sayfası için bitiş noktası yapılandırmasına `Startup.Configure`düşük öncelikli bir rota ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea107-141">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="ea107-142">Uygulamaya routable bileşenleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ea107-142">Add routable components to the app.</span></span> <span data-ttu-id="ea107-143">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ea107-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="ea107-144">Ad alanları hakkında daha fazla bilgi için [Bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ea107-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="ea107-145">Bir MVC uygulamasında routable bileşenleri ni kullanma</span><span class="sxs-lookup"><span data-stu-id="ea107-145">Use routable components in an MVC app</span></span>

<span data-ttu-id="ea107-146">*Bu bölüm, kullanıcı isteklerinden doğrudan routable bileşenleri ekleme ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="ea107-146">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="ea107-147">MVC uygulamalarında routable Razor bileşenlerini desteklemek için:</span><span class="sxs-lookup"><span data-stu-id="ea107-147">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="ea107-148">Sayfaları ve [görünümleri bölümündebileşenleri kullanmak için uygulamayı Hazırla'daki](#prepare-the-app-to-use-components-in-pages-and-views) kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="ea107-148">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="ea107-149">Aşağıdaki içerikle projenin köküne bir *App.razor* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea107-149">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="ea107-150">*Görünümler/Ev* klasörüne aşağıdaki içeriği içeren bir *_Host.cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea107-150">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="ea107-151">Bileşenler, düzenleri için paylaşılan *_Layout.cshtml* dosyasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="ea107-151">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="ea107-152">Giriş denetleyicisine bir eylem ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea107-152">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="ea107-153">*_Host.cshtml* görünümünü bitiş noktası yapılandırmasına döndüren denetleyici eylemi için `Startup.Configure`düşük öncelikli bir rota ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea107-153">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="ea107-154">Bir *Sayfalar* klasörü oluşturun ve uygulamaya routable bileşenleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ea107-154">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="ea107-155">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ea107-155">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="ea107-156">Ad alanları hakkında daha fazla bilgi için [Bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ea107-156">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="ea107-157">Bileşen ad alanları</span><span class="sxs-lookup"><span data-stu-id="ea107-157">Component namespaces</span></span>

<span data-ttu-id="ea107-158">Uygulamanın bileşenlerini tutmak için özel bir klasör kullanırken, klasörü temsil eden ad alanını sayfaya/görünüme veya *_ViewImports.cshtml* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ea107-158">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="ea107-159">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="ea107-159">In the following example:</span></span>

* <span data-ttu-id="ea107-160">Uygulamanın ad alanına değiştirin. `MyAppNamespace`</span><span class="sxs-lookup"><span data-stu-id="ea107-160">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="ea107-161">Bileşenleri tutmak için *Bileşenler* adlı bir klasör kullanılmazsa, bileşenlerin bulunduğu klasöre değiştirin. `Components`</span><span class="sxs-lookup"><span data-stu-id="ea107-161">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="ea107-162">*_ViewImports.cshtml* dosyası, Bir Razor Pages uygulamasının *Sayfalar* klasöründe veya bir MVC uygulamasının *Görünümler* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="ea107-162">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="ea107-163">Daha fazla bilgi için bkz. <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="ea107-163">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="ea107-164">Bileşenleri bir sayfaveya görünümden oluşturma</span><span class="sxs-lookup"><span data-stu-id="ea107-164">Render components from a page or view</span></span>

<span data-ttu-id="ea107-165">*Bu bölüm, bileşenlerin kullanıcı isteklerinden doğrudan çıkarılabildiği sayfalara veya görünümlere bileşenler eklemekle ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="ea107-165">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="ea107-166">Bir bileşeni bir sayfa veya görünümden işlemek için [Bileşen Etiket Yardımcısı'nı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)kullanın.</span><span class="sxs-lookup"><span data-stu-id="ea107-166">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="ea107-167">Bileşenlerin nasıl işlendiği, bileşen durumu ve `Component` Etiket Yardımcısı hakkında daha fazla bilgi için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="ea107-167">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
