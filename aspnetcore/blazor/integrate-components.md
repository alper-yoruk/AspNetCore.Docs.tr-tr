---
<span data-ttu-id="e9013-101">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-103">'Blazor'</span></span>
- <span data-ttu-id="e9013-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-104">'Identity'</span></span>
- <span data-ttu-id="e9013-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-106">'Razor'</span></span>
- <span data-ttu-id="e9013-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="e9013-108">ASP.NET Core Razor bileşenlerini Razor Sayfalar ve MVC uygulamalarıyla tümleştirme</span><span class="sxs-lookup"><span data-stu-id="e9013-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="e9013-109">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="e9013-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="e9013-110">bileşenler, Razor sayfalarla ve MVC uygulamalarıyla tümleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e9013-110"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="e9013-111">Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden alınabilir.</span><span class="sxs-lookup"><span data-stu-id="e9013-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="e9013-112">[Uygulamayı hazırladıktan](#prepare-the-app)sonra, uygulamanın gereksinimlerine bağlı olarak aşağıdaki bölümlerde yer alan kılavuzu kullanın:</span><span class="sxs-lookup"><span data-stu-id="e9013-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="e9013-113">Yönlendirilebilir bileşenler: Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenler Için.</span><span class="sxs-lookup"><span data-stu-id="e9013-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="e9013-114">Ziyaretçilerin, bir yönergesi olan bir bileşen için tarayıcılarında bir HTTP isteği yapabilmeleri gerektiğinde bu kılavuzu izleyin [`@page`](xref:mvc/views/razor#page) .</span><span class="sxs-lookup"><span data-stu-id="e9013-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="e9013-115">[Bir sayfalar uygulamasında yönlendirilebilir bileşenleri kullanma Razor](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="e9013-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="e9013-116">MVC uygulamasında yönlendirilebilir bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="e9013-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="e9013-117">[Bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view): doğrudan Kullanıcı isteklerinden yönlendirilemeyen bileşenler için.</span><span class="sxs-lookup"><span data-stu-id="e9013-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="e9013-118">Uygulama bileşenleri [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)ile var olan sayfalara ve görünümlere eklerken bu kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="e9013-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="e9013-119">Uygulamayı hazırlama</span><span class="sxs-lookup"><span data-stu-id="e9013-119">Prepare the app</span></span>

<span data-ttu-id="e9013-120">Mevcut Razor Sayfalar ve MVC uygulamaları, Razor bileşenleri sayfalar ve görünümler ile tümleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="e9013-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="e9013-121">Uygulamanın düzen dosyasında (*_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="e9013-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="e9013-122">Aşağıdaki `<base>` etiketi `<head>` öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9013-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="e9013-123">`href`Yukarıdaki örnekte yer alan değer ( *uygulama temel yolu*), uygulamanın kök URL yolunda () bulunduğunu varsayar `/` .</span><span class="sxs-lookup"><span data-stu-id="e9013-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="e9013-124">Uygulama bir alt uygulama ise, makalenin *uygulama temel yolu* bölümündeki yönergeleri izleyin <xref:host-and-deploy/blazor/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="e9013-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="e9013-125">*_Layout. cshtml* dosyası, bir MVC uygulamasında sayfalar uygulaması veya görünümler/paylaşılan klasöründe *Sayfalar/paylaşılan* klasöründe bulunur Razor . *Views/Shared*</span><span class="sxs-lookup"><span data-stu-id="e9013-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="e9013-126">`<script>`Kapanış etiketinden hemen önce *blazor. Server. js* betiği için bir etiket ekleyin `</body>` :</span><span class="sxs-lookup"><span data-stu-id="e9013-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="e9013-127">Framework, *blazor. Server. js* betiğini uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="e9013-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="e9013-128">Betiği uygulamaya el ile eklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e9013-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="e9013-129">Aşağıdaki içerikle projenin kök klasörüne bir *_Imports. Razor* dosyası ekleyin (son ad alanını `MyAppNamespace` uygulamanın ad alanına değiştirin):</span><span class="sxs-lookup"><span data-stu-id="e9013-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="e9013-130">`Startup.ConfigureServices`' De, Blazor sunucu hizmetini kaydedin:</span><span class="sxs-lookup"><span data-stu-id="e9013-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="e9013-131">İçinde `Startup.Configure` , Blazor hub bitiş noktasını şu şekilde ekleyin `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="e9013-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="e9013-132">Bileşenleri herhangi bir sayfa veya görünümle tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="e9013-132">Integrate components into any page or view.</span></span> <span data-ttu-id="e9013-133">Daha fazla bilgi için, [bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e9013-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="e9013-134">Bir sayfalar uygulamasında yönlendirilebilir bileşenleri kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="e9013-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="e9013-135">*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*</span><span class="sxs-lookup"><span data-stu-id="e9013-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="e9013-136">RazorSayfalar uygulamalarında yönlendirilebilir bileşenleri desteklemek için Razor :</span><span class="sxs-lookup"><span data-stu-id="e9013-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="e9013-137">[Uygulamayı hazırlama](#prepare-the-app) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="e9013-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="e9013-138">Aşağıdaki içeriğe sahip proje köküne bir *app. Razor* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9013-138">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="e9013-139">*Sayfalar* klasörüne aşağıdaki içeriğe sahip bir *_Host. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9013-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="e9013-140">Bileşenler, düzeni için paylaşılan *_Layout. cshtml* dosyasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="e9013-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="e9013-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır `App` :</span><span class="sxs-lookup"><span data-stu-id="e9013-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="e9013-142">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="e9013-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="e9013-143">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9013-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="e9013-144">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="e9013-144">Render Mode</span></span> | <span data-ttu-id="e9013-145">Açıklama</span><span class="sxs-lookup"><span data-stu-id="e9013-145">Description</span></span> |
   | ---
<span data-ttu-id="e9013-146">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-147">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-148">'Blazor'</span></span>
- <span data-ttu-id="e9013-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-149">'Identity'</span></span>
- <span data-ttu-id="e9013-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-151">'Razor'</span></span>
- <span data-ttu-id="e9013-152">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e9013-153">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-154">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-155">'Blazor'</span></span>
- <span data-ttu-id="e9013-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-156">'Identity'</span></span>
- <span data-ttu-id="e9013-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-158">'Razor'</span></span>
- <span data-ttu-id="e9013-159">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e9013-160">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-161">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-162">'Blazor'</span></span>
- <span data-ttu-id="e9013-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-163">'Identity'</span></span>
- <span data-ttu-id="e9013-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-165">'Razor'</span></span>
- <span data-ttu-id="e9013-166">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-166">'SignalR' uid:</span></span> 

<span data-ttu-id="e9013-167">------ | ---title: ' ASP.NET Core Razor bileşenlerini sayfalarla tümleştirin Razor ve MVC Apps ' Yazar: Açıklama: ' uygulamalardaki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-168">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-169">'Blazor'</span></span>
- <span data-ttu-id="e9013-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-170">'Identity'</span></span>
- <span data-ttu-id="e9013-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-172">'Razor'</span></span>
- <span data-ttu-id="e9013-173">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e9013-174">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-175">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-176">'Blazor'</span></span>
- <span data-ttu-id="e9013-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-177">'Identity'</span></span>
- <span data-ttu-id="e9013-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-179">'Razor'</span></span>
- <span data-ttu-id="e9013-180">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e9013-181">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-182">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-183">'Blazor'</span></span>
- <span data-ttu-id="e9013-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-184">'Identity'</span></span>
- <span data-ttu-id="e9013-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-186">'Razor'</span></span>
- <span data-ttu-id="e9013-187">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-187">'SignalR' uid:</span></span> 

<span data-ttu-id="e9013-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App`Bileşeni STATIK HTML olarak işler ve sunucu uygulaması için bir işaret içerir Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9013-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e9013-189">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9013-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="e9013-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Sunucu uygulaması için bir işaret oluşturur Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9013-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e9013-191">`App`Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="e9013-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="e9013-192">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9013-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="e9013-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App`Bileşeni STATIK HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="e9013-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="e9013-194">Bileşen etiketi Yardımcısı hakkında daha fazla bilgi için bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..</span><span class="sxs-lookup"><span data-stu-id="e9013-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="e9013-195">*_Host. cshtml* sayfasına yönelik düşük öncelikli bir yolu, içindeki uç nokta yapılandırmasına ekleyin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e9013-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="e9013-196">Uygulamaya yönlendirilebilir bileşenler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e9013-196">Add routable components to the app.</span></span> <span data-ttu-id="e9013-197">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e9013-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="e9013-198">Ad alanları hakkında daha fazla bilgi için [bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e9013-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="e9013-199">MVC uygulamasında yönlendirilebilir bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="e9013-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="e9013-200">*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*</span><span class="sxs-lookup"><span data-stu-id="e9013-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="e9013-201">RazorMVC uygulamalarında yönlendirilebilir bileşenleri desteklemek için:</span><span class="sxs-lookup"><span data-stu-id="e9013-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="e9013-202">[Uygulamayı hazırlama](#prepare-the-app) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="e9013-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="e9013-203">Aşağıdaki içeriğe sahip projenin köküne bir *app. Razor* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9013-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="e9013-204">Aşağıdaki içeriğe sahip *Görünümler/giriş* klasörüne bir *_Host. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9013-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="e9013-205">Bileşenler, düzeni için paylaşılan *_Layout. cshtml* dosyasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="e9013-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="e9013-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır `App` :</span><span class="sxs-lookup"><span data-stu-id="e9013-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="e9013-207">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="e9013-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="e9013-208">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9013-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="e9013-209">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="e9013-209">Render Mode</span></span> | <span data-ttu-id="e9013-210">Açıklama</span><span class="sxs-lookup"><span data-stu-id="e9013-210">Description</span></span> |
   | ---
<span data-ttu-id="e9013-211">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-212">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-213">'Blazor'</span></span>
- <span data-ttu-id="e9013-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-214">'Identity'</span></span>
- <span data-ttu-id="e9013-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-216">'Razor'</span></span>
- <span data-ttu-id="e9013-217">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e9013-218">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-219">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-220">'Blazor'</span></span>
- <span data-ttu-id="e9013-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-221">'Identity'</span></span>
- <span data-ttu-id="e9013-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-223">'Razor'</span></span>
- <span data-ttu-id="e9013-224">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e9013-225">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-226">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-227">'Blazor'</span></span>
- <span data-ttu-id="e9013-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-228">'Identity'</span></span>
- <span data-ttu-id="e9013-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-230">'Razor'</span></span>
- <span data-ttu-id="e9013-231">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-231">'SignalR' uid:</span></span> 

<span data-ttu-id="e9013-232">------ | ---title: ' ASP.NET Core Razor bileşenlerini sayfalarla tümleştirin Razor ve MVC Apps ' Yazar: Açıklama: ' uygulamalardaki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-233">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-234">'Blazor'</span></span>
- <span data-ttu-id="e9013-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-235">'Identity'</span></span>
- <span data-ttu-id="e9013-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-237">'Razor'</span></span>
- <span data-ttu-id="e9013-238">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e9013-239">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-240">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-241">'Blazor'</span></span>
- <span data-ttu-id="e9013-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-242">'Identity'</span></span>
- <span data-ttu-id="e9013-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-244">'Razor'</span></span>
- <span data-ttu-id="e9013-245">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e9013-246">title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="e9013-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="e9013-247">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9013-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9013-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9013-248">'Blazor'</span></span>
- <span data-ttu-id="e9013-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9013-249">'Identity'</span></span>
- <span data-ttu-id="e9013-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9013-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9013-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9013-251">'Razor'</span></span>
- <span data-ttu-id="e9013-252">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9013-252">'SignalR' uid:</span></span> 

<span data-ttu-id="e9013-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App`Bileşeni STATIK HTML olarak işler ve sunucu uygulaması için bir işaret içerir Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9013-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e9013-254">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9013-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="e9013-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Sunucu uygulaması için bir işaret oluşturur Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9013-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e9013-256">`App`Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="e9013-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="e9013-257">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9013-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="e9013-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App`Bileşeni STATIK HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="e9013-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="e9013-259">Bileşen etiketi Yardımcısı hakkında daha fazla bilgi için bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..</span><span class="sxs-lookup"><span data-stu-id="e9013-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="e9013-260">Ana denetleyiciye bir eylem ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9013-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="e9013-261">İçindeki uç nokta yapılandırmasına *_Host. cshtml* görünümünü döndüren denetleyici eylemi için düşük öncelikli bir yol ekleyin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e9013-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="e9013-262">Bir *Sayfalar* klasörü oluşturun ve uygulamaya yönlendirilebilir bileşenler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e9013-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="e9013-263">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e9013-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="e9013-264">Ad alanları hakkında daha fazla bilgi için [bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e9013-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="e9013-265">Bir sayfadan veya görünümden bileşenleri işleme</span><span class="sxs-lookup"><span data-stu-id="e9013-265">Render components from a page or view</span></span>

<span data-ttu-id="e9013-266">*Bu bölüm, bileşenlerin Kullanıcı isteklerinden doğrudan yönlendirilemeyen sayfalara veya görünümlere bileşen eklenmesine aittir.*</span><span class="sxs-lookup"><span data-stu-id="e9013-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="e9013-267">Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)kullanın.</span><span class="sxs-lookup"><span data-stu-id="e9013-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="e9013-268">Durum bilgisi olan etkileşimli bileşenleri işle</span><span class="sxs-lookup"><span data-stu-id="e9013-268">Render stateful interactive components</span></span>

<span data-ttu-id="e9013-269">Durum bilgisi olan etkileşimli bileşenler, bir Razor sayfaya veya görünüme eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="e9013-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="e9013-270">Sayfa veya görünüm şunları işler:</span><span class="sxs-lookup"><span data-stu-id="e9013-270">When the page or view renders:</span></span>

* <span data-ttu-id="e9013-271">Bileşen sayfa veya görünümle birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9013-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="e9013-272">Prerendering için kullanılan ilk bileşen durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="e9013-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="e9013-273">Bağlantı kurulduunda yeni bileşen durumu oluşturulur SignalR .</span><span class="sxs-lookup"><span data-stu-id="e9013-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="e9013-274">Aşağıdaki Razor sayfa bir bileşeni işler `Counter` :</span><span class="sxs-lookup"><span data-stu-id="e9013-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="e9013-275">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="e9013-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="e9013-276">Etkileşimsiz bileşenleri işle</span><span class="sxs-lookup"><span data-stu-id="e9013-276">Render noninteractive components</span></span>

<span data-ttu-id="e9013-277">Aşağıdaki Razor sayfada, `Counter` bileşen bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="e9013-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="e9013-278">Bileşen statik olarak işlendiğinden, bileşen etkileşimli değildir:</span><span class="sxs-lookup"><span data-stu-id="e9013-278">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="e9013-279">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="e9013-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="e9013-280">Bileşen ad alanları</span><span class="sxs-lookup"><span data-stu-id="e9013-280">Component namespaces</span></span>

<span data-ttu-id="e9013-281">Uygulamanın bileşenlerini tutmak için özel bir klasör kullanırken, klasörü/görünümü veya *_ViewImports. cshtml* dosyasını temsil eden ad alanını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e9013-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="e9013-282">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="e9013-282">In the following example:</span></span>

* <span data-ttu-id="e9013-283">`MyAppNamespace`Uygulamanın ad alanına geçin.</span><span class="sxs-lookup"><span data-stu-id="e9013-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="e9013-284">Bileşenleri tutmak için *bileşen* adlı bir klasör kullanılmazsa, bileşenlerin bulunduğu klasöre geçin `Components` .</span><span class="sxs-lookup"><span data-stu-id="e9013-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="e9013-285">*_ViewImports. cshtml* dosyası bir Pages uygulamasının *Pages* klasöründe Razor veya bir MVC uygulamasının *views* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="e9013-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="e9013-286">Daha fazla bilgi için bkz. <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="e9013-286">For more information, see <xref:blazor/components#import-components>.</span></span>
