---
title: ASP.NET Core ara yazılıma HTTP işleyicileri ve modülleri geçirme
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
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
uid: migration/http-modules
ms.openlocfilehash: 4abba1d4304bf537bd96623527c851d9d15774a4
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508168"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>ASP.NET Core ara yazılıma HTTP işleyicileri ve modülleri geçirme

Bu makalede [, System. webserver 'deki mevcut ASP.net http modüllerinin ve işleyicilerinin](/iis/configuration/system.webserver/) ASP.NET Core [Ara yazılıma](xref:fundamentals/middleware/index)nasıl geçirileceği gösterilmektedir.

## <a name="modules-and-handlers-revisited"></a>Modüller ve işleyiciler yeniden ziyaret edildi

ASP.NET Core ara yazılıma devam etmeden önce, ilk olarak HTTP modüllerinin ve işleyicilerinin nasıl çalıştığını görelim:

![Modüller Işleyicisi](http-modules/_static/moduleshandlers.png)

**İşleyiciler şunlardır:**

* [IHttpHandler](/dotnet/api/system.web.ihttphandler) uygulayan sınıflar

* Verilen bir dosya adına veya uzantıya sahip istekleri işlemek için kullanılır *. rapor*

* *Web.config* ' de [yapılandırıldı](/iis/configuration/system.webserver/handlers/)

**Modüller şunlardır:**

* [IHttpModule](/dotnet/api/system.web.ihttpmodule) uygulayan sınıflar

* Her istek için çağrılır

* Kısa devre verebilir (bir isteğin daha fazla işlenmesini durdurabilirsiniz)

* HTTP yanıtına ekleme yapabilir veya kendi kendine oluşturma

* *Web.config* ' de [yapılandırıldı](/iis/configuration/system.webserver/modules/)

**Modüllerin gelen istekleri işleme sırası şu şekilde belirlenir:**

1. , <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> ASP.NET tarafından tetiklenen bir dizi olaydır: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [kimlik doğrulayan terequest](/dotnet/api/system.web.httpapplication.authenticaterequest), vb. Her modül, bir veya daha fazla olay için bir işleyici oluşturabilir.

2. Aynı olay için, *Web.config* yapılandırıldığı sıra.

Modüllere ek olarak, *Global.asax.cs* dosyanıza yaşam döngüsü olaylarının işleyicilerini ekleyebilirsiniz. Bu işleyiciler, yapılandırılan modüllerdeki işleyicilerden sonra çalışır.

## <a name="from-handlers-and-modules-to-middleware"></a>İşleyiciler ve modüllerden ara yazılıma

**Ara yazılım HTTP modülleriyle ve işleyicilerinden daha basittir:**

* Modüller, işleyiciler, *Global.asax.cs* , *Web.config* (IIS yapılandırması hariç) ve uygulama yaşam döngüsü kayboldu

* Her iki modülün ve işleyicinin rolleri, ara yazılım tarafından ele alınmıştır

* Ara yazılım *Web.config* yerine kod kullanarak yapılandırılır

::: moniker range=">= aspnetcore-3.0"

* [Ardışık düzen dallandırma](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) , yalnızca URL 'ye değil, istek üst bilgileri, sorgu dizeleri vb. gibi belirli bir ara yazılıma istek göndermenizi sağlar.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* [Ardışık düzen dallandırma](xref:fundamentals/middleware/index#use-run-and-map) , yalnızca URL 'ye değil, istek üst bilgileri, sorgu dizeleri vb. gibi belirli bir ara yazılıma istek göndermenizi sağlar.

::: moniker-end

**Ara yazılım, modüllere çok benzer:**

* Her istek için prensibi çağrıldı

* İstek, bir [sonraki ara yazılıma geçirilmeyen](#http-modules-shortcircuiting-middleware) bir istek için kısa devre verebilir

* Kendi HTTP yanıtlarını oluşturabilebiliyor

**Ara yazılım ve modüller farklı bir sırayla işlenir:**

* Ara yazılım sırası, istek ardışık düzenine göre eklendikleri sıraya göre, modüller sırası genellikle olayları temel alır <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>

* Yanıt için ara yazılım sırası, istekler için bu istekten ters, modüller sırası istekler ve yanıtlar için de aynıdır

* Bkz. [IApplicationBuilder ile ara yazılım işlem hattı oluşturma](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![Ara yazılım](http-modules/_static/middleware.png)

Yukarıdaki görüntüde, kimlik doğrulama ara, isteği kabul eden bir şekilde kabul edin.

## <a name="migrating-module-code-to-middleware"></a>Modül kodunu ara yazılıma geçirme

Var olan bir HTTP modülü şuna benzer olacaktır:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

[Ara](xref:fundamentals/middleware/index) yazılım sayfasında gösterildiği gibi, ASP.NET Core bir ara yazılım, bir `Invoke` `HttpContext` ve döndüren bir yöntemi ortaya çıkaran bir sınıftır `Task` . Yeni ara yazılım şu şekilde görünür:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

Yukarıdaki ara yazılım şablonu, [Ara yazılım yazma](xref:fundamentals/middleware/write)bölümündeki bölümden alınmıştır.

*MyMiddlewareExtensions* yardımcı sınıfı, kendi ara ortamınızı `Startup` sınıfınıza yapılandırmayı kolaylaştırır. `UseMyMiddleware`Yöntemi, ara yazılım sınıfınızı istek ardışık düzenine ekler. Ara yazılım tarafından istenen hizmetler, ara yazılım oluşturucusuna eklenir.

<a name="http-modules-shortcircuiting-middleware"></a>

Modülünüzün bir isteği sonlandırabilir, örneğin Kullanıcı yetkilendirilmezse:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Bir ara yazılım `Invoke` , işlem hattındaki sonraki ara yazılıma çağrı gerçekleştirerek bunu işler. Yanıt işlem hattı üzerinden geri geldiğinde, önceki middlewares hala çağrıldığı için, bu, isteği tamamen sonlandıramadığından emin olun.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Modülünüzün işlevselliğini yeni ara yazılıma geçirdiğinizde, `HttpContext` sınıf ASP.NET Core içinde önemli ölçüde değiştiğinden kodunuzun derlenmediğini fark edebilirsiniz. [Daha sonra](#migrating-to-the-new-httpcontext), yeni ASP.NET Core HttpContext 'e nasıl geçirebileceğiniz göreceksiniz.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Modül ekleme isteği ardışık düzenine geçiriliyor

HTTP modülleri, genellikle *Web.config* kullanarak istek ardışık düzenine eklenir:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

[Yeni ara ortamınızı](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) sınıfınızdaki istek ardışık düzenine ekleyerek bunu dönüştürün `Startup` :

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

Yeni ara hattınızı eklediğiniz işlem hattının tam noktası, bir modül ( `BeginRequest` , `EndRequest` , vb.) olarak işlenen olaya ve *Web.config* içindeki modüller listenizde sıraya bağlıdır.

Daha önce belirtildiği gibi, ASP.NET Core hiçbir uygulama yaşam döngüsü yoktur ve bu işlem, yaşam döngüsünün işlenme sırası modüller tarafından kullanılan siparişten farklıdır. Bu, sipariş kararlarınızı daha zor hale getirir.

Sıralama bir sorun haline gelirse, modülünüzü bağımsız olarak sıralanmış birden fazla ara yazılım bileşenine bölebilirsiniz.

## <a name="migrating-handler-code-to-middleware"></a>İşleyici kodunu ara yazılıma geçirme

HTTP işleyicisi şuna benzer:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

ASP.NET Core projenizde bunu şuna benzer bir ara yazılıma çevirmeniz gerekir:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Bu ara yazılım, modüllerle ilgili olan ara yazılıma çok benzer. Gerçek fark, burada hiçbir çağrı olmadığı için geçerlidir `_next.Invoke(context)` . Bu anlamlı hale gelir, çünkü işleyici istek ardışık düzeninin sonunda olduğundan, çağırmak için bir sonraki ara yazılım olmayacaktır.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>İşleyici ekleme isteği ardışık düzenine geçiriliyor

HTTP işleyicisini yapılandırma *Web.config* yapılır ve şuna benzer:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Bu, yeni işleyici ara ortamınızı sınıfınızdaki istek ardışık düzenine ekleyerek `Startup` , modüllerden dönüştürülen ara yazılıma benzer şekilde dönüştürebilirsiniz. Bu yaklaşımla ilgili sorun, tüm istekleri yeni işleyici ara yazılıma göndermektir. Ancak, yalnızca belirli bir uzantıya sahip isteklerin ara yazılıma ulaşmasını istiyorsunuz. Bu, size HTTP işleyiciniz ile aynı işlevselliği sağlar.

Bir çözüm, genişletme yöntemi kullanılarak belirli bir uzantıya sahip istekler için ardışık düzeni dallandırmayı kullanmaktır `MapWhen` . Bunu, `Configure` diğer ara yazılımı eklediğiniz yöntemde yapabilirsiniz:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` Şu parametreleri alır:

1. `HttpContext`' İ alan ve `true` isteğin dala gitmesi gerekiyorsa, döndüren bir lambda. Bu, yalnızca uzantısına göre değil, istek üst bilgileri, sorgu dizesi parametreleri vb. için istekleri dallayabileceğiniz anlamına gelir.

2. `IApplicationBuilder`Ve dalı için tüm ara yazılımı ekleyen bir lambda. Bu, işleyici ara uçınızın önünde dala ek ara yazılım ekleyebileceğiniz anlamına gelir.

Dal tüm isteklerde çağrılmadan önce işlem hattına eklenen ara yazılım; dalın üzerinde hiçbir etkisi olmaz.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Seçenekler modelini kullanarak ara yazılım seçeneklerini yükleme

Bazı modüller ve işleyiciler *Web.config* depolanan yapılandırma seçeneklerine sahiptir. Ancak ASP.NET Core *Web.config* yerine yeni bir yapılandırma modeli kullanılır.

Yeni [yapılandırma sistemi](xref:fundamentals/configuration/index) , bunu çözümlemek için size bu seçenekleri sunar:

* [Sonraki bölümde](#loading-middleware-options-through-direct-injection)gösterildiği gibi, seçenekleri doğrudan ara yazılıma ekleyin.

* [Seçenekler modelini](xref:fundamentals/configuration/options)kullanın:

1. Ara yazılım seçeneklerinizi barındıracak bir sınıf oluşturun, örneğin:

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Seçenek değerlerini depolama

   Yapılandırma sistemi istediğiniz her yerde seçenek değerlerini depolamanıza olanak tanır. Ancak, çoğu site *appsettings.json* tarafından kullanılır, bu yaklaşım şu şekilde ele alacağız:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   Burada *MyMiddlewareOptionsSection* bir bölüm adıdır. Seçenek sınıfınızın adıyla aynı olması gerekmez.

3. Seçenek değerlerini Options sınıfıyla ilişkilendirin

    Seçenekler stili, seçenek türünü (gibi `MyMiddlewareOptions` ) gerçek seçeneklere sahip bir nesneyle ilişkilendirmek için ASP.NET Core bağımlılık ekleme çerçevesini kullanır `MyMiddlewareOptions` .

    Sınıfınızı güncelleştirin `Startup` :

   1. Kullanıyorsanız *appsettings.json* , oluşturucunun yapılandırma oluşturucusuna ekleyin `Startup` :

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Seçenekler hizmetini yapılandırın:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Seçeneklerinizi seçenekler sınıfınız ile ilişkilendirin:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Seçenekleri, ara yazılım yapıcısına ekleyin. Bu, ekleme seçeneklerine bir denetleyiciye benzer.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   Ara ortamınızı bağımlılık ekleme ' ye ekleyen [Useara yazılım](#http-modules-usemiddleware) genişletme yöntemi `IApplicationBuilder` .

   Bu nesnelerle sınırlı değildir `IOptions` . Bu şekilde, ara yazılım için gereken diğer tüm nesneler bu şekilde eklenebilir.

## <a name="loading-middleware-options-through-direct-injection"></a>Doğrudan ekleme yoluyla ara yazılım seçeneklerini yükleme

Seçenekler deseninin, seçenek değerleri ve tüketicileri arasında gevşek bir bağlantısını oluşturduğunu avantajı vardır. Bir seçenek sınıfını gerçek seçenek değerleriyle ilişkilendirdikten sonra, diğer herhangi bir sınıf, bağımlılık ekleme çerçevesi aracılığıyla seçeneklere erişim alabilir. Seçenek değerlerini geçirmeye gerek yoktur.

Bu, farklı seçeneklerle aynı ara yazılımı iki kez kullanmak istiyorsanız bu seçeneği keser. Örneğin farklı dallarda farklı rollere izin veren bir yetkilendirme ara yazılımı. İki farklı seçenek nesnesini tek bir seçenek sınıfıyla ilişkilendiremezsiniz.

Çözüm, seçenek nesnelerini sınıfınıza gerçek seçenek değerleriyle almak `Startup` ve bunları doğrudan ara yazılım örneğine iletmektir.

1. İkinci bir anahtar Ekle *appsettings.json*

   Dosyaya ikinci bir seçenek kümesi eklemek için *appsettings.json* , benzersiz bir şekilde tanımlamak için yeni bir anahtar kullanın:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Seçenek değerlerini alın ve bunları ara yazılıma geçirin. `Use...`Genişletme yöntemi (ara yazılımını işlem hattına ekleyen), seçenek değerlerinde geçirilecek mantıksal bir yerdir: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Bir seçenek parametresi almak için ara yazılımı etkinleştirin. Uzantı yönteminin bir aşırı yüklemesini sağlayın `Use...` (Options parametresini alır ve ' ye geçirir `UseMiddleware` ). `UseMiddleware`Parametreler ile çağrıldığında, ara yazılım nesnesini örnekleyen zaman, parametreleri ara yazılım yapıcısına geçirir.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Bunun nesne içindeki Options nesnesini nasıl saryacağını aklınızda edin `OptionsWrapper` . Bu `IOptions` , ara yazılım oluşturucusu tarafından beklendiği şekilde uygulanır.

## <a name="migrating-to-the-new-httpcontext"></a>Yeni HttpContext 'e geçiş

Daha önce, `Invoke` Ara ortamınızdaki yöntemin türünde bir parametre alıp aldıklarını gördünüz `HttpContext` :

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` ASP.NET Core içinde önemli ölçüde değişmiştir. Bu bölümde, en yaygın olarak kullanılan [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) özelliklerinin yeni sürümüne nasıl çevrilebileceğini gösterir `Microsoft.AspNetCore.Http.HttpContext` .

### <a name="httpcontext"></a>HttpContext

**HttpContext. Items** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**Benzersiz istek KIMLIĞI (System. Web. HttpContext karşılığı)**

Size her istek için benzersiz bir kimlik verir. Günlüklerinizi eklemek çok yararlı.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext. Request

**HttpContext. Request. HttpMethod** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext. Request. QueryString** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext. Request. URL** ve **HttpContext. Request. RawUrl** şu şekilde çeviri yapar:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext. Request. IsSecureConnection** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext. Request. UserHostAddress** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext. Request. Cookie , şu** şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext. Request. RequestContext. RouteData** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext. Request. Headers** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext. Request. UserAgent** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext. Request. Urlbaşvuran** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext. Request. ContentType şuna** çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext. Request. form** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Form değerlerini yalnızca içerik alt türü *x-www-form-urlencoded* veya *form-Data* ise okuyun.

**HttpContext. Request. InputStream** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Bu kodu yalnızca bir işlem hattının sonundaki bir işleyici türü ara yazılım içinde kullanın.
>
>Ham gövdeyi, her istek için yukarıda yalnızca bir kez gösterildiği gibi okuyabilirsiniz. İlk okuduktan sonra gövde okumayı deneyen ara yazılım boş bir gövde okur.
>
>Bu, bir arabelleğin yapıldığı için daha önce gösterildiği gibi bir formu okumak için uygulanmaz.

### <a name="httpcontextresponse"></a>HttpContext. Response

**HttpContext. Response. Status** ve **HttpContext. Response. StatusDescription** şu şekilde çeviri yapar:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext. Response. Contentenkodlamaya** ve **HttpContext. Response. ContentType şuna** çevir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

Yalnızca **HttpContext. Response. ContentType şuna** çevrilir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext. Response. Output** şu şekilde çevirir:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext. Response. TransmitFile**

Bir dosya sunma konusunda açıklanmaktadır <xref:fundamentals/request-features> .

**HttpContext. Response. Headers**

Yanıt üst bilgilerinin gönderilmesi, yanıt gövdesine herhangi bir şey yazıldıktan sonra ayarlandıklarında, gönderilmeyeceği için karmaşıktır.

Çözüm, yanıt başladıktan sonra hemen çağrılacak bir geri çağırma yöntemi ayarlamak olacaktır. Bu en iyi işlem, `Invoke` Ara ortamınızdaki yöntemin başlangıcında yapılır. Yanıt başlıklarınızı ayarlayan bu geri çağırma yöntemidir.

Aşağıdaki kod, adlı bir geri çağırma yöntemi ayarlar `SetHeaders` :

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetHeaders`Geri çağırma yöntemi şöyle görünür:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext. Response. Cookie malar**

Cookies, bir *set- Cookie* Response üst bilgisinde tarayıcıya seyahat ediyor. Sonuç olarak, ' cookie ın gönderilmesi yanıt üst bilgilerini göndermek için kullanılan geri çağırma işlemini gerektirir:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetCookies`Geri çağırma yöntemi aşağıdaki gibi görünür:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP Işleyicileri ve HTTP modüllerine genel bakış](/iis/configuration/system.webserver/)
* [Yapılandırma](xref:fundamentals/configuration/index)
* [Uygulama başlatma](xref:fundamentals/startup)
* [Ara yazılım](xref:fundamentals/middleware/index)
