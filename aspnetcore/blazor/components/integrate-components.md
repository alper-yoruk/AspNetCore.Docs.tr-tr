---
title: ASP.NET Core Razor bileşenlerini Razor Sayfalar ve MVC uygulamalarıyla tümleştirme
author: guardrex
description: Uygulamalarda bileşenler ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: 29360174ea86623491d5d8aacd4038162855bba8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399068"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="d4517-103">ASP.NET Core Razor bileşenlerini Razor Sayfalar ve MVC uygulamalarıyla tümleştirme</span><span class="sxs-lookup"><span data-stu-id="d4517-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="d4517-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="d4517-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="d4517-105">bileşenler, Razor sayfalarla ve MVC uygulamalarıyla tümleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="d4517-105"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="d4517-106">Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden alınabilir.</span><span class="sxs-lookup"><span data-stu-id="d4517-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="d4517-107">[Uygulamayı hazırladıktan](#prepare-the-app)sonra, uygulamanın gereksinimlerine bağlı olarak aşağıdaki bölümlerde yer alan kılavuzu kullanın:</span><span class="sxs-lookup"><span data-stu-id="d4517-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="d4517-108">Yönlendirilebilir bileşenler: Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenler Için.</span><span class="sxs-lookup"><span data-stu-id="d4517-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="d4517-109">Ziyaretçilerin, bir yönergesi olan bir bileşen için tarayıcılarında bir HTTP isteği yapabilmeleri gerektiğinde bu kılavuzu izleyin [`@page`](xref:mvc/views/razor#page) .</span><span class="sxs-lookup"><span data-stu-id="d4517-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="d4517-110">[Bir sayfalar uygulamasında yönlendirilebilir bileşenleri kullanma Razor](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="d4517-110">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="d4517-111">MVC uygulamasında yönlendirilebilir bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="d4517-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="d4517-112">[Bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view): doğrudan Kullanıcı isteklerinden yönlendirilemeyen bileşenler için.</span><span class="sxs-lookup"><span data-stu-id="d4517-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="d4517-113">Uygulama bileşenleri [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)ile var olan sayfalara ve görünümlere eklerken bu kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="d4517-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="d4517-114">Uygulamayı hazırlama</span><span class="sxs-lookup"><span data-stu-id="d4517-114">Prepare the app</span></span>

<span data-ttu-id="d4517-115">Mevcut Razor Sayfalar ve MVC uygulamaları, Razor bileşenleri sayfalar ve görünümler ile tümleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="d4517-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="d4517-116">Uygulamanın düzen dosyasında ( `_Layout.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="d4517-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="d4517-117">Aşağıdaki `<base>` etiketi `<head>` öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d4517-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="d4517-118">`href`Yukarıdaki örnekte yer alan değer ( *uygulama temel yolu*), uygulamanın kök URL yolunda () bulunduğunu varsayar `/` .</span><span class="sxs-lookup"><span data-stu-id="d4517-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="d4517-119">Uygulama bir alt uygulama ise, makalenin *uygulama temel yolu* bölümündeki yönergeleri izleyin <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="d4517-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="d4517-120">`_Layout.cshtml`Dosya, BIR MVC uygulamasında sayfalar uygulaması veya görünümler/paylaşılan klasöründe *Sayfalar/paylaşılan* klasöründe bulunur Razor . *Views/Shared*</span><span class="sxs-lookup"><span data-stu-id="d4517-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="d4517-121">`<script>`Kapanış etiketinden hemen önce *blazor.server.js* betiği için bir etiket ekleyin `</body>` :</span><span class="sxs-lookup"><span data-stu-id="d4517-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="d4517-122">Framework *blazor.server.js* betiği uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="d4517-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="d4517-123">Betiği uygulamaya el ile eklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d4517-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="d4517-124">`_Imports.razor`Aşağıdaki içeriğe sahip projenin kök klasörüne bir dosya ekleyin (son ad alanını `MyAppNamespace` uygulamanın ad alanına değiştirin):</span><span class="sxs-lookup"><span data-stu-id="d4517-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="d4517-125">`Startup.ConfigureServices`' De, Blazor Server hizmeti kaydedin:</span><span class="sxs-lookup"><span data-stu-id="d4517-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="d4517-126">İçinde `Startup.Configure` , Blazor hub bitiş noktasını şu şekilde ekleyin `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="d4517-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="d4517-127">Bileşenleri herhangi bir sayfa veya görünümle tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="d4517-127">Integrate components into any page or view.</span></span> <span data-ttu-id="d4517-128">Daha fazla bilgi için, [bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d4517-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="d4517-129">Bir sayfalar uygulamasında yönlendirilebilir bileşenleri kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="d4517-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="d4517-130">*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*</span><span class="sxs-lookup"><span data-stu-id="d4517-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="d4517-131">RazorSayfalar uygulamalarında yönlendirilebilir bileşenleri desteklemek için Razor :</span><span class="sxs-lookup"><span data-stu-id="d4517-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="d4517-132">[Uygulamayı hazırlama](#prepare-the-app) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="d4517-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="d4517-133">`App.razor`Aşağıdaki içeriğe sahip proje köküne bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d4517-133">Add an `App.razor` file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="d4517-134">`_Host.cshtml` `Pages` Klasöre aşağıdaki içeriğe sahip bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d4517-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="d4517-135">Bileşenler, `_Layout.cshtml` düzen için paylaşılan dosyayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="d4517-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="d4517-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır `App` :</span><span class="sxs-lookup"><span data-stu-id="d4517-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="d4517-137">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="d4517-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="d4517-138">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="d4517-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="d4517-139">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="d4517-139">Render Mode</span></span> | <span data-ttu-id="d4517-140">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d4517-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="d4517-141">`App`Bileşeni STATIK HTML olarak işler ve uygulama için bir işaret içerir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="d4517-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="d4517-142">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="d4517-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="d4517-143">Bir uygulama için işaretleyici işler Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="d4517-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="d4517-144">`App`Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="d4517-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="d4517-145">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="d4517-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="d4517-146">`App`Bileşeni STATIK HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="d4517-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="d4517-147">Bileşen etiketi Yardımcısı hakkında daha fazla bilgi için bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..</span><span class="sxs-lookup"><span data-stu-id="d4517-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="d4517-148">`_Host.cshtml`İçindeki uç nokta yapılandırmasına sayfanın düşük öncelikli bir yolunu ekleyin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d4517-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="d4517-149">Uygulamaya yönlendirilebilir bileşenler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4517-149">Add routable components to the app.</span></span> <span data-ttu-id="d4517-150">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d4517-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="d4517-151">Ad alanları hakkında daha fazla bilgi için [bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d4517-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="d4517-152">MVC uygulamasında yönlendirilebilir bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="d4517-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="d4517-153">*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*</span><span class="sxs-lookup"><span data-stu-id="d4517-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="d4517-154">RazorMVC uygulamalarında yönlendirilebilir bileşenleri desteklemek için:</span><span class="sxs-lookup"><span data-stu-id="d4517-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="d4517-155">[Uygulamayı hazırlama](#prepare-the-app) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="d4517-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="d4517-156">`App.razor`Aşağıdaki içeriğe sahip projenin köküne bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d4517-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="d4517-157">`_Host.cshtml` `Views/Home` Klasöre aşağıdaki içeriğe sahip bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d4517-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="d4517-158">Bileşenler, `_Layout.cshtml` düzen için paylaşılan dosyayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="d4517-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="d4517-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır `App` :</span><span class="sxs-lookup"><span data-stu-id="d4517-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="d4517-160">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="d4517-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="d4517-161">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="d4517-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="d4517-162">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="d4517-162">Render Mode</span></span> | <span data-ttu-id="d4517-163">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d4517-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="d4517-164">`App`Bileşeni STATIK HTML olarak işler ve uygulama için bir işaret içerir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="d4517-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="d4517-165">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="d4517-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="d4517-166">Bir uygulama için işaretleyici işler Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="d4517-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="d4517-167">`App`Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="d4517-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="d4517-168">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="d4517-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="d4517-169">`App`Bileşeni STATIK HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="d4517-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="d4517-170">Bileşen etiketi Yardımcısı hakkında daha fazla bilgi için bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..</span><span class="sxs-lookup"><span data-stu-id="d4517-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="d4517-171">Ana denetleyiciye bir eylem ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d4517-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="d4517-172">`_Host.cshtml`' Deki uç nokta yapılandırmasına görünümü döndüren denetleyici eylemi için düşük öncelikli bir yol ekleyin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d4517-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="d4517-173">Bir `Pages` klasör oluşturun ve uygulamaya yönlendirilebilir bileşenler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4517-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="d4517-174">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d4517-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="d4517-175">Ad alanları hakkında daha fazla bilgi için [bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d4517-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="d4517-176">Bir sayfadan veya görünümden bileşenleri işleme</span><span class="sxs-lookup"><span data-stu-id="d4517-176">Render components from a page or view</span></span>

<span data-ttu-id="d4517-177">*Bu bölüm, bileşenlerin Kullanıcı isteklerinden doğrudan yönlendirilemeyen sayfalara veya görünümlere bileşen eklenmesine aittir.*</span><span class="sxs-lookup"><span data-stu-id="d4517-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="d4517-178">Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)kullanın.</span><span class="sxs-lookup"><span data-stu-id="d4517-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="d4517-179">Durum bilgisi olan etkileşimli bileşenleri işle</span><span class="sxs-lookup"><span data-stu-id="d4517-179">Render stateful interactive components</span></span>

<span data-ttu-id="d4517-180">Durum bilgisi olan etkileşimli bileşenler, bir Razor sayfaya veya görünüme eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="d4517-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="d4517-181">Sayfa veya görünüm şunları işler:</span><span class="sxs-lookup"><span data-stu-id="d4517-181">When the page or view renders:</span></span>

* <span data-ttu-id="d4517-182">Bileşen sayfa veya görünümle birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d4517-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="d4517-183">Prerendering için kullanılan ilk bileşen durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="d4517-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="d4517-184">Bağlantı kurulduunda yeni bileşen durumu oluşturulur SignalR .</span><span class="sxs-lookup"><span data-stu-id="d4517-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="d4517-185">Aşağıdaki Razor sayfa bir bileşeni işler `Counter` :</span><span class="sxs-lookup"><span data-stu-id="d4517-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="d4517-186">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d4517-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="d4517-187">Etkileşimsiz bileşenleri işle</span><span class="sxs-lookup"><span data-stu-id="d4517-187">Render noninteractive components</span></span>

<span data-ttu-id="d4517-188">Aşağıdaki Razor sayfada, `Counter` bileşen bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="d4517-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="d4517-189">Bileşen statik olarak işlendiğinden, bileşen etkileşimli değildir:</span><span class="sxs-lookup"><span data-stu-id="d4517-189">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="d4517-190">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d4517-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="d4517-191">Bileşen ad alanları</span><span class="sxs-lookup"><span data-stu-id="d4517-191">Component namespaces</span></span>

<span data-ttu-id="d4517-192">Uygulamanın bileşenlerini tutmak için özel bir klasör kullanırken, klasörü/görünümü ya da dosyaya veya dosyayı temsil eden ad alanını ekleyin `_ViewImports.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="d4517-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="d4517-193">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="d4517-193">In the following example:</span></span>

* <span data-ttu-id="d4517-194">`MyAppNamespace`Uygulamanın ad alanına geçin.</span><span class="sxs-lookup"><span data-stu-id="d4517-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="d4517-195">Bileşenleri tutmak için *bileşen* adlı bir klasör kullanılmazsa, bileşenlerin bulunduğu klasöre geçin `Components` .</span><span class="sxs-lookup"><span data-stu-id="d4517-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="d4517-196">`_ViewImports.cshtml`Dosya, `Pages` bir Razor Sayfalar uygulamasının KLASÖRÜNDE veya `Views` bir MVC uygulamasının klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="d4517-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="d4517-197">Daha fazla bilgi için bkz. <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="d4517-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>
