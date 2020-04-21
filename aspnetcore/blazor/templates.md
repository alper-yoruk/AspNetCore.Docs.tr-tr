---
title: ASP.NET Blazor Çekirdek şablonları
author: guardrex
description: ASP.NET Core Blazor uygulama şablonları Blazor ve proje yapısı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 0a4a508beeae3d7bc665372d925989aa4e34ad52
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661721"
---
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="31cde-103">ASP.NET Blazor Çekirdek şablonları</span><span class="sxs-lookup"><span data-stu-id="31cde-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="31cde-104">Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="31cde-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="31cde-105">Çerçeve, Blazor Blazor barındırma modellerinin her biri için uygulamalar geliştirmek için şablonlar sağlar:</span><span class="sxs-lookup"><span data-stu-id="31cde-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="31cde-106">WebAssembly`blazorwasm`( )</span><span class="sxs-lookup"><span data-stu-id="31cde-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="31cde-107">Sunucu`blazorserver`( )</span><span class="sxs-lookup"><span data-stu-id="31cde-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="31cde-108">'s barındırma Blazormodelleri hakkında daha <xref:blazor/hosting-models>fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="31cde-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="31cde-109">Şablondan Blazor uygulama oluşturmayla ilgili adım adım talimatlar <xref:blazor/get-started>için bkz.</span><span class="sxs-lookup"><span data-stu-id="31cde-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="31cde-110">Şablon seçenekleri, `--help` seçeneği [dotnet yeni](/dotnet/core/tools/dotnet-new) CLI komutuna geçirerek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="31cde-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="31cde-111">proje yapısı</span><span class="sxs-lookup"><span data-stu-id="31cde-111"> project structure</span></span>

