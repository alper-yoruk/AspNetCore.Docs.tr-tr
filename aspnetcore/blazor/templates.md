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
ms.openlocfilehash: c84d6415728bf56836d98cfa66d1b9d46d2eadc8
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770920"
---
# <a name="aspnet-core-blazor-templates"></a>ASP.NET Core Blazor şablonları

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor Framework, Blazor barındırma modellerinin her biri için uygulama geliştirmeye yönelik şablonlar sağlar:

* BlazorWebAssembly (`blazorwasm`)
* BlazorSunucu (`blazorserver`)

Barındırma modelleri hakkında Blazordaha fazla bilgi için bkz <xref:blazor/hosting-models>..

Şablondan Blazor uygulama oluşturmaya yönelik adım adım yönergeler için, bkz <xref:blazor/get-started>..

Şablon seçenekleri, bu `--help` seçeneği [DotNet New](/dotnet/core/tools/dotnet-new) CLI komutuna geçirerek kullanılabilir:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorproje yapısı

Aşağıdaki dosya ve klasörler bir Blazor Blazor şablondan oluşturulan bir uygulamayı yapar:

* *Program.cs* &ndash; , uygulamanın giriş noktasını şu şekilde ayarlar:

  * ASP.NET Core [ana bilgisayar](xref:fundamentals/host/generic-host) (Blazor sunucu)
  * WebAssembly Host (Blazor webassembly) &ndash; bu dosyadaki kod, Blazor webassembly şablonundan (`blazorwasm`) oluşturulan uygulamalar için benzersizdir.
    * Uygulamanın `App` kök bileşeni olan bileşeni, `app` `Add` yönteminin DOM öğesi olarak belirtilir.
    * Hizmetler, ana bilgisayar Oluşturucu 'daki `ConfigureServices` yöntemiyle yapılandırılabilir (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).
    * Yapılandırma, ana bilgisayar Oluşturucu (`builder.Configuration`) yoluyla sağlanabilir.

* *Startup.cs* (Blazor sunucu) &ndash; uygulamanın başlangıç mantığını içerir. `Startup` Sınıfı iki yöntemi tanımlar:

  * `ConfigureServices`&ndash; Uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır. Blazor Sunucu uygulamalarında, Hizmetleri çağırarak <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>eklenir ve `WeatherForecastService` örnek `FetchData` bileşen tarafından kullanılmak üzere hizmet kapsayıcısına eklenir.
  * `Configure`&ndash; Uygulamanın istek işleme ardışık düzenini yapılandırır:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>, tarayıcıya gerçek zamanlı bağlantı için bir uç nokta ayarlamak üzere çağırılır. Bağlantı, uygulamalarına gerçek zamanlı [SignalR](xref:signalr/introduction)Web işlevselliği ekleme çerçevesi olan ile oluşturulur.
    * [Mapfallbacktopage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) , uygulamanın kök sayfasını (*Pages/_Host. cshtml*) ayarlamak ve gezinmeyi etkinleştirmek için çağırılır.

* *Wwwroot/index.html* (Blazor webassembly) &ndash; bir HTML sayfası olarak uygulanan uygulamanın kök sayfası:
  * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
  * Bu sayfa, kök `App` bileşenin nerede işleneceğini belirtir. `App` Bileşen (*app. Razor*), içindeki `app` `AddComponent` `Startup.Configure`metoduna DOM öğesi olarak belirtilir.
  * `_framework/blazor.webassembly.js` JavaScript dosyası yüklenir ve şunları yapın:
    * .NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirir.
    * Uygulamayı çalıştırmak için çalışma zamanını başlatır.

* *App. Razor* &ndash; <xref:Microsoft.AspNetCore.Components.Routing.Router> bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın kök bileşeni. `Router` Bileşen tarayıcı gezintisini karşılar ve istenen adresle eşleşen sayfayı işler.

* *Sayfalar* klasörü &ndash; , bir Blazor sunucu uygulamasının Blazor uygulamasını ve kök Razor sayfasını oluşturan yönlendirilebilir bileşenleri/sayfaları (*. Razor*) içerir. Her sayfanın yolu, [`@page`](xref:mvc/views/razor#page) yönergesi kullanılarak belirtilir. Şablon şunları içerir:
  * *_Host. cshtml* (Blazor sunucu) &ndash; bir Razor sayfa olarak uygulanan uygulamanın kök sayfası:
    * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
    * Tarayıcı `_framework/blazor.server.js` ve sunucu arasındaki gerçek zamanlı SignalR bağlantıyı ayarlayan JavaScript dosyası yüklenir.
    * Ana bilgisayar sayfası, kök `App` bileşeni 'Nin (*app. Razor*) nerede işleneceğini belirtir.
  * `Counter`(*Counter. Razor*) &ndash; sayaç sayfasını uygular.
  * `Error`(*Error. Razor*, Blazor yalnızca sunucu uygulaması) &ndash; Uygulamada işlenmeyen bir özel durum oluştuğunda işlenir.
  * `FetchData`(*Fetchdata. Razor*) &ndash; veri getirme sayfasını uygular.
  * `Index`(*Index. Razor*) &ndash; giriş sayfasını uygular.

* *Paylaşılan* klasör &ndash; , uygulama tarafından kullanılan diğer Kullanıcı Arabirimi bileşenlerini (*. Razor*) içerir:
  * `MainLayout`(*Mainlayout. Razor*) &ndash; uygulamanın Düzen bileşeni.
  * `NavMenu`(*Navmenu. Razor*) &ndash; kenar çubuğu gezintisini uygular. Diğer Razor bileşenlere yönelik gezinti bağlantılarını işleyen<xref:Microsoft.AspNetCore.Components.Routing.NavLink> [navlink bileşenini](xref:blazor/routing#navlink-component) () içerir. `NavLink` Bileşeni, bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.

* *_Imports. Razor* &ndash; , uygulamanın Razor bileşenlerine (*. Razor*) dahil etmek için, ad alanları için [`@using`](xref:mvc/views/razor#using) yönergeler gibi genel yönergeleri içerir.

* *Veri* klasörü (Blazor sunucu) &ndash; , `WeatherForecast` `WeatherForecastService` uygulamanın `FetchData` bileşenine örnek Hava durumu verileri sağlayan sınıfını ve uygulamasını içerir.

* uygulamanın genel statik varlıklarını içeren uygulamanın [Web kök](xref:fundamentals/index#web-root) klasörünü *Wwwroot* &ndash; .

* uygulama için *appSettings. JSON* Blazor ( &ndash; sunucu) yapılandırma ayarları.
