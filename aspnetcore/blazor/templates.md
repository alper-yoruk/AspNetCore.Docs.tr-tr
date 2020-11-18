---
title: ASP.NET Core Blazor şablonları
author: guardrex
description: ASP.NET Core Blazor uygulama şablonları ve proje yapısı hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
no-loc:
- appsettings.json
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
uid: blazor/templates
ms.openlocfilehash: 602ad2908d607703a3b77b2047d51d912645b043
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703728"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="1c9bd-103">ASP.NET Core Blazor şablonları</span><span class="sxs-lookup"><span data-stu-id="1c9bd-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="1c9bd-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="1c9bd-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1c9bd-105">BlazorFramework, barındırma modellerinin her biri için uygulama geliştirmeye yönelik şablonlar sağlar Blazor :</span><span class="sxs-lookup"><span data-stu-id="1c9bd-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="1c9bd-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="1c9bd-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="1c9bd-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="1c9bd-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="1c9bd-108">Barındırma modelleri hakkında daha fazla bilgi için Blazor bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="1c9bd-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="1c9bd-109">`--help`Seçenek CLI komutuna geçirerek şablon seçenekleri mevcuttur [`dotnet new`](/dotnet/core/tools/dotnet-new) :</span><span class="sxs-lookup"><span data-stu-id="1c9bd-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="1c9bd-110">Blazor proje yapısı</span><span class="sxs-lookup"><span data-stu-id="1c9bd-110">Blazor project structure</span></span>

