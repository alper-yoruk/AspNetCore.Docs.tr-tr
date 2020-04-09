---
title: ASP.NET Core'da Tek Sayfauygulamaları Oluşturmak için JavaScript Hizmetlerini Kullanın
author: scottaddie
description: ASP.NET Core tarafından desteklenen tek sayfalık bir Uygulama (SPA) oluşturmak için JavaScript Hizmetlerini kullanmanın yararları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663781"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>ASP.NET Core'da Tek Sayfauygulamaları Oluşturmak için JavaScript Hizmetlerini Kullanın

Scott [Addie](https://github.com/scottaddie) ve [Fiyaz Hasan](https://fiyazhasan.me/) tarafından

Tek Sayfa uygulaması (SPA), doğası nda olan zengin kullanıcı deneyimi nedeniyle popüler bir web uygulaması türüdür. Istemci tarafındaki SPA çerçevelerini veya [Açısal](https://angular.io/) veya [React](https://facebook.github.io/react/)gibi kitaplıkları ASP.NET Core gibi sunucu tarafındaki çerçevelerle tümleştirmek zor olabilir. JavaScript Hizmetleri entegrasyon sürecinde sürtünmeyi azaltmak için geliştirilmiştir. Farklı istemci ve sunucu teknolojisi yığınları arasında sorunsuz çalışma sağlar.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Bu makalede açıklanan özellikler, ASP.NET Core 3.0 itibariyle geçersizdir. Daha basit bir SPA çerçeveleri tümleştirme mekanizması [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet paketinde mevcuttur. Daha fazla bilgi için [bkz: [Duyuru] Obsoleting Microsoft.AspNetCore.SpaServices ve Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>JavaScript Hizmetleri Nedir

JavaScript Hizmetleri, ASP.NET Core için istemci tarafı teknolojilerinin bir koleksiyonudur. Amacı, ASP.NET Core'u geliştiricilerin SP'leri oluşturmak için tercih ettiği sunucu tarafı platformu olarak konumlandırmaktır.

JavaScript Hizmetleri iki farklı NuGet paketinden oluşur:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Bu paketler aşağıdaki senaryolarda yararlıdır:

* JavaScript'i sunucuda çalıştırın
* SPA çerçevesi veya kitaplık kullanma
* Webpack ile istemci tarafı varlıklar oluşturma

Bu makaledeki odak noktası nın çoğu SpaServices paketinin kullanılmasıdır.

## <a name="what-is-spaservices"></a>SpaServices Nedir

SpaServices, ASP.NET Core'u geliştiricilerin SP'leri oluşturmak için tercih ettiği sunucu tarafı platformu olarak konumlandırmak için oluşturulmuştur. SpaServices ASP.NET Core ile SP'ler geliştirmek için gerekli değildir ve geliştiricileri belirli bir istemci çerçevesine kilitlemez.

SpaServices aşağıdakiler gibi yararlı altyapı sağlar:

* [Sunucu tarafı ön oluşturma](#server-side-prerendering)
* [Webpack Dev Middleware](#webpack-dev-middleware)
* [Sıcak Modül Değişimi](#hot-module-replacement)
* [Yönlendirme yardımcıları](#routing-helpers)

Bu altyapı bileşenleri toplu olarak hem geliştirme iş akışını hem de çalışma zamanı deneyimini geliştirir. Bileşenler ayrı ayrı kabul edilebilir.

## <a name="prerequisites-for-using-spaservices"></a>SpaServices kullanmak için ön koşullar

SpaServices ile çalışmak için aşağıdakileri yükleyin:

* [Node.js](https://nodejs.org/) (sürüm 6 veya sonraki) npm ile

  * Bu bileşenlerin yüklü olduğunu ve bulunabileceğini doğrulamak için komut satırından aşağıdakileri çalıştırın:

    ```console
    node -v && npm -v
    ```

  * Bir Azure web sitesine dağıtılıyorsanız,&mdash;Node.js yüklü ve sunucu ortamlarında kullanılabilir bir eylem gerekmez.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * Visual Studio 2017'yi kullanan Windows'da SDK, **.NET Core çapraz platform geliştirme** iş yükünü seçerek yüklenir.

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet paketi

## <a name="server-side-prerendering"></a>Sunucu tarafı ön oluşturma

Evrensel (izomorfik olarak da bilinir) uygulama, hem sunucuda hem de istemcide çalıştırabilen bir JavaScript uygulamasıdır. Açısal, React ve diğer popüler çerçeveler bu uygulama geliştirme stili için evrensel bir platform sağlar. Fikir, çerçeve bileşenlerini önce Node.js üzerinden sunucuda işlemek ve daha sonra daha fazla yürütmeyi istemciye devretmektir.

SpaServices tarafından sağlanan ASP.NET Çekirdek [Etiket Yardımcıları,](xref:mvc/views/tag-helpers/intro) sunucudaki JavaScript işlevlerini çağırarak sunucu tarafında ön oluşturma uygulamasını basitleştirir.

### <a name="server-side-prerendering-prerequisites"></a>Sunucu tarafı önoluşturma önkoşulları

[aspnet önişleme](https://www.npmjs.com/package/aspnet-prerendering) npm paketini yükleyin:

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Sunucu tarafı önoluşturma yapılandırması

Tag Yardımcıları, projenin *_ViewImports.cshtml* dosyasındaki ad alanı kaydı ile bulunabilir hale getirilir:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Bu Tag Yardımcıları, Razor görünümü nde HTML benzeri bir sözdiziminden yararlanarak düşük seviyeli API'lerle doğrudan iletişim kurmanın inceliklerini özetler:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>asp-prerender-modül Etiket Yardımcı

Önceki `asp-prerender-module` kod örneğinde kullanılan Etiket Yardımcısı, Node.js aracılığıyla sunucuda *ClientApp/dist/main-server.js* yürütür. Netlik aşkına, *main-server.js* dosyası [Webpack](https://webpack.github.io/) oluşturma işleminde TypeScript-to-JavaScript transpilation task bir artifakı olduğunu. Webpack bir giriş noktası diğer `main-server`ad tanımlar; ve bu diğer ad için bağımlılık grafiğinin geçişi *ClientApp/boot-server.ts* dosyasında başlar:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

Aşağıdaki Açısal örnekte, *ClientApp/boot-server.ts* dosyası, `createServerRenderer` Node.js `aspnet-prerendering` üzerinden sunucu oluşturmayı yapılandırmak için npm paketinin işlevini ve `RenderResult` türünü kullanır. Sunucu tarafı oluşturma için ayrılan HTML biçimlendirmesi, güçlü bir şekilde yazılmış bir JavaScript `Promise` nesnesine sarılmış bir çözüm işlevi çağrısına aktarılır. Nesnenin `Promise` önemi, HTML biçimlendirmesini DOM'un yer tutucu öğesinde enjeksiyon için sayfaya eş senkronize olarak tesbit etmesidir.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>asp-prerender-data Tag Helper

`asp-prerender-module` Tag Helper ile birleştiğinde, Tag Helper, Razor görünümünden sunucu tarafındaki JavaScript'e bağlamsal bilgileri aktarmak için `asp-prerender-data` kullanılabilir. Örneğin, aşağıdaki biçimlendirme kullanıcı verilerini `main-server` modüle geçirir:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Alınan `UserName` bağımsız değişken yerleşik JSON serilandırıcı kullanılarak seri hale getirilmiştir `params.data` ve nesnede depolanır. Aşağıdaki Açısal örnekte, veriler bir `h1` öğe içinde kişiselleştirilmiş bir karşılama oluşturmak için kullanılır:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Tag Helpers'da geçirilen özellik adları **PascalCase** gösterimi ile temsil edilir. Aynı özellik adlarının **camelCase**ile temsil edildiği JavaScript ile karşıtlık. Varsayılan JSON serileştirme yapılandırması bu farkın sorumlusudur.

Önceki kod örneğini genişletmek için, veriler `globals` `resolve` işleve sağlanan özelliği nemlendirerek sunucudan görünüme aktarılabilir:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

Nesneiçinde `postList` `globals` tanımlanan dizi tarayıcının genel `window` nesnesine eklenir. Bu değişken in global kapsama çekilmesi, özellikle aynı verileri sunucuya ve istemciye bir kez yüklemeyle ilgili olduğundan, çabanın çoğaltılmasını ortadan kaldırır.

![pencere nesnesine bağlı global postList değişkeni](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpack Dev Middleware

[Webpack Dev Middleware, Webpack'in](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) talep üzerine kaynak oluşturduğu kolaylaştırılmış bir geliştirme iş akışı sunar. Bir sayfa tarayıcıda yeniden yüklendiğinde ara yazılım istemci tarafındaki kaynakları otomatik olarak derler ve hizmet vermektedir. Alternatif yaklaşım, bir üçüncü taraf bağımlılık veya özel kod değiştiğinde projenin npm yapı komut dosyası üzerinden Webpack'i el ile çağırmaktır. *package.json* dosyasındaki bir npm yapı komut dosyası aşağıdaki örnekte gösterilir:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Webpack Dev Middleware ön koşullar

[aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm paketini yükleyin:

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Webpack Dev Middleware yapılandırması

Webpack Dev Middleware, *Startup.cs* dosyasının `Configure` yönteminde aşağıdaki kod aracılığıyla HTTP istek ardışık hattına kaydedilir:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Uzantı `UseWebpackDevMiddleware` yöntemi ile `UseStaticFiles` [statik dosya barındırma kaydetmeden](xref:fundamentals/static-files) önce uzantısı yöntemi çağrılmalıdır. Güvenlik nedenleriyle, ara yazılımı yalnızca uygulama geliştirme modunda çalıştığında kaydedin.

*Webpack.config.js* dosyasının `output.publicPath` özelliği, ara yazılıma `dist` değişiklikler için klasörü izlemelerini söyler:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Sıcak Modül Değişimi

Webpack'in [Sıcak Modül Değiştirme](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) özelliğini [Webpack Dev Middleware'in](#webpack-dev-middleware)bir evrimi olarak düşünün. HMR tüm aynı avantajları sunar, ancak değişiklikleri derledikten sonra sayfa içeriğini otomatik olarak güncelleyerek geliştirme iş akışını daha da kolaylaştırır. Bunu, SPA'nın geçerli bellek içi durumu ve hata ayıklama oturumunu engelleyecek tarayıcının yenilenmesiyle karıştırmayın. Webpack Dev Middleware hizmeti ile tarayıcı arasında canlı bir bağlantı vardır, bu da değişikliklerin tarayıcıya itildiği anlamına gelir.

### <a name="hot-module-replacement-prerequisites"></a>Sıcak Modül Değiştirme ön koşulları

[Webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm paketini yükleyin:

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Sıcak Modül Değiştirme yapılandırması

HMR bileşeni yöntemde MVC'nin HTTP istek `Configure` ardışık hattına kaydedilmelidir:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

[Webpack Dev Middleware'de](#webpack-dev-middleware)olduğu `UseWebpackDevMiddleware` gibi, uzantı `UseStaticFiles` yöntemi uzatma yönteminden önce çağrılmalıdır. Güvenlik nedenleriyle, ara yazılımı yalnızca uygulama geliştirme modunda çalıştığında kaydedin.

*Webpack.config.js* dosyası boş `plugins` bırakılmış olsa bile bir dizi tanımlamalıdır:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Uygulamayı tarayıcıya yükledikten sonra, geliştirici araçlarının Konsol sekmesi HMR aktivasyonunun onayını sağlar:

![Sıcak Modül Değiştirme bağlı mesaj](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Yönlendirme yardımcıları

Çoğu ASP.NET Core tabanlı SP'lerde, istemci tarafı yönlendirmegenellikle sunucu tarafı yönlendirmeye ek olarak istenir. SPA ve MVC yönlendirme sistemleri müdahale olmadan bağımsız olarak çalışabilir. Ancak, zorluklar alabilmeniz için bir kenar durumu vardır: 404 HTTP yanıtlarını tanımlamak.

Uzantısız bir rotanın kullanıldığı `/some/page` senaryoyu göz önünde bulundurun. İsteğin sunucu tarafındaki bir rotayla eşleşmediğini, ancak deseni istemci tarafındaki bir rotayla eşleşmiyor. Şimdi genellikle sunucuda `/images/user-512.png`bir görüntü dosyası bulmak için bekliyor , için gelen bir istek düşünün. İstenen bu kaynak yolu herhangi bir sunucu tarafı rotası veya statik dosyayla eşleşmiyorsa, istemci tarafındaki uygulamanın&mdash;genellikle 404 HTTP durum kodunu döndürerek işlemesi olası değildir.

### <a name="routing-helpers-prerequisites"></a>Yönlendirme yardımcıları ön koşullar

İstemci tarafı yönlendirme npm paketini yükleyin. Açısal'ı örnek olarak kullanma:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Yardımcı yapılandırmayı yönlendirme

Yöntemde adlandırılmış bir `MapSpaFallbackRoute` uzantı yöntemi kullanılır: `Configure`

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Rotalar, yapılandırıldıkları sırada değerlendirilir. Sonuç olarak, `default` önceki kod örneğindeki rota önce desen eşleştirme için kullanılır.

## <a name="create-a-new-project"></a>Yeni bir proje oluşturma

JavaScript Hizmetleri önceden yapılandırılmış uygulama şablonları sağlar. SpaServices bu şablonlarda Açısal, React ve Redux gibi farklı çerçeveler ve kitaplıklarla birlikte kullanılır.

Bu şablonlar aşağıdaki komutu çalıştırarak .NET Core CLI üzerinden yüklenebilir:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Kullanılabilir SPA şablonlarının listesi görüntülenir:

| Şablonlar                                 | Kısa Ad | Dil | Etiketler        |
| ------------------------------------------| :--------: | :------: | :---------: |
| Açısal ASP.NET Çekirdek             | Açısal    | [C#]     | Web/MVC/SPA |
| React.js ile MVC ASP.NET Çekirdek            | Tepki      | [C#]     | Web/MVC/SPA |
| React.js ve Redux ile MVC ASP.NET Çekirdek  | reactredux | [C#]     | Web/MVC/SPA |

SPA şablonlarından birini kullanarak yeni bir proje oluşturmak için, şablonun **kısa adını** [dotnet yeni](/dotnet/core/tools/dotnet-new) komutuna ekleyin. Aşağıdaki komut, sunucu tarafı için yapılandırılan ASP.NET Core MVC ile açısal bir uygulama oluşturur:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Çalışma zamanı yapılandırma modunu ayarlama

İki birincil çalışma zamanı yapılandırma modu vardır:

* **Geliştirme**:
  * Hata ayıklamayı kolaylaştırmak için kaynak haritalar içerir.
  * Performans için istemci tarafı kodunu optimize etmez.
* **Üretim**:
  * Kaynak eşlemleri hariç tutar.
  * Birleştirme ve kıyma yoluyla istemci tarafı kodunu optimize eder.

ASP.NET Core, yapılandırma `ASPNETCORE_ENVIRONMENT` modunu depolamak için bir ortam değişkeni kullanır. Daha fazla bilgi için [bkz.](xref:fundamentals/environments#set-the-environment)

### <a name="run-with-net-core-cli"></a>.NET Core CLI ile çalıştırın

Proje kökünde aşağıdaki komutu çalıştırarak gerekli NuGet ve npm paketlerini geri yükleyin:

```dotnetcli
dotnet restore && npm i
```

Uygulamayı oluşturun ve çalıştırın:

```dotnetcli
dotnet run
```

Uygulama [çalışma zamanı yapılandırma moduna](#set-the-runtime-configuration-mode)göre localhost'ta başlar. `http://localhost:5000` Tarayıcıda gezinirken açılış sayfası görüntülenir.

### <a name="run-with-visual-studio-2017"></a>Visual Studio ile Çalıştır 2017

[dotnet yeni](/dotnet/core/tools/dotnet-new) komutu tarafından oluşturulan *.csproj* dosyasını açın. Gerekli NuGet ve npm paketleri proje açıldıktan sonra otomatik olarak geri yüklenir. Bu geri yükleme işlemi birkaç dakika kadar sürebilir ve uygulama tamamlandığında çalışmaya hazırdır. Yeşil çalıştır düğmesine `Ctrl + F5`tıklayın veya basın ve tarayıcı uygulamanın açılış sayfasına açılır. Uygulama [çalışma zamanı yapılandırma moduna](#set-the-runtime-configuration-mode)göre localhost çalışır.

## <a name="test-the-app"></a>Uygulamayı test edin

SpaServices şablonları [Karma](https://karma-runner.github.io/1.0/index.html) ve [Yasemin](https://jasmine.github.io/)kullanarak istemci tarafı testleri çalıştırmak için önceden yapılandırılmıştır. Yasemin JavaScript için popüler bir birim test çerçevesi, Karma ise bu testler için bir test koşucusu. Karma, [web paketi Dev Middleware](#webpack-dev-middleware) ile çalışacak şekilde yapılandırılmıştır, bu nedenle geliştiricinin her değişiklik yapıldığında testi durdurması ve çalıştırması gerekmez. İster test çalışmasına karşı çalışan kod ister test çalışmasının kendisine karşı olsun, test otomatik olarak çalışır.

Açısal uygulamayı örnek olarak kullanarak, `CounterComponent` *counter.component.spec.ts* dosyasında iki Yasemin test çalışması zaten sağlanmıştır:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

*ClientApp* dizinindeki komut istemini açın. Şu komutu çalıştırın:

```console
npm test
```

Komut dosyası *karma.conf.js* dosyasında tanımlanan ayarları okuyan Karma test koşucusu başlattı. Diğer ayarların yanı sıra *karma.conf.js,* dizi aracılığıyla `files` yürütülecek test dosyalarını tanımlar:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Uygulamayı yayımlama

Azure'da yayımlama hakkında daha fazla bilgi için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/12474) bakın.

Oluşturulan istemci tarafı varlıkları ve yayınlanan ASP.NET Core yapılarını dağıtmaya hazır bir pakette birleştirmek hantal olabilir. Neyse ki, SpaServices adlı `RunWebpack`özel bir MSBuild hedef ile tüm yayın süreci yönetir:

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

MSBuild hedefi aşağıdaki sorumluluklara sahiptir:

1. npm paketlerini geri yükleyin.
1. Üçüncü taraf, istemci tarafı varlıkların üretim sınıfı bir yapı oluşturun.
1. Özel istemci tarafı varlıklarının üretim sınıfı bir yapı oluşturun.
1. Web paketi tarafından oluşturulan varlıkları yayımlama klasörüne kopyalayın.

Çalışırken MSBuild hedefi çağrılır:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Ek kaynaklar

* [Açısal Dokümanlar](https://angular.io/docs)
