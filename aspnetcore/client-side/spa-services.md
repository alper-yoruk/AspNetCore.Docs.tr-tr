---
title: ASP.NET Core içinde tek sayfalı uygulamalar oluşturmak için JavaScript hizmetlerini kullanın
author: scottaddie
description: ASP.NET Core tarafından desteklenen tek sayfalı uygulama (SPA) oluşturmak için JavaScript Hizmetleri kullanmanın avantajları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/spa-services
ms.openlocfilehash: 1b9f5b4b4e066cdd3dd5fbfa666c7a087949979f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054651"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>ASP.NET Core içinde tek sayfalı uygulamalar oluşturmak için JavaScript hizmetlerini kullanın

[Scott Ade](https://github.com/scottaddie) ve [fiyaz hasan](https://fiyazhasan.me/) tarafından

Tek sayfalı uygulama (SPA), kendi kendine ait zengin Kullanıcı deneyimi nedeniyle popüler bir Web uygulaması türüdür. ASP.NET Core gibi sunucu tarafı çerçeveleri ile, [angular](https://angular.io/) veya yanıt verme gibi ISTEMCI tarafı Spa [çerçevelerini veya kitaplıklarını](https://facebook.github.io/react/)tümleştirme zor olabilir. Tümleştirme sürecinde uçuşmayı azaltmak için JavaScript Hizmetleri geliştirilmiştir. Farklı istemci ve sunucu teknoloji yığınları arasında sorunsuz işleme sunar.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Bu makalede açıklanan özellikler ASP.NET Core 3,0 itibariyle kullanımdan kalkmıştır. [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet paketinde daha basıt bir spa çerçeveleri tümleştirme mekanizması mevcuttur. Daha fazla bilgi için bkz. [Microsoft. aspnetcore. spaservices ve Microsoft. AspNetCore. NodeServices üzerinde kullanımdan bulunan [Duyuru]](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>JavaScript Hizmetleri nedir?

JavaScript Hizmetleri, ASP.NET Core yönelik istemci tarafı teknolojilerinin bir koleksiyonudur. Amacı, ASP.NET Core, geliştiricilerin, maça 'Ları oluşturmak için tercih edilen sunucu tarafı platformu olarak konumlandırmaktır.

JavaScript Hizmetleri iki ayrı NuGet paketi içerir:

* [Microsoft. AspNetCore. nodeservices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (nodeservices)
* [Microsoft. AspNetCore. spaservices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (spaservices)

Bu paketler, aşağıdaki senaryolarda faydalıdır:

* Sunucuda JavaScript çalıştırma
* SPA çerçevesi veya kitaplığı kullanma
* WebPack ile istemci tarafı varlıkları derleme

Bu makaledeki odağın çoğu, SpaServices paketi kullanılarak yerleştirilir.

## <a name="what-is-spaservices"></a>Maça Hizmetleri nedir?

İstenmeyen hizmetler ASP.NET Core, geliştiricilerin, maça 'Ları oluşturmak için tercih edilen sunucu tarafı platformu olarak konumlandırmak üzere oluşturulmuştur. Istenmeyen hizmetler ASP.NET Core ile maça geliştirmek için gerekli değildir ve geliştiricileri belirli bir istemci çerçevesinde kilitlemez.

SpaServices şu gibi yararlı bir altyapı sağlar:

* [Sunucu tarafı prerendering](#server-side-prerendering)
* [Web paketi geliştirme ara yazılımı](#webpack-dev-middleware)
* [Sık kullanılan modül değiştirme](#hot-module-replacement)
* [Yönlendirme Yardımcıları](#routing-helpers)

Toplu olarak, bu altyapı bileşenleri hem geliştirme iş akışını hem de çalışma zamanı deneyimini geliştirir. Bileşenler tek tek benimsemiş olabilir.

## <a name="prerequisites-for-using-spaservices"></a>Spaservice kullanımı için Önkoşullar

SpaServices ile çalışmak için aşağıdakileri yüklemelisiniz:

* NPM ile [Node.js](https://nodejs.org/) (sürüm 6 veya üzeri)

  * Bu bileşenlerin yüklendiğini doğrulamak ve bulunabilir olması için komut satırından aşağıdakileri çalıştırın:

    ```console
    node -v && npm -v
    ```

  * Bir Azure Web sitesine dağıtım yapıyorsanız, &mdash; sunucu ortamlarında yüklü ve kullanılabilirNode.js herhangi bir eylem gerekmez.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * Visual Studio 2017 kullanarak Windows 'da SDK, **.NET Core platformlar arası geliştirme** iş yükü seçilerek yüklenir.

* [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet paketi

## <a name="server-side-prerendering"></a>Sunucu tarafı prerendering

Evrensel (isomorphic olarak da bilinir) uygulaması, hem sunucu hem de istemci üzerinde çalışan bir JavaScript uygulamasıdır. Angular, tepki verme ve diğer popüler çerçeveler, bu uygulama geliştirme stili için evrensel bir platform sağlar. Fikir, öncelikle sunucu üzerinde Node.js aracılığıyla çerçeve bileşenlerini işlemek ve ardından istemciye daha fazla yürütme devretmek.

SpaServices tarafından sunulan ASP.NET Core [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucuda JavaScript işlevlerini çağırarak sunucu tarafı prerendering 'in uygulanmasını basitleştirir.

### <a name="server-side-prerendering-prerequisites"></a>Sunucu tarafı prerendering önkoşulları

[ASPNET-prerendering](https://www.npmjs.com/package/aspnet-prerendering) NPM paketini yükler:

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Sunucu tarafı prerendering yapılandırması

Etiket Yardımcıları, projenin *_ViewImports. cshtml* dosyasında ad alanı kaydı aracılığıyla bulunabilir hale getirilir:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Bu etiket yardımcıları, görünümün içindeki HTML benzeri bir söz dizimini kullanarak doğrudan alt düzey API 'lerle iletişim kurmanın karmaşık özelliklerini soyutlar Razor .

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>ASP-PreRender-Module etiketi Yardımcısı

`asp-prerender-module`Yukarıdaki kod örneğinde kullanılan etiket Yardımcısı, sunucu üzerinde *clientapp/dist/main-server.js* ' ı Node.js aracılığıyla yürütür. Netlik için *main-server.js* dosyası, [WebPack](https://webpack.github.io/) derleme sürecinde TypeScript-to-JavaScript transpilation görevinin yapıtı. WebPack, bir giriş noktası diğer adını tanımlar `main-server` ; ve, bu diğer ad için bağımlılık grafiğinin çapraz geçişi *clientapp/Boot-Server. TS* dosyasında başlar:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

Aşağıdaki angular örneğinde, *clientapp/Boot-Server. TS* dosyası, `createServerRenderer` `RenderResult` `aspnet-prerendering` Node.js aracılığıyla sunucu işlemesini yapılandırmak için NPM paketinin işlevini ve türünü kullanır. Sunucu tarafı işlemeye yönelik HTML biçimlendirmesi, kesin türü belirtilmiş bir JavaScript nesnesine Sarmalanan bir Resolve işlev çağrısına geçirilir `Promise` . `Promise`Nesnenin önemi, Dom 'ın yer tutucu öğesine ekleme için sayfaya HTML işaretlemesini zaman uyumsuz olarak sağlar.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>ASP-PreRender-veri etiketi Yardımcısı

Etiket Yardımcısı ile birlikte kullanıldığında `asp-prerender-module` , `asp-prerender-data` bağlam bilgilerini Razor görünümden sunucu tarafı JavaScript 'e geçirmek için etiket Yardımcısı kullanılabilir. Örneğin, aşağıdaki biçimlendirme Kullanıcı verilerini `main-server` modüle geçirir:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Alınan `UserName` bağımsız değişken YERLEŞIK JSON seri hale getirici kullanılarak serileştirilir ve `params.data` nesnesinde depolanır. Aşağıdaki angular örneğinde, verileri bir öğesi içinde kişiselleştirilmiş bir selamlama oluşturmak için kullanılır `h1` :

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Etiket yardımcılarının geçirildiği Özellik adları **PascalCase** gösterimi ile temsil edilir. Aynı özellik adlarının **camelCase** ile temsil edildiği JavaScript 'e kontrast. Varsayılan JSON serileştirme yapılandırması, bu farkından sorumludur.

Yukarıdaki kod örneğini genişletmek için, işlevine verilen özelliği hibir şekilde görüntüleyerek, veriler sunucudan görünüme geçirilebilir `globals` `resolve` :

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

`postList`Nesnenin içinde tanımlanan dizi `globals` tarayıcının genel `window` nesnesine iliştirilir. Genel kapsama açık olan bu değişken, özellikle de aynı verileri bir kez sunucuya ve istemciye yeniden yüklemeye yönelik olarak çabaların çoğaltılmasını ortadan kaldırır.

![pencere nesnesine eklenen genel postList değişkeni](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Web paketi geliştirme ara yazılımı

[Web paketi geliştirme ara yazılımı](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) , Web paketinin kaynakları isteğe bağlı olarak oluşturmakta olan kolaylaştırılmış bir geliştirme iş akışı Yazılım, tarayıcıda bir sayfa yeniden yüklendiğinde istemci tarafı kaynaklarını otomatik olarak derler ve sunar. Alternatif yaklaşım, üçüncü taraf bir bağımlılık veya özel kod değiştiğinde, projenin NPM derleme betiği aracılığıyla WebPack 'i el ile çağırmalıdır. *package.js* dosyadaki bir NPM derleme betiği aşağıdaki örnekte gösterilmiştir:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>WebPack dev ara yazılım önkoşulları

[ASPNET-WebPack](https://www.npmjs.com/package/aspnet-webpack) NPM paketini yükler:

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>WebPack dev ara yazılım yapılandırması

Web paketi geliştirme ara yazılımı, *Startup.cs* dosyasının yönteminde aşağıdaki kod aracılığıyla http istek ardışık düzenine kaydedilir `Configure` :

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Uzantı yöntemi `UseWebpackDevMiddleware` aracılığıyla [statik dosya barındırma](xref:fundamentals/static-files) kaydedilmeden önce genişletme yöntemi çağrılmalıdır `UseStaticFiles` . Güvenlik nedenleriyle, yalnızca uygulama geliştirme modunda çalışırken ara yazılımı kaydedin.

*webpack.config.js* dosyanın `output.publicPath` özelliği, ara yazılımlar için klasörü değişiklikleri izlemesini söyler `dist` :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Sık kullanılan modül değiştirme

WebPack [geliştirme ara yazılımı](#webpack-dev-middleware)'nın bir evrimi olarak Web paketi 'Nin [sık kullanılan modül değiştirme](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) özelliğini düşünün. HMR aynı avantajları sunar, ancak değişiklikleri derledikten sonra sayfa içeriğini otomatik olarak güncelleştirerek geliştirme iş akışını daha da kolaylaştırır. Bunu tarayıcı yenilemesine karıştırmayın, bu da geçerli bellek içi durumu ve SPA 'nın hata ayıklama oturumunu kesintiye uğratır. Web paketi geliştirme ara yazılımı hizmeti ile tarayıcı arasında canlı bir bağlantı vardır ve bu, değişikliklerin tarayıcıya gönderildiği anlamına gelir.

### <a name="hot-module-replacement-prerequisites"></a>Sık kullanılan modül değiştirme önkoşulları

[WebPack-Hot-ara yazılım](https://www.npmjs.com/package/webpack-hot-middleware) NPM paketini yükler:

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Sık kullanılan modül değiştirme yapılandırması

HMR bileşeni, metotta MVC 'nin HTTP isteği ardışık düzeninde kayıtlı olmalıdır `Configure` :

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

[Web paketi geliştirme ara yazılımı](#webpack-dev-middleware)ile doğru olduğu için, genişletme yönteminin `UseWebpackDevMiddleware` uzantı yönteminden önce çağrılması gerekir `UseStaticFiles` . Güvenlik nedenleriyle, yalnızca uygulama geliştirme modunda çalışırken ara yazılımı kaydedin.

 `plugins` Boş bırakılmış olsa bilewebpack.config.jsdosyası bir dizi tanımlamalıdır:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Uygulamayı tarayıcıya yükledikten sonra, Geliştirici araçlarının konsol sekmesi HMR etkinleştirmenin onayını sağlar:

![Sık kullanılan modül değiştirme bağlı iletisi](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Yönlendirme Yardımcıları

Çoğu ASP.NET Core tabanlı maça, istemci tarafı yönlendirme genellikle sunucu tarafı yönlendirmeye ek olarak istenir. SPA ve MVC yönlendirme sistemleri, parazit olmadan bağımsız olarak çalışabilir. Ancak, bir sınır büyük/küçük harf sorunları: 404 HTTP yanıtlarını tanımlama.

Uzantısız bir yolunun kullanıldığı senaryoyu göz önünde bulundurun `/some/page` . İsteğin bir sunucu tarafı rotası ile eşleşmediğini, ancak deseninin bir istemci tarafı rotayla eşleştiğini varsayın. Bundan böyle `/images/user-512.png` , genellikle sunucusunda bir görüntü dosyası bulmayı bekleyen bir gelen isteği düşünün. İstenen kaynak yolu herhangi bir sunucu tarafı yolu veya statik dosya ile eşleşmiyorsa, istemci tarafı uygulamanın bu &mdash; işlemi genellikle bir 404 HTTP durum kodu döndürerek işlemesi çok düşüktür.

### <a name="routing-helpers-prerequisites"></a>Yönlendirme Yardımcıları önkoşulları

İstemci tarafı yönlendirme NPM paketini yükler. Örnek olarak angular kullanma:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Yönlendirme Yardımcıları yapılandırması

Yönteminde adlı uzantı yöntemi `MapSpaFallbackRoute` kullanılır `Configure` :

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Yollar yapılandırıldıkları sırayla değerlendirilir. Sonuç olarak, `default` önceki kod örneğinde yol, önce model eşleştirme için kullanılır.

## <a name="create-a-new-project"></a>Yeni proje oluşturma

JavaScript Hizmetleri önceden yapılandırılmış uygulama şablonları sağlar. Istenmeyen hizmetler, bu şablonlarda, angular, tepki ve Redux gibi farklı çerçeveler ve kitaplıklarla birlikte kullanılır.

Bu şablonlar, aşağıdaki komut çalıştırılarak .NET Core CLI aracılığıyla yüklenebilir:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Kullanılabilir SPA şablonlarının listesi görüntülenir:

| Şablonlar                                 | Kısa Ad | Dil | Etiketler        |
| ------------------------------------------| :--------: | :------: | :---------: |
| Angular ile MVC ASP.NET Core             | Angular    | Þ     | Web/MVC/SPA |
| React.js ile MVC ASP.NET Core            | tıkla      | Þ     | Web/MVC/SPA |
| React.js ve Redux ile MVC ASP.NET Core  | reactredux | Þ     | Web/MVC/SPA |

SPA şablonlarından birini kullanarak yeni bir proje oluşturmak için, [DotNet New](/dotnet/core/tools/dotnet-new) komutuna şablonun **kısa adını** ekleyin. Aşağıdaki komut, sunucu tarafı için yapılandırılmış ASP.NET Core MVC ile bir angular uygulaması oluşturur:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Çalışma zamanı yapılandırma modunu ayarla

İki birincil çalışma zamanı yapılandırma modu var:

* **Geliştirme**:
  * Hata ayıklamayı kolaylaştırmak için kaynak haritaları içerir.
  * , Performans için istemci tarafı kodunu iyileştirmez.
* **Üretim**:
  * Kaynak eşlemelerini dışlar.
  * Paketleme ve küçültmeye göre istemci tarafı kodunu iyileştirir.

ASP.NET Core `ASPNETCORE_ENVIRONMENT` , yapılandırma modunu depolamak için adlı bir ortam değişkenini kullanır. Daha fazla bilgi için bkz. [ortamı ayarlama](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>.NET Core CLI ile Çalıştır

Proje kökünde aşağıdaki komutu çalıştırarak gerekli NuGet ve NPM paketlerini geri yükleyin:

```dotnetcli
dotnet restore && npm i
```

Uygulamayı derleyin ve çalıştırın:

```dotnetcli
dotnet run
```

Uygulama, [çalışma zamanı yapılandırma moduna](#set-the-runtime-configuration-mode)göre localhost üzerinde başlatılır. Tarayıcıda gezinmek `http://localhost:5000` giriş sayfasını görüntüler.

### <a name="run-with-visual-studio-2017"></a>Visual Studio 2017 ile çalıştırma

[DotNet New](/dotnet/core/tools/dotnet-new) komutu tarafından oluşturulan *. csproj* dosyasını açın. Gerekli NuGet ve NPM paketleri proje açık olduğunda otomatik olarak geri yüklenir. Bu geri yükleme işlemi birkaç dakika sürebilir ve bu işlem tamamlandığında uygulama çalıştırılmaya başlayabilir. Yeşil çalışma düğmesine veya tuşuna basın `Ctrl + F5` ve tarayıcı uygulamanın giriş sayfasında açılır. Uygulama, [çalışma zamanı yapılandırma moduna](#set-the-runtime-configuration-mode)göre localhost üzerinde çalışır.

## <a name="test-the-app"></a>Uygulamayı test etme

SpaServices şablonları, [karma](https://karma-runner.github.io/1.0/index.html) ve [Jasmine](https://jasmine.github.io/)kullanarak istemci tarafı testleri çalıştıracak şekilde önceden yapılandırılmıştır. Jasmine JavaScript için popüler bir birim testi çerçevesidir, ancak karma bu testler için bir Test Çalıştırıcısı olur. Karma, geliştiricilerin her değişiklik yapıldığında testi durdurmak ve çalıştırmak için gerekli olmaması gibi [WebPack dev ara yazılımı](#webpack-dev-middleware) ile birlikte çalışmak üzere yapılandırılmıştır. Test çalışmasına veya test çalışmasının kendisine karşı çalışan kod olup olmadığı, test otomatik olarak çalışır.

Angular uygulamasını örnek olarak kullanarak, `CounterComponent` *Counter. Component. spec. TS* dosyasında Için Iki Jasmine test çalışması zaten sunulmaktadır:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

*Clientapp* dizininde komut istemi ' ni açın. Şu komutu çalıştırın:

```console
npm test
```

Betik, *karma.conf.js* dosyasında tanımlanan ayarları okuyan karma Test Çalıştırıcısı 'nı başlatır. Diğer ayarlar arasında *karma.conf.js* , kendi dizisi aracılığıyla yürütülecek test dosyalarını tanımlar `files` :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Uygulamayı yayımlama

Azure 'da yayımlama hakkında daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/12474) bakın.

Oluşturulan istemci tarafı varlıkları ve yayımlanan ASP.NET Core yapıtları, dağıtım için hazırlamış bir pakette birleştirmek çok daha fazla olabilir. Ktam, Spaservice, yayın sürecinin tamamını şu adlı özel bir MSBuild hedefi ile düzenler `RunWebpack` :

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

MSBuild hedefi aşağıdaki sorumluluklara sahiptir:

1. NPM paketlerini geri yükleyin.
1. Üçüncü taraf, istemci tarafı varlıkların üretim sınıfı derlemesini oluşturun.
1. Özel istemci tarafı varlıkların üretim sınıfı derlemesini oluşturma.
1. Web paketi tarafından oluşturulan varlıkları Yayımla klasörüne kopyalayın.

Şu çalıştırılırken MSBuild hedefi çağrılır:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Ek kaynaklar

* [Angular belgeleri](https://angular.io/docs)
