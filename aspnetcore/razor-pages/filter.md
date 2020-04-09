---
title: ASP.NET Core'daki Jilet Sayfaları için filtreleme yöntemleri
author: Rick-Anderson
description: ASP.NET Core'da Razor Pages için filtre yöntemlerini nasıl oluşturabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: cd772da8ed565bc779d8c6bcc7c9949a0c1c7c60
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660757"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>ASP.NET Core'daki Jilet Sayfaları için filtreleme yöntemleri

::: moniker range=">= aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor Page [filtreleri IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) ve [IAsyncPageFilter,](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) Razor Page işleyicisi çalıştırılmadan önce ve sonra Razor Page'in kod çalışmasına olanak tanır. Razor Page filtreleri [ASP.NET Core MVC eylem filtrelerine](xref:mvc/controllers/filters#action-filters)benzer, ancak tek tek sayfa işleyici yöntemlerine uygulanamamaktadır.

Jilet Sayfa filtreleri:

* İşleyici yöntemi seçildikten sonra kodu çalıştır, ancak model bağlama oluşmadan önce.
* İşleyici yöntemi yürütülmeden önce, model bağlama tamamlandıktan sonra kodu çalıştırın.
* İşleyici yöntemi çalıştırdıktan sonra kodu çalıştırın.
* Bir sayfada veya genel olarak uygulanabilir.
* Belirli sayfa işleyici yöntemlerine uygulanamaz.
* [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) (DI) tarafından doldurulan yapıcı bağımlılıklara sahip olabilir. Daha fazla bilgi için [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) ve [TypeFilterAttribute'a](/aspnet/core/mvc/controllers/filters#typefilterattribute)bakın.

Sayfa oluşturucular ve ara yazılımlar işleyici yöntemi uygulanmadan önce özel kodun yürütülmesini sağlarken, yalnızca Razor Page filtreleri sayfaya ve sayfaya <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> erişimi sağlar. Middleware erişimi vardır `HttpContext`, ama "sayfa bağlamı". Filtreler, ''ye erişim sağlayan türemiş bir <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> parametreye `HttpContext`sahiptir. Örneğin, [bir filtre özniteliği uygula](#ifa) örneği yanıta, oluşturucularla veya ara yazılımlarla yapılameyecek bir üstbilgi ekler.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Razor Page filtreleri, genel olarak veya sayfa düzeyinde uygulanabilen aşağıdaki yöntemleri sağlar:

* Senkron yöntemler:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Bir işleyici yöntemi seçildikten sonra çağrılır, ancak model bağlama gerçekleşmeden önce.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Işleyici yöntemi yürütülmeden önce, model bağlama tamamlandıktan sonra çağrılır.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Eylem sonucundan önce işleyici yöntemi çalıştırdıktan sonra çağrılır.

* Asenkron yöntemler:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : İşleyici yöntemi seçildikten sonra eşsenkronize olarak adlandırılır, ancak model bağlama gerçekleşmeden önce.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Model bağlama tamamlandıktan sonra işleyici yöntemi çağrılmadan önce eşsenkronize olarak adlandırılır.

Her **ikisini de** **değil,** filtre arabiriminin senkron veya async sürümünü uygulayın. Çerçeve, filtrenin async arabirimini uygulayıp uygulamadığını görmek için önce denetler ve eğer öyleyse, bunu çağırır. Değilse, senkron arabirimin yöntemi(ler) çağırır. Her iki arabirim de uygulanırsa, yalnızca async yöntemleri çağrılır. Aynı kural sayfalardaki geçersiz kılmalar için de geçerlidir, geçersiz kılmanın eşzamanlı veya eşitlenmiş sürümünü uygulamak, her ikisini de değil.

## <a name="implement-razor-page-filters-globally"></a>Razor Page filtrelerini genel olarak uygulayın

Aşağıdaki kod `IAsyncPageFilter`uygular:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

Önceki kodda, `ProcessUserAgent.Write` kullanıcı aracısı dizeilesiyle çalışan kullanıcı tarafından sağlanan kod vardır.

Aşağıdaki kod `SampleAsyncPageFilter` sınıfta sağlar: `Startup`

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> */Movies'deki*yalnızca `SampleAsyncPageFilter` sayfalara uygulamak için aşağıdaki kod çağrıları:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

Aşağıdaki kod senkron `IPageFilter`uyguluyor:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Aşağıdaki kod `SamplePageFilter`sağlar:

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Filtre yöntemlerini geçersiz kılarak Razor Page filtrelerini uygulayın

Aşağıdaki kod, eşzamanlı Jilet Sayfası filtrelerini geçersiz kılar:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Filtre özniteliği uygulama

Yerleşik öznitelik tabanlı filtre <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filtresi alt sınıflanabilir. Aşağıdaki filtre yanıta bir üstbilgi ekler:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Aşağıdaki kod özniteliği `AddHeader` uygular:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

Üstbilgiincelemek için tarayıcı geliştirici araçları gibi bir araç kullanın. **Yanıt Üstbilgi** `author: Rick` altında görüntülenir.

Bkz. Siparişin geçersiz kılınması yla ilgili yönergeler için [varsayılan sırayı geçersiz kılma.](xref:mvc/controllers/filters#overriding-the-default-order)

Bkz. Filtre boru hattını bir filtreden kısa devre yapmak için talimatlar için [İptal ve kısa devre.](xref:mvc/controllers/filters#cancellation-and-short-circuiting)

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Filtre özniteliğini yetkilendirme

[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) özniteliği aşağıdakilere `PageModel`uygulanabilir:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor Page [filtreleri IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) ve [IAsyncPageFilter,](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) Razor Page işleyicisi çalıştırılmadan önce ve sonra Razor Page'in kod çalışmasına olanak tanır. Razor Page filtreleri [ASP.NET Core MVC eylem filtrelerine](xref:mvc/controllers/filters#action-filters)benzer, ancak tek tek sayfa işleyici yöntemlerine uygulanamamaktadır.

Jilet Sayfa filtreleri:

* İşleyici yöntemi seçildikten sonra kodu çalıştır, ancak model bağlama oluşmadan önce.
* İşleyici yöntemi yürütülmeden önce, model bağlama tamamlandıktan sonra kodu çalıştırın.
* İşleyici yöntemi çalıştırdıktan sonra kodu çalıştırın.
* Bir sayfada veya genel olarak uygulanabilir.
* Belirli sayfa işleyici yöntemlerine uygulanamaz.

Kod, bir işleyici yöntemi sayfa oluşturucu veya ara yazılım kullanarak yürütülmeden önce çalıştırılabilir, ancak yalnızca Razor Page filtreleri [HttpContext'a](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext)erişebilir. Filtreler, [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) türetilmiş parametreye `HttpContext`erişebilir. Örneğin, [bir filtre özniteliği uygula](#ifa) örneği yanıta, oluşturucularla veya ara yazılımlarla yapılameyecek bir üstbilgi ekler.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Razor Page filtreleri, genel olarak veya sayfa düzeyinde uygulanabilen aşağıdaki yöntemleri sağlar:

* Senkron yöntemler:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Bir işleyici yöntemi seçildikten sonra çağrılır, ancak model bağlama gerçekleşmeden önce.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Işleyici yöntemi yürütülmeden önce, model bağlama tamamlandıktan sonra çağrılır.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Eylem sonucundan önce işleyici yöntemi çalıştırdıktan sonra çağrılır.

* Asenkron yöntemler:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : İşleyici yöntemi seçildikten sonra eşsenkronize olarak adlandırılır, ancak model bağlama gerçekleşmeden önce.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Model bağlama tamamlandıktan sonra işleyici yöntemi çağrılmadan önce eşsenkronize olarak adlandırılır.

> [!NOTE]
> Her **ikisini de** değil, filtre arabiriminin senkron veya async sürümünü uygulayın. Çerçeve, filtrenin async arabirimini uygulayıp uygulamadığını görmek için önce denetler ve eğer öyleyse, bunu çağırır. Değilse, senkron arabirimin yöntemi(ler) çağırır. Her iki arabirim de uygulanırsa, yalnızca async yöntemleri çağrılır. Aynı kural sayfalardaki geçersiz kılmalar için de geçerlidir, geçersiz kılmanın eşzamanlı veya eşitlenmiş sürümünü uygulamak, her ikisini de değil.

## <a name="implement-razor-page-filters-globally"></a>Razor Page filtrelerini genel olarak uygulayın

Aşağıdaki kod `IAsyncPageFilter`uygular:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

Önceki kodda, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) gerekli değildir. Uygulama için iz bilgileri sağlamak için örnekte kullanılır.

Aşağıdaki kod `SampleAsyncPageFilter` sınıfta sağlar: `Startup`

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

Aşağıdaki kod sınıfının `Startup` tamamını gösterir:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

`AddFolderApplicationModelConvention` */subFolder'daki*yalnızca `SampleAsyncPageFilter` sayfalara uygulamak için aşağıdaki kod çağrıları:

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

Aşağıdaki kod senkron `IPageFilter`uyguluyor:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Aşağıdaki kod `SamplePageFilter`sağlar:

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Filtre yöntemlerini geçersiz kılarak Razor Page filtrelerini uygulayın

Aşağıdaki kod eşzamanlı Razor Page filtrelerini geçersiz kılar:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Filtre özniteliği uygulama

Yerleşik öznitelik tabanlı filtre [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtresi alt sınıflanabilir. Aşağıdaki filtre yanıta bir üstbilgi ekler:

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Aşağıdaki kod özniteliği `AddHeader` uygular:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

Bkz. Siparişin geçersiz kılınması yla ilgili yönergeler için [varsayılan sırayı geçersiz kılma.](xref:mvc/controllers/filters#overriding-the-default-order)

Bkz. Filtre boru hattını bir filtreden kısa devre yapmak için talimatlar için [İptal ve kısa devre.](xref:mvc/controllers/filters#cancellation-and-short-circuiting) 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Filtre özniteliğini yetkilendirme

[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) özniteliği aşağıdakilere `PageModel`uygulanabilir:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
