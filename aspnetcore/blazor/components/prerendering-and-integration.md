---
title: 'PreRender ve ASP.NET Core :::no-loc(Razor)::: bileşenleri tümleştirme'
author: guardrex
description: ':::no-loc(Razor)::: :::no-loc(Blazor)::: Sunucu üzerindeki bileşenler prerendering dahil olmak üzere uygulamalar için bileşen tümleştirme senaryoları hakkında bilgi edinin :::no-loc(Razor)::: .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: affca6c9b585b91787f94a13144d07bedfefdd37
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431707"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a><span data-ttu-id="42c6e-103">PreRender ve ASP.NET Core :::no-loc(Razor)::: bileşenleri tümleştirme</span><span class="sxs-lookup"><span data-stu-id="42c6e-103">Prerender and integrate ASP.NET Core :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="42c6e-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="42c6e-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="42c6e-105">:::no-loc(Razor)::: bileşenler, :::no-loc(Razor)::: barındırılan bir çözümde sayfalarla ve MVC uygulamalarıyla tümleştirilebilir :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="42c6e-105">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps in a hosted :::no-loc(Blazor WebAssembly)::: solution.</span></span> <span data-ttu-id="42c6e-106">Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden alınabilir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="42c6e-107">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="42c6e-107">Configuration</span></span>

<span data-ttu-id="42c6e-108">Bir uygulama için prerendering ayarlamak için :::no-loc(Blazor WebAssembly)::: :</span><span class="sxs-lookup"><span data-stu-id="42c6e-108">To set up prerendering for a :::no-loc(Blazor WebAssembly)::: app:</span></span>

1. <span data-ttu-id="42c6e-109">:::no-loc(Blazor WebAssembly):::Uygulamayı bir ASP.NET Core uygulamasında barındırın.</span><span class="sxs-lookup"><span data-stu-id="42c6e-109">Host the :::no-loc(Blazor WebAssembly)::: app in an ASP.NET Core app.</span></span> <span data-ttu-id="42c6e-110">Tek başına bir :::no-loc(Blazor WebAssembly)::: uygulama ASP.NET Core çözümüne eklenebilir veya :::no-loc(Blazor WebAssembly)::: :::no-loc(Blazor)::: barındırılan proje şablonundan oluşturulmuş bir barındırılan uygulama kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="42c6e-110">A standalone :::no-loc(Blazor WebAssembly)::: app can be added to an ASP.NET Core solution, or you can use a hosted :::no-loc(Blazor WebAssembly)::: app created from the :::no-loc(Blazor)::: Hosted project template.</span></span>

1. <span data-ttu-id="42c6e-111">Varsayılan statik `wwwroot/index.html` dosyayı :::no-loc(Blazor WebAssembly)::: istemci projesinden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="42c6e-111">Remove the default static `wwwroot/index.html` file from the :::no-loc(Blazor WebAssembly)::: client project.</span></span>