<span data-ttu-id="31cde-112">Aşağıdaki dosya ve klasörler Blazor bir Blazor şablondan oluşturulan bir uygulamayı oluşur:</span><span class="sxs-lookup"><span data-stu-id="31cde-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="31cde-113">*Program.cs* &ndash; Uygulamanın aşağıdakileri ayarlayan giriş noktası:</span><span class="sxs-lookup"><span data-stu-id="31cde-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="31cde-114">ASP.NET CoreBlazor ana [bilgisayar](xref:fundamentals/host/generic-host) (Sunucu)</span><span class="sxs-lookup"><span data-stu-id="31cde-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="31cde-115">WebAssembly anaBlazor bilgisayarı &ndash; (WebAssembly) Bu dosyadaki Blazor kod, WebAssembly`blazorwasm`şablonundan oluşturulan uygulamalara özgüdür ( ).</span><span class="sxs-lookup"><span data-stu-id="31cde-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="31cde-116">Uygulamanın `App` kök bileşeni olan bileşen, `app` `Add` yönteme DOM öğesi olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="31cde-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="31cde-117">Hizmetler ana bilgisayar oluşturucu (örneğin, `ConfigureServices` `builder.Services.AddSingleton<IMyDependency, MyDependency>();`) yöntemi ile yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="31cde-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="31cde-118">Yapılandırma ana bilgisayar oluşturucu (`builder.Configuration`) aracılığıyla sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="31cde-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="31cde-119">*Startup.cs* Blazor (Sunucu) &ndash; Uygulamanın başlangıç mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="31cde-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="31cde-120">Sınıf `Startup` iki yöntem tanımlar:</span><span class="sxs-lookup"><span data-stu-id="31cde-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="31cde-121">`ConfigureServices`&ndash; Uygulamanın [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="31cde-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="31cde-122">Sunucu Blazor uygulamalarında, hizmetler çağrılar <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>ekleyerek eklenir ve örnek `WeatherForecastService` `FetchData` bileşen tarafından kullanılmak üzere servis kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="31cde-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="31cde-123">`Configure`&ndash; Uygulamanın istek işleme ardışık hattını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="31cde-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="31cde-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>tarayıcı ile gerçek zamanlı bağlantı için bir bitiş noktası kurmak için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="31cde-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="31cde-125">[SignalR](xref:signalr/introduction)Bağlantı, uygulamalara gerçek zamanlı web işlevselliği eklemek için bir çerçeve olan ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="31cde-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="31cde-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) uygulamanın kök sayfasını *(Pages/_Host.cshtml)* ayarlamak ve gezinmeyi etkinleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="31cde-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="31cde-127">*wwwroot/index.html* Blazor (WebAssembly) &ndash; HTML sayfası olarak uygulanan uygulamanın kök sayfası:</span><span class="sxs-lookup"><span data-stu-id="31cde-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="31cde-128">Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, bu sayfa işlenir ve yanıt olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="31cde-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="31cde-129">Sayfa, kök `App` bileşenin nerede işlenir olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="31cde-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="31cde-130">Bileşen `App` *(App.razor)* `app` `AddComponent` yönteminin DOM elemanı olarak `Startup.Configure`belirtilir.</span><span class="sxs-lookup"><span data-stu-id="31cde-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="31cde-131">`_framework/blazor.webassembly.js` JavaScript dosyası yüklenir:</span><span class="sxs-lookup"><span data-stu-id="31cde-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="31cde-132">.NET çalışma saatini, uygulamayı ve uygulamanın bağımlılıklarını indirir.</span><span class="sxs-lookup"><span data-stu-id="31cde-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="31cde-133">Uygulamayı çalıştırmak için çalışma süresini başlangıç olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="31cde-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="31cde-134">*App.razor* &ndash; Bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın <xref:Microsoft.AspNetCore.Components.Routing.Router> kök bileşeni.</span><span class="sxs-lookup"><span data-stu-id="31cde-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="31cde-135">Bileşen `Router` tarayıcı gezintisini keser ve istenen adresle eşleşen sayfayı işler.</span><span class="sxs-lookup"><span data-stu-id="31cde-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="31cde-136">*Sayfalar* &ndash; klasörü Blazor Uygulamayı oluşturan routable bileşenleri/sayfaları *(.jilet)* ve bir Blazor Server uygulamasının root Razor sayfasını içerir.</span><span class="sxs-lookup"><span data-stu-id="31cde-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="31cde-137">Her sayfanın [`@page`](xref:mvc/views/razor#page) rotası yönerge kullanılarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="31cde-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="31cde-138">Şablon aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="31cde-138">The template includes the following:</span></span>
  * <span data-ttu-id="31cde-139">*_Host.cshtml* Blazor (Server) &ndash; Razor Page olarak uygulanan uygulamanın kök sayfası:</span><span class="sxs-lookup"><span data-stu-id="31cde-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="31cde-140">Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, bu sayfa işlenir ve yanıt olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="31cde-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="31cde-141">`_framework/blazor.server.js` JavaScript dosyası, tarayıcı ve sunucu arasındaki SignalR gerçek zamanlı bağlantıyı ayarlayan yüklenir.</span><span class="sxs-lookup"><span data-stu-id="31cde-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="31cde-142">Ana Bilgisayar sayfası kök `App` bileşenin *(App.razor)* nerede işlenir olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="31cde-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="31cde-143">`Counter`(*Counter.razor*) &ndash; Sayaç sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="31cde-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="31cde-144">`Error`(*Error.razor*, Blazor Yalnızca Sunucu uygulaması) &ndash; Uygulamada işlenmemiş bir özel durum oluştuğunda işlenir.</span><span class="sxs-lookup"><span data-stu-id="31cde-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="31cde-145">`FetchData`(*FetchData.razor*) &ndash; Veri Getir sayfasını uygular.</span><span class="sxs-lookup"><span data-stu-id="31cde-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="31cde-146">`Index`(*Index.razor*) &ndash; Ana sayfayı uygular.</span><span class="sxs-lookup"><span data-stu-id="31cde-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="31cde-147">*Paylaşılan* &ndash; klasör Uygulama tarafından kullanılan diğer kullanıcı arabirimi bileşenlerini (*.jilet)* içerir:</span><span class="sxs-lookup"><span data-stu-id="31cde-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="31cde-148">`MainLayout`(*MainLayout.razor*) &ndash; Uygulamanın düzen bileşeni.</span><span class="sxs-lookup"><span data-stu-id="31cde-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="31cde-149">`NavMenu`(*NavMenu.razor*) &ndash; Kenar çubuğu navigasyon uyguluyor.</span><span class="sxs-lookup"><span data-stu-id="31cde-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="31cde-150">Diğer Razor bileşenlerine<xref:Microsoft.AspNetCore.Components.Routing.NavLink>navigasyon bağlantıları sağlayan [NavLink bileşenini](xref:blazor/routing#navlink-component) (), içerir.</span><span class="sxs-lookup"><span data-stu-id="31cde-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="31cde-151">Bileşen, `NavLink` bileşeni yüklendiğinde seçili durumu otomatik olarak gösterir ve bu da kullanıcının hangi bileşenin şu anda görüntülendiğini anlamasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="31cde-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="31cde-152">*_Imports.razor,* &ndash; ad boşlukları için direktifler gibi [`@using`](xref:mvc/views/razor#using) uygulamanın bileşenlerine *(.jilet)* dahil etmek için yaygın Razor yönergeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="31cde-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="31cde-153">*Veri* Blazor klasörü &ndash; (Sunucu) Uygulamanın `WeatherForecast` `WeatherForecastService` `FetchData` bileşenine örnek hava durumu verileri sağlayan sınıfın ve uygulamanın uygulanmasını içerir.</span><span class="sxs-lookup"><span data-stu-id="31cde-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="31cde-154">*wwwroot* &ndash; Uygulamanın ortak statik varlıklarını içeren uygulama için [Web Root](xref:fundamentals/index#web-root) klasörü.</span><span class="sxs-lookup"><span data-stu-id="31cde-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="31cde-155">uygulama için *appsettings.json* (Sunucu)Blazor &ndash; Yapılandırma ayarları.</span><span class="sxs-lookup"><span data-stu-id="31cde-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
