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
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.NET Blazor Çekirdek şablonları

Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Çerçeve, Blazor Blazor barındırma modellerinin her biri için uygulamalar geliştirmek için şablonlar sağlar:

* BlazorWebAssembly`blazorwasm`( )
* BlazorSunucu`blazorserver`( )

's barındırma Blazormodelleri hakkında daha <xref:blazor/hosting-models>fazla bilgi için bkz.

Şablondan Blazor uygulama oluşturmayla ilgili adım adım talimatlar <xref:blazor/get-started>için bkz.

Şablon seçenekleri, `--help` seçeneği [dotnet yeni](/dotnet/core/tools/dotnet-new) CLI komutuna geçirerek kullanılabilir:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="opno-locblazor-project-structure"></a>Blazorproje yapısı

Aşağıdaki dosya ve klasörler Blazor bir Blazor şablondan oluşturulan bir uygulamayı oluşur:

* *Program.cs* &ndash; Uygulamanın aşağıdakileri ayarlayan giriş noktası:

  * ASP.NET CoreBlazor ana [bilgisayar](xref:fundamentals/host/generic-host) (Sunucu)
  * WebAssembly anaBlazor bilgisayarı &ndash; (WebAssembly) Bu dosyadaki Blazor kod, WebAssembly`blazorwasm`şablonundan oluşturulan uygulamalara özgüdür ( ).
    * Uygulamanın `App` kök bileşeni olan bileşen, `app` `Add` yönteme DOM öğesi olarak belirtilir.
    * Hizmetler ana bilgisayar oluşturucu (örneğin, `ConfigureServices` `builder.Services.AddSingleton<IMyDependency, MyDependency>();`) yöntemi ile yapılandırılabilir.
    * Yapılandırma ana bilgisayar oluşturucu (`builder.Configuration`) aracılığıyla sağlanabilir.

* *Startup.cs* Blazor (Sunucu) &ndash; Uygulamanın başlangıç mantığını içerir. Sınıf `Startup` iki yöntem tanımlar:

  * `ConfigureServices`&ndash; Uygulamanın [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır. Sunucu Blazor uygulamalarında, hizmetler çağrılar <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>ekleyerek eklenir ve örnek `WeatherForecastService` `FetchData` bileşen tarafından kullanılmak üzere servis kapsayıcısına eklenir.
  * `Configure`&ndash; Uygulamanın istek işleme ardışık hattını yapılandırır:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>tarayıcı ile gerçek zamanlı bağlantı için bir bitiş noktası kurmak için çağrılır. [SignalR](xref:signalr/introduction)Bağlantı, uygulamalara gerçek zamanlı web işlevselliği eklemek için bir çerçeve olan ile oluşturulur.
    * [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) uygulamanın kök sayfasını *(Pages/_Host.cshtml)* ayarlamak ve gezinmeyi etkinleştirmek için çağrılır.

* *wwwroot/index.html* Blazor (WebAssembly) &ndash; HTML sayfası olarak uygulanan uygulamanın kök sayfası:
  * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, bu sayfa işlenir ve yanıt olarak döndürülür.
  * Sayfa, kök `App` bileşenin nerede işlenir olduğunu belirtir. Bileşen `App` *(App.razor)* `app` `AddComponent` yönteminin DOM elemanı olarak `Startup.Configure`belirtilir.
  * `_framework/blazor.webassembly.js` JavaScript dosyası yüklenir:
    * .NET çalışma saatini, uygulamayı ve uygulamanın bağımlılıklarını indirir.
    * Uygulamayı çalıştırmak için çalışma süresini başlangıç olarak değerlendirir.

* *App.razor* &ndash; Bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın <xref:Microsoft.AspNetCore.Components.Routing.Router> kök bileşeni. Bileşen `Router` tarayıcı gezintisini keser ve istenen adresle eşleşen sayfayı işler.

* *Sayfalar* &ndash; klasörü Blazor Uygulamayı oluşturan routable bileşenleri/sayfaları *(.jilet)* ve bir Blazor Server uygulamasının root Razor sayfasını içerir. Her sayfanın [`@page`](xref:mvc/views/razor#page) rotası yönerge kullanılarak belirtilir. Şablon aşağıdakileri içerir:
  * *_Host.cshtml* Blazor (Server) &ndash; Razor Page olarak uygulanan uygulamanın kök sayfası:
    * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, bu sayfa işlenir ve yanıt olarak döndürülür.
    * `_framework/blazor.server.js` JavaScript dosyası, tarayıcı ve sunucu arasındaki SignalR gerçek zamanlı bağlantıyı ayarlayan yüklenir.
    * Ana Bilgisayar sayfası kök `App` bileşenin *(App.razor)* nerede işlenir olduğunu belirtir.
  * `Counter`(*Counter.razor*) &ndash; Sayaç sayfasını uygular.
  * `Error`(*Error.razor*, Blazor Yalnızca Sunucu uygulaması) &ndash; Uygulamada işlenmemiş bir özel durum oluştuğunda işlenir.
  * `FetchData`(*FetchData.razor*) &ndash; Veri Getir sayfasını uygular.
  * `Index`(*Index.razor*) &ndash; Ana sayfayı uygular.

* *Paylaşılan* &ndash; klasör Uygulama tarafından kullanılan diğer kullanıcı arabirimi bileşenlerini (*.jilet)* içerir:
  * `MainLayout`(*MainLayout.razor*) &ndash; Uygulamanın düzen bileşeni.
  * `NavMenu`(*NavMenu.razor*) &ndash; Kenar çubuğu navigasyon uyguluyor. Diğer Razor bileşenlerine<xref:Microsoft.AspNetCore.Components.Routing.NavLink>navigasyon bağlantıları sağlayan [NavLink bileşenini](xref:blazor/routing#navlink-component) (), içerir. Bileşen, `NavLink` bileşeni yüklendiğinde seçili durumu otomatik olarak gösterir ve bu da kullanıcının hangi bileşenin şu anda görüntülendiğini anlamasına yardımcı olur.

* *_Imports.razor,* &ndash; ad boşlukları için direktifler gibi [`@using`](xref:mvc/views/razor#using) uygulamanın bileşenlerine *(.jilet)* dahil etmek için yaygın Razor yönergeleri içerir.

* *Veri* Blazor klasörü &ndash; (Sunucu) Uygulamanın `WeatherForecast` `WeatherForecastService` `FetchData` bileşenine örnek hava durumu verileri sağlayan sınıfın ve uygulamanın uygulanmasını içerir.

* *wwwroot* &ndash; Uygulamanın ortak statik varlıklarını içeren uygulama için [Web Root](xref:fundamentals/index#web-root) klasörü.

* uygulama için *appsettings.json* (Sunucu)Blazor &ndash; Yapılandırma ayarları.
