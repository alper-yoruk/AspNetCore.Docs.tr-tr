---
title: ASP.NET Web API 'sinden ASP.NET Core 'e geçiş
author: ardalis
description: ASP.NET 4. x Web API 'sinden bir Web API uygulamasını ASP.NET Core MVC 'ye geçirmeyi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: dda457daa0cb8a59ccd4c326a601e375fe4a81bb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766595"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>ASP.NET Web API 'sinden ASP.NET Core 'e geçiş

[Scott Ade](https://twitter.com/scott_addie) ve [Steve Smith](https://ardalis.com/) tarafından

ASP.NET 4. x Web API 'SI, tarayıcılar ve mobil cihazlar dahil olmak üzere çok çeşitli istemcilere ulaşan bir HTTP hizmetidir. ASP.NET Core, ASP.NET 4. x ' in MVC ve Web API uygulaması modellerini ASP.NET Core MVC olarak bilinen daha basit bir programlama modeline ayırır. Bu makalede, ASP.NET 4. x Web API 'sinden ASP.NET Core MVC 'ye geçiş yapmak için gereken adımlar gösterilmektedir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4. x Web API projesini inceleyin

Başlangıç noktası olarak bu makale, [ASP.NET Web API 2 Ile çalışmaya](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)başlama bölümünde oluşturulan *productsapp* projesini kullanır. Bu projede, basit bir ASP.NET 4. x Web API projesi aşağıdaki şekilde yapılandırılır.

*Global.asax.cs*' de, şu şekilde `WebApiConfig.Register`bir çağrı yapılır:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig` Sınıfı *App_Start* klasöründe bulunur ve statik `Register` bir yönteme sahiptir:

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

Bu sınıf, proje içinde gerçekten kullanılmasa da [öznitelik yönlendirmeyi](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)yapılandırır. Ayrıca, ASP.NET Web API 'SI tarafından kullanılan yönlendirme tablosunu da yapılandırır. Bu durumda, ASP.NET 4. x Web API 'SI, isteğe bağlı olarak URL 'Lerin `/api/{controller}/{id}`biçimle `{id}` eşleşmesini bekler.

*Productsapp* projesi bir denetleyici içerir. Denetleyici öğesinden `ApiController` devralır ve iki eylem içerir:

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

Tarafından `Product` `ProductsController` kullanılan model basit bir sınıftır:

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

Aşağıdaki bölümlerde, Web API projesinin ASP.NET Core MVC 'ye geçişi gösterilmektedir.

## <a name="create-destination-project"></a>Hedef proje oluştur

Visual Studio 'da aşağıdaki adımları uygulayın:

* **Dosya** > **Yeni** > **Other Project Types****Project** > **Visual Studio Solutions**proje diğer proje türleri Visual Studio çözümleri ' ne gidin. >  **Boş çözüm**' i seçin ve çözümü *WebAPIMigration*olarak adlandırın. **Tamam** düğmesine tıklayın.
* Varolan *Productsapp* projesini çözüme ekleyin.
* Çözüme yeni bir **ASP.NET Core Web uygulaması** projesi ekleyin. Açılan listeden **.NET Core** hedef çerçevesini seçin ve **API** proje şablonunu seçin. Projeyi *Productscore*olarak adlandırın ve **Tamam** düğmesine tıklayın.

Çözüm artık iki proje içerir. Aşağıdaki bölümlerde *Productsapp* projesinin Içeriğinin *productscore* projesine geçirilmesi açıklanmaktadır.

## <a name="migrate-configuration"></a>Yapılandırmayı geçir

ASP.NET Core, *App_Start* klasörünü veya *Global. asax* dosyasını kullanmaz ve *Web. config* dosyası yayımlama zamanında eklenir. *Startup.cs* , *Global. asax* 'in yerini alır ve proje kökünde bulunur. `Startup` Sınıfı tüm uygulama başlangıç görevlerini işler. Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

ASP.NET Core MVC 'de, ' de <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure`çağrıldığında öznitelik yönlendirme varsayılan olarak dahil edilir. Aşağıdaki `UseMvc` çağrı *productsapp* projesinin *App_Start/webapiconfig.cs* dosyasının yerini alır:

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>Modelleri ve denetleyicileri geçirme

*Productapp* projesinin denetleyicisi ve kullandığı model üzerine kopyalayın. Şu adımları uygulayın:

1. *Denetleyiciyi/ProductsController. cs* öğesini özgün projeden yeni bir kopyaya kopyalayın.
1. Tüm *modeller* klasörünü özgün projeden yeni bir klasöre kopyalayın.
1. Kopyalanan dosyaların ad alanlarını yeni proje adıyla (*Productscore*) eşleşecek şekilde değiştirin. `using ProductsApp.Models;` Deyimdeki *ProductsController.cs* de ayarlayın.

Bu noktada, uygulamanın oluşturulması bir dizi derleme hatası ile sonuçlanır. Aşağıdaki bileşenler ASP.NET Core mevcut olmadığından hatalar oluşur:

* `ApiController` sınıfı
* `System.Web.Http`uzayına
* `IHttpActionResult`arayüz

Hataları aşağıdaki gibi düzeltir:

1. Olarak `ApiController` <xref:Microsoft.AspNetCore.Mvc.ControllerBase>değiştirin. Başvuruyu çözümlemek için ekleyin `using Microsoft.AspNetCore.Mvc;` `ControllerBase`
1. `using System.Web.Http;` klasörünü silin.
1. `GetProduct` Eylemin dönüş türünü `IHttpActionResult` olarak `ActionResult<Product>`değiştirin.

`GetProduct` Eylemin `return` ifadesini aşağıdaki şekilde kolaylaştırın:

```csharp
return product;
```

## <a name="configure-routing"></a>Yönlendirmeyi yapılandırma

Yönlendirmeyi aşağıdaki şekilde yapılandırın:

1. `ProductsController` Sınıfı aşağıdaki özniteliklere işaretle:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Önceki [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) öznitelik, denetleyicinin öznitelik yönlendirme modelini yapılandırır. Öznitelik [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) , bu denetleyicideki tüm eylemler için öznitelik yönlendirme bir gereksinim yapar.

    Öznitelik yönlendirme, `[controller]` ve `[action]`gibi belirteçleri destekler. Çalışma zamanında, her belirteç, sırasıyla özniteliğin uygulandığı denetleyicinin veya eylemin adı ile değiştirilmiştir. Belirteçler, projedeki sihirli dize sayısını azaltır. Belirteçler Ayrıca otomatik yeniden adlandırma yeniden düzenlemeler uygulandığında, yolların karşılık gelen denetleyiciler ve eylemlerle eşitlenmiş durumda kalmasını da güvence altına aldığından emin olun.
1. Projenin uyumluluk modunu ASP.NET Core 2,2 olarak ayarlayın:

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    Önceki değişiklik:

    * `[ApiController]` Özniteliği denetleyici düzeyinde kullanmak için gereklidir.
    * ASP.NET Core 2,2 ' de ortaya çıkan davranışlardan oluşan davranışa izin verebilir.
1. `ProductController` Eylemlere http get isteklerini etkinleştirin:
    * [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) Özniteliği `GetAllProducts` eyleme uygulayın.
    * `[HttpGet("{id}")]` Özniteliği `GetProduct` eyleme uygulayın.

Önceki değişikliklerden ve kullanılmayan `using` deyimler kaldırıldıktan sonra, *ProductsController.cs* dosyası şuna benzer:

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

Geçirilen projeyi çalıştırın ve konumuna gidin `/api/products`. Üç ürünün tam bir listesi görüntülenir. `/api/products/1` adresine gidin. İlk ürün görüntülenir.

## <a name="compatibility-shim"></a>Uyumluluk dolgusu

[Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) kitaplığı, ASP.NET 4. x Web apı projelerini ASP.NET Core 'ye taşımak için bir uyumluluk dolgusu sağlar. Uyumluluk dolgusu, ASP.NET 4. x Web API 2 ' den çok sayıda kuralı desteklemek için ASP.NET Core genişletir. Bu belgede daha önce yer alan örnek, uyumluluk dolgunun gereksiz olduğu kadar temel bir örnektir. Daha büyük projeler için, uyumluluk dolgunun kullanılması, ASP.NET Core ile ASP.NET 4. x Web API 2 arasındaki API boşluğunu geçici olarak köprülemesi için yararlı olabilir.

Web API 'SI uyumluluk dolgusu, büyük ASP.NET 4. x Web API projelerini ASP.NET Core geçirmeyi desteklemek için geçici bir ölçü olarak kullanılmak üzere tasarlanmıştır. Zaman içinde, projelerin uyumluluk dolgusunda güvenmek yerine ASP.NET Core desenler kullanması için güncelleştirilmeleri gerekir.

İçinde `Microsoft.AspNetCore.Mvc.WebApiCompatShim` bulunan uyumluluk özellikleri şunlardır:

* Denetleyicilerin temel `ApiController` türlerinin güncelleştirilmesini gerektirmeyen bir tür ekler.
* Web API stili model bağlamayı etkinleştirilir. ASP.NET Core MVC modeli bağlama işlevleri, varsayılan olarak ASP.NET 4. x MVC 5 ' in benzer şekilde. Uyumluluk dolgusu model bağlamasını ASP.NET 4. x Web API 2 model bağlama kurallarına benzer olacak şekilde değiştirir. Örneğin, karmaşık türler, istek gövdesinden otomatik olarak bağlanır.
* Denetleyici eylemlerinin tür `HttpRequestMessage`parametreleri alması için model bağlamayı genişletir.
* Eylemlerin tür `HttpResponseMessage`sonuçları döndürmesini sağlayan ileti biçimleri ekler.
* Web API 2 eylemlerinin yanıtları karşılamak için kullanmış olabileceği ek yanıt yöntemleri ekler:
  * `HttpResponseMessage`oluşturucuları
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Eylem sonucu yöntemleri:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Uygulamanın bağımlılık ekleme ( `IContentNegotiator` dı) kapsayıcısına bir örneğini ekler ve [Microsoft. Aspnet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)öğesinden içerik anlaşmasına ilişkin türleri kullanılabilir hale getirir. Bu tür türlere örnekler ve `DefaultContentNegotiator` `MediaTypeFormatter`içerir.

Uyumluluk Shim 'yi kullanmak için:

1. [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet paketini yükler.
1. İçinde `services.AddMvc().AddWebApiConventions()` `Startup.ConfigureServices`arayarak uyumluluk dolgunun hizmetlerini uygulamanın dı kapsayıcısına kaydedin.
1. Uygulamanın `MapWebApiRoute` `IApplicationBuilder.UseMvc` çağrısında üzerinde `IRouteBuilder` kullanarak Web API 'sine özgü yollar tanımlayın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
