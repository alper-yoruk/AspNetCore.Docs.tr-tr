---
title: ASP.NET Core'da Razor Pages rota ve uygulama kuralları
author: rick-anderson
description: Rota ve uygulama modeli sağlayıcısı kurallarının sayfa yönlendirme, bulma ve işlemeyi denetlemenize nasıl yardımcı olduğunu keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: f45e327051aba54d1cab67148eb540fb1a5cc149
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667862"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>ASP.NET Core'da Razor Pages rota ve uygulama kuralları

::: moniker range=">= aspnetcore-3.0"

Razor Pages uygulamalarında sayfa yönlendirmesini, keşfini ve işlemeyi denetlemek için sayfa [rotası ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin.

Tek tek sayfalar için özel sayfa rotalarını yapılandırmanız gerektiğinde, yönlendirmeyi daha sonra bu konuda açıklanan [AddPageRoute kuralıyla](#configure-a-page-route) sayfalara yapılandırın.

Bir sayfa rotası belirtmek için, rota bölümleri eklemek veya bir rotaya `@page` parametreler eklemek için sayfanın yönergesini kullanın. Daha fazla bilgi için [özel yollara](xref:razor-pages/index#custom-routes)bakın.

Rota bölümleri veya parametre adları olarak kullanılameyecek ayrılmış sözcükler vardır. Daha fazla bilgi için Bkz. [Yönlendirme: Ayrılmış yönlendirme adları.](xref:fundamentals/routing#reserved-routing-names)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

| Senaryo | Örnek gösterir ... |
| -------- | --------------------------- |
| [Model kuralları](#model-conventions)<br><br>Sözleşmeler.Ekle<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Uygulamanın sayfalarına bir rota şablonu ve üstbilgi ekleyin. |
| [Sayfa rotası eylem kuralları](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Bir klasördeki sayfalara ve tek bir sayfaya bir rota şablonu ekleyin. |
| [Sayfa modeli eylem kuralları](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</li></ul> | Klasördeki sayfalara üstbilgi ekleyin, tek bir sayfaya üstbilgi ekleyin ve uygulamanın sayfalarına üstbilgi eklemek için bir [filtre fabrikasını](xref:mvc/controllers/filters#ifilterfactory) yapılandırın. |

Razor Pages kuralları <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` eklenir ve sınıftaki hizmet koleksiyonuna uzantı yöntemi kullanılarak yapılandırılır. Bu konuda aşağıdaki sözleşme örnekleri daha sonra açıklanmıştır:

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

## <a name="route-order"></a>Rota siparişi

Yollar işleme <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (rota eşleştirme) için bir belirtin.

| Sipariş verme            | Davranış |
| :--------------: | -------- |
| -1               | Rota, diğer rotalar işlenmeden önce işlenir. |
| 0                | Sipariş belirtilmemiş (varsayılan değer). Atamamak `Order` (`Order = null`) varsayılan `Order` olarak rotayı işleme için 0 (sıfır) olarak alır. |
| 1, 2, &hellip; n | Rota işleme sırasını belirtir. |

Rota işleme sözleşme ile kurulur:

* Rotalar sıralı sırayla işlenir (-1, &hellip; 0, 1, 2, n).
* Rotalar aynı `Order`olduğunda, en özel rota önce daha az belirli rotalar tarafından eşleşir.
* Aynı `Order` ve aynı sayıda parametreye sahip rotalar bir istek URL'si ile eşleştiğinde, rotalar eklenme sırasına göre <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>işlenir.

Mümkünse, kurulan bir rota işleme siparişine bağlı olarak kaçının. Genellikle, yönlendirme URL eşleştirme ile doğru rotayı seçer. Rota `Order` özelliklerini istekleri doğru yönlendirmek için ayarlamanız gerekiyorsa, uygulamanın yönlendirme düzeni büyük olasılıkla istemciler için kafa karıştırıcı ve bakımı için kırılgandır. Uygulamanın yönlendirme düzenini basitleştirmeye çalış. Örnek uygulama, tek bir uygulamayı kullanarak birkaç yönlendirme senaryosu göstermek için açık bir rota işleme emri gerektirir. Ancak, üretim uygulamalarında rota `Order` ayarlama uygulamasından kaçınmanız gerekir.

Razor Pages yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır. MVC konularında rota sırası ile ilgili bilgiler [Yönlendirme'de eylemleri denetleyiciye sahiptir: Öznitelik yollarını sıralama.](xref:mvc/controllers/routing#ordering-attribute-routes)

## <a name="model-conventions"></a>Model kuralları

Razor Pages <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> için geçerli [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için bir temsilci ekleyin.

### <a name="add-a-route-model-convention-to-all-pages"></a>Tüm sayfalara rota modeli kuralı ekleme

Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> rota modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örneklerin koleksiyonuoluşturmak ve eklemek için kullanın.

Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `1`ayarlanır. Bu, örnek uygulamada aşağıdaki rota eşleştirme davranışını sağlar:

* Konunun daha `TheContactPage/{text?}` sonra için bir rota şablonu eklenir. İlgili Kişi Sayfası rotasının `null` varsayılan`Order = 0`sırası (), `{globalTemplate?}` böylece rota şablonundan önce eşleşir.
* Daha `{aboutTemplate?}` sonra konuya bir rota şablonu eklenir. Şablon `{aboutTemplate?}` bir `Order` `2`verilir. `/About/RouteDataValue`Hakkında sayfa istendiğinde , "RouteDataValue" ( `RouteData.Values["globalTemplate"]` `Order = 1`) ve `RouteData.Values["aboutTemplate"]` (`Order = 2`) `Order` özelliğia ayarı nedeniyle yüklenir.
* Daha `{otherPagesTemplate?}` sonra konuya bir rota şablonu eklenir. Şablon `{otherPagesTemplate?}` bir `Order` `2`verilir. *Sayfalar/Öteki Sayfalar* `/OtherPages/Page1/RouteDataValue`klasöründeki herhangi bir sayfa bir rota parametresi ile istendiğinde (örneğin, ),`Order = 2` `Order` özelliğin ayarlanması nedeniyle "RouteDataValue" `RouteData.Values["globalTemplate"]` (`Order = 1`) yüklendi ve ( ) yüklenmez. `RouteData.Values["otherPagesTemplate"]`

Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın. Doğru rotayı seçmek için yönlendirmeye güvenin.

MVC, hizmet koleksiyonuna eklendiğinde, ekleme <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>gibi Jilet `Startup.ConfigureServices`Sayfaları seçenekleri eklenir. Örneğin, [örnek uygulamaya](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)bakın.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:

![Hakkında sayfası GlobalRouteValue'in bir rota bölümüyle istenir. İşlenen sayfa, rota veri değerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Tüm sayfalara uygulama modeli kuralı ekleme

Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> uygulaması modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örnekler in bir koleksiyonunu oluşturmak ve eklemek için kullanın.

Bu ve diğer kuralları daha sonra konuyla göstermek `AddHeaderAttribute` için, örnek uygulama bir sınıf içerir. Sınıf oluşturucu bir `name` dize `values` ve dize dizisini kabul eder. Bu değerler, bir `OnResultExecuting` yanıt üstbilgi ayarlamak için yönteminde kullanılır. Tam sınıf, daha sonra konusayfa [modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.

Örnek uygulama, `GlobalHeader` `AddHeaderAttribute` uygulamadaki tüm sayfalara bir üstbilgi eklemek için sınıfı kullanır:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:

![Hakkında sayfasının yanıt başlıkları GlobalHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Tüm sayfalara işleyici modeli kuralı ekleme

Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> işleyicisi <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> modeli oluşturma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sırasında uygulanan örnekler in bir koleksiyon oluşturmak ve eklemek için kullanın.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Sayfa rotası eylem kuralları

Sayfa yollarını yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış çağırır kurallarından <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> türeyen varsayılan rota modeli sağlayıcısı.

### <a name="folder-route-model-convention"></a>Klasör rota modeli kuralı

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> tüm sayfalar <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> için bir eylem çağıran bir oluşturmak ve eklemek için kullanın.

Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *OtherPages* klasöründeki sayfalara bir rota şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır. Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar. *Sayfalar/Öteki Sayfalar* klasöründeki bir sayfa rota parametresi değeriyle `/OtherPages/Page1/RouteDataValue`(örneğin), `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` `Order` özelliğin ayarlanması nedeniyle () değil, () yüklenir.

Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın. Doğru rotayı seçmek için yönlendirmeye güvenin.

Numunenin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:

![OtherPages klasöründeki Page1, GlobalRouteValue ve OtherPagesRouteValue'nin bir rota segmenti ile birlikte istenir. İşlenen sayfa, rota veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Sayfa rota modeli kuralı

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek uygulama, `AddPageRouteModelConvention` Hakkında `{aboutTemplate?}` sayfasına bir rota şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır. Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar. Hakkında sayfa bir rota parametre değeri ile `/About/RouteDataValue`istenirse , "RouteDataValue" `RouteData.Values["aboutTemplate"]` (`Order = 2``Order = 1`) içine `Order` yüklenir `RouteData.Values["globalTemplate"]` ve ( ) özelliği ayarı nedeniyle değil.

Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın. Doğru rotayı seçmek için yönlendirmeye güvenin.

Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:

![Hakkında sayfa GlobalRouteValue ve AboutRouteValue için rota segmentleri ile istenir. İşlenen sayfa, rota veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Sayfa rotalarını özelleştirmek için parametre transformatörü kullanma

ASP.NET Core tarafından oluşturulan sayfa yolları bir parametre transformatörü kullanılarak özelleştirilebilir. Bir parametre transformatörü parametrelerin değerini uygular `IOutboundParameterTransformer` ve dönüştürür. Örneğin, özel `SlugifyParameterTransformer` bir parametre `SubscriptionManagement` transformatörü `subscription-management`rota değerini .

Sayfa `PageRouteTransformerConvention` rotası modeli kuralı, bir uygulamada otomatik olarak oluşturulan sayfa yollarının klasör ve dosya adı segmentlerine bir parametre transformatörü uygular. Örneğin, */Pages/SubscriptionManagement/ViewAll.cshtml* adresindeki Razor Pages dosyasının rotası `/SubscriptionManagement/ViewAll` `/subscription-management/view-all`' ndan .

`PageRouteTransformerConvention`yalnızca Razor Pages klasöründen ve dosya adından gelen bir sayfa rotasının otomatik olarak oluşturulan kesimlerini dönüştürür. `@page` Yönergeyle eklenen rota bölümlerini dönüştürmez. Sözleşme, ekleyen yolları da <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>dönüştürmez.

Bir `PageRouteTransformerConvention` seçenek olarak `Startup.ConfigureServices`kayıtlıdır:

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

## <a name="configure-a-page-route"></a>Sayfa rotasını yapılandırma

Belirtilen <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> sayfa yolundaki bir sayfaya rota yapılandırmak için kullanın. Sayfaya oluşturulan bağlantılar, belirtilen rotanızı kullanır. `AddPageRoute`rotayı oluşturmak için kullanır. `AddPageRouteModelConvention`

Örnek uygulama `/TheContactPage` *Contact.cshtml*için bir yol oluşturur:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

İlgili Kişi sayfasına varsayılan `/Contact` rotası üzerinden de ulaşılabilir.

Örnek uygulamanın İletişim sayfasına özel rotası isteğe `text` bağlı`{text?}`bir rota segmentine izin verir ( ). Sayfa, ziyaretçinin sayfaya `@page` `/Contact` kendi rotasından erişme ihtimaline karşı yönergesinde bu isteğe bağlı bölümü de içerir:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

İşlenen sayfadaki **Kişi** bağlantısı için oluşturulan URL'nin güncelleştirilmiş rotayı yansıttığını unutmayın:

![Navigasyon çubuğundaki örnek uygulama İletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenen HTML'deki Kişi bağlantısının incelenmesi, href'in '/TheContactPage' olarak ayarlandığını gösterir](razor-pages-conventions/_static/contact-link-source.png)

Kişi sayfasını normal rotasından `/Contact`veya özel rotadan `/TheContactPage`ziyaret edin. Ek `text` bir rota kesimi sağlarsanız, sayfa sağladığınız HTML kodlanmış kesimi gösterir:

![URL'de 'TextValue' isteğe bağlı bir 'metin' rota segmenti sağlayan kenar tarayıcı örneği. İşlenen sayfa 'metin' segment değerini gösterir.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Sayfa modeli eylem kuralları

Uygulayan varsayılan sayfa modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sağlayıcısı, sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış kuralları çağırır. Bu kurallar, sayfa bulma ve işleme senaryoları oluşturma ve değiştirirken yararlıdır.

Bu bölümdeki örnekler için, örnek `AddHeaderAttribute` uygulama bir sınıf <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>kullanır, bir , bir yanıt üstbilgisi geçerlidir:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Örnekler, özniteliğin bir klasördeki tüm sayfalara ve tek bir sayfaya nasıl uygulanacağı gösteriş eder.

**Klasör uygulaması modeli kuralı**

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> tüm sayfalar için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> örneklerde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, `AddFolderApplicationModelConvention` `OtherPagesHeader`uygulamanın *OtherPages* klasöründeki sayfalara bir üstbilgi ekleyerek kullanımını gösterir:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Sonucu görüntülemek için örneğin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve üstbilgide inceleyin:

![OtherPages/Page1 sayfasının yanıt başlıkları OtherPagesHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Sayfa uygulaması modeli kuralı**

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, `AboutHeader`Hakkında sayfasına `AddPageApplicationModelConvention` bir üstbilgi ekleyerek kullanımını gösterir:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:

![About sayfasının yanıt başlıkları AboutHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

**Filtreyi yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>uygulamak için belirtilen filtreyi yapılandırır. Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama, bir filtreyi döndüren bir fabrika olarak sahne arkasında uygulanan lambda ifadesinde filtrenin nasıl uygulanacağını gösterir:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Sayfa uygulaması modeli, *OtherPages* klasöründeki Sayfa2 sayfasına giden kesimler için göreli yolu denetlemek için kullanılır. Koşul geçerse, bir üstbilgi eklenir. Değilse, `EmptyFilter` uygulanır.

`EmptyFilter`bir [Eylem filtresidir.](xref:mvc/controllers/filters#action-filters) Eylem filtreleri Razor Pages tarafından `EmptyFilter` göz ardı edildiklerinden, yol içermiyorsa `OtherPages/Page2`amaçlandığı gibi hiçbir etkisi yoktur.

Numunenin Sayfa2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üstbilgiinceleyin:

![OtherPagesPage2Header Sayfa2 için yanıt eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

**Filtre fabrikasını yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm Razor [Sayfalarına filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikayı yapılandırır.

Örnek uygulama, `FilterFactoryHeader`uygulamanın sayfalarına iki değer içeren bir üstbilgi ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanma örneği sağlar:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:

![Hakkında sayfanın yanıt üstbilgisi iki FilterFactoryHeader üstbilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC Filtreler ve Sayfa filtresi (IPageFilter)

Jilet Sayfaları işleyici yöntemlerini kullandığından, MVC [Eylem filtreleri](xref:mvc/controllers/filters#action-filters) Razor Pages tarafından yoksayılır. Kullanabileceğiniz diğer MVC filtre türleri mevcuttur: [Yetkilendirme,](xref:mvc/controllers/filters#authorization-filters) [Özel Durum,](xref:mvc/controllers/filters#exception-filters) [Kaynak](xref:mvc/controllers/filters#resource-filters)ve [Sonuç](xref:mvc/controllers/filters#result-filters). Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.

Sayfa filtresi<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) Jilet Sayfaları için geçerli olan bir filtredir. Daha fazla bilgi [için, Razor Pages için Filtre yöntemlerine](xref:razor-pages/filter)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Razor Pages uygulamalarında sayfa yönlendirmesini, keşfini ve işlemeyi denetlemek için sayfa [rotası ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin.

Tek tek sayfalar için özel sayfa rotalarını yapılandırmanız gerektiğinde, yönlendirmeyi daha sonra bu konuda açıklanan [AddPageRoute kuralıyla](#configure-a-page-route) sayfalara yapılandırın.

Bir sayfa rotası belirtmek için, rota bölümleri eklemek veya bir rotaya `@page` parametreler eklemek için sayfanın yönergesini kullanın. Daha fazla bilgi için [özel yollara](xref:razor-pages/index#custom-routes)bakın.

Rota bölümleri veya parametre adları olarak kullanılameyecek ayrılmış sözcükler vardır. Daha fazla bilgi için Bkz. [Yönlendirme: Ayrılmış yönlendirme adları.](xref:fundamentals/routing#reserved-routing-names)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

| Senaryo | Örnek gösterir ... |
| -------- | --------------------------- |
| [Model kuralları](#model-conventions)<br><br>Sözleşmeler.Ekle<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Uygulamanın sayfalarına bir rota şablonu ve üstbilgi ekleyin. |
| [Sayfa rotası eylem kuralları](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Bir klasördeki sayfalara ve tek bir sayfaya bir rota şablonu ekleyin. |
| [Sayfa modeli eylem kuralları](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</li></ul> | Klasördeki sayfalara üstbilgi ekleyin, tek bir sayfaya üstbilgi ekleyin ve uygulamanın sayfalarına üstbilgi eklemek için bir [filtre fabrikasını](xref:mvc/controllers/filters#ifilterfactory) yapılandırın. |

Razor Pages kuralları <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` eklenir ve sınıftaki hizmet koleksiyonuna uzantı yöntemi kullanılarak yapılandırılır. Bu konuda aşağıdaki sözleşme örnekleri daha sonra açıklanmıştır:

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

## <a name="route-order"></a>Rota siparişi

Yollar işleme <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (rota eşleştirme) için bir belirtin.

| Sipariş verme            | Davranış |
| :--------------: | -------- |
| -1               | Rota, diğer rotalar işlenmeden önce işlenir. |
| 0                | Sipariş belirtilmemiş (varsayılan değer). Atamamak `Order` (`Order = null`) varsayılan `Order` olarak rotayı işleme için 0 (sıfır) olarak alır. |
| 1, 2, &hellip; n | Rota işleme sırasını belirtir. |

Rota işleme sözleşme ile kurulur:

* Rotalar sıralı sırayla işlenir (-1, &hellip; 0, 1, 2, n).
* Rotalar aynı `Order`olduğunda, en özel rota önce daha az belirli rotalar tarafından eşleşir.
* Aynı `Order` ve aynı sayıda parametreye sahip rotalar bir istek URL'si ile eşleştiğinde, rotalar eklenme sırasına göre <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>işlenir.

Mümkünse, kurulan bir rota işleme siparişine bağlı olarak kaçının. Genellikle, yönlendirme URL eşleştirme ile doğru rotayı seçer. Rota `Order` özelliklerini istekleri doğru yönlendirmek için ayarlamanız gerekiyorsa, uygulamanın yönlendirme düzeni büyük olasılıkla istemciler için kafa karıştırıcı ve bakımı için kırılgandır. Uygulamanın yönlendirme düzenini basitleştirmeye çalış. Örnek uygulama, tek bir uygulamayı kullanarak birkaç yönlendirme senaryosu göstermek için açık bir rota işleme emri gerektirir. Ancak, üretim uygulamalarında rota `Order` ayarlama uygulamasından kaçınmanız gerekir.

Razor Pages yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır. MVC konularında rota sırası ile ilgili bilgiler [Yönlendirme'de eylemleri denetleyiciye sahiptir: Öznitelik yollarını sıralama.](xref:mvc/controllers/routing#ordering-attribute-routes)

## <a name="model-conventions"></a>Model kuralları

Razor Pages <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> için geçerli [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için bir temsilci ekleyin.

### <a name="add-a-route-model-convention-to-all-pages"></a>Tüm sayfalara rota modeli kuralı ekleme

Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> rota modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örneklerin koleksiyonuoluşturmak ve eklemek için kullanın.

Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `1`ayarlanır. Bu, örnek uygulamada aşağıdaki rota eşleştirme davranışını sağlar:

* Konunun daha `TheContactPage/{text?}` sonra için bir rota şablonu eklenir. İlgili Kişi Sayfası rotasının `null` varsayılan`Order = 0`sırası (), `{globalTemplate?}` böylece rota şablonundan önce eşleşir.
* Daha `{aboutTemplate?}` sonra konuya bir rota şablonu eklenir. Şablon `{aboutTemplate?}` bir `Order` `2`verilir. `/About/RouteDataValue`Hakkında sayfa istendiğinde , "RouteDataValue" ( `RouteData.Values["globalTemplate"]` `Order = 1`) ve `RouteData.Values["aboutTemplate"]` (`Order = 2`) `Order` özelliğia ayarı nedeniyle yüklenir.
* Daha `{otherPagesTemplate?}` sonra konuya bir rota şablonu eklenir. Şablon `{otherPagesTemplate?}` bir `Order` `2`verilir. *Sayfalar/Öteki Sayfalar* `/OtherPages/Page1/RouteDataValue`klasöründeki herhangi bir sayfa bir rota parametresi ile istendiğinde (örneğin, ),`Order = 2` `Order` özelliğin ayarlanması nedeniyle "RouteDataValue" `RouteData.Values["globalTemplate"]` (`Order = 1`) yüklendi ve ( ) yüklenmez. `RouteData.Values["otherPagesTemplate"]`

Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın. Doğru rotayı seçmek için yönlendirmeye güvenin.

MVC, hizmet koleksiyonuna eklendiğinde, ekleme <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>gibi Jilet `Startup.ConfigureServices`Sayfaları seçenekleri eklenir. Örneğin, [örnek uygulamaya](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)bakın.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:

![Hakkında sayfası GlobalRouteValue'in bir rota bölümüyle istenir. İşlenen sayfa, rota veri değerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Tüm sayfalara uygulama modeli kuralı ekleme

Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> uygulaması modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örnekler in bir koleksiyonunu oluşturmak ve eklemek için kullanın.

Bu ve diğer kuralları daha sonra konuyla göstermek `AddHeaderAttribute` için, örnek uygulama bir sınıf içerir. Sınıf oluşturucu bir `name` dize `values` ve dize dizisini kabul eder. Bu değerler, bir `OnResultExecuting` yanıt üstbilgi ayarlamak için yönteminde kullanılır. Tam sınıf, daha sonra konusayfa [modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.

Örnek uygulama, `GlobalHeader` `AddHeaderAttribute` uygulamadaki tüm sayfalara bir üstbilgi eklemek için sınıfı kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:

![Hakkında sayfasının yanıt başlıkları GlobalHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Tüm sayfalara işleyici modeli kuralı ekleme

Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> işleyicisi <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> modeli oluşturma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sırasında uygulanan örnekler in bir koleksiyon oluşturmak ve eklemek için kullanın.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Sayfa rotası eylem kuralları

Sayfa yollarını yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış çağırır kurallarından <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> türeyen varsayılan rota modeli sağlayıcısı.

### <a name="folder-route-model-convention"></a>Klasör rota modeli kuralı

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> tüm sayfalar <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> için bir eylem çağıran bir oluşturmak ve eklemek için kullanın.

Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *OtherPages* klasöründeki sayfalara bir rota şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır. Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar. *Sayfalar/Öteki Sayfalar* klasöründeki bir sayfa rota parametresi değeriyle `/OtherPages/Page1/RouteDataValue`(örneğin), `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` `Order` özelliğin ayarlanması nedeniyle () değil, () yüklenir.

Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın. Doğru rotayı seçmek için yönlendirmeye güvenin.

Numunenin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:

![OtherPages klasöründeki Page1, GlobalRouteValue ve OtherPagesRouteValue'nin bir rota segmenti ile birlikte istenir. İşlenen sayfa, rota veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Sayfa rota modeli kuralı

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek uygulama, `AddPageRouteModelConvention` Hakkında `{aboutTemplate?}` sayfasına bir rota şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır. Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar. Hakkında sayfa bir rota parametre değeri ile `/About/RouteDataValue`istenirse , "RouteDataValue" `RouteData.Values["aboutTemplate"]` (`Order = 2``Order = 1`) içine `Order` yüklenir `RouteData.Values["globalTemplate"]` ve ( ) özelliği ayarı nedeniyle değil.

Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın. Doğru rotayı seçmek için yönlendirmeye güvenin.

Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:

![Hakkında sayfa GlobalRouteValue ve AboutRouteValue için rota segmentleri ile istenir. İşlenen sayfa, rota veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Sayfa rotalarını özelleştirmek için parametre transformatörü kullanma

ASP.NET Core tarafından oluşturulan sayfa yolları bir parametre transformatörü kullanılarak özelleştirilebilir. Bir parametre transformatörü parametrelerin değerini uygular `IOutboundParameterTransformer` ve dönüştürür. Örneğin, özel `SlugifyParameterTransformer` bir parametre `SubscriptionManagement` transformatörü `subscription-management`rota değerini .

Sayfa `PageRouteTransformerConvention` rotası modeli kuralı, bir uygulamada otomatik olarak oluşturulan sayfa yollarının klasör ve dosya adı segmentlerine bir parametre transformatörü uygular. Örneğin, */Pages/SubscriptionManagement/ViewAll.cshtml* adresindeki Razor Pages dosyasının rotası `/SubscriptionManagement/ViewAll` `/subscription-management/view-all`' ndan .

`PageRouteTransformerConvention`yalnızca Razor Pages klasöründen ve dosya adından gelen bir sayfa rotasının otomatik olarak oluşturulan kesimlerini dönüştürür. `@page` Yönergeyle eklenen rota bölümlerini dönüştürmez. Sözleşme, ekleyen yolları da <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>dönüştürmez.

Bir `PageRouteTransformerConvention` seçenek olarak `Startup.ConfigureServices`kayıtlıdır:

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

## <a name="configure-a-page-route"></a>Sayfa rotasını yapılandırma

Belirtilen <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> sayfa yolundaki bir sayfaya rota yapılandırmak için kullanın. Sayfaya oluşturulan bağlantılar, belirtilen rotanızı kullanır. `AddPageRoute`rotayı oluşturmak için kullanır. `AddPageRouteModelConvention`

Örnek uygulama `/TheContactPage` *Contact.cshtml*için bir yol oluşturur:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

İlgili Kişi sayfasına varsayılan `/Contact` rotası üzerinden de ulaşılabilir.

Örnek uygulamanın İletişim sayfasına özel rotası isteğe `text` bağlı`{text?}`bir rota segmentine izin verir ( ). Sayfa, ziyaretçinin sayfaya `@page` `/Contact` kendi rotasından erişme ihtimaline karşı yönergesinde bu isteğe bağlı bölümü de içerir:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

İşlenen sayfadaki **Kişi** bağlantısı için oluşturulan URL'nin güncelleştirilmiş rotayı yansıttığını unutmayın:

![Navigasyon çubuğundaki örnek uygulama İletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenen HTML'deki Kişi bağlantısının incelenmesi, href'in '/TheContactPage' olarak ayarlandığını gösterir](razor-pages-conventions/_static/contact-link-source.png)

Kişi sayfasını normal rotasından `/Contact`veya özel rotadan `/TheContactPage`ziyaret edin. Ek `text` bir rota kesimi sağlarsanız, sayfa sağladığınız HTML kodlanmış kesimi gösterir:

![URL'de 'TextValue' isteğe bağlı bir 'metin' rota segmenti sağlayan kenar tarayıcı örneği. İşlenen sayfa 'metin' segment değerini gösterir.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Sayfa modeli eylem kuralları

Uygulayan varsayılan sayfa modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sağlayıcısı, sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış kuralları çağırır. Bu kurallar, sayfa bulma ve işleme senaryoları oluşturma ve değiştirirken yararlıdır.

Bu bölümdeki örnekler için, örnek `AddHeaderAttribute` uygulama bir sınıf <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>kullanır, bir , bir yanıt üstbilgisi geçerlidir:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Örnekler, özniteliğin bir klasördeki tüm sayfalara ve tek bir sayfaya nasıl uygulanacağı gösteriş eder.

**Klasör uygulaması modeli kuralı**

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> tüm sayfalar için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> örneklerde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, `AddFolderApplicationModelConvention` `OtherPagesHeader`uygulamanın *OtherPages* klasöründeki sayfalara bir üstbilgi ekleyerek kullanımını gösterir:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Sonucu görüntülemek için örneğin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve üstbilgide inceleyin:

![OtherPages/Page1 sayfasının yanıt başlıkları OtherPagesHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Sayfa uygulaması modeli kuralı**

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, `AboutHeader`Hakkında sayfasına `AddPageApplicationModelConvention` bir üstbilgi ekleyerek kullanımını gösterir:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:

![About sayfasının yanıt başlıkları AboutHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

**Filtreyi yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>uygulamak için belirtilen filtreyi yapılandırır. Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama, bir filtreyi döndüren bir fabrika olarak sahne arkasında uygulanan lambda ifadesinde filtrenin nasıl uygulanacağını gösterir:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Sayfa uygulaması modeli, *OtherPages* klasöründeki Sayfa2 sayfasına giden kesimler için göreli yolu denetlemek için kullanılır. Koşul geçerse, bir üstbilgi eklenir. Değilse, `EmptyFilter` uygulanır.

`EmptyFilter`bir [Eylem filtresidir.](xref:mvc/controllers/filters#action-filters) Eylem filtreleri Razor Pages tarafından `EmptyFilter` göz ardı edildiklerinden, yol içermiyorsa `OtherPages/Page2`amaçlandığı gibi hiçbir etkisi yoktur.

Numunenin Sayfa2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üstbilgiinceleyin:

![OtherPagesPage2Header Sayfa2 için yanıt eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

**Filtre fabrikasını yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm Razor [Sayfalarına filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikayı yapılandırır.

Örnek uygulama, `FilterFactoryHeader`uygulamanın sayfalarına iki değer içeren bir üstbilgi ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanma örneği sağlar:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:

![Hakkında sayfanın yanıt üstbilgisi iki FilterFactoryHeader üstbilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC Filtreler ve Sayfa filtresi (IPageFilter)

Jilet Sayfaları işleyici yöntemlerini kullandığından, MVC [Eylem filtreleri](xref:mvc/controllers/filters#action-filters) Razor Pages tarafından yoksayılır. Kullanabileceğiniz diğer MVC filtre türleri mevcuttur: [Yetkilendirme,](xref:mvc/controllers/filters#authorization-filters) [Özel Durum,](xref:mvc/controllers/filters#exception-filters) [Kaynak](xref:mvc/controllers/filters#resource-filters)ve [Sonuç](xref:mvc/controllers/filters#result-filters). Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.

Sayfa filtresi<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) Jilet Sayfaları için geçerli olan bir filtredir. Daha fazla bilgi [için, Razor Pages için Filtre yöntemlerine](xref:razor-pages/filter)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Razor Pages uygulamalarında sayfa yönlendirmesini, keşfini ve işlemeyi denetlemek için sayfa [rotası ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin.

Tek tek sayfalar için özel sayfa rotalarını yapılandırmanız gerektiğinde, yönlendirmeyi daha sonra bu konuda açıklanan [AddPageRoute kuralıyla](#configure-a-page-route) sayfalara yapılandırın.

Bir sayfa rotası belirtmek için, rota bölümleri eklemek veya bir rotaya `@page` parametreler eklemek için sayfanın yönergesini kullanın. Daha fazla bilgi için [özel yollara](xref:razor-pages/index#custom-routes)bakın.

Rota bölümleri veya parametre adları olarak kullanılameyecek ayrılmış sözcükler vardır. Daha fazla bilgi için Bkz. [Yönlendirme: Ayrılmış yönlendirme adları.](xref:fundamentals/routing#reserved-routing-names)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

| Senaryo | Örnek gösterir ... |
| -------- | --------------------------- |
| [Model kuralları](#model-conventions)<br><br>Sözleşmeler.Ekle<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Uygulamanın sayfalarına bir rota şablonu ve üstbilgi ekleyin. |
| [Sayfa rotası eylem kuralları](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Bir klasördeki sayfalara ve tek bir sayfaya bir rota şablonu ekleyin. |
| [Sayfa modeli eylem kuralları](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</li></ul> | Klasördeki sayfalara üstbilgi ekleyin, tek bir sayfaya üstbilgi ekleyin ve uygulamanın sayfalarına üstbilgi eklemek için bir [filtre fabrikasını](xref:mvc/controllers/filters#ifilterfactory) yapılandırın. |

Razor Pages kuralları <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` eklenir ve sınıftaki hizmet koleksiyonuna uzantı yöntemi kullanılarak yapılandırılır. Bu konuda aşağıdaki sözleşme örnekleri daha sonra açıklanmıştır:

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

## <a name="route-order"></a>Rota siparişi

Yollar işleme <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (rota eşleştirme) için bir belirtin.

| Sipariş verme            | Davranış |
| :--------------: | -------- |
| -1               | Rota, diğer rotalar işlenmeden önce işlenir. |
| 0                | Sipariş belirtilmemiş (varsayılan değer). Atamamak `Order` (`Order = null`) varsayılan `Order` olarak rotayı işleme için 0 (sıfır) olarak alır. |
| 1, 2, &hellip; n | Rota işleme sırasını belirtir. |

Rota işleme sözleşme ile kurulur:

* Rotalar sıralı sırayla işlenir (-1, &hellip; 0, 1, 2, n).
* Rotalar aynı `Order`olduğunda, en özel rota önce daha az belirli rotalar tarafından eşleşir.
* Aynı `Order` ve aynı sayıda parametreye sahip rotalar bir istek URL'si ile eşleştiğinde, rotalar eklenme sırasına göre <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>işlenir.

Mümkünse, kurulan bir rota işleme siparişine bağlı olarak kaçının. Genellikle, yönlendirme URL eşleştirme ile doğru rotayı seçer. Rota `Order` özelliklerini istekleri doğru yönlendirmek için ayarlamanız gerekiyorsa, uygulamanın yönlendirme düzeni büyük olasılıkla istemciler için kafa karıştırıcı ve bakımı için kırılgandır. Uygulamanın yönlendirme düzenini basitleştirmeye çalış. Örnek uygulama, tek bir uygulamayı kullanarak birkaç yönlendirme senaryosu göstermek için açık bir rota işleme emri gerektirir. Ancak, üretim uygulamalarında rota `Order` ayarlama uygulamasından kaçınmanız gerekir.

Razor Pages yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır. MVC konularında rota sırası ile ilgili bilgiler [Yönlendirme'de eylemleri denetleyiciye sahiptir: Öznitelik yollarını sıralama.](xref:mvc/controllers/routing#ordering-attribute-routes)

## <a name="model-conventions"></a>Model kuralları

Razor Pages <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> için geçerli [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için bir temsilci ekleyin.

### <a name="add-a-route-model-convention-to-all-pages"></a>Tüm sayfalara rota modeli kuralı ekleme

Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> rota modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örneklerin koleksiyonuoluşturmak ve eklemek için kullanın.

Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `1`ayarlanır. Bu, örnek uygulamada aşağıdaki rota eşleştirme davranışını sağlar:

* Konunun daha `TheContactPage/{text?}` sonra için bir rota şablonu eklenir. İlgili Kişi Sayfası rotasının `null` varsayılan`Order = 0`sırası (), `{globalTemplate?}` böylece rota şablonundan önce eşleşir.
* Daha `{aboutTemplate?}` sonra konuya bir rota şablonu eklenir. Şablon `{aboutTemplate?}` bir `Order` `2`verilir. `/About/RouteDataValue`Hakkında sayfa istendiğinde , "RouteDataValue" ( `RouteData.Values["globalTemplate"]` `Order = 1`) ve `RouteData.Values["aboutTemplate"]` (`Order = 2`) `Order` özelliğia ayarı nedeniyle yüklenir.
* Daha `{otherPagesTemplate?}` sonra konuya bir rota şablonu eklenir. Şablon `{otherPagesTemplate?}` bir `Order` `2`verilir. *Sayfalar/Öteki Sayfalar* `/OtherPages/Page1/RouteDataValue`klasöründeki herhangi bir sayfa bir rota parametresi ile istendiğinde (örneğin, ),`Order = 2` `Order` özelliğin ayarlanması nedeniyle "RouteDataValue" `RouteData.Values["globalTemplate"]` (`Order = 1`) yüklendi ve ( ) yüklenmez. `RouteData.Values["otherPagesTemplate"]`

Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın. Doğru rotayı seçmek için yönlendirmeye güvenin.

MVC, hizmet koleksiyonuna eklendiğinde, ekleme <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>gibi Jilet `Startup.ConfigureServices`Sayfaları seçenekleri eklenir. Örneğin, [örnek uygulamaya](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)bakın.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:

![Hakkında sayfası GlobalRouteValue'in bir rota bölümüyle istenir. İşlenen sayfa, rota veri değerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Tüm sayfalara uygulama modeli kuralı ekleme

Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> uygulaması modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örnekler in bir koleksiyonunu oluşturmak ve eklemek için kullanın.

Bu ve diğer kuralları daha sonra konuyla göstermek `AddHeaderAttribute` için, örnek uygulama bir sınıf içerir. Sınıf oluşturucu bir `name` dize `values` ve dize dizisini kabul eder. Bu değerler, bir `OnResultExecuting` yanıt üstbilgi ayarlamak için yönteminde kullanılır. Tam sınıf, daha sonra konusayfa [modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.

Örnek uygulama, `GlobalHeader` `AddHeaderAttribute` uygulamadaki tüm sayfalara bir üstbilgi eklemek için sınıfı kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:

![Hakkında sayfasının yanıt başlıkları GlobalHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Tüm sayfalara işleyici modeli kuralı ekleme

Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> işleyicisi <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> modeli oluşturma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sırasında uygulanan örnekler in bir koleksiyon oluşturmak ve eklemek için kullanın.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Sayfa rotası eylem kuralları

Sayfa yollarını yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış çağırır kurallarından <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> türeyen varsayılan rota modeli sağlayıcısı.

### <a name="folder-route-model-convention"></a>Klasör rota modeli kuralı

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> tüm sayfalar <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> için bir eylem çağıran bir oluşturmak ve eklemek için kullanın.

Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *OtherPages* klasöründeki sayfalara bir rota şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır. Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar. *Sayfalar/Öteki Sayfalar* klasöründeki bir sayfa rota parametresi değeriyle `/OtherPages/Page1/RouteDataValue`(örneğin), `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` `Order` özelliğin ayarlanması nedeniyle () değil, () yüklenir.

Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın. Doğru rotayı seçmek için yönlendirmeye güvenin.

Numunenin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:

![OtherPages klasöründeki Page1, GlobalRouteValue ve OtherPagesRouteValue'nin bir rota segmenti ile birlikte istenir. İşlenen sayfa, rota veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Sayfa rota modeli kuralı

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek uygulama, `AddPageRouteModelConvention` Hakkında `{aboutTemplate?}` sayfasına bir rota şablonu eklemek için kullanır:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır. Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar. Hakkında sayfa bir rota parametre değeri ile `/About/RouteDataValue`istenirse , "RouteDataValue" `RouteData.Values["aboutTemplate"]` (`Order = 2``Order = 1`) içine `Order` yüklenir `RouteData.Values["globalTemplate"]` ve ( ) özelliği ayarı nedeniyle değil.

Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın. Doğru rotayı seçmek için yönlendirmeye güvenin.

Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:

![Hakkında sayfa GlobalRouteValue ve AboutRouteValue için rota segmentleri ile istenir. İşlenen sayfa, rota veri değerlerinin sayfanın OnGet yönteminde yakalandığını gösterir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Sayfa rotasını yapılandırma

Belirtilen <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> sayfa yolundaki bir sayfaya rota yapılandırmak için kullanın. Sayfaya oluşturulan bağlantılar, belirtilen rotanızı kullanır. `AddPageRoute`rotayı oluşturmak için kullanır. `AddPageRouteModelConvention`

Örnek uygulama `/TheContactPage` *Contact.cshtml*için bir yol oluşturur:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

İlgili Kişi sayfasına varsayılan `/Contact` rotası üzerinden de ulaşılabilir.

Örnek uygulamanın İletişim sayfasına özel rotası isteğe `text` bağlı`{text?}`bir rota segmentine izin verir ( ). Sayfa, ziyaretçinin sayfaya `@page` `/Contact` kendi rotasından erişme ihtimaline karşı yönergesinde bu isteğe bağlı bölümü de içerir:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

İşlenen sayfadaki **Kişi** bağlantısı için oluşturulan URL'nin güncelleştirilmiş rotayı yansıttığını unutmayın:

![Navigasyon çubuğundaki örnek uygulama İletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenen HTML'deki Kişi bağlantısının incelenmesi, href'in '/TheContactPage' olarak ayarlandığını gösterir](razor-pages-conventions/_static/contact-link-source.png)

Kişi sayfasını normal rotasından `/Contact`veya özel rotadan `/TheContactPage`ziyaret edin. Ek `text` bir rota kesimi sağlarsanız, sayfa sağladığınız HTML kodlanmış kesimi gösterir:

![URL'de 'TextValue' isteğe bağlı bir 'metin' rota segmenti sağlayan kenar tarayıcı örneği. İşlenen sayfa 'metin' segment değerini gösterir.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Sayfa modeli eylem kuralları

Uygulayan varsayılan sayfa modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sağlayıcısı, sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış kuralları çağırır. Bu kurallar, sayfa bulma ve işleme senaryoları oluşturma ve değiştirirken yararlıdır.

Bu bölümdeki örnekler için, örnek `AddHeaderAttribute` uygulama bir sınıf <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>kullanır, bir , bir yanıt üstbilgisi geçerlidir:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Örnekler, özniteliğin bir klasördeki tüm sayfalara ve tek bir sayfaya nasıl uygulanacağı gösteriş eder.

**Klasör uygulaması modeli kuralı**

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> tüm sayfalar için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> örneklerde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, `AddFolderApplicationModelConvention` `OtherPagesHeader`uygulamanın *OtherPages* klasöründeki sayfalara bir üstbilgi ekleyerek kullanımını gösterir:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Sonucu görüntülemek için örneğin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve üstbilgide inceleyin:

![OtherPages/Page1 sayfasının yanıt başlıkları OtherPagesHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Sayfa uygulaması modeli kuralı**

Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.

Örnek, `AboutHeader`Hakkında sayfasına `AddPageApplicationModelConvention` bir üstbilgi ekleyerek kullanımını gösterir:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:

![About sayfasının yanıt başlıkları AboutHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

**Filtreyi yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>uygulamak için belirtilen filtreyi yapılandırır. Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama, bir filtreyi döndüren bir fabrika olarak sahne arkasında uygulanan lambda ifadesinde filtrenin nasıl uygulanacağını gösterir:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Sayfa uygulaması modeli, *OtherPages* klasöründeki Sayfa2 sayfasına giden kesimler için göreli yolu denetlemek için kullanılır. Koşul geçerse, bir üstbilgi eklenir. Değilse, `EmptyFilter` uygulanır.

`EmptyFilter`bir [Eylem filtresidir.](xref:mvc/controllers/filters#action-filters) Eylem filtreleri Razor Pages tarafından `EmptyFilter` göz ardı edildiklerinden, yol içermiyorsa `OtherPages/Page2`amaçlandığı gibi hiçbir etkisi yoktur.

Numunenin Sayfa2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üstbilgiinceleyin:

![OtherPagesPage2Header Sayfa2 için yanıt eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

**Filtre fabrikasını yapılandırma**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm Razor [Sayfalarına filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikayı yapılandırır.

Örnek uygulama, `FilterFactoryHeader`uygulamanın sayfalarına iki değer içeren bir üstbilgi ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanma örneği sağlar:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:

![Hakkında sayfanın yanıt üstbilgisi iki FilterFactoryHeader üstbilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC Filtreler ve Sayfa filtresi (IPageFilter)

Jilet Sayfaları işleyici yöntemlerini kullandığından, MVC [Eylem filtreleri](xref:mvc/controllers/filters#action-filters) Razor Pages tarafından yoksayılır. Kullanabileceğiniz diğer MVC filtre türleri mevcuttur: [Yetkilendirme,](xref:mvc/controllers/filters#authorization-filters) [Özel Durum,](xref:mvc/controllers/filters#exception-filters) [Kaynak](xref:mvc/controllers/filters#resource-filters)ve [Sonuç](xref:mvc/controllers/filters#result-filters). Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.

Sayfa filtresi<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) Jilet Sayfaları için geçerli olan bir filtredir. Daha fazla bilgi [için, Razor Pages için Filtre yöntemlerine](xref:razor-pages/filter)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
