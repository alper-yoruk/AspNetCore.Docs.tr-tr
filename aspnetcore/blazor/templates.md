---
title: ASP.NET Core Blazor şablonları
author: guardrex
description: ASP.NET Core Blazor uygulama şablonları ve Blazor proje yapısı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 705fa32ee72221b3c18653e9f3495b9cd61e9ad1
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967434"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="583b0-103">ASP.NET Core Blazor şablonları</span><span class="sxs-lookup"><span data-stu-id="583b0-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="583b0-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="583b0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="583b0-105">Blazor Framework, Blazor barındırma modellerinin her biri için uygulama geliştirmeye yönelik şablonlar sağlar:</span><span class="sxs-lookup"><span data-stu-id="583b0-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="583b0-106">WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="583b0-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="583b0-107">Sunucu (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="583b0-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="583b0-108">Barındırma modelleri hakkında Blazordaha fazla bilgi için bkz <xref:blazor/hosting-models>..</span><span class="sxs-lookup"><span data-stu-id="583b0-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="583b0-109">Şablondan Blazor uygulama oluşturmaya yönelik adım adım yönergeler için, bkz <xref:blazor/get-started>..</span><span class="sxs-lookup"><span data-stu-id="583b0-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="583b0-110">Şablon seçenekleri, bu `--help` seçeneği [DotNet New](/dotnet/core/tools/dotnet-new) CLI komutuna geçirerek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="583b0-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="583b0-111">proje yapısı</span><span class="sxs-lookup"><span data-stu-id="583b0-111"> project structure</span></span>

<span data-ttu-id="583b0-112">Aşağıdaki dosya ve klasörler bir Blazor Blazor şablondan oluşturulan bir uygulamayı yapar:</span><span class="sxs-lookup"><span data-stu-id="583b0-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="583b0-113">*Program.cs* &ndash; , uygulamanın giriş noktasını şu şekilde ayarlar:</span><span class="sxs-lookup"><span data-stu-id="583b0-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="583b0-114">ASP.NET Core [ana bilgisayar](xref:fundamentals/host/generic-host) (Blazor sunucu)</span><span class="sxs-lookup"><span data-stu-id="583b0-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="583b0-115">WebAssembly Host (Blazor webassembly) &ndash; bu dosyadaki kod, Blazor webassembly şablonundan (`blazorwasm`) oluşturulan uygulamalar için benzersizdir.</span><span class="sxs-lookup"><span data-stu-id="583b0-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="583b0-116">Uygulamanın `App` kök bileşeni olan bileşeni, `app` `Add` yönteminin DOM öğesi olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="583b0-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="583b0-117">Hizmetler, ana bilgisayar Oluşturucu 'daki `ConfigureServices` yöntemiyle yapılandırılabilir (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span><span class="sxs-lookup"><span data-stu-id="583b0-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="583b0-118">Yapılandırma, ana bilgisayar Oluşturucu (`builder.Configuration`) yoluyla sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="583b0-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="583b0-119">*Startup.cs* (Blazor sunucu) &ndash; uygulamanın başlangıç mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="583b0-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="583b0-120">`Startup` Sınıfı iki yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="583b0-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="583b0-121">`ConfigureServices`&ndash; Uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="583b0-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="583b0-122">Blazor Sunucu uygulamalarında, Hizmetleri çağırarak <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>eklenir ve `WeatherForecastService` örnek `FetchData` bileşen tarafından kullanılmak üzere hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="583b0-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="583b0-123">`Configure`&ndash; Uygulamanın istek işleme ardışık düzenini yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="583b0-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="583b0-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>, tarayıcıya gerçek zamanlı bağlantı için bir uç nokta ayarlamak üzere çağırılır.</span><span class="sxs-lookup"><span data-stu-id="583b0-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="583b0-125">Bağlantı, uygulamalarına gerçek zamanlı [SignalR](xref:signalr/introduction)Web işlevselliği ekleme çerçevesi olan ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="583b0-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="583b0-126">[Mapfallbacktopage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) , uygulamanın kök sayfasını (*Pages/_Host. cshtml*) ayarlamak ve gezinmeyi etkinleştirmek için çağırılır.</span><span class="sxs-lookup"><span data-stu-id="583b0-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="583b0-127">*Wwwroot/index.html* (Blazor webassembly) &ndash; bir HTML sayfası olarak uygulanan uygulamanın kök sayfası:</span><span class="sxs-lookup"><span data-stu-id="583b0-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="583b0-128">Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.</span><span class="sxs-lookup"><span data-stu-id="583b0-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="583b0-129">Bu sayfa, kök `App` bileşenin nerede işleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="583b0-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="583b0-130">`App` Bileşen (*app. Razor*), içindeki `app` `AddComponent` `Startup.Configure`metoduna DOM öğesi olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="583b0-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="583b0-131">`_framework/blazor.webassembly.js` JavaScript dosyası yüklenir ve şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="583b0-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="583b0-132">.NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirir.</span><span class="sxs-lookup"><span data-stu-id="583b0-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="583b0-133">Uygulamayı çalıştırmak için çalışma zamanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="583b0-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="583b0-134">*App. Razor* &ndash; <xref:Microsoft.AspNetCore.Components.Routing.Router> bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın kök bileşeni.</span><span class="sxs-lookup"><span data-stu-id="583b0-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="583b0-135">`Router` Bileşen tarayıcı gezintisini karşılar ve istenen adresle eşleşen sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="583b0-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="583b0-136">*Sayfalar* klasörü &ndash; , bir Blazor sunucu uygulamasının Blazor uygulamasını ve kök Razor sayfasını oluşturan yönlendirilebilir bileşenleri/sayfaları (*. Razor*) içerir.</span><span class="sxs-lookup"><span data-stu-id="583b0-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="583b0-137">Her sayfanın yolu, [`@page`](xref:mvc/views/razor#page) yönergesi kullanılarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="583b0-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="583b0-138">Şablon şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="583b0-138">The template includes the following:</span></span>
  * <span data-ttu-id="583b0-139">*_Host. cshtml* (Blazor sunucu) &ndash; bir Razor sayfa olarak uygulanan uygulamanın kök sayfası:</span><span class="sxs-lookup"><span data-stu-id="583b0-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="583b0-140">Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.</span><span class="sxs-lookup"><span data-stu-id="583b0-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="583b0-141">Tarayıcı `_framework/blazor.server.js` ve sunucu arasındaki gerçek zamanlı SignalR bağlantıyı ayarlayan JavaScript dosyası yüklenir.</span><span class="sxs-lookup"><span data-stu-id="583b0-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="583b0-142">Ana bilgisayar sayfası, kök `App` bileşeni 'Nin (*app. Razor*) nerede işleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="583b0-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="583b0-143">`Counter`(*Counter. Razor*) &ndash; sayaç sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="583b0-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="583b0-144">`Error`(*Error. Razor*, Blazor yalnızca sunucu uygulaması) &ndash; Uygulamada işlenmeyen bir özel durum oluştuğunda işlenir.</span><span class="sxs-lookup"><span data-stu-id="583b0-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="583b0-145">`FetchData`(*Fetchdata. Razor*) &ndash; veri getirme sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="583b0-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="583b0-146">`Index`(*Index. Razor*) &ndash; giriş sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="583b0-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="583b0-147">*Paylaşılan* klasör &ndash; , uygulama tarafından kullanılan diğer Kullanıcı Arabirimi bileşenlerini (*. Razor*) içerir:</span><span class="sxs-lookup"><span data-stu-id="583b0-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="583b0-148">`MainLayout`(*Mainlayout. Razor*) &ndash; uygulamanın Düzen bileşeni.</span><span class="sxs-lookup"><span data-stu-id="583b0-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="583b0-149">`NavMenu`(*Navmenu. Razor*) &ndash; kenar çubuğu gezintisini uygular.</span><span class="sxs-lookup"><span data-stu-id="583b0-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="583b0-150">Diğer Razor bileşenlere yönelik gezinti bağlantılarını işleyen<xref:Microsoft.AspNetCore.Components.Routing.NavLink> [navlink bileşenini](xref:blazor/routing#navlink-component) () içerir.</span><span class="sxs-lookup"><span data-stu-id="583b0-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="583b0-151">`NavLink` Bileşeni, bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="583b0-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="583b0-152">*_Imports. Razor* &ndash; , uygulamanın Razor bileşenlerine (*. Razor*) dahil etmek için, ad alanları için [`@using`](xref:mvc/views/razor#using) yönergeler gibi genel yönergeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="583b0-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="583b0-153">*Veri* klasörü (Blazor sunucu) &ndash; , `WeatherForecast` `WeatherForecastService` uygulamanın `FetchData` bileşenine örnek Hava durumu verileri sağlayan sınıfını ve uygulamasını içerir.</span><span class="sxs-lookup"><span data-stu-id="583b0-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="583b0-154">uygulamanın genel statik varlıklarını içeren uygulamanın [Web kök](xref:fundamentals/index#web-root) klasörünü *Wwwroot* &ndash; .</span><span class="sxs-lookup"><span data-stu-id="583b0-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="583b0-155">uygulama için *appSettings. JSON* Blazor ( &ndash; sunucu) yapılandırma ayarları.</span><span class="sxs-lookup"><span data-stu-id="583b0-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
