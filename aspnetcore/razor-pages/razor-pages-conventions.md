---
title: RazorASP.NET Core 'de sayfa yönlendirme ve uygulama kuralları
author: rick-anderson
description: Yönlendirme ve uygulama modeli sağlayıcısı kurallarının sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemenize nasıl yardımcı olduğunu öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 308ca4401289a55e5dba8d61de50644cb2a53433
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405256"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>RazorASP.NET Core 'de sayfa yönlendirme ve uygulama kuralları

::: moniker range=">= aspnetcore-3.0"

Sayfalar uygulamalarında sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemek için sayfa [yolu ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin Razor .

Ayrı sayfalar için özel sayfa yolları yapılandırmanız gerektiğinde, bu konunun ilerleyen kısımlarında açıklanan [Addpageroute kuralına](#configure-a-page-route) sahip sayfalara yönlendirmeyi yapılandırın.

Bir sayfa yolu belirtmek, yol kesimleri eklemek veya bir rotaya parametre eklemek için, sayfanın `@page` yönergesini kullanın. Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes).

Yol kesimleri veya parametre adları olarak kullanılamayan ayrılmış sözcükler vardır. Daha fazla bilgi için bkz. [Yönlendirme: ayrılmış yönlendirme adları](xref:mvc/controllers/routing#reserved-routing-names).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

| Senaryo | Örnek gösterilmektedir... |
| -------- | --------------------------- |
| [Model kurallar](#model-conventions)<br><br>Kurallar. Add<ul><li>Ipageroutemodelconvention</li><li>Ipageapplicationmodelconvention</li><li>Ipagehandlermodelconvention</li></ul> | Uygulamanın sayfalarına bir yol şablonu ve üst bilgi ekleyin. |
| [Sayfa yolu eylem kuralları](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Bir klasördeki sayfalara ve tek bir sayfaya rota şablonu ekleyin. |
| [Sayfa modeli eylem kuralları](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</li></ul> | Bir klasördeki sayfalara üst bilgi ekleyin, tek bir sayfaya üst bilgi ekleyin ve bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) yapılandırarak uygulamanın sayfalarına üst bilgi ekleyin. |

RazorSayfa kuralları, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> sınıfındaki hizmet koleksiyonunda öğesine genişletme yöntemi kullanılarak eklenir ve yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` . Aşağıdaki kural örnekleri bu konunun ilerleyen kısımlarında açıklanmıştır:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Rota sırası

Rotalar bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> işlem için (rota eşleştirme) belirtir.

| Sipariş verme            | Davranış |
| :--------------: | -------- |
| -1               | Yol, diğer rotalar işlenmeden önce işlenir. |
| 0                | Sıra belirtilmemiş (varsayılan değer). Atama değil `Order` ( `Order = null` ), `Order` işleme için varsayılan yolu 0 (sıfır) olarak belirler. |
| 1, 2, &hellip; n | Yol işleme sırasını belirtir. |

Yol işleme, kurala göre belirlenir:

* Yollar sıralı sırada işlenir (-1, 0, 1, 2, &hellip; n).
* Yollar aynı olduğunda `Order` , en belirli yol önce daha az özel yollarla eşleştirilir.
* Aynı `Order` ve aynı parametre sayısına sahip rotalar bir Istek URL 'siyle eşleşiyorsa, rotalar öğesine eklendikleri sırada işlenir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .

Mümkünse, belirlenen bir yol işleme sırasına bağlı olarak kullanmaktan kaçının. Genellikle Yönlendirme, URL eşleştirme ile doğru yolu seçer. `Order`İstekleri doğru yönlendirmek için yol özelliklerini ayarlamanız gerekiyorsa, uygulamanın yönlendirme şeması büyük olasılıkla istemciler için kafa karıştırıcı olur ve bakım için kırıcı olur. Uygulamanın yönlendirme şemasını basitleştirecek şekilde arama yapın. Örnek uygulama, tek bir uygulama kullanarak birkaç yönlendirme senaryosunu göstermek için açık bir yol işleme sırası gerektirir. Ancak, üretim uygulamalarında rota ayarlama uygulamalarından kaçınmaya çalışmalısınız `Order` .

RazorSayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır. MVC konularındaki yol sırasıyla ilgili bilgiler, [Denetleyici eylemlerine yönlendirme sırasında mevcuttur: öznitelik yollarını sıralama](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Model kurallar

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>Sayfasına uygulanan [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için için bir temsilci ekleyin Razor .

### <a name="add-a-route-model-convention-to-all-pages"></a>Tüm sayfalara bir rota modeli kuralı ekleme

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa yönlendirme modeli oluşturma sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.

Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `1` . Bu, örnek uygulamada aşağıdaki yol eşleştirme davranışını sağlar:

* İçin bir yol şablonu `TheContactPage/{text?}` , konusuna daha sonra eklenir. Iletişim sayfası yolu, () varsayılan sırasına sahiptir `null` `Order = 0` , bu nedenle `{globalTemplate?}` yol şablonundan önce eşleşir.
* `{aboutTemplate?}`Konuya daha sonra bir yol şablonu eklenir. `{aboutTemplate?}`Şablonuna bir verilebilir `Order` `2` . Hakkında sayfası istendiğinde `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue", () içine () yüklenir.
* `{otherPagesTemplate?}`Konuya daha sonra bir yol şablonu eklenir. `{otherPagesTemplate?}`Şablonuna bir verilebilir `Order` `2` . *Sayfalar/otherpages* klasöründeki herhangi bir sayfa bir yol parametresi ile istendiğinde (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () öğesine yüklenir `Order = 2` `Order` .

Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` . Doğru yolu seçmek için yönlendirmeyi güvenin.

Razor<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>İçindeki hizmet KOLEKSIYONUNA MVC eklendiğinde ekleme gibi sayfa seçenekleri eklenir `Startup.ConfigureServices` . Örnek için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:

![Hakkında sayfası, GlobalRouteValue 'un rota segmentiyle istenir. İşlenen sayfa, yol veri değerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Tüm sayfalara uygulama modeli kuralı ekleme

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Oluşturma ve <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfa uygulama modeli oluşturma sırasında uygulanan örnek koleksiyonuna eklemek için kullanın.

Bu ve diğer kuralları konunun ilerleyen kısımlarında göstermek için, örnek uygulama bir `AddHeaderAttribute` sınıfı içerir. Sınıf Oluşturucusu bir `name` dize ve bir `values` dize dizisi kabul eder. Bu değerler, `OnResultExecuting` bir yanıt üst bilgisi ayarlamak için yönteminde kullanılır. Tam sınıf, konusunun ilerleyen kısımlarında [sayfa modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.

Örnek uygulama, `AddHeaderAttribute` `GlobalHeader` uygulama içindeki tüm sayfalara üst bilgi eklemek için sınıfını kullanır:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Hakkında sayfasının yanıt üstbilgileri GlobalHeader 'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Tüm sayfalara bir işleyici modeli kuralı ekleme

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa işleyici modelinin oluşturulması sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Sayfa yolu eylem kuralları

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>Sayfa yollarının yapılandırılması için genişletilebilirlik noktaları sağlamak üzere tasarlanan, çağıran kurallarından türetilen varsayılan yol modeli sağlayıcısı.

### <a name="folder-route-model-convention"></a>Klasör Yönlendirme modeli kuralı

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Belirtilen klasörün altındaki tüm sayfalar için üzerinde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *diğer sayfalar* klasöründeki sayfalara bir yol şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` . Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar. *Sayfalar/otherpages* klasöründeki bir sayfa bir rota parametresi değeriyle isteniyorsa (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () içine yüklenir `Order = 2` `Order` .

Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` . Doğru yolu seçmek için yönlendirmeyi güvenin.

Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:

![Diğer sayfalar klasöründeki Sayfa1, GlobalRouteValue ve OtherPagesRouteValue yol kesimiyle istenir. İşlenen sayfa, yol veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Sayfa yönlendirme modeli kuralı

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek uygulama, `AddPageRouteModelConvention` `{aboutTemplate?}` hakkında sayfasına bir yol şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` . Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar. Hakkında sayfasında bir yol parametresi değeri varsa `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue" () içine () yüklenir.

Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` . Doğru yolu seçmek için yönlendirmeyi güvenin.

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:

![GlobalRouteValue ve AboutRouteValue için rota kesimlerle ilgili sayfa hakkında bilgi istenir. İşlenen sayfa, yol veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Sayfa yollarını özelleştirmek için bir parametre transformatörü kullanın

ASP.NET Core tarafından oluşturulan sayfa yolları, bir parametre transformatörü kullanılarak özelleştirilebilir. Bir parametre transformatörü `IOutboundParameterTransformer` , parametrelerinin değerini uygular ve dönüştürür. Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini olarak değiştirir `subscription-management` .

`PageRouteTransformerConvention`Sayfa yolu modeli kuralı, bir uygulamadaki otomatik olarak oluşturulan sayfa yollarının klasör ve dosya adı kesimlerine bir parametre transformatörü uygular. Örneğin, Razor */Pages/subscriptionmanagement/viewAll.exe* konumundaki sayfa dosyası, yolu ' den ' e yeniden yazıldı `/SubscriptionManagement/ViewAll` `/subscription-management/view-all` .

`PageRouteTransformerConvention`yalnızca Razor sayfalar klasöründen ve dosya adından gelen bir sayfa yolunun otomatik olarak oluşturulan segmentlerini dönüştürür. Yönergeyle eklenen yol parçalarını dönüştürmez `@page` . Kural, tarafından eklenen yolları da dönüştürmez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .

, `PageRouteTransformerConvention` İçinde bir seçenek olarak kaydedilir `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a>Sayfa yolu yapılandırma

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>Belirtilen sayfa yolundaki sayfaya bir yol yapılandırmak için kullanın. Sayfa için oluşturulan bağlantılar belirtilen rotayı kullanır. `AddPageRoute``AddPageRouteModelConvention`yolu oluşturmak için kullanır.

Örnek uygulama, `/TheContactPage` *Contact. cshtml*için bir yol oluşturur:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

Iletişim sayfasına, `/Contact` varsayılan yolu üzerinden de erişilebilir.

Örnek uygulamanın kişi sayfasına özel yolu, isteğe bağlı bir `text` yol segmentine () izin verir `{text?}` . Bu sayfa, `@page` ziyaretçinin yolunda sayfaya erişmesi durumunda bu isteğe bağlı segmenti de içerir `/Contact` :

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

İşlenmiş sayfadaki **kişi** bağlantısı IÇIN oluşturulan URL 'nin güncelleştirilmiş yolu yansıttığını unutmayın:

![Gezinti çubuğundaki örnek uygulama Iletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenmiş HTML 'deki kişi bağlantısının inceleniyor href 'in '/theContact tpage ' olarak ayarlandığını belirtir](razor-pages-conventions/_static/contact-link-source.png)

Kendi normal rotasında, veya özel rotada kişi sayfasını ziyaret edin `/Contact` `/TheContactPage` . Ek bir `text` Rota kesimi sağlarsanız, sayfada SAĞLADıĞıNıZ HTML kodlu segment görüntülenir:

![Edge tarayıcı, URL 'de ' TextValue ' öğesinin isteğe bağlı ' text ' yol segmentini sağlamaya yönelik örnek. İşlenmiş sayfa ' metin ' segmenti değerini gösterir.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Sayfa modeli eylem kuralları

Uygulayan varsayılan sayfa modeli sağlayıcısı, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanan kuralları çağırır. Bu kurallar sayfa bulma ve işleme senaryolarını oluştururken ve değiştirirken yararlıdır.

Bu bölümdeki örneklerde, örnek uygulama `AddHeaderAttribute` bir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> yanıt üst bilgisi uygulayan olan bir sınıfı kullanır:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Kurallar kullanılarak, örnek bir klasördeki tüm sayfalara ve tek bir sayfaya özniteliğin nasıl uygulanacağını gösterir.

**Klasör uygulama modeli kuralı**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Belirtilen klasör altındaki tüm sayfalar için örneklere bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, uygulamasının, `AddFolderApplicationModelConvention` `OtherPagesHeader` uygulamanın *diğer sayfalar* klasörünün içindeki sayfalara bir başlık ekleyerek kullanımını gösterir:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Diğer sayfalar/Sayfa1 sayfasının yanıt üstbilgileri, OtherPagesHeader öğesinin eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Sayfa uygulama modeli kuralı**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, `AddPageApplicationModelConvention` hakkında sayfasına bir başlık ekleyerek öğesinin kullanımını gösterir `AboutHeader` :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Hakkında sayfasının yanıt üstbilgileri AboutHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

**Filtre yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Belirtilen filtreyi uygulamak için yapılandırır. Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama bir lambda ifadesinde bir filtrenin nasıl uygulanacağını gösterir, bu da bir filtre döndüren bir fabrika olarak arka planda uygulandı:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Sayfa uygulama modeli, *diğer sayfalar* klasöründeki Page2 sayfasına yol açan parçaların göreli yolunu denetlemek için kullanılır. Koşul geçerse, bir üst bilgi eklenir. Aksi takdirde, `EmptyFilter` uygulanır.

`EmptyFilter`bir [eylem filtresidir](xref:mvc/controllers/filters#action-filters). Eylem filtreleri sayfalar tarafından yoksayıldığından, Razor `EmptyFilter` yol içermiyorsa hiçbir etkisi yoktur `OtherPages/Page2` .

Örnek Page2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üst bilgileri inceleyin:

![OtherPagesPage2Header, Page2 için yanıta eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

**Filtre fabrikası yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm sayfalara [filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikası yapılandırır Razor .

Örnek uygulama, uygulamanın sayfalarına iki değerli bir başlık ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanmanın bir örneğini sağlar `FilterFactoryHeader` :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Hakkında sayfasının yanıt üstbilgileri iki FilterFactoryHeader üst bilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC filtreleri ve sayfa filtresi (ıpagefilter)

[Action filters](xref:mvc/controllers/filters#action-filters) Razor Sayfalar işleyici YÖNTEMLERI kullandığından, MVC eylem filtreleri sayfalar tarafından yok sayılır Razor . Diğer MVC filtresi türleri şunlardır: [Yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters)ve [sonuç](xref:mvc/controllers/filters#result-filters). Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.

Sayfa filtresi ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ), sayfalar için geçerli olan bir filtredir Razor . Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Sayfalar uygulamalarında sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemek için sayfa [yolu ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin Razor .

Ayrı sayfalar için özel sayfa yolları yapılandırmanız gerektiğinde, bu konunun ilerleyen kısımlarında açıklanan [Addpageroute kuralına](#configure-a-page-route) sahip sayfalara yönlendirmeyi yapılandırın.

Bir sayfa yolu belirtmek, yol kesimleri eklemek veya bir rotaya parametre eklemek için, sayfanın `@page` yönergesini kullanın. Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes).

Yol kesimleri veya parametre adları olarak kullanılamayan ayrılmış sözcükler vardır. Daha fazla bilgi için bkz. [Yönlendirme: ayrılmış yönlendirme adları](xref:fundamentals/routing#reserved-routing-names).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

| Senaryo | Örnek gösterilmektedir... |
| -------- | --------------------------- |
| [Model kurallar](#model-conventions)<br><br>Kurallar. Add<ul><li>Ipageroutemodelconvention</li><li>Ipageapplicationmodelconvention</li><li>Ipagehandlermodelconvention</li></ul> | Uygulamanın sayfalarına bir yol şablonu ve üst bilgi ekleyin. |
| [Sayfa yolu eylem kuralları](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Bir klasördeki sayfalara ve tek bir sayfaya rota şablonu ekleyin. |
| [Sayfa modeli eylem kuralları](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</li></ul> | Bir klasördeki sayfalara üst bilgi ekleyin, tek bir sayfaya üst bilgi ekleyin ve bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) yapılandırarak uygulamanın sayfalarına üst bilgi ekleyin. |

RazorSayfa kuralları, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> sınıfındaki hizmet koleksiyonunda öğesine genişletme yöntemi kullanılarak eklenir ve yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` . Aşağıdaki kural örnekleri bu konunun ilerleyen kısımlarında açıklanmıştır:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Rota sırası

Rotalar bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> işlem için (rota eşleştirme) belirtir.

| Sipariş verme            | Davranış |
| :--------------: | -------- |
| -1               | Yol, diğer rotalar işlenmeden önce işlenir. |
| 0                | Sıra belirtilmemiş (varsayılan değer). Atama değil `Order` ( `Order = null` ), `Order` işleme için varsayılan yolu 0 (sıfır) olarak belirler. |
| 1, 2, &hellip; n | Yol işleme sırasını belirtir. |

Yol işleme, kurala göre belirlenir:

* Yollar sıralı sırada işlenir (-1, 0, 1, 2, &hellip; n).
* Yollar aynı olduğunda `Order` , en belirli yol önce daha az özel yollarla eşleştirilir.
* Aynı `Order` ve aynı parametre sayısına sahip rotalar bir Istek URL 'siyle eşleşiyorsa, rotalar öğesine eklendikleri sırada işlenir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .

Mümkünse, belirlenen bir yol işleme sırasına bağlı olarak kullanmaktan kaçının. Genellikle Yönlendirme, URL eşleştirme ile doğru yolu seçer. `Order`İstekleri doğru yönlendirmek için yol özelliklerini ayarlamanız gerekiyorsa, uygulamanın yönlendirme şeması büyük olasılıkla istemciler için kafa karıştırıcı olur ve bakım için kırıcı olur. Uygulamanın yönlendirme şemasını basitleştirecek şekilde arama yapın. Örnek uygulama, tek bir uygulama kullanarak birkaç yönlendirme senaryosunu göstermek için açık bir yol işleme sırası gerektirir. Ancak, üretim uygulamalarında rota ayarlama uygulamalarından kaçınmaya çalışmalısınız `Order` .

RazorSayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır. MVC konularındaki yol sırasıyla ilgili bilgiler, [Denetleyici eylemlerine yönlendirme sırasında mevcuttur: öznitelik yollarını sıralama](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Model kurallar

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>Sayfasına uygulanan [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için için bir temsilci ekleyin Razor .

### <a name="add-a-route-model-convention-to-all-pages"></a>Tüm sayfalara bir rota modeli kuralı ekleme

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa yönlendirme modeli oluşturma sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.

Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `1` . Bu, örnek uygulamada aşağıdaki yol eşleştirme davranışını sağlar:

* İçin bir yol şablonu `TheContactPage/{text?}` , konusuna daha sonra eklenir. Iletişim sayfası yolu, () varsayılan sırasına sahiptir `null` `Order = 0` , bu nedenle `{globalTemplate?}` yol şablonundan önce eşleşir.
* `{aboutTemplate?}`Konuya daha sonra bir yol şablonu eklenir. `{aboutTemplate?}`Şablonuna bir verilebilir `Order` `2` . Hakkında sayfası istendiğinde `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue", () içine () yüklenir.
* `{otherPagesTemplate?}`Konuya daha sonra bir yol şablonu eklenir. `{otherPagesTemplate?}`Şablonuna bir verilebilir `Order` `2` . *Sayfalar/otherpages* klasöründeki herhangi bir sayfa bir yol parametresi ile istendiğinde (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () öğesine yüklenir `Order = 2` `Order` .

Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` . Doğru yolu seçmek için yönlendirmeyi güvenin.

Razor<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>İçindeki hizmet KOLEKSIYONUNA MVC eklendiğinde ekleme gibi sayfa seçenekleri eklenir `Startup.ConfigureServices` . Örnek için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:

![Hakkında sayfası, GlobalRouteValue 'un rota segmentiyle istenir. İşlenen sayfa, yol veri değerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Tüm sayfalara uygulama modeli kuralı ekleme

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Oluşturma ve <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfa uygulama modeli oluşturma sırasında uygulanan örnek koleksiyonuna eklemek için kullanın.

Bu ve diğer kuralları konunun ilerleyen kısımlarında göstermek için, örnek uygulama bir `AddHeaderAttribute` sınıfı içerir. Sınıf Oluşturucusu bir `name` dize ve bir `values` dize dizisi kabul eder. Bu değerler, `OnResultExecuting` bir yanıt üst bilgisi ayarlamak için yönteminde kullanılır. Tam sınıf, konusunun ilerleyen kısımlarında [sayfa modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.

Örnek uygulama, `AddHeaderAttribute` `GlobalHeader` uygulama içindeki tüm sayfalara üst bilgi eklemek için sınıfını kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Hakkında sayfasının yanıt üstbilgileri GlobalHeader 'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Tüm sayfalara bir işleyici modeli kuralı ekleme

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa işleyici modelinin oluşturulması sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Sayfa yolu eylem kuralları

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>Sayfa yollarının yapılandırılması için genişletilebilirlik noktaları sağlamak üzere tasarlanan, çağıran kurallarından türetilen varsayılan yol modeli sağlayıcısı.

### <a name="folder-route-model-convention"></a>Klasör Yönlendirme modeli kuralı

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Belirtilen klasörün altındaki tüm sayfalar için üzerinde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *diğer sayfalar* klasöründeki sayfalara bir yol şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` . Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar. *Sayfalar/otherpages* klasöründeki bir sayfa bir rota parametresi değeriyle isteniyorsa (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () içine yüklenir `Order = 2` `Order` .

Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` . Doğru yolu seçmek için yönlendirmeyi güvenin.

Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:

![Diğer sayfalar klasöründeki Sayfa1, GlobalRouteValue ve OtherPagesRouteValue yol kesimiyle istenir. İşlenen sayfa, yol veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Sayfa yönlendirme modeli kuralı

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek uygulama, `AddPageRouteModelConvention` `{aboutTemplate?}` hakkında sayfasına bir yol şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` . Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar. Hakkında sayfasında bir yol parametresi değeri varsa `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue" () içine () yüklenir.

Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` . Doğru yolu seçmek için yönlendirmeyi güvenin.

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:

![GlobalRouteValue ve AboutRouteValue için rota kesimlerle ilgili sayfa hakkında bilgi istenir. İşlenen sayfa, yol veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Sayfa yollarını özelleştirmek için bir parametre transformatörü kullanın

ASP.NET Core tarafından oluşturulan sayfa yolları, bir parametre transformatörü kullanılarak özelleştirilebilir. Bir parametre transformatörü `IOutboundParameterTransformer` , parametrelerinin değerini uygular ve dönüştürür. Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini olarak değiştirir `subscription-management` .

`PageRouteTransformerConvention`Sayfa yolu modeli kuralı, bir uygulamadaki otomatik olarak oluşturulan sayfa yollarının klasör ve dosya adı kesimlerine bir parametre transformatörü uygular. Örneğin, Razor */Pages/subscriptionmanagement/viewAll.exe* konumundaki sayfa dosyası, yolu ' den ' e yeniden yazıldı `/SubscriptionManagement/ViewAll` `/subscription-management/view-all` .

`PageRouteTransformerConvention`yalnızca Razor sayfalar klasöründen ve dosya adından gelen bir sayfa yolunun otomatik olarak oluşturulan segmentlerini dönüştürür. Yönergeyle eklenen yol parçalarını dönüştürmez `@page` . Kural, tarafından eklenen yolları da dönüştürmez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .

, `PageRouteTransformerConvention` İçinde bir seçenek olarak kaydedilir `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a>Sayfa yolu yapılandırma

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>Belirtilen sayfa yolundaki sayfaya bir yol yapılandırmak için kullanın. Sayfa için oluşturulan bağlantılar belirtilen rotayı kullanır. `AddPageRoute``AddPageRouteModelConvention`yolu oluşturmak için kullanır.

Örnek uygulama, `/TheContactPage` *Contact. cshtml*için bir yol oluşturur:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Iletişim sayfasına, `/Contact` varsayılan yolu üzerinden de erişilebilir.

Örnek uygulamanın kişi sayfasına özel yolu, isteğe bağlı bir `text` yol segmentine () izin verir `{text?}` . Bu sayfa, `@page` ziyaretçinin yolunda sayfaya erişmesi durumunda bu isteğe bağlı segmenti de içerir `/Contact` :

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

İşlenmiş sayfadaki **kişi** bağlantısı IÇIN oluşturulan URL 'nin güncelleştirilmiş yolu yansıttığını unutmayın:

![Gezinti çubuğundaki örnek uygulama Iletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenmiş HTML 'deki kişi bağlantısının inceleniyor href 'in '/theContact tpage ' olarak ayarlandığını belirtir](razor-pages-conventions/_static/contact-link-source.png)

Kendi normal rotasında, veya özel rotada kişi sayfasını ziyaret edin `/Contact` `/TheContactPage` . Ek bir `text` Rota kesimi sağlarsanız, sayfada SAĞLADıĞıNıZ HTML kodlu segment görüntülenir:

![Edge tarayıcı, URL 'de ' TextValue ' öğesinin isteğe bağlı ' text ' yol segmentini sağlamaya yönelik örnek. İşlenmiş sayfa ' metin ' segmenti değerini gösterir.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Sayfa modeli eylem kuralları

Uygulayan varsayılan sayfa modeli sağlayıcısı, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanan kuralları çağırır. Bu kurallar sayfa bulma ve işleme senaryolarını oluştururken ve değiştirirken yararlıdır.

Bu bölümdeki örneklerde, örnek uygulama `AddHeaderAttribute` bir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> yanıt üst bilgisi uygulayan olan bir sınıfı kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Kurallar kullanılarak, örnek bir klasördeki tüm sayfalara ve tek bir sayfaya özniteliğin nasıl uygulanacağını gösterir.

**Klasör uygulama modeli kuralı**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Belirtilen klasör altındaki tüm sayfalar için örneklere bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, uygulamasının, `AddFolderApplicationModelConvention` `OtherPagesHeader` uygulamanın *diğer sayfalar* klasörünün içindeki sayfalara bir başlık ekleyerek kullanımını gösterir:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Diğer sayfalar/Sayfa1 sayfasının yanıt üstbilgileri, OtherPagesHeader öğesinin eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Sayfa uygulama modeli kuralı**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, `AddPageApplicationModelConvention` hakkında sayfasına bir başlık ekleyerek öğesinin kullanımını gösterir `AboutHeader` :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Hakkında sayfasının yanıt üstbilgileri AboutHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

**Filtre yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Belirtilen filtreyi uygulamak için yapılandırır. Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama bir lambda ifadesinde bir filtrenin nasıl uygulanacağını gösterir, bu da bir filtre döndüren bir fabrika olarak arka planda uygulandı:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Sayfa uygulama modeli, *diğer sayfalar* klasöründeki Page2 sayfasına yol açan parçaların göreli yolunu denetlemek için kullanılır. Koşul geçerse, bir üst bilgi eklenir. Aksi takdirde, `EmptyFilter` uygulanır.

`EmptyFilter`bir [eylem filtresidir](xref:mvc/controllers/filters#action-filters). Eylem filtreleri sayfalar tarafından yoksayıldığından, Razor `EmptyFilter` yol içermiyorsa hiçbir etkisi yoktur `OtherPages/Page2` .

Örnek Page2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üst bilgileri inceleyin:

![OtherPagesPage2Header, Page2 için yanıta eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

**Filtre fabrikası yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm sayfalara [filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikası yapılandırır Razor .

Örnek uygulama, uygulamanın sayfalarına iki değerli bir başlık ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanmanın bir örneğini sağlar `FilterFactoryHeader` :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Hakkında sayfasının yanıt üstbilgileri iki FilterFactoryHeader üst bilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC filtreleri ve sayfa filtresi (ıpagefilter)

[Action filters](xref:mvc/controllers/filters#action-filters) Razor Sayfalar işleyici YÖNTEMLERI kullandığından, MVC eylem filtreleri sayfalar tarafından yok sayılır Razor . Diğer MVC filtresi türleri şunlardır: [Yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters)ve [sonuç](xref:mvc/controllers/filters#result-filters). Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.

Sayfa filtresi ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ), sayfalar için geçerli olan bir filtredir Razor . Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Sayfalar uygulamalarında sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemek için sayfa [yolu ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin Razor .

Ayrı sayfalar için özel sayfa yolları yapılandırmanız gerektiğinde, bu konunun ilerleyen kısımlarında açıklanan [Addpageroute kuralına](#configure-a-page-route) sahip sayfalara yönlendirmeyi yapılandırın.

Bir sayfa yolu belirtmek, yol kesimleri eklemek veya bir rotaya parametre eklemek için, sayfanın `@page` yönergesini kullanın. Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes).

Yol kesimleri veya parametre adları olarak kullanılamayan ayrılmış sözcükler vardır. Daha fazla bilgi için bkz. [Yönlendirme: ayrılmış yönlendirme adları](xref:fundamentals/routing#reserved-routing-names).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

| Senaryo | Örnek gösterilmektedir... |
| -------- | --------------------------- |
| [Model kurallar](#model-conventions)<br><br>Kurallar. Add<ul><li>Ipageroutemodelconvention</li><li>Ipageapplicationmodelconvention</li><li>Ipagehandlermodelconvention</li></ul> | Uygulamanın sayfalarına bir yol şablonu ve üst bilgi ekleyin. |
| [Sayfa yolu eylem kuralları](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Bir klasördeki sayfalara ve tek bir sayfaya rota şablonu ekleyin. |
| [Sayfa modeli eylem kuralları](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</li></ul> | Bir klasördeki sayfalara üst bilgi ekleyin, tek bir sayfaya üst bilgi ekleyin ve bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) yapılandırarak uygulamanın sayfalarına üst bilgi ekleyin. |

RazorSayfa kuralları, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> sınıfındaki hizmet koleksiyonunda öğesine genişletme yöntemi kullanılarak eklenir ve yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` . Aşağıdaki kural örnekleri bu konunun ilerleyen kısımlarında açıklanmıştır:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Rota sırası

Rotalar bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> işlem için (rota eşleştirme) belirtir.

| Sipariş verme            | Davranış |
| :--------------: | -------- |
| -1               | Yol, diğer rotalar işlenmeden önce işlenir. |
| 0                | Sıra belirtilmemiş (varsayılan değer). Atama değil `Order` ( `Order = null` ), `Order` işleme için varsayılan yolu 0 (sıfır) olarak belirler. |
| 1, 2, &hellip; n | Yol işleme sırasını belirtir. |

Yol işleme, kurala göre belirlenir:

* Yollar sıralı sırada işlenir (-1, 0, 1, 2, &hellip; n).
* Yollar aynı olduğunda `Order` , en belirli yol önce daha az özel yollarla eşleştirilir.
* Aynı `Order` ve aynı parametre sayısına sahip rotalar bir Istek URL 'siyle eşleşiyorsa, rotalar öğesine eklendikleri sırada işlenir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .

Mümkünse, belirlenen bir yol işleme sırasına bağlı olarak kullanmaktan kaçının. Genellikle Yönlendirme, URL eşleştirme ile doğru yolu seçer. `Order`İstekleri doğru yönlendirmek için yol özelliklerini ayarlamanız gerekiyorsa, uygulamanın yönlendirme şeması büyük olasılıkla istemciler için kafa karıştırıcı olur ve bakım için kırıcı olur. Uygulamanın yönlendirme şemasını basitleştirecek şekilde arama yapın. Örnek uygulama, tek bir uygulama kullanarak birkaç yönlendirme senaryosunu göstermek için açık bir yol işleme sırası gerektirir. Ancak, üretim uygulamalarında rota ayarlama uygulamalarından kaçınmaya çalışmalısınız `Order` .

RazorSayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır. MVC konularındaki yol sırasıyla ilgili bilgiler, [Denetleyici eylemlerine yönlendirme sırasında mevcuttur: öznitelik yollarını sıralama](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Model kurallar

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>Sayfasına uygulanan [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için için bir temsilci ekleyin Razor .

### <a name="add-a-route-model-convention-to-all-pages"></a>Tüm sayfalara bir rota modeli kuralı ekleme

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa yönlendirme modeli oluşturma sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.

Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `1` . Bu, örnek uygulamada aşağıdaki yol eşleştirme davranışını sağlar:

* İçin bir yol şablonu `TheContactPage/{text?}` , konusuna daha sonra eklenir. Iletişim sayfası yolu, () varsayılan sırasına sahiptir `null` `Order = 0` , bu nedenle `{globalTemplate?}` yol şablonundan önce eşleşir.
* `{aboutTemplate?}`Konuya daha sonra bir yol şablonu eklenir. `{aboutTemplate?}`Şablonuna bir verilebilir `Order` `2` . Hakkında sayfası istendiğinde `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue", () içine () yüklenir.
* `{otherPagesTemplate?}`Konuya daha sonra bir yol şablonu eklenir. `{otherPagesTemplate?}`Şablonuna bir verilebilir `Order` `2` . *Sayfalar/otherpages* klasöründeki herhangi bir sayfa bir yol parametresi ile istendiğinde (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () öğesine yüklenir `Order = 2` `Order` .

Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` . Doğru yolu seçmek için yönlendirmeyi güvenin.

Razor<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>İçindeki hizmet KOLEKSIYONUNA MVC eklendiğinde ekleme gibi sayfa seçenekleri eklenir `Startup.ConfigureServices` . Örnek için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:

![Hakkında sayfası, GlobalRouteValue 'un rota segmentiyle istenir. İşlenen sayfa, yol veri değerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Tüm sayfalara uygulama modeli kuralı ekleme

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Oluşturma ve <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfa uygulama modeli oluşturma sırasında uygulanan örnek koleksiyonuna eklemek için kullanın.

Bu ve diğer kuralları konunun ilerleyen kısımlarında göstermek için, örnek uygulama bir `AddHeaderAttribute` sınıfı içerir. Sınıf Oluşturucusu bir `name` dize ve bir `values` dize dizisi kabul eder. Bu değerler, `OnResultExecuting` bir yanıt üst bilgisi ayarlamak için yönteminde kullanılır. Tam sınıf, konusunun ilerleyen kısımlarında [sayfa modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.

Örnek uygulama, `AddHeaderAttribute` `GlobalHeader` uygulama içindeki tüm sayfalara üst bilgi eklemek için sınıfını kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Hakkında sayfasının yanıt üstbilgileri GlobalHeader 'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Tüm sayfalara bir işleyici modeli kuralı ekleme

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa işleyici modelinin oluşturulması sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Sayfa yolu eylem kuralları

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>Sayfa yollarının yapılandırılması için genişletilebilirlik noktaları sağlamak üzere tasarlanan, çağıran kurallarından türetilen varsayılan yol modeli sağlayıcısı.

### <a name="folder-route-model-convention"></a>Klasör Yönlendirme modeli kuralı

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Belirtilen klasörün altındaki tüm sayfalar için üzerinde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *diğer sayfalar* klasöründeki sayfalara bir yol şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` . Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar. *Sayfalar/otherpages* klasöründeki bir sayfa bir rota parametresi değeriyle isteniyorsa (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () içine yüklenir `Order = 2` `Order` .

Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` . Doğru yolu seçmek için yönlendirmeyi güvenin.

Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:

![Diğer sayfalar klasöründeki Sayfa1, GlobalRouteValue ve OtherPagesRouteValue yol kesimiyle istenir. İşlenen sayfa, yol veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Sayfa yönlendirme modeli kuralı

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek uygulama, `AddPageRouteModelConvention` `{aboutTemplate?}` hakkında sayfasına bir yol şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` . Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar. Hakkında sayfasında bir yol parametresi değeri varsa `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue" () içine () yüklenir.

Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` . Doğru yolu seçmek için yönlendirmeyi güvenin.

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:

![GlobalRouteValue ve AboutRouteValue için rota kesimlerle ilgili sayfa hakkında bilgi istenir. İşlenen sayfa, yol veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Sayfa yolu yapılandırma

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>Belirtilen sayfa yolundaki sayfaya bir yol yapılandırmak için kullanın. Sayfa için oluşturulan bağlantılar belirtilen rotayı kullanır. `AddPageRoute``AddPageRouteModelConvention`yolu oluşturmak için kullanır.

Örnek uygulama, `/TheContactPage` *Contact. cshtml*için bir yol oluşturur:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Iletişim sayfasına, `/Contact` varsayılan yolu üzerinden de erişilebilir.

Örnek uygulamanın kişi sayfasına özel yolu, isteğe bağlı bir `text` yol segmentine () izin verir `{text?}` . Bu sayfa, `@page` ziyaretçinin yolunda sayfaya erişmesi durumunda bu isteğe bağlı segmenti de içerir `/Contact` :

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

İşlenmiş sayfadaki **kişi** bağlantısı IÇIN oluşturulan URL 'nin güncelleştirilmiş yolu yansıttığını unutmayın:

![Gezinti çubuğundaki örnek uygulama Iletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenmiş HTML 'deki kişi bağlantısının inceleniyor href 'in '/theContact tpage ' olarak ayarlandığını belirtir](razor-pages-conventions/_static/contact-link-source.png)

Kendi normal rotasında, veya özel rotada kişi sayfasını ziyaret edin `/Contact` `/TheContactPage` . Ek bir `text` Rota kesimi sağlarsanız, sayfada SAĞLADıĞıNıZ HTML kodlu segment görüntülenir:

![Edge tarayıcı, URL 'de ' TextValue ' öğesinin isteğe bağlı ' text ' yol segmentini sağlamaya yönelik örnek. İşlenmiş sayfa ' metin ' segmenti değerini gösterir.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Sayfa modeli eylem kuralları

Uygulayan varsayılan sayfa modeli sağlayıcısı, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanan kuralları çağırır. Bu kurallar sayfa bulma ve işleme senaryolarını oluştururken ve değiştirirken yararlıdır.

Bu bölümdeki örneklerde, örnek uygulama `AddHeaderAttribute` bir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> yanıt üst bilgisi uygulayan olan bir sınıfı kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Kurallar kullanılarak, örnek bir klasördeki tüm sayfalara ve tek bir sayfaya özniteliğin nasıl uygulanacağını gösterir.

**Klasör uygulama modeli kuralı**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Belirtilen klasör altındaki tüm sayfalar için örneklere bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, uygulamasının, `AddFolderApplicationModelConvention` `OtherPagesHeader` uygulamanın *diğer sayfalar* klasörünün içindeki sayfalara bir başlık ekleyerek kullanımını gösterir:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Diğer sayfalar/Sayfa1 sayfasının yanıt üstbilgileri, OtherPagesHeader öğesinin eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Sayfa uygulama modeli kuralı**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, `AddPageApplicationModelConvention` hakkında sayfasına bir başlık ekleyerek öğesinin kullanımını gösterir `AboutHeader` :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Hakkında sayfasının yanıt üstbilgileri AboutHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

**Filtre yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Belirtilen filtreyi uygulamak için yapılandırır. Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama bir lambda ifadesinde bir filtrenin nasıl uygulanacağını gösterir, bu da bir filtre döndüren bir fabrika olarak arka planda uygulandı:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Sayfa uygulama modeli, *diğer sayfalar* klasöründeki Page2 sayfasına yol açan parçaların göreli yolunu denetlemek için kullanılır. Koşul geçerse, bir üst bilgi eklenir. Aksi takdirde, `EmptyFilter` uygulanır.

`EmptyFilter`bir [eylem filtresidir](xref:mvc/controllers/filters#action-filters). Eylem filtreleri sayfalar tarafından yoksayıldığından, Razor `EmptyFilter` yol içermiyorsa hiçbir etkisi yoktur `OtherPages/Page2` .

Örnek Page2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üst bilgileri inceleyin:

![OtherPagesPage2Header, Page2 için yanıta eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

**Filtre fabrikası yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm sayfalara [filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikası yapılandırır Razor .

Örnek uygulama, uygulamanın sayfalarına iki değerli bir başlık ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanmanın bir örneğini sağlar `FilterFactoryHeader` :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:

![Hakkında sayfasının yanıt üstbilgileri iki FilterFactoryHeader üst bilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC filtreleri ve sayfa filtresi (ıpagefilter)

[Action filters](xref:mvc/controllers/filters#action-filters) Razor Sayfalar işleyici YÖNTEMLERI kullandığından, MVC eylem filtreleri sayfalar tarafından yok sayılır Razor . Diğer MVC filtresi türleri şunlardır: [Yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters)ve [sonuç](xref:mvc/controllers/filters#result-filters). Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.

Sayfa filtresi ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ), sayfalar için geçerli olan bir filtredir Razor . Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