1. <span data-ttu-id="42c6e-112">İstemci projesinde aşağıdaki satırı silin `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-112">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="42c6e-113">`Pages/_Host.cshtml`Sunucu projesine bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-113">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="42c6e-114">Bir `_Host.cshtml` :::no-loc(Blazor Server)::: komut kabuğunda komutuyla şablondan oluşturulan bir uygulamadan dosya elde edebilirsiniz `dotnet new blazorserver -o :::no-loc(Blazor):::Server` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-114">You can obtain a `_Host.cshtml` file from an app created from the :::no-loc(Blazor Server)::: template with the `dotnet new blazorserver -o :::no-loc(Blazor):::Server` command in a command shell.</span></span> <span data-ttu-id="42c6e-115">`Pages/_Host.cshtml`Dosyayı barındırılan çözümün sunucu uygulamasına yerleştirdikten sonra :::no-loc(Blazor WebAssembly)::: , dosyasında aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="42c6e-115">After placing the `Pages/_Host.cshtml` file into the server app of the hosted :::no-loc(Blazor WebAssembly)::: solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="42c6e-116">Ad alanını sunucu uygulamasının `Pages` klasörüne (örneğin, `@namespace :::no-loc(Blazor):::Hosted.Server.Pages` ) ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="42c6e-116">Set the namespace to the server app's `Pages` folder (for example, `@namespace :::no-loc(Blazor):::Hosted.Server.Pages`).</span></span>
   * <span data-ttu-id="42c6e-117">[`@using`](xref:mvc/views/razor#using)İstemci projesi için bir yönerge ayarlayın (örneğin, `@using :::no-loc(Blazor):::Hosted.Client` ).</span><span class="sxs-lookup"><span data-stu-id="42c6e-117">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using :::no-loc(Blazor):::Hosted.Client`).</span></span>
   * <span data-ttu-id="42c6e-118">Stil sayfası bağlantılarını, WebAssembly uygulamasının stil sayfasına işaret etmek üzere güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-118">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="42c6e-119">Aşağıdaki örnekte, istemci uygulamanın ad alanı şu şekilde olur `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-119">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="42c6e-120">`render-mode` [Bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) ' nı, kök bileşeni olan PreRender 'dan güncelleştirin `App` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-120">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="42c6e-121">Komut dosyası :::no-loc(Blazor)::: kaynağını istemci tarafı komut dosyasını kullanacak şekilde güncelleştirin :::no-loc(Blazor WebAssembly)::: :</span><span class="sxs-lookup"><span data-stu-id="42c6e-121">Update the :::no-loc(Blazor)::: script source to use the client-side :::no-loc(Blazor WebAssembly)::: script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="42c6e-122">`Startup.Configure` `Startup.cs` Sunucu projesinin içindeki ():</span><span class="sxs-lookup"><span data-stu-id="42c6e-122">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="42c6e-123">`UseDeveloperExceptionPage`Geliştirme ortamında uygulama tasarımcısında çağırın.</span><span class="sxs-lookup"><span data-stu-id="42c6e-123">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="42c6e-124">`Use:::no-loc(Blazor):::FrameworkFiles`Uygulama Oluşturucu 'da çağırın.</span><span class="sxs-lookup"><span data-stu-id="42c6e-124">Call `Use:::no-loc(Blazor):::FrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="42c6e-125">`index.html`Sayfadan () geri dönüşü değiştirin `endpoints.MapFallbackToFile("index.html");` `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-125">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.Use:::no-loc(Blazor):::FrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.Map:::no-loc(Razor):::Pages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="42c6e-126">Bileşen etiketi Yardımcısı ile bir sayfada veya görünümde bileşenleri işleme</span><span class="sxs-lookup"><span data-stu-id="42c6e-126">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="42c6e-127">Bileşen etiketi Yardımcısı, bir sayfadaki veya görünümdeki bir bileşeni işlemek için iki işleme modunu destekler :::no-loc(Blazor WebAssembly)::: :</span><span class="sxs-lookup"><span data-stu-id="42c6e-127">The Component Tag Helper supports two render modes for rendering a component from a :::no-loc(Blazor WebAssembly)::: app in a page or view:</span></span>

* <span data-ttu-id="42c6e-128">`WebAssembly`: Bir :::no-loc(Blazor WebAssembly)::: uygulamanın tarayıcıya yüklendiğinde etkileşimli bir bileşeni içermesi için kullanılacak bir işaret oluşturur.</span><span class="sxs-lookup"><span data-stu-id="42c6e-128">`WebAssembly`: Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="42c6e-129">Bileşen ön işlenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-129">The component isn't prerendered.</span></span> <span data-ttu-id="42c6e-130">Bu seçenek farklı sayfalarda farklı bileşenlerin işlenmesine daha kolay hale gelir :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="42c6e-130">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span>
* <span data-ttu-id="42c6e-131">`WebAssemblyPrerendered`: Bileşeni statik HTML 'ye ön ekler ve :::no-loc(Blazor WebAssembly)::: daha sonra, tarayıcıya yüklendiğinde bileşeni etkileşimli hale getirmek için bir uygulamanın işaretçisini içerir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-131">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="42c6e-132">Aşağıdaki :::no-loc(Razor)::: Sayfalar örneğinde, `Counter` bileşen bir sayfada işlenir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-132">In the following :::no-loc(Razor)::: Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="42c6e-133">Bileşeni etkileşimli hale getirmek için, :::no-loc(Blazor WebAssembly)::: komut dosyası sayfanın [render bölümüne](xref:mvc/views/layout#sections)eklenir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-133">To make the component interactive, the :::no-loc(Blazor WebAssembly)::: script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="42c6e-134">Bileşen etiketi Yardımcısı () ile bileşen için tam ad alanını kullanmaktan kaçınmak için `Counter` `{APP ASSEMBLY}.Pages.Counter` , [`@using`](xref:mvc/views/razor#using) istemci uygulamanın ad alanı için bir yönerge ekleyin `Pages` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-134">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="42c6e-135">Aşağıdaki örnekte, istemci uygulamanın ad alanı şu şekilde olur `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-135">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
...
@using :::no-loc(Blazor):::Hosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="42c6e-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="42c6e-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="42c6e-137">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-137">Is prerendered into the page.</span></span>
* <span data-ttu-id="42c6e-138">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="42c6e-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