<span data-ttu-id="1c9bd-111">Aşağıdaki dosyalar ve klasörler Blazor bir proje şablonundan oluşturulan bir uygulamayı yapar Blazor :</span><span class="sxs-lookup"><span data-stu-id="1c9bd-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="1c9bd-112">`Program.cs`: Uygulamanın şunları ayarlayan giriş noktası:</span><span class="sxs-lookup"><span data-stu-id="1c9bd-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="1c9bd-113">ASP.NET Core [Host](xref:fundamentals/host/generic-host) ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="1c9bd-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="1c9bd-114">WebAssembly Host ( Blazor WebAssembly ): Bu dosyadaki kod, şablondan oluşturulan uygulamalar için benzersizdir Blazor WebAssembly ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="1c9bd-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="1c9bd-115">`App`Bileşen, uygulamanın kök bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="1c9bd-116">`App`Bileşen, `app` `<div id="app">Loading...</div>` `wwwroot/index.html` kök bileşen koleksiyonunda () DOM öğesi olarak belirtilir `builder.RootComponents.Add<App>("#app")` .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-116">The `App` component is specified as the `app` DOM element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="1c9bd-117">[Hizmetler](xref:blazor/fundamentals/dependency-injection) eklenir ve yapılandırılır (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="1c9bd-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="1c9bd-118">`Program.cs`: Uygulamanın şunları ayarlayan giriş noktası:</span><span class="sxs-lookup"><span data-stu-id="1c9bd-118">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="1c9bd-119">ASP.NET Core [Host](xref:fundamentals/host/generic-host) ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="1c9bd-119">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="1c9bd-120">WebAssembly Host ( Blazor WebAssembly ): Bu dosyadaki kod, şablondan oluşturulan uygulamalar için benzersizdir Blazor WebAssembly ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="1c9bd-120">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="1c9bd-121">`App`Bileşen, uygulamanın kök bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-121">The `App` component is the root component of the app.</span></span> <span data-ttu-id="1c9bd-122">`App`Bileşen, `app` `<app>Loading...</app>` `wwwroot/index.html` kök bileşen koleksiyonunda () DOM öğesi olarak belirtilir `builder.RootComponents.Add<App>("app")` .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-122">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="1c9bd-123">[Hizmetler](xref:blazor/fundamentals/dependency-injection) eklenir ve yapılandırılır (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="1c9bd-123">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="1c9bd-124">`Startup.cs` ( Blazor Server ): Uygulamanın başlangıç mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-124">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="1c9bd-125">`Startup`Sınıfı iki yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="1c9bd-125">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="1c9bd-126">`ConfigureServices`: Uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-126">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="1c9bd-127">Uygulamalar ' da Blazor Server ,, çağırarak <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> ve `WeatherForecastService` hizmet kapsayıcısına örnek bileşen tarafından kullanılmak üzere eklendikten sonra, hizmetler eklenir `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-127">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="1c9bd-128">`Configure`: Uygulamanın istek işleme ardışık düzenini yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="1c9bd-128">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="1c9bd-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> , tarayıcıya gerçek zamanlı bağlantı için bir uç nokta ayarlamak üzere çağırılır.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="1c9bd-130">Bağlantı, [SignalR](xref:signalr/introduction) uygulamalarına gerçek zamanlı Web işlevselliği ekleme çerçevesi olan ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-130">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="1c9bd-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) , uygulamanın () kök sayfasını ayarlamak `Pages/_Host.cshtml` ve gezinmeyi etkinleştirmek için çağırılır.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="1c9bd-132">`wwwroot/index.html` ( Blazor WebAssembly ): BIR HTML sayfası olarak uygulanan uygulamanın kök sayfası:</span><span class="sxs-lookup"><span data-stu-id="1c9bd-132">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="1c9bd-133">Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="1c9bd-134">Bu sayfa, kök bileşenin nerede `App` işleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="1c9bd-135">Bileşen, `app` DOM öğesinin () konumunda işlenir `<app>...</app>` .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-135">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="1c9bd-136">`_framework/blazor.webassembly.js`JavaScript dosyası yüklenir ve şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="1c9bd-136">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="1c9bd-137">.NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirir.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-137">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="1c9bd-138">Uygulamayı çalıştırmak için çalışma zamanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-138">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="1c9bd-139">`App.razor`: Bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın kök bileşeni <xref:Microsoft.AspNetCore.Components.Routing.Router> .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-139">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="1c9bd-140"><xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen tarayıcı gezintisini karşılar ve istenen adresle eşleşen sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-140">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="1c9bd-141">`Pages` klasör: uygulamayı `.razor` Blazor ve uygulamanın kök sayfasını oluşturan yönlendirilebilir bileşenleri/sayfaları () içerir Razor Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-141">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="1c9bd-142">Her sayfanın yolu, yönergesi kullanılarak belirtilir [`@page`](xref:mvc/views/razor#page) .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-142">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="1c9bd-143">Şablon şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="1c9bd-143">The template includes the following:</span></span>
  * <span data-ttu-id="1c9bd-144">`_Host.cshtml` ( Blazor Server ): Uygulamanın kök sayfası olarak Razor uygulandı Sayfasında</span><span class="sxs-lookup"><span data-stu-id="1c9bd-144">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="1c9bd-145">Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-145">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="1c9bd-146">`_framework/blazor.server.js`Tarayıcı ve sunucu arasındaki gerçek zamanlı bağlantıyı ayarlayan JavaScript dosyası yüklenir SignalR .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-146">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="1c9bd-147">Ana bilgisayar sayfası, kök bileşeni 'nin `App` ( `App.razor` ) nerede işleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-147">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="1c9bd-148">`Counter` Component ( `Pages/Counter.razor` ): sayaç sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-148">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="1c9bd-149">`Error` bileşen ( `Error.razor` Blazor Server yalnızca uygulama): uygulamada işlenmeyen bir özel durum oluştuğunda işlenir.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-149">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="1c9bd-150">`FetchData` Component ( `Pages/FetchData.razor` ): veri getirme sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-150">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="1c9bd-151">`Index` Component ( `Pages/Index.razor` ): giriş sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-151">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="1c9bd-152">`Properties/launchSettings.json`: [Geliştirme ortamı yapılandırmasını](xref:fundamentals/environments#development-and-launchsettingsjson)barındırır.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-152">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="1c9bd-153">`Shared` klasör: uygulama tarafından kullanılan diğer Kullanıcı Arabirimi bileşenlerini ( `.razor` ) içerir:</span><span class="sxs-lookup"><span data-stu-id="1c9bd-153">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="1c9bd-154">`MainLayout` Component ( `MainLayout.razor` ): uygulamanın [Düzen bileşeni](xref:blazor/layouts).</span><span class="sxs-lookup"><span data-stu-id="1c9bd-154">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="1c9bd-155">`MainLayout.razor.css`: Uygulamanın ana düzeni için stil sayfası.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-155">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="1c9bd-156">`NavMenu` Component ( `NavMenu.razor` ): kenar çubuğu gezintisini uygular.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-156">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="1c9bd-157">[ `NavLink` ](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-157">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="1c9bd-158">Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-158">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="1c9bd-159">`NavMenu.razor.css`: Uygulamanın gezinti menüsünün stil sayfası.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-159">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="1c9bd-160">`Shared` klasör: uygulama tarafından kullanılan diğer Kullanıcı Arabirimi bileşenlerini ( `.razor` ) içerir:</span><span class="sxs-lookup"><span data-stu-id="1c9bd-160">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="1c9bd-161">`MainLayout` Component ( `MainLayout.razor` ): uygulamanın [Düzen bileşeni](xref:blazor/layouts).</span><span class="sxs-lookup"><span data-stu-id="1c9bd-161">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="1c9bd-162">`NavMenu` Component ( `NavMenu.razor` ): kenar çubuğu gezintisini uygular.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-162">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="1c9bd-163">[ `NavLink` ](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-163">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="1c9bd-164">Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-164">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="1c9bd-165">`_Imports.razor`: Razor `.razor` [`@using`](xref:mvc/views/razor#using) Ad alanları için yönergeler gibi uygulamanın bileşenlerine () dahil etmek için ortak yönergeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-165">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="1c9bd-166">`Data` Folder ( Blazor Server ): `WeatherForecast` `WeatherForecastService` uygulamanın bileşenine örnek Hava durumu verileri sağlayan sınıfını ve uygulamasını içerir `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="1c9bd-166">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="1c9bd-167">`wwwroot`: Uygulamanın ortak statik varlıklarını içeren uygulamanın [Web kök](xref:fundamentals/index#web-root) klasörü.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-167">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="1c9bd-168">`appsettings.json`: Uygulama için [yapılandırma ayarlarını](xref:blazor/fundamentals/configuration) tutar.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-168">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="1c9bd-169">Bir Blazor WebAssembly uygulamada, uygulama ayarları dosyası `wwwroot` klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-169">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="1c9bd-170">Bir Blazor Server uygulamada, uygulama ayarları dosyası proje kökünde bulunur.</span><span class="sxs-lookup"><span data-stu-id="1c9bd-170">In a Blazor Server app, the app settings file is located at the project root.</span></span>
