---
Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>ASP.NET Web API 'sinden ASP.NET Core 'e geçiş

[Scott Ade](https://twitter.com/scott_addie) ve [Steve Smith](https://ardalis.com/) tarafından

ASP.NET 4. x Web API 'SI, tarayıcılar ve mobil cihazlar dahil olmak üzere çok çeşitli istemcilere ulaşan bir HTTP hizmetidir. ASP.NET Core ASP.NET 4. x ' in MVC ve Web API 'SI uygulama modellerini ASP.NET Core MVC olarak bilinen tek bir programlama modelinde birleştirir. Bu makalede, ASP.NET 4. x Web API 'sinden ASP.NET Core MVC 'ye geçiş yapmak için gereken adımlar gösterilmektedir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4. x Web API projesini inceleyin

Bu makale, [ASP.NET Web API 2 Ile çalışmaya](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)başlama bölümünde oluşturulan *productsapp* projesini kullanır. Bu projede, temel bir ASP.NET 4. x Web API projesi aşağıdaki şekilde yapılandırılır.

*Global.asax.cs*' de, şu şekilde bir çağrı yapılır `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`Sınıfı *App_Start* klasöründe bulunur ve statik bir `Register` yönteme sahiptir:

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

Önceki sınıf:

* , Gerçekten kullanılmasa da, [öznitelik yönlendirmeyi](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)yapılandırır.
* Yönlendirme tablosunu yapılandırır.
Örnek kod, isteğe bağlı olarak, URL 'Lerin biçimle eşleşmesini bekler `/api/{controller}/{id}` `{id}` .

Aşağıdaki bölümlerde, Web API projesinin ASP.NET Core MVC 'ye geçişi gösterilmektedir.

## <a name="create-the-destination-project"></a>Hedef projeyi oluşturma

Visual Studio 'da yeni bir boş çözüm oluşturun ve geçirilecek ASP.NET 4. x Web API projesini ekleyin:

1. **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
1. **Boş çözüm** şablonunu seçin ve Ileri ' **yi**seçin.
1. Çözümü *WebAPIMigration*olarak adlandırın. **Oluştur**’u seçin.
1. Varolan *Productsapp* projesini çözüme ekleyin.

Geçirilecek yeni bir API projesi ekleyin:

1. Çözüme yeni bir **ASP.NET Core Web uygulaması** projesi ekleyin.
1. **Yeni projenizi yapılandırın** iletişim kutusunda, proje *Productscore*' ı adlandırın ve **Oluştur**' u seçin.
1. **Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın. **API** proje şablonunu seçin ve **Oluştur**' u seçin.
1. *WeatherForecast.cs* ve *Controllers/dalgalı sıra. cs* örnek dosyalarını yeni *productscore* projesinden kaldırın.

Çözüm artık iki proje içerir. Aşağıdaki bölümlerde *Productsapp* projesinin Içeriğinin *productscore* projesine geçirilmesi açıklanmaktadır.

## <a name="migrate-configuration"></a>Yapılandırmayı geçir

ASP.NET Core *App_Start* klasörünü veya *Global. asax* dosyasını kullanmaz. Ayrıca, *Web. config* dosyası yayımlama zamanında eklenir.

`Startup` sınıfı:

* *Global. asax*öğesini değiştirir.
* Tüm uygulama başlangıç görevlerini işler.

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="migrate-models-and-controllers"></a>Modelleri ve denetleyicileri geçirme

Aşağıdaki kod, `ProductsController` ASP.NET Core için güncelleştirileceğini gösterir:

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

`ProductsController`ASP.NET Core için Güncelleştir:

1. *Denetleyiciyi/ProductsController. cs* ve *modeller* klasörünü özgün projeden yeni bir klasöre kopyalayın.
1. Kopyalanan dosyaların kök ad alanını olarak değiştirin `ProductsCore` .
1. `using ProductsApp.Models;`İfadesini olarak güncelleştirin `using ProductsCore.Models;` .

Aşağıdaki bileşenler ASP.NET Core yok:

* `ApiController` sınıfı
* `System.Web.Http`uzayına
* `IHttpActionResult`arayüz

Aşağıdaki değişiklikleri yapın:

1. `ApiController`Olarak değiştirin <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . `using Microsoft.AspNetCore.Mvc;`Başvuruyu çözümlemek için ekleyin `ControllerBase` .
1. `using System.Web.Http;` klasörünü silin.
1. `GetProduct`Eylemin dönüş türünü `IHttpActionResult` olarak değiştirin `ActionResult<Product>` .
1. `GetProduct`Eylemin `return` ifadesini aşağıdaki şekilde kolaylaştırın:

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Yönlendirmeyi yapılandırma

ASP.NET Core *API* proje şablonu, üretilen koddaki Endpoint Routing yapılandırmasını içerir.

Aşağıdakiler <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> çağırır:

* Yönlendirme eşleştirme ve uç nokta yürütmeyi [Ara yazılım](xref:fundamentals/middleware/index) ardışık düzeninde kaydedin.
* *Productsapp* projesinin *App_Start/webapiconfig.cs* dosyasını değiştirin.

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

Yönlendirmeyi aşağıdaki şekilde yapılandırın:

1. `ProductsController`Sınıfı aşağıdaki özniteliklere işaretle:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Önceki [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) öznitelik, denetleyicinin öznitelik yönlendirme modelini yapılandırır. [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Öznitelik, bu denetleyicideki tüm eylemler için öznitelik yönlendirme bir gereksinim yapar.

    Öznitelik yönlendirme, ve gibi belirteçleri destekler `[controller]` `[action]` . Çalışma zamanında, her belirteç, sırasıyla özniteliğin uygulandığı denetleyicinin veya eylemin adı ile değiştirilmiştir. Belirteçler:
    * Projedeki sihirli dize sayısını azaltın.
    * Otomatik yeniden adlandırma yeniden düzenlemeler uygulandığında, yolların karşılık gelen denetleyiciler ve eylemlerle eşitlenmiş durumda kalmasını sağlayın.
1. Eylemlere HTTP Get isteklerini etkinleştirin `ProductController` :
    * [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Özniteliği `GetAllProducts` eyleme uygulayın.
    * `[HttpGet("{id}")]`Özniteliği `GetProduct` eyleme uygulayın.

Geçirilen projeyi çalıştırın ve konumuna gidin `/api/products` . Üç ürünün tam bir listesi görüntülenir. `/api/products/1` adresine gidin. İlk ürün görüntülenir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4. x Web API projesini inceleyin

Bu makale, [ASP.NET Web API 2 Ile çalışmaya](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)başlama bölümünde oluşturulan *productsapp* projesini kullanır. Bu projede, temel bir ASP.NET 4. x Web API projesi aşağıdaki şekilde yapılandırılır.

*Global.asax.cs*' de, şu şekilde bir çağrı yapılır `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`Sınıfı *App_Start* klasöründe bulunur ve statik bir `Register` yönteme sahiptir:

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

Bu sınıf, proje içinde gerçekten kullanılmasa da [öznitelik yönlendirmeyi](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)yapılandırır. Ayrıca, ASP.NET Web API 'SI tarafından kullanılan yönlendirme tablosunu da yapılandırır. Bu durumda, ASP.NET 4. x Web API 'SI, isteğe bağlı olarak URL 'Lerin biçimle eşleşmesini bekler `/api/{controller}/{id}` `{id}` .

Aşağıdaki bölümlerde, Web API projesinin ASP.NET Core MVC 'ye geçişi gösterilmektedir.

## <a name="create-the-destination-project"></a>Hedef projeyi oluşturma

Visual Studio 'da aşağıdaki adımları uygulayın:

* **Dosya**  >  **Yeni**  >  **Proje**  >  **diğer proje türleri**  >  **Visual Studio çözümleri**' ne gidin. **Boş çözüm**' i seçin ve çözümü *WebAPIMigration*olarak adlandırın. **Tamam** düğmesine tıklayın.
* Varolan *Productsapp* projesini çözüme ekleyin.
* Çözüme yeni bir **ASP.NET Core Web uygulaması** projesi ekleyin. Açılan listeden **.NET Core** hedef çerçevesini seçin ve **API** proje şablonunu seçin. Projeyi *Productscore*olarak adlandırın ve **Tamam** düğmesine tıklayın.

Çözüm artık iki proje içerir. Aşağıdaki bölümlerde *Productsapp* projesinin Içeriğinin *productscore* projesine geçirilmesi açıklanmaktadır.

## <a name="migrate-configuration"></a>Yapılandırmayı geçir

ASP.NET Core şunu kullanmaz:

* *App_Start* klasörü veya *Global. asax* dosyası
* *Web. config* dosyası yayımlama zamanında eklenir.

`Startup` sınıfı:

* *Global. asax*öğesini değiştirir.
* Tüm uygulama başlangıç görevlerini işler.

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

ASP.NET Core MVC 'de, ' de çağrıldığında öznitelik yönlendirme varsayılan olarak dahil edilir <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure` . Aşağıdaki `UseMvc` çağrı *Productsapp* projesinin *App_Start/webapiconfig.cs* dosyasının yerini alır:

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a>Modelleri ve denetleyicileri geçirme

Aşağıdaki kod `ProductsController` ASP.NET Core güncelleştirmesini gösterir:[!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]

`ProductsController`ASP.NET Core için Güncelleştir:

1. *Denetleyiciyi/ProductsController. cs* öğesini özgün projeden yeni bir kopyaya kopyalayın.
1. *Modeller* klasörünü özgün projeden yeni bir klasöre kopyalayın.
1. Kopyalanan dosyaların kök ad alanını olarak değiştirin `ProductsCore` .
1. `using ProductsApp.Models;`İfadesini olarak güncelleştirin `using ProductsCore.Models;` .

Aşağıdaki bileşenler ASP.NET Core yok:

* `ApiController` sınıfı
* `System.Web.Http`uzayına
* `IHttpActionResult`arayüz

Aşağıdaki değişiklikleri yapın:

1. `ApiController`Olarak değiştirin <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . `using Microsoft.AspNetCore.Mvc;`Başvuruyu çözümlemek için ekleyin `ControllerBase` .
1. `using System.Web.Http;` klasörünü silin.
1. `GetProduct`Eylemin dönüş türünü `IHttpActionResult` olarak değiştirin `ActionResult<Product>` .
1. `GetProduct`Eylemin `return` ifadesini aşağıdaki şekilde kolaylaştırın:

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Yönlendirmeyi yapılandırma

Yönlendirmeyi aşağıdaki şekilde yapılandırın:

1. `ProductsController`Sınıfı aşağıdaki özniteliklere işaretle:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Önceki [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) öznitelik, denetleyicinin öznitelik yönlendirme modelini yapılandırır. [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Öznitelik, bu denetleyicideki tüm eylemler için öznitelik yönlendirme bir gereksinim yapar.

    Öznitelik yönlendirme, ve gibi belirteçleri destekler `[controller]` `[action]` . Çalışma zamanında, her belirteç, sırasıyla özniteliğin uygulandığı denetleyicinin veya eylemin adı ile değiştirilmiştir. Belirteçler, projedeki sihirli dize sayısını azaltır. Belirteçler Ayrıca otomatik yeniden adlandırma yeniden düzenlemeler uygulandığında, yolların karşılık gelen denetleyiciler ve eylemlerle eşitlenmiş durumda kalmasını da güvence altına aldığından emin olun.
1. Projenin uyumluluk modunu ASP.NET Core 2,2 olarak ayarlayın:

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    Önceki değişiklik:

    * `[ApiController]`Özniteliği denetleyici düzeyinde kullanmak için gereklidir.
    * ASP.NET Core 2,2 ' de ortaya çıkan davranışlardan oluşan davranışa izin verebilir.
1. Eylemlere HTTP Get isteklerini etkinleştirin `ProductController` :
    * [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Özniteliği `GetAllProducts` eyleme uygulayın.
    * `[HttpGet("{id}")]`Özniteliği `GetProduct` eyleme uygulayın.

Geçirilen projeyi çalıştırın ve konumuna gidin `/api/products` . Üç ürünün tam bir listesi görüntülenir. `/api/products/1` adresine gidin. İlk ürün görüntülenir.

## <a name="compatibility-shim"></a>Uyumluluk dolgusu

[Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) kitaplığı, ASP.NET 4. x Web apı projelerini ASP.NET Core 'ye taşımak için bir uyumluluk dolgusu sağlar. Uyumluluk dolgusu, ASP.NET 4. x Web API 2 ' den çok sayıda kuralı desteklemek için ASP.NET Core genişletir. Bu belgede daha önce yer alan örnek, uyumluluk dolgunun gereksiz olduğu kadar temel bir örnektir. Daha büyük projeler için, uyumluluk dolgunun kullanılması, ASP.NET Core ile ASP.NET 4. x Web API 2 arasındaki API boşluğunu geçici olarak köprülemesi için yararlı olabilir.

Web API 'SI uyumluluk dolgusu, büyük ASP.NET 4. x Web API projelerini ASP.NET Core geçirmeyi desteklemek için geçici bir ölçü olarak kullanılmak üzere tasarlanmıştır. Zaman içinde, projelerin uyumluluk dolgusunda güvenmek yerine ASP.NET Core desenler kullanması için güncelleştirilmeleri gerekir.

İçinde bulunan uyumluluk özellikleri `Microsoft.AspNetCore.Mvc.WebApiCompatShim` şunlardır:

* `ApiController`Denetleyicilerin temel türlerinin güncelleştirilmesini gerektirmeyen bir tür ekler.
* Web API stili model bağlamayı etkinleştirilir. ASP.NET Core MVC modeli bağlama işlevleri, varsayılan olarak ASP.NET 4. x MVC 5 ' in benzer şekilde. Uyumluluk dolgusu model bağlamasını ASP.NET 4. x Web API 2 model bağlama kurallarına benzer olacak şekilde değiştirir. Örneğin, karmaşık türler, istek gövdesinden otomatik olarak bağlanır.
* Denetleyici eylemlerinin tür parametreleri alması için model bağlamayı genişletir `HttpRequestMessage` .
* Eylemlerin tür sonuçları döndürmesini sağlayan ileti biçimleri ekler `HttpResponseMessage` .
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
* `IContentNegotiator`Uygulamanın bağımlılık ekleme (dı) kapsayıcısına bir örneğini ekler ve [Microsoft. Aspnet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)öğesinden içerik anlaşmasına ilişkin türleri kullanılabilir hale getirir. Bu tür türlere örnekler `DefaultContentNegotiator` ve içerir `MediaTypeFormatter` .

Uyumluluk Shim 'yi kullanmak için:

1. [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet paketini yükler.
1. İçinde arayarak uyumluluk dolgunun hizmetlerini uygulamanın dı kapsayıcısına kaydedin `services.AddMvc().AddWebApiConventions()` `Startup.ConfigureServices` .
1. `MapWebApiRoute`Uygulamanın çağrısında üzerinde kullanarak Web API 'sine özgü yollar tanımlayın `IRouteBuilder` `IApplicationBuilder.UseMvc` .

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