<span data-ttu-id="42c6e-139">Bileşen etiketi Yardımcısı hakkında, parametreleri ve yapılandırmayı geçirme dahil daha fazla bilgi için <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..</span><span class="sxs-lookup"><span data-stu-id="42c6e-139">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="42c6e-140">Yukarıdaki örnekte, sunucu uygulamasının düzeninin ( `_Layout.cshtml` ) kapanış etiketi içindeki betik için bir [render bölümü](xref:mvc/views/layout#sections) () bulunmalıdır <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A> `</body>` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-140">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="42c6e-141">Dosya, bir `_Layout.cshtml` `Pages/Shared` MVC uygulamasında bir :::no-loc(Razor)::: Sayfalar uygulamasının veya klasörünün klasöründe bulunur `Views/Shared` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-141">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="42c6e-142">Uygulama aynı zamanda uygulama stillerinin bulunduğu bileşenlere stil :::no-loc(Blazor WebAssembly)::: eklemek istiyorsanız, uygulamanın stillerini `_Layout.cshtml` dosyaya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-142">If the app should also style components with the styles in the :::no-loc(Blazor WebAssembly)::: app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="42c6e-143">Aşağıdaki örnekte, istemci uygulamanın ad alanı şu şekilde olur `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-143">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="42c6e-144">Bir sayfada veya görünümde bir CSS seçiciyle bileşenleri işleme</span><span class="sxs-lookup"><span data-stu-id="42c6e-144">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="42c6e-145">() İçindeki *istemci* projesine kök bileşenleri ekleyin `Program.Main` `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-145">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="42c6e-146">Aşağıdaki örnekte bileşen, eşleşen bir `Counter` öğeyi seçen CSS seçiciyle bir kök bileşen olarak bildirilmiştir `id` `my-counter` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-146">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="42c6e-147">Aşağıdaki örnekte, istemci uygulamanın ad alanı şu şekilde olur `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-147">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```csharp
using :::no-loc(Blazor):::Hosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="42c6e-148">Aşağıdaki :::no-loc(Razor)::: Sayfalar örneğinde, `Counter` bileşen bir sayfada işlenir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-148">In the following :::no-loc(Razor)::: Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="42c6e-149">Bileşeni etkileşimli hale getirmek için, :::no-loc(Blazor WebAssembly)::: komut dosyası sayfanın [render bölümüne](xref:mvc/views/layout#sections)eklenir:</span><span class="sxs-lookup"><span data-stu-id="42c6e-149">To make the component interactive, the :::no-loc(Blazor WebAssembly)::: script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="42c6e-150">Yukarıdaki örnekte, sunucu uygulamasının düzeninin ( `_Layout.cshtml` ) kapanış etiketi içindeki betik için bir [render bölümü](xref:mvc/views/layout#sections) () bulunmalıdır <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A> `</body>` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-150">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="42c6e-151">Dosya, bir `_Layout.cshtml` `Pages/Shared` MVC uygulamasında bir :::no-loc(Razor)::: Sayfalar uygulamasının veya klasörünün klasöründe bulunur `Views/Shared` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-151">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="42c6e-152">Uygulama aynı zamanda uygulama stillerinin bulunduğu bileşenlere stil :::no-loc(Blazor WebAssembly)::: eklemek istiyorsanız, uygulamanın stillerini `_Layout.cshtml` dosyaya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-152">If the app should also style components with the styles in the :::no-loc(Blazor WebAssembly)::: app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="42c6e-153">Aşağıdaki örnekte, istemci uygulamanın ad alanı şu şekilde olur `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-153">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="42c6e-154">:::no-loc(Razor)::: :::no-loc(Razor)::: Barındırılan bir çözümde bileşenleri sayfalar ve MVC uygulamalarıyla tümleştirmek :::no-loc(Blazor WebAssembly)::: , .NET 5 veya sonraki sürümlerde ASP.NET Core desteklenir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-154">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a hosted :::no-loc(Blazor WebAssembly)::: solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="42c6e-155">Bu makalenin .NET 5 veya sonraki bir sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-155">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="42c6e-156">:::no-loc(Razor)::: bileşenler, :::no-loc(Razor)::: bir uygulamadaki sayfalarla ve MVC uygulamalarıyla tümleştirilebilir :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="42c6e-156">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps in a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="42c6e-157">Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden alınabilir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-157">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="42c6e-158">[Uygulamayı yapılandırdıktan](#configuration)sonra, uygulamanın gereksinimlerine bağlı olarak aşağıdaki bölümlerde yer alan kılavuzu kullanın:</span><span class="sxs-lookup"><span data-stu-id="42c6e-158">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="42c6e-159">Yönlendirilebilir bileşenler: Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenler Için.</span><span class="sxs-lookup"><span data-stu-id="42c6e-159">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="42c6e-160">Ziyaretçilerin, bir yönergesi olan bir bileşen için tarayıcılarında bir HTTP isteği yapabilmeleri gerektiğinde bu kılavuzu izleyin [`@page`](xref:mvc/views/razor#page) .</span><span class="sxs-lookup"><span data-stu-id="42c6e-160">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="42c6e-161">Bir sayfalar uygulamasında yönlendirilebilir bileşenleri kullanma :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="42c6e-161">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="42c6e-162">MVC uygulamasında yönlendirilebilir bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="42c6e-162">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="42c6e-163">[Bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view): doğrudan Kullanıcı isteklerinden yönlendirilemeyen bileşenler için.</span><span class="sxs-lookup"><span data-stu-id="42c6e-163">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="42c6e-164">Uygulama bileşenleri [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)ile var olan sayfalara ve görünümlere eklerken bu kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-164">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="42c6e-165">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="42c6e-165">Configuration</span></span>

<span data-ttu-id="42c6e-166">Mevcut :::no-loc(Razor)::: Sayfalar ve MVC uygulamaları, :::no-loc(Razor)::: bileşenleri sayfalar ve görünümler ile tümleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="42c6e-166">An existing :::no-loc(Razor)::: Pages or MVC app can integrate :::no-loc(Razor)::: components into pages and views:</span></span>

1. <span data-ttu-id="42c6e-167">Uygulamanın düzen dosyasında ( `_Layout.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="42c6e-167">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="42c6e-168">Aşağıdaki `<base>` etiketi `<head>` öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="42c6e-168">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="42c6e-169">`href`Yukarıdaki örnekte yer alan değer ( *uygulama temel yolu* ), uygulamanın kök URL yolunda () bulunduğunu varsayar `/` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-169">The `href` value (the *app base path* ) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="42c6e-170">Uygulama bir alt uygulama ise, makalenin *uygulama temel yolu* bölümündeki yönergeleri izleyin <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="42c6e-170">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="42c6e-171">Dosya, bir `_Layout.cshtml` `Pages/Shared` MVC uygulamasında bir :::no-loc(Razor)::: Sayfalar uygulamasının veya klasörünün klasöründe bulunur `Views/Shared` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-171">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="42c6e-172">`<script>` `blazor.server.js` Render bölümünden hemen önce betik için bir etiket ekleyin `Scripts` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-172">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="42c6e-173">Çerçeve, `blazor.server.js` betiği uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="42c6e-173">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="42c6e-174">Betiği uygulamaya el ile eklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="42c6e-174">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="42c6e-175">`_Imports.razor`Aşağıdaki içeriğe sahip projenin kök klasörüne bir dosya ekleyin (son ad alanını `MyAppNamespace` uygulamanın ad alanına değiştirin):</span><span class="sxs-lookup"><span data-stu-id="42c6e-175">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="42c6e-176">`Startup.ConfigureServices`' De, :::no-loc(Blazor Server)::: hizmeti kaydedin:</span><span class="sxs-lookup"><span data-stu-id="42c6e-176">In `Startup.ConfigureServices`, register the :::no-loc(Blazor Server)::: service:</span></span>

   ```csharp
   services.AddServerSide:::no-loc(Blazor):::();
   ```

1. <span data-ttu-id="42c6e-177">İçinde `Startup.Configure` , :::no-loc(Blazor)::: hub bitiş noktasını şu şekilde ekleyin `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-177">In `Startup.Configure`, add the :::no-loc(Blazor)::: Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.Map:::no-loc(Blazor):::Hub();
   ```

1. <span data-ttu-id="42c6e-178">Bileşenleri herhangi bir sayfa veya görünümle tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-178">Integrate components into any page or view.</span></span> <span data-ttu-id="42c6e-179">Daha fazla bilgi için, [bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="42c6e-179">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="42c6e-180">Bir sayfalar uygulamasında yönlendirilebilir bileşenleri kullanma :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="42c6e-180">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>

<span data-ttu-id="42c6e-181">*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*</span><span class="sxs-lookup"><span data-stu-id="42c6e-181">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="42c6e-182">:::no-loc(Razor):::Sayfalar uygulamalarında yönlendirilebilir bileşenleri desteklemek için :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="42c6e-182">To support routable :::no-loc(Razor)::: components in :::no-loc(Razor)::: Pages apps:</span></span>

1. <span data-ttu-id="42c6e-183">[Yapılandırma](#configuration) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-183">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="42c6e-184">`App.razor`Aşağıdaki içeriğe sahip proje köküne bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="42c6e-184">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="42c6e-185">`_Host.cshtml` `Pages` Klasöre aşağıdaki içeriğe sahip bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="42c6e-185">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="42c6e-186">Bileşenler, `_Layout.cshtml` düzen için paylaşılan dosyayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="42c6e-186">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="42c6e-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır `App` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="42c6e-188">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-188">Is prerendered into the page.</span></span>
   * <span data-ttu-id="42c6e-189">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="42c6e-189">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   <span data-ttu-id="42c6e-190">Bileşen etiketi Yardımcısı hakkında, parametreleri ve yapılandırmayı geçirme dahil daha fazla bilgi için <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..</span><span class="sxs-lookup"><span data-stu-id="42c6e-190">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="42c6e-191">`_Host.cshtml`İçindeki uç nokta yapılandırmasına sayfanın düşük öncelikli bir yolunu ekleyin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-191">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="42c6e-192">Uygulamaya yönlendirilebilir bileşenler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-192">Add routable components to the app.</span></span> <span data-ttu-id="42c6e-193">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="42c6e-193">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="42c6e-194">Ad alanları hakkında daha fazla bilgi için [bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="42c6e-194">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="42c6e-195">MVC uygulamasında yönlendirilebilir bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="42c6e-195">Use routable components in an MVC app</span></span>

<span data-ttu-id="42c6e-196">*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*</span><span class="sxs-lookup"><span data-stu-id="42c6e-196">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="42c6e-197">:::no-loc(Razor):::MVC uygulamalarında yönlendirilebilir bileşenleri desteklemek için:</span><span class="sxs-lookup"><span data-stu-id="42c6e-197">To support routable :::no-loc(Razor)::: components in MVC apps:</span></span>

1. <span data-ttu-id="42c6e-198">[Yapılandırma](#configuration) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-198">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="42c6e-199">`App.razor`Aşağıdaki içeriğe sahip projenin köküne bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="42c6e-199">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="42c6e-200">`_Host.cshtml` `Views/Home` Klasöre aşağıdaki içeriğe sahip bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="42c6e-200">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="42c6e-201">Bileşenler, `_Layout.cshtml` düzen için paylaşılan dosyayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="42c6e-201">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="42c6e-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır `App` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="42c6e-203">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-203">Is prerendered into the page.</span></span>
   * <span data-ttu-id="42c6e-204">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="42c6e-204">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   <span data-ttu-id="42c6e-205">Bileşen etiketi Yardımcısı hakkında, parametreleri ve yapılandırmayı geçirme dahil daha fazla bilgi için <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..</span><span class="sxs-lookup"><span data-stu-id="42c6e-205">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="42c6e-206">Ana denetleyiciye bir eylem ekleyin:</span><span class="sxs-lookup"><span data-stu-id="42c6e-206">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult :::no-loc(Blazor):::()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="42c6e-207">`_Host.cshtml`' Deki uç nokta yapılandırmasına görünümü döndüren denetleyici eylemi için düşük öncelikli bir yol ekleyin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-207">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController(":::no-loc(Blazor):::", "Home");
   });
   ```

1. <span data-ttu-id="42c6e-208">Bir `Pages` klasör oluşturun ve uygulamaya yönlendirilebilir bileşenler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-208">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="42c6e-209">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="42c6e-209">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="42c6e-210">Ad alanları hakkında daha fazla bilgi için [bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="42c6e-210">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="42c6e-211">Bir sayfadan veya görünümden bileşenleri işleme</span><span class="sxs-lookup"><span data-stu-id="42c6e-211">Render components from a page or view</span></span>

<span data-ttu-id="42c6e-212">*Bu bölüm, bileşenlerin Kullanıcı isteklerinden doğrudan yönlendirilemeyen sayfalara veya görünümlere bileşen eklenmesine aittir.*</span><span class="sxs-lookup"><span data-stu-id="42c6e-212">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="42c6e-213">Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)kullanın.</span><span class="sxs-lookup"><span data-stu-id="42c6e-213">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="42c6e-214">Durum bilgisi olan etkileşimli bileşenleri işle</span><span class="sxs-lookup"><span data-stu-id="42c6e-214">Render stateful interactive components</span></span>

<span data-ttu-id="42c6e-215">Durum bilgisi olan etkileşimli bileşenler, bir :::no-loc(Razor)::: sayfaya veya görünüme eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-215">Stateful interactive components can be added to a :::no-loc(Razor)::: page or view.</span></span>

<span data-ttu-id="42c6e-216">Sayfa veya görünüm şunları işler:</span><span class="sxs-lookup"><span data-stu-id="42c6e-216">When the page or view renders:</span></span>

* <span data-ttu-id="42c6e-217">Bileşen sayfa veya görünümle birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="42c6e-217">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="42c6e-218">Prerendering için kullanılan ilk bileşen durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="42c6e-218">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="42c6e-219">Bağlantı kurulduunda yeni bileşen durumu oluşturulur :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="42c6e-219">New component state is created when the :::no-loc(SignalR)::: connection is established.</span></span>

<span data-ttu-id="42c6e-220">Aşağıdaki :::no-loc(Razor)::: sayfa bir bileşeni işler `Counter` :</span><span class="sxs-lookup"><span data-stu-id="42c6e-220">The following :::no-loc(Razor)::: page renders a `Counter` component:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="42c6e-221">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="42c6e-221">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="42c6e-222">Etkileşimsiz bileşenleri işle</span><span class="sxs-lookup"><span data-stu-id="42c6e-222">Render noninteractive components</span></span>

<span data-ttu-id="42c6e-223">Aşağıdaki :::no-loc(Razor)::: sayfada, `Counter` bileşen bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="42c6e-223">In the following :::no-loc(Razor)::: page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="42c6e-224">Bileşen statik olarak işlendiğinden, bileşen etkileşimli değildir:</span><span class="sxs-lookup"><span data-stu-id="42c6e-224">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

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

<span data-ttu-id="42c6e-225">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="42c6e-225">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="42c6e-226">Bileşen ad alanları</span><span class="sxs-lookup"><span data-stu-id="42c6e-226">Component namespaces</span></span>

<span data-ttu-id="42c6e-227">Uygulamanın bileşenlerini tutmak için özel bir klasör kullanırken, klasörü/görünümü ya da dosyaya veya dosyayı temsil eden ad alanını ekleyin `_ViewImports.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="42c6e-227">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="42c6e-228">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="42c6e-228">In the following example:</span></span>

* <span data-ttu-id="42c6e-229">`MyAppNamespace`Uygulamanın ad alanına geçin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-229">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="42c6e-230">Adlı bir klasör, `Components` bileşenleri tutmak için kullanılmazsa, `Components` bileşenlerin bulunduğu klasöre geçin.</span><span class="sxs-lookup"><span data-stu-id="42c6e-230">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="42c6e-231">`_ViewImports.cshtml`Dosya, `Pages` bir :::no-loc(Razor)::: Sayfalar uygulamasının KLASÖRÜNDE veya `Views` bir MVC uygulamasının klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="42c6e-231">The `_ViewImports.cshtml` file is located in the `Pages` folder of a :::no-loc(Razor)::: Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="42c6e-232">Daha fazla bilgi için bkz. <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="42c6e-232">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
