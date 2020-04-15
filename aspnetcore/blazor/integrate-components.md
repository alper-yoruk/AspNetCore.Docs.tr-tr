---
title: Core Razor bileşenleriASP.NET Razor Pages ve MVC uygulamalarına entegre edin
author: guardrex
description: Uygulamalardaki Blazor bileşenler ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: c242fbef70d289929d5c005abc0aa431619862b3
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383964"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="cc871-103">Core Razor bileşenleriASP.NET Razor Pages ve MVC uygulamalarına entegre edin</span><span class="sxs-lookup"><span data-stu-id="cc871-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="cc871-104">Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="cc871-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="cc871-105">Razor bileşenleri Razor Pages ve MVC uygulamalarına entegre edilebilir.</span><span class="sxs-lookup"><span data-stu-id="cc871-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="cc871-106">Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="cc871-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="cc871-107">[Uygulamayı hazırladıktan](#prepare-the-app)sonra, uygulamanın gereksinimlerine bağlı olarak aşağıdaki bölümlerdeki kılavuzu kullanın:</span><span class="sxs-lookup"><span data-stu-id="cc871-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="cc871-108">Routable &ndash; bileşenleri Kullanıcı isteklerinden doğrudan routable bileşenleri için.</span><span class="sxs-lookup"><span data-stu-id="cc871-108">Routable components &ndash; For components that are directly routable from user requests.</span></span> <span data-ttu-id="cc871-109">Ziyaretçilerin tarayıcılarında bir [`@page`](xref:mvc/views/razor#page) yönergeye sahip bir bileşen için HTTP isteğinde bulunabilmeleri gerektiğinde bu kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="cc871-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="cc871-110">Razor Pages uygulamasında routable bileşenlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="cc871-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="cc871-111">Bir MVC uygulamasında routable bileşenleri ni kullanma</span><span class="sxs-lookup"><span data-stu-id="cc871-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="cc871-112">[Bileşenleri bir sayfadan veya görünümden,](#render-components-from-a-page-or-view) &ndash; kullanıcı isteklerinden doğrudan kaynak sayılabilen bileşenler için görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="cc871-112">[Render components from a page or view](#render-components-from-a-page-or-view) &ndash; For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="cc871-113">[Bileşen Etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)ile bileşenleri varolan sayfalara ve görünümlere yerleştirdiğinde bu kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="cc871-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="cc871-114">Uygulamayı hazırlama</span><span class="sxs-lookup"><span data-stu-id="cc871-114">Prepare the app</span></span>

<span data-ttu-id="cc871-115">Mevcut bir Razor Pages veya MVC uygulaması, Razor bileşenlerini sayfalara ve görünümlere entegre edebilir:</span><span class="sxs-lookup"><span data-stu-id="cc871-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="cc871-116">Uygulamanın düzen dosyasında (*_Layout.cshtml):*</span><span class="sxs-lookup"><span data-stu-id="cc871-116">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="cc871-117">`<head>` Öğeye aşağıdaki `<base>` etiketi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cc871-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="cc871-118">Yukarıdaki `href` örnekteki değer *(uygulama temel yolu)* uygulamanın kök URL yolunda bulunduğunu`/`varsayar ( ).</span><span class="sxs-lookup"><span data-stu-id="cc871-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="cc871-119">Uygulama bir alt uygulamaysa, <xref:host-and-deploy/blazor/index#app-base-path> makalenin Uygulama temel *yolu* bölümündeki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="cc871-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="cc871-120">*_Layout.cshtml* dosyası, Bir Jilet Sayfaları uygulamasındaki *Sayfalar/Paylaşılan* klasöründe veya Bir MVC *uygulamasındaki Görünümler/Paylaşılan* klasörde bulunur.</span><span class="sxs-lookup"><span data-stu-id="cc871-120">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="cc871-121">Kapanış `</body>` `<script>` etiketinden hemen önce *blazor.server.js* komut dosyası için bir etiket ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cc871-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="cc871-122">Çerçeve uygulamaya *blazor.server.js* komut dosyası ekler.</span><span class="sxs-lookup"><span data-stu-id="cc871-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="cc871-123">Komut dosyasını uygulamaya el ile eklemenize gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="cc871-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="cc871-124">Aşağıdaki içeriği içeren projenin kök klasörüne bir *_Imports.razor* dosyası ekleyin (son ad alanını, `MyAppNamespace`uygulamanın ad alanına değiştirin):</span><span class="sxs-lookup"><span data-stu-id="cc871-124">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="cc871-125">Blazor `Startup.ConfigureServices`Server hizmetini kaydedin:</span><span class="sxs-lookup"><span data-stu-id="cc871-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="cc871-126">In `Startup.Configure`, Blazor Hub bitiş `app.UseEndpoints`noktasını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cc871-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="cc871-127">Bileşenleri herhangi bir sayfaya veya görünüme tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="cc871-127">Integrate components into any page or view.</span></span> <span data-ttu-id="cc871-128">Daha fazla bilgi için [bir sayfa veya görünüm bölümünden Render bileşenlerine](#render-components-from-a-page-or-view) bakın.</span><span class="sxs-lookup"><span data-stu-id="cc871-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="cc871-129">Razor Pages uygulamasında routable bileşenlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="cc871-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="cc871-130">*Bu bölüm, kullanıcı isteklerinden doğrudan routable bileşenleri ekleme ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="cc871-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="cc871-131">Razor Pages uygulamalarında routable Razor bileşenlerini desteklemek için:</span><span class="sxs-lookup"><span data-stu-id="cc871-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="cc871-132">[Uygulamayı Hazırla](#prepare-the-app) bölümündeki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="cc871-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="cc871-133">Proje köküne aşağıdaki içerikle bir *App.razor* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cc871-133">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="cc871-134">Aşağıdaki içeriği içeren *Sayfalar* klasörüne *bir _Host.cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cc871-134">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="cc871-135">Bileşenler, düzenleri için paylaşılan *_Layout.cshtml* dosyasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="cc871-135">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="cc871-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin: `App`</span><span class="sxs-lookup"><span data-stu-id="cc871-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="cc871-137">Sayfaya önceden işlenir.</span><span class="sxs-lookup"><span data-stu-id="cc871-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="cc871-138">Sayfada statik HTML olarak işlenir veya kullanıcı aracısından bir Blazor uygulamasını önyükleme için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="cc871-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="cc871-139">Render Modu</span><span class="sxs-lookup"><span data-stu-id="cc871-139">Render Mode</span></span> | <span data-ttu-id="cc871-140">Açıklama</span><span class="sxs-lookup"><span data-stu-id="cc871-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cc871-141">`App` Bileşeni statik HTML'ye dönüştürür ve Blazor Server uygulaması için bir işaretçi içerir.</span><span class="sxs-lookup"><span data-stu-id="cc871-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc871-142">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamasını önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc871-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cc871-143">Blazor Server uygulaması için bir işaretçi işler.</span><span class="sxs-lookup"><span data-stu-id="cc871-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc871-144">`App` Bileşenden gelen çıktı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="cc871-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="cc871-145">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamasını önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc871-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cc871-146">`App` Bileşeni statik HTML'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="cc871-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="cc871-147">Bileşen Etiket Yardımcısı hakkında daha fazla <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="cc871-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="cc871-148">*_Host.cshtml* sayfası için bitiş noktası yapılandırmasına `Startup.Configure`düşük öncelikli bir rota ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cc871-148">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="cc871-149">Uygulamaya routable bileşenleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cc871-149">Add routable components to the app.</span></span> <span data-ttu-id="cc871-150">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="cc871-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="cc871-151">Ad alanları hakkında daha fazla bilgi için [Bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="cc871-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="cc871-152">Bir MVC uygulamasında routable bileşenleri ni kullanma</span><span class="sxs-lookup"><span data-stu-id="cc871-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="cc871-153">*Bu bölüm, kullanıcı isteklerinden doğrudan routable bileşenleri ekleme ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="cc871-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="cc871-154">MVC uygulamalarında routable Razor bileşenlerini desteklemek için:</span><span class="sxs-lookup"><span data-stu-id="cc871-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="cc871-155">[Uygulamayı Hazırla](#prepare-the-app) bölümündeki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="cc871-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="cc871-156">Aşağıdaki içerikle projenin köküne bir *App.razor* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cc871-156">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="cc871-157">*Görünümler/Ev* klasörüne aşağıdaki içeriği içeren bir *_Host.cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cc871-157">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="cc871-158">Bileşenler, düzenleri için paylaşılan *_Layout.cshtml* dosyasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="cc871-158">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="cc871-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin: `App`</span><span class="sxs-lookup"><span data-stu-id="cc871-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="cc871-160">Sayfaya önceden işlenir.</span><span class="sxs-lookup"><span data-stu-id="cc871-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="cc871-161">Sayfada statik HTML olarak işlenir veya kullanıcı aracısından bir Blazor uygulamasını önyükleme için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="cc871-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="cc871-162">Render Modu</span><span class="sxs-lookup"><span data-stu-id="cc871-162">Render Mode</span></span> | <span data-ttu-id="cc871-163">Açıklama</span><span class="sxs-lookup"><span data-stu-id="cc871-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cc871-164">`App` Bileşeni statik HTML'ye dönüştürür ve sunucu Blazor uygulaması için bir işaretçi içerir.</span><span class="sxs-lookup"><span data-stu-id="cc871-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc871-165">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc871-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cc871-166">Blazor Sunucu uygulaması için işaretleyici işler.</span><span class="sxs-lookup"><span data-stu-id="cc871-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc871-167">`App` Bileşenden gelen çıktı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="cc871-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="cc871-168">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc871-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cc871-169">`App` Bileşeni statik HTML'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="cc871-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="cc871-170">Bileşen Etiket Yardımcısı hakkında daha fazla <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="cc871-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="cc871-171">Giriş denetleyicisine bir eylem ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cc871-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="cc871-172">*_Host.cshtml* görünümünü bitiş noktası yapılandırmasına döndüren denetleyici eylemi için `Startup.Configure`düşük öncelikli bir rota ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cc871-172">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="cc871-173">Bir *Sayfalar* klasörü oluşturun ve uygulamaya routable bileşenleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cc871-173">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="cc871-174">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="cc871-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="cc871-175">Ad alanları hakkında daha fazla bilgi için [Bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="cc871-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="cc871-176">Bileşenleri bir sayfaveya görünümden oluşturma</span><span class="sxs-lookup"><span data-stu-id="cc871-176">Render components from a page or view</span></span>

<span data-ttu-id="cc871-177">*Bu bölüm, bileşenlerin kullanıcı isteklerinden doğrudan çıkarılabildiği sayfalara veya görünümlere bileşenler eklemekle ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="cc871-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="cc871-178">Bir bileşeni bir sayfa veya görünümden işlemek için [Bileşen Etiket Yardımcısı'nı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)kullanın.</span><span class="sxs-lookup"><span data-stu-id="cc871-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="cc871-179">Bileşenlerin nasıl işlendiği, bileşen durumu ve `Component` Etiket Yardımcısı hakkında daha fazla bilgi için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="cc871-179">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>

## <a name="component-namespaces"></a><span data-ttu-id="cc871-180">Bileşen ad alanları</span><span class="sxs-lookup"><span data-stu-id="cc871-180">Component namespaces</span></span>

<span data-ttu-id="cc871-181">Uygulamanın bileşenlerini tutmak için özel bir klasör kullanırken, klasörü temsil eden ad alanını sayfaya/görünüme veya *_ViewImports.cshtml* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cc871-181">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="cc871-182">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="cc871-182">In the following example:</span></span>

* <span data-ttu-id="cc871-183">Uygulamanın ad alanına değiştirin. `MyAppNamespace`</span><span class="sxs-lookup"><span data-stu-id="cc871-183">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="cc871-184">Bileşenleri tutmak için *Bileşenler* adlı bir klasör kullanılmazsa, bileşenlerin bulunduğu klasöre değiştirin. `Components`</span><span class="sxs-lookup"><span data-stu-id="cc871-184">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="cc871-185">*_ViewImports.cshtml* dosyası, Bir Razor Pages uygulamasının *Sayfalar* klasöründe veya bir MVC uygulamasının *Görünümler* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="cc871-185">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="cc871-186">Daha fazla bilgi için bkz. <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="cc871-186">For more information, see <xref:blazor/components#import-components>.</span></span>
