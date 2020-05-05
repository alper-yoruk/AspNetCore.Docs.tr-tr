---
title: ASP.NET Core 'de ıhttpclientfactory kullanarak HTTP istekleri yapın
author: stevejgordon
description: ASP.NET Core içindeki mantıksal HttpClient örneklerini yönetmek için ıhttpclientfactory arabirimini kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: ae33218d6944c62a08e677592ac0c66f9026b15f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766556"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="07b68-103">ASP.NET Core 'de ıhttpclientfactory kullanarak HTTP istekleri yapın</span><span class="sxs-lookup"><span data-stu-id="07b68-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07b68-104">[Glenn CONDRON](https://github.com/glennc), [Ryan şimdi ak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Kirk larkabağı](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="07b68-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="07b68-105">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak <xref:System.Net.Http.HttpClient> için kayıt yapılabilir ve kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="07b68-106">`IHttpClientFactory`aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="07b68-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="07b68-107">, Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="07b68-108">Örneğin, *GitHub* adlı bir Istemci, [GitHub](https://github.com/)'a erişmek için kaydedilebilir ve yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="07b68-109">Varsayılan istemci, genel erişim için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="07b68-110">' De `HttpClient`işleyiciler temsilci seçme yoluyla giden ara yazılım kavramını daha da artırır.</span><span class="sxs-lookup"><span data-stu-id="07b68-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="07b68-111">' De `HttpClient`işleyiciler temsilci atama avantajlarından faydalanmak Için, Polya tabanlı bir ara yazılım için uzantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="07b68-112">Temel `HttpClientMessageHandler` örneklerin biriktirmesini ve ömrünü yönetir.</span><span class="sxs-lookup"><span data-stu-id="07b68-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="07b68-113">Otomatik yönetim, yaşam sürelerini el ile yönetirken `HttpClient` oluşan ortak DNS (etki alanı adı sistemi) sorunlarını önler.</span><span class="sxs-lookup"><span data-stu-id="07b68-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="07b68-114">Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm `ILogger`istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).</span><span class="sxs-lookup"><span data-stu-id="07b68-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="07b68-115">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="07b68-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="07b68-116">Bu konu sürümündeki örnek kod, HTTP yanıtlarında döndürülen JSON içeriğinin serisini kaldırmak için kullanır <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="07b68-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="07b68-117">Ve `Json.NET` `ReadAsAsync<T>`kullanan örnekler için, bu konunun 2. x sürümünü seçmek üzere sürüm seçiciyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="07b68-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="07b68-118">Tüketim desenleri</span><span class="sxs-lookup"><span data-stu-id="07b68-118">Consumption patterns</span></span>

<span data-ttu-id="07b68-119">Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="07b68-120">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="07b68-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="07b68-121">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="07b68-122">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="07b68-123">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="07b68-124">En iyi yaklaşım, uygulamanın gereksinimlerine bağlı olarak değişir.</span><span class="sxs-lookup"><span data-stu-id="07b68-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="07b68-125">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="07b68-125">Basic usage</span></span>

<span data-ttu-id="07b68-126">`IHttpClientFactory`, çağırarak `AddHttpClient`kaydedilebilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="07b68-127">`IHttpClientFactory` [Bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)kullanılarak bir istek yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="07b68-128">Aşağıdaki kod bir `HttpClient` örnek `IHttpClientFactory` oluşturmak için kullanır:</span><span class="sxs-lookup"><span data-stu-id="07b68-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="07b68-129">Yukarıdaki `IHttpClientFactory` örnekte olduğu gibi kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="07b68-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="07b68-130">Kullanım hakkında hiçbir etkisi `HttpClient` yoktur.</span><span class="sxs-lookup"><span data-stu-id="07b68-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="07b68-131">Var olan bir `HttpClient` uygulamada örneklerin oluşturulduğu yerlerde, bu oluşumların ' i çağrılarıyla değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="07b68-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="07b68-132">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-132">Named clients</span></span>

<span data-ttu-id="07b68-133">Adlandırılmış istemciler şu durumlarda iyi bir seçimdir:</span><span class="sxs-lookup"><span data-stu-id="07b68-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="07b68-134">Uygulama birçok farklı kullanımı gerektirir `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="07b68-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="07b68-135">Birçok `HttpClient`s farklı yapılandırmaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="07b68-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="07b68-136">Adlandırılmış `HttpClient` için yapılandırma, içinde `Startup.ConfigureServices`kayıt sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="07b68-137">İstemcinin yapılandırıldığı önceki kodda:</span><span class="sxs-lookup"><span data-stu-id="07b68-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="07b68-138">Temel adres `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="07b68-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="07b68-139">GitHub API 'SI ile çalışmak için iki üst bilgi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="07b68-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="07b68-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="07b68-140">CreateClient</span></span>

<span data-ttu-id="07b68-141">Her zaman <xref:System.Net.Http.IHttpClientFactory.CreateClient*> çağrılır:</span><span class="sxs-lookup"><span data-stu-id="07b68-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="07b68-142">Yeni bir örneği `HttpClient` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="07b68-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="07b68-143">Yapılandırma eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-143">The configuration action is called.</span></span>

<span data-ttu-id="07b68-144">Adlandırılmış bir istemci oluşturmak için adını içine `CreateClient`geçirin:</span><span class="sxs-lookup"><span data-stu-id="07b68-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="07b68-145">Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="07b68-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="07b68-146">İstemci için yapılandırılan taban adresi kullanıldığından, kod yalnızca yolu geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="07b68-147">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-147">Typed clients</span></span>

<span data-ttu-id="07b68-148">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="07b68-148">Typed clients:</span></span>

* <span data-ttu-id="07b68-149">Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="07b68-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="07b68-150">İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="07b68-151">Yapılandırmak ve belirli `HttpClient`bir ile etkileşimde bulunmak için tek bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="07b68-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="07b68-152">Örneğin, tek bir türü belirtilmiş istemci kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="07b68-153">Tek bir arka uç uç noktası için.</span><span class="sxs-lookup"><span data-stu-id="07b68-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="07b68-154">Uç nokta ile ilgili tüm mantığı kapsüllemek için.</span><span class="sxs-lookup"><span data-stu-id="07b68-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="07b68-155">DI ile birlikte çalışın ve uygulamada gerektiğinde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="07b68-156">Türü belirtilmiş istemci, oluşturucusunda `HttpClient` bir parametreyi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="07b68-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="07b68-157">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="07b68-157">In the preceding code:</span></span>

* <span data-ttu-id="07b68-158">Yapılandırma, yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="07b68-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="07b68-159">`HttpClient` Nesne bir ortak özellik olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="07b68-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="07b68-160">İşlevselliği kullanıma `HttpClient` sunan, API 'ye özgü Yöntemler oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="07b68-161">Örneğin, `GetAspNetDocsIssues` yöntemi açık sorunları almak için kodu kapsüller.</span><span class="sxs-lookup"><span data-stu-id="07b68-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="07b68-162">Aşağıdaki kod, türü <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> belirtilmiş `Startup.ConfigureServices` bir istemci sınıfını kaydetmek için ' de çağırır:</span><span class="sxs-lookup"><span data-stu-id="07b68-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="07b68-163">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="07b68-164">Yukarıdaki kodda geçici bir hizmet `AddHttpClient` olarak `GitHubService` kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="07b68-165">Bu kayıt, için bir fabrika yöntemi kullanır:</span><span class="sxs-lookup"><span data-stu-id="07b68-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="07b68-166">`HttpClient` örneği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="07b68-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="07b68-167">Örneğini oluşturucusuna geçirerek `HttpClient` bir `GitHubService`örneğini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="07b68-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="07b68-168">Yazılan istemci doğrudan eklenebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="07b68-169">Türü belirtilmiş bir istemcinin yapılandırması `Startup.ConfigureServices`, türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="07b68-170">, `HttpClient` Türü belirlenmiş bir istemci içinde kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="07b68-171">Bunu bir özellik olarak göstermek yerine, `HttpClient` örneği dahili olarak çağıran bir yöntem tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="07b68-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="07b68-172">Yukarıdaki kodda `HttpClient` , bir özel alanda depolanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="07b68-173">Öğesine erişimi, `HttpClient` genel `GetRepos` yöntemi tarafından yapılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="07b68-174">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-174">Generated clients</span></span>

<span data-ttu-id="07b68-175">`IHttpClientFactory`, [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi üçüncü taraf kitaplıklarla birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="07b68-176">Yeniden sığdırma, .NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="07b68-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="07b68-177">REST API 'Leri canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="07b68-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="07b68-178">Arabirim bir uygulama, dış HTTP çağrıları yapmak için kullanılarak `RestService` `HttpClient` tarafından dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="07b68-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="07b68-179">Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="07b68-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="07b68-180">Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="07b68-181">Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="07b68-182">Giden istek ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="07b68-182">Outgoing request middleware</span></span>

<span data-ttu-id="07b68-183">`HttpClient`, giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramıdır.</span><span class="sxs-lookup"><span data-stu-id="07b68-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="07b68-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="07b68-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="07b68-185">Her bir adlandırılmış istemci için uygulanacak işleyiciler tanımlamayı basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="07b68-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="07b68-186">Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydedilmesini ve zincirleme kullanımını destekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="07b68-187">Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="07b68-188">Bu model:</span><span class="sxs-lookup"><span data-stu-id="07b68-188">This pattern:</span></span>

  * <span data-ttu-id="07b68-189">ASP.NET Core gelen ara yazılım ardışık düzenine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="07b68-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="07b68-190">, HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar, örneğin:</span><span class="sxs-lookup"><span data-stu-id="07b68-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="07b68-191">önbelleği</span><span class="sxs-lookup"><span data-stu-id="07b68-191">caching</span></span>
    * <span data-ttu-id="07b68-192">hata işleme</span><span class="sxs-lookup"><span data-stu-id="07b68-192">error handling</span></span>
    * <span data-ttu-id="07b68-193">getir</span><span class="sxs-lookup"><span data-stu-id="07b68-193">serialization</span></span>
    * <span data-ttu-id="07b68-194">günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="07b68-194">logging</span></span>

<span data-ttu-id="07b68-195">Temsilci seçme işleyicisi oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="07b68-195">To create a delegating handler:</span></span>

* <span data-ttu-id="07b68-196">Türet <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="07b68-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="07b68-197">Geçersiz <xref:System.Net.Http.DelegatingHandler.SendAsync*>kıl.</span><span class="sxs-lookup"><span data-stu-id="07b68-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="07b68-198">İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütün:</span><span class="sxs-lookup"><span data-stu-id="07b68-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="07b68-199">Yukarıdaki kod, `X-API-KEY` üstbilginin istekte olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="07b68-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="07b68-200">`X-API-KEY` Eksikse, <xref:System.Net.HttpStatusCode.BadRequest> döndürülür.</span><span class="sxs-lookup"><span data-stu-id="07b68-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="07b68-201">İçin `HttpClient` yapılandırmasına birden fazla işleyici eklenebilir <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="07b68-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="07b68-202">Yukarıdaki kodda `ValidateHeaderHandler` ,, dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="07b68-203">Her `IHttpClientFactory` işleyici için ayrı bir dı kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="07b68-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="07b68-204">İşleyiciler herhangi bir kapsamın hizmetlerine bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="07b68-205">İşleyicilerin bağımlı olduğu hizmetler, işleyicinin elden çıkarılmasıyla kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="07b68-206">Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyicinin türü geçirerek, çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="07b68-207">Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="07b68-208">Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarmalar:</span><span class="sxs-lookup"><span data-stu-id="07b68-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="07b68-209">İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="07b68-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="07b68-210">[HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties)kullanarak işleyicide veri geçirin.</span><span class="sxs-lookup"><span data-stu-id="07b68-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="07b68-211">Geçerli <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="07b68-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="07b68-212">Verileri geçirmek için <xref:System.Threading.AsyncLocal`1> özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="07b68-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="07b68-213">Polly tabanlı işleyiciler kullanın</span><span class="sxs-lookup"><span data-stu-id="07b68-213">Use Polly-based handlers</span></span>

<span data-ttu-id="07b68-214">`IHttpClientFactory`üçüncü taraf kitaplığı [Polly](https://github.com/App-vNext/Polly)ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="07b68-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="07b68-215">Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="07b68-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="07b68-216">Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="07b68-217">Uzantı yöntemleri, yapılandırılmış `HttpClient` örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="07b68-218">Polly uzantıları, istemcilere Polly tabanlı işleyiciler eklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="07b68-219">Polly, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="07b68-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="07b68-220">Geçici hataları işle</span><span class="sxs-lookup"><span data-stu-id="07b68-220">Handle transient faults</span></span>

<span data-ttu-id="07b68-221">Hatalar genellikle dış HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="07b68-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="07b68-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>geçici hataları işlemek için bir ilkenin tanımlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="07b68-223">Aşağıdaki yanıtları `AddTransientHttpErrorPolicy` işleyecek şekilde yapılandırılan ilkeler:</span><span class="sxs-lookup"><span data-stu-id="07b68-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="07b68-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="07b68-224">HTTP 5xx</span></span>
* <span data-ttu-id="07b68-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="07b68-225">HTTP 408</span></span>

<span data-ttu-id="07b68-226">`AddTransientHttpErrorPolicy`olası bir geçici hatayı `PolicyBuilder` temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="07b68-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="07b68-227">Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="07b68-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="07b68-228">Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="07b68-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="07b68-229">Dinamik olarak ilke seçme</span><span class="sxs-lookup"><span data-stu-id="07b68-229">Dynamically select policies</span></span>

<span data-ttu-id="07b68-230">Uzantı yöntemleri, örneğin, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>Polly tabanlı işleyiciler eklemek için sağlanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="07b68-231">Aşağıdaki `AddPolicyHandler` aşırı yükleme, hangi ilkenin uygulanacağını belirlemek için isteği inceler:</span><span class="sxs-lookup"><span data-stu-id="07b68-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="07b68-232">Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="07b68-233">Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="07b68-234">Birden çok Polly işleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="07b68-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="07b68-235">Polly ilkeleri iç içe almak yaygın bir şekilde yapılır:</span><span class="sxs-lookup"><span data-stu-id="07b68-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="07b68-236">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="07b68-236">In the preceding example:</span></span>

* <span data-ttu-id="07b68-237">İki işleyici eklenir.</span><span class="sxs-lookup"><span data-stu-id="07b68-237">Two handlers are added.</span></span>
* <span data-ttu-id="07b68-238">İlk işleyici, yeniden <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> deneme ilkesi eklemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="07b68-239">Başarısız istekler en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="07b68-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="07b68-240">İkinci `AddTransientHttpErrorPolicy` çağrı bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="07b68-241">5 başarısız girişim sıralı olarak gerçekleşirse, daha fazla dış istek 30 saniye boyunca engellenir.</span><span class="sxs-lookup"><span data-stu-id="07b68-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="07b68-242">Devre kesici ilkeleri durum bilgisi vardır.</span><span class="sxs-lookup"><span data-stu-id="07b68-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="07b68-243">Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="07b68-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="07b68-244">Polly kayıt defterinden ilke ekleme</span><span class="sxs-lookup"><span data-stu-id="07b68-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="07b68-245">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir `PolicyRegistry`ile kaydetmektir.</span><span class="sxs-lookup"><span data-stu-id="07b68-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="07b68-246">Aşağıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="07b68-246">In the following code:</span></span>

* <span data-ttu-id="07b68-247">"Normal" ve "uzun" ilkeler eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="07b68-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="07b68-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>kayıt defterinden "normal" ve "uzun" ilkeleri ekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="07b68-249">Ve daha fazla tümleştirme `IHttpClientFactory` hakkında daha fazla bilgi için bkz. [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="07b68-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="07b68-250">HttpClient ve ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="07b68-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="07b68-251">Her `HttpClient` `CreateClient` çağrıldığında yeni bir örnek döndürülür `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="07b68-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="07b68-252"><xref:System.Net.Http.HttpMessageHandler> Adlandırılmış istemci başına oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="07b68-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="07b68-253">Fabrika, `HttpMessageHandler` örneklerin yaşam sürelerini yönetir.</span><span class="sxs-lookup"><span data-stu-id="07b68-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="07b68-254">`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="07b68-255">Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni `HttpClient` bir örnek oluştururken havuzdan yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="07b68-256">Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="07b68-257">Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="07b68-258">Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS (etki alanı adı sistemi) değişikliklerine yeniden davranmasını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="07b68-259">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="07b68-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="07b68-260">Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="07b68-261">`HttpClient`örnekler genellikle aktiften **çıkarma gerektirmeyen .NET nesneleri olarak** kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="07b68-262">Çıkarma giden istekleri iptal eder ve çağırma `HttpClient` <xref:System.IDisposable.Dispose*>sonrasında verilen örneğin kullanılamaz olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="07b68-263">`IHttpClientFactory`örnekler tarafından `HttpClient` kullanılan kaynakları izler ve ortadan kaldırdık.</span><span class="sxs-lookup"><span data-stu-id="07b68-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="07b68-264">Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde `IHttpClientFactory`kullanılmadan önce kullanılan ortak bir modeldir.</span><span class="sxs-lookup"><span data-stu-id="07b68-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="07b68-265">Bu model, ' a geçtikten sonra `IHttpClientFactory`gereksiz hale gelir.</span><span class="sxs-lookup"><span data-stu-id="07b68-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="07b68-266">Ihttpclientfactory alternatifleri</span><span class="sxs-lookup"><span data-stu-id="07b68-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="07b68-267">Dı `IHttpClientFactory` etkin bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="07b68-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="07b68-268">Havuz `HttpMessageHandler` örneklerine göre kaynak tükenmesi sorunları.</span><span class="sxs-lookup"><span data-stu-id="07b68-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="07b68-269">Düzenli aralıklarla `HttpMessageHandler` örnekleri GEÇIREREK eski DNS sorunları.</span><span class="sxs-lookup"><span data-stu-id="07b68-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="07b68-270">Uzun süreli <xref:System.Net.Http.SocketsHttpHandler> bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır.</span><span class="sxs-lookup"><span data-stu-id="07b68-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="07b68-271">Uygulamanın başladığı `SocketsHttpHandler` zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="07b68-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="07b68-272">DNS <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yenileme süreleri temelinde uygun bir değere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="07b68-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="07b68-273">Gerektiğinde `HttpClient` örnek `new HttpClient(handler, disposeHandler: false)` oluşturun.</span><span class="sxs-lookup"><span data-stu-id="07b68-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="07b68-274">Yukarıdaki yaklaşımlar, benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="07b68-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="07b68-275">, `SocketsHttpHandler` Örnekleri arasında `HttpClient` bağlantıları paylaşır.</span><span class="sxs-lookup"><span data-stu-id="07b68-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="07b68-276">Bu paylaşım, yuva azalmasına engel olur.</span><span class="sxs-lookup"><span data-stu-id="07b68-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="07b68-277">Bağlantıları `SocketsHttpHandler` , eski DNS sorunlarından `PooledConnectionLifetime` kaçınmak için öğesine göre döngüler.</span><span class="sxs-lookup"><span data-stu-id="07b68-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="07b68-278">Tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="07b68-278">Cookies</span></span>

<span data-ttu-id="07b68-279">Havuza alınmış `HttpMessageHandler` örnekler, paylaşılan `CookieContainer` nesneler ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="07b68-280">Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="07b68-281">Tanımlama bilgileri gerektiren uygulamalar için şunlardan birini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="07b68-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="07b68-282">Otomatik tanımlama bilgisi işlemeyi devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="07b68-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="07b68-283">Önlemenin`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="07b68-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="07b68-284">Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> tanımlama bilgisi işlemesini devre dışı bırakmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="07b68-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="07b68-285">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="07b68-285">Logging</span></span>

<span data-ttu-id="07b68-286">Tüm istekler için `IHttpClientFactory` kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="07b68-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="07b68-287">Varsayılan günlük iletilerini görmek için günlük yapılandırmasında uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="07b68-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="07b68-288">İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="07b68-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="07b68-289">Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="07b68-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="07b68-290">Örneğin, *Mynamedclient*adlı bir istemci, "System .net. http. HttpClient" kategorisine sahip iletileri günlüğe kaydeder. **Mynamedclient**. LogicalHandler ".</span><span class="sxs-lookup"><span data-stu-id="07b68-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="07b68-291">*Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="07b68-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="07b68-292">İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="07b68-293">Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="07b68-294">Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="07b68-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="07b68-295">*Mynamedclient* örneğinde, bu Iletiler "System .net. http. HttpClient" günlük kategorisiyle günlüğe kaydedilir. **Mynamedclient**. ClientHandler ".</span><span class="sxs-lookup"><span data-stu-id="07b68-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="07b68-296">İstek için bu, tüm diğer işleyiciler çalıştırıldıktan sonra ve istek gönderilmeden hemen önce gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="07b68-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="07b68-297">Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="07b68-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="07b68-298">İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="07b68-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="07b68-299">Bu, istek üst bilgilerinde veya yanıt durum kodunda yapılan değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="07b68-300">İstemcinin adını log kategorisinde da içermek, belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.</span><span class="sxs-lookup"><span data-stu-id="07b68-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="07b68-301">HttpMessageHandler 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="07b68-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="07b68-302">İstemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmayı denetlemek gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="07b68-303">Adlandırılmış `IHttpClientBuilder` veya yazılan istemciler eklenirken bir döndürülür.</span><span class="sxs-lookup"><span data-stu-id="07b68-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="07b68-304"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="07b68-305">Temsilci, bu istemci tarafından kullanılan birincili `HttpMessageHandler` oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="07b68-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="07b68-306">Konsol uygulamasında ıhttpclientfactory kullanma</span><span class="sxs-lookup"><span data-stu-id="07b68-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="07b68-307">Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="07b68-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="07b68-308">Microsoft. Extensions. Hosting</span><span class="sxs-lookup"><span data-stu-id="07b68-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="07b68-309">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="07b68-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="07b68-310">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="07b68-310">In the following example:</span></span>

* <span data-ttu-id="07b68-311"><xref:System.Net.Http.IHttpClientFactory>, [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="07b68-312">`MyService`hizmetinden bir `HttpClient`istemci fabrikası örneği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="07b68-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="07b68-313">`HttpClient`, bir Web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="07b68-314">`Main`Hizmetin `GetPage` yöntemini yürütmek için bir kapsam oluşturur ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.</span><span class="sxs-lookup"><span data-stu-id="07b68-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="07b68-315">Üst bilgi yayma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="07b68-315">Header propagation middleware</span></span>

<span data-ttu-id="07b68-316">Üst bilgi yayma, gelen istekten giden HTTP Istemci isteklerine HTTP üstbilgilerini yaymaya yönelik bir ASP.NET Core ara istemcindedir.</span><span class="sxs-lookup"><span data-stu-id="07b68-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="07b68-317">Üst bilgi yaymayı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="07b68-317">To use header propagation:</span></span>

* <span data-ttu-id="07b68-318">[Microsoft. AspNetCore. Headeryayma](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="07b68-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="07b68-319">Ara yazılımı ve `HttpClient` içinde `Startup`yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="07b68-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="07b68-320">İstemci giden isteklerde yapılandırılan üst bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="07b68-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="07b68-321">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="07b68-321">Additional resources</span></span>

* [<span data-ttu-id="07b68-322">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="07b68-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="07b68-323">HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın</span><span class="sxs-lookup"><span data-stu-id="07b68-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="07b68-324">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="07b68-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="07b68-325">.NET 'te JSON serileştirme ve serisini kaldırma</span><span class="sxs-lookup"><span data-stu-id="07b68-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="07b68-326">, [Glenn CONDRON](https://github.com/glennc), [Ryan şimdi e](https://github.com/rynowak)ve [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="07b68-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="07b68-327">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak <xref:System.Net.Http.HttpClient> için kayıt yapılabilir ve kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="07b68-328">Aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="07b68-328">It offers the following benefits:</span></span>

* <span data-ttu-id="07b68-329">, Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="07b68-330">Örneğin, *GitHub istemcisi kayıtlı* ve [GitHub](https://github.com/)'a erişebilecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="07b68-331">Varsayılan istemci, diğer amaçlar için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="07b68-332">' De `HttpClient` işleyiciler için temsilci atama ile giden ara yazılım kavramı ve bundan faydalanmak Için, Polly tabanlı ara yazılım için uzantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="07b68-333">Yaşam sürelerini el ile yönetirken `HttpClientMessageHandler` `HttpClient` gerçekleşen yaygın DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir.</span><span class="sxs-lookup"><span data-stu-id="07b68-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="07b68-334">Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm `ILogger`istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).</span><span class="sxs-lookup"><span data-stu-id="07b68-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="07b68-335">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07b68-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="07b68-336">Tüketim desenleri</span><span class="sxs-lookup"><span data-stu-id="07b68-336">Consumption patterns</span></span>

<span data-ttu-id="07b68-337">Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="07b68-338">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="07b68-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="07b68-339">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="07b68-340">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="07b68-341">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="07b68-342">Hiçbiri diğerinden tamamen üst değildir.</span><span class="sxs-lookup"><span data-stu-id="07b68-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="07b68-343">En iyi yaklaşım, uygulamanın kısıtlamalarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="07b68-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="07b68-344">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="07b68-344">Basic usage</span></span>

<span data-ttu-id="07b68-345">`IHttpClientFactory` `AddHttpClient` , `Startup.ConfigureServices` Yöntemi içindeki içindeki genişletme yöntemi `IServiceCollection`çağırarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="07b68-346">Kaydedildikten sonra kod, `IHttpClientFactory` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile her yerden bir hizmeti kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="07b68-347">`IHttpClientFactory` Bir `HttpClient` örnek oluşturmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="07b68-348">Bu `IHttpClientFactory` biçimde kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="07b68-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="07b68-349">Kullanım şekli `HttpClient` üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="07b68-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="07b68-350">`HttpClient` Örneklerin Şu anda oluşturulduğu yerlerde, bu tekrarlamaları ' a çağrı ile değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="07b68-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="07b68-351">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-351">Named clients</span></span>

<span data-ttu-id="07b68-352">Bir uygulama `HttpClient`, her biri farklı bir yapılandırmaya sahip birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemciler**kullanılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="07b68-353">Adlandırılmış `HttpClient` için yapılandırma, içinde `Startup.ConfigureServices`kayıt sırasında belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="07b68-354">Yukarıdaki kodda, `AddHttpClient` *GitHub*adının sağlanması denir.</span><span class="sxs-lookup"><span data-stu-id="07b68-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="07b68-355">Bu istemci, GitHub API 'siyle birlikte&mdash;çalışmak için gerekli olan temel adres ve iki üst bilgi olan bazı varsayılan yapılandırma uygulanmış.</span><span class="sxs-lookup"><span data-stu-id="07b68-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="07b68-356">Her seferinde `CreateClient` her çağrıldığında yeni bir örneği `HttpClient` oluşturulur ve yapılandırma eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="07b68-357">Adlandırılmış bir istemciyi kullanmak için, bir dize parametresi iletilebilir `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="07b68-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="07b68-358">Oluşturulacak istemcinin adını belirtin:</span><span class="sxs-lookup"><span data-stu-id="07b68-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="07b68-359">Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="07b68-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="07b68-360">İstemci için yapılandırılan taban adresi kullanıldığından, bu yalnızca yolu geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="07b68-361">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-361">Typed clients</span></span>

<span data-ttu-id="07b68-362">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="07b68-362">Typed clients:</span></span>

* <span data-ttu-id="07b68-363">Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="07b68-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="07b68-364">İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="07b68-365">Yapılandırmak ve belirli `HttpClient`bir ile etkileşimde bulunmak için tek bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="07b68-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="07b68-366">Örneğin, tek bir arka uç uç noktası için tek bir adet yazılmış istemci kullanılabilir ve bu uç nokta ile ilgili tüm mantığı kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="07b68-367">DI ile birlikte çalışın ve uygulamanızda gerektiğinde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="07b68-368">Türü belirtilmiş istemci, oluşturucusunda `HttpClient` bir parametreyi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="07b68-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="07b68-369">Önceki kodda, yapılandırma yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="07b68-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="07b68-370">`HttpClient` Nesne bir ortak özellik olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="07b68-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="07b68-371">İşlevselliği kullanıma `HttpClient` sunan API 'ye özel yöntemler tanımlamak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="07b68-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="07b68-372">Yöntemi `GetAspNetDocsIssues` , GitHub deposundan en son açık sorunları sorgulamak ve ayrıştırmak için gereken kodu saklar.</span><span class="sxs-lookup"><span data-stu-id="07b68-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="07b68-373">Türü belirtilmiş bir istemciyi kaydettirmek için, genel <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genişletme yöntemi içinde `Startup.ConfigureServices`kullanılabilir istemci sınıfını belirterek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="07b68-374">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="07b68-375">Yazılan istemci doğrudan eklenebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="07b68-376">Tercih edilirse, yazılan istemcinin yapılandırması, türü belirlenmiş istemcinin Oluşturucusu yerine kayıt `Startup.ConfigureServices`sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="07b68-377">Türü belirtilmiş bir istemci `HttpClient` içinde tamamen kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="07b68-378">Bunu bir özellik olarak göstermek yerine, `HttpClient` örneği dahili olarak çağıran ortak Yöntemler sunulabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="07b68-379">Önceki kodda `HttpClient` , bir özel alan olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="07b68-380">Dış çağrıları yapmak için tüm erişim `GetRepos` yönteminden geçer.</span><span class="sxs-lookup"><span data-stu-id="07b68-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="07b68-381">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-381">Generated clients</span></span>

<span data-ttu-id="07b68-382">`IHttpClientFactory`, [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıklarıyla birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="07b68-383">Yeniden sığdırma, .NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="07b68-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="07b68-384">REST API 'Leri canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="07b68-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="07b68-385">Arabirim bir uygulama, dış HTTP çağrıları yapmak için kullanılarak `RestService` `HttpClient` tarafından dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="07b68-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="07b68-386">Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="07b68-386">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="07b68-387">Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-387">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="07b68-388">Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="07b68-389">Giden istek ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="07b68-389">Outgoing request middleware</span></span>

<span data-ttu-id="07b68-390">`HttpClient`, giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramı zaten var.</span><span class="sxs-lookup"><span data-stu-id="07b68-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="07b68-391">, `IHttpClientFactory` Her bir adlandırılmış istemci için uygulanacak işleyicileri tanımlamanızı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="07b68-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="07b68-392">Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydını ve zincirlemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="07b68-393">Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="07b68-394">Bu düzen, ASP.NET Core gelen ara yazılım ardışık düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="07b68-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="07b68-395">Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="07b68-396">Bir işleyici oluşturmak için, öğesinden türeten <xref:System.Net.Http.DelegatingHandler>bir sınıf tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="07b68-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="07b68-397">İsteği ardışık `SendAsync` düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="07b68-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="07b68-398">Yukarıdaki kod, temel bir işleyiciyi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="07b68-399">İsteğe bağlı bir `X-API-KEY` başlık olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="07b68-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="07b68-400">Üst bilgi eksikse, HTTP çağrısından kaçınabilir ve uygun bir yanıt döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="07b68-401">Kayıt sırasında, bir veya daha fazla işleyici bir `HttpClient`için yapılandırmasına eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="07b68-402">Bu görev, <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>üzerindeki genişletme yöntemleri aracılığıyla gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="07b68-403">Yukarıdaki kodda `ValidateHeaderHandler` ,, dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="07b68-404">Her `IHttpClientFactory` işleyici için ayrı bir dı kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="07b68-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="07b68-405">İşleyiciler herhangi bir kapsamın hizmetlerine bağlı olarak ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="07b68-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="07b68-406">İşleyicilerin bağımlı olduğu hizmetler, işleyicinin elden çıkarılmasıyla kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="07b68-407">Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyicinin türü geçirerek, çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="07b68-408">Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="07b68-409">Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarmalar:</span><span class="sxs-lookup"><span data-stu-id="07b68-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="07b68-410">İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="07b68-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="07b68-411">Kullanarak `HttpRequestMessage.Properties`işleyicide veri geçirin.</span><span class="sxs-lookup"><span data-stu-id="07b68-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="07b68-412">Geçerli `IHttpContextAccessor` isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="07b68-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="07b68-413">Verileri geçirmek için `AsyncLocal` özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="07b68-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="07b68-414">Polly tabanlı işleyiciler kullanın</span><span class="sxs-lookup"><span data-stu-id="07b68-414">Use Polly-based handlers</span></span>

<span data-ttu-id="07b68-415">`IHttpClientFactory`, [Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kitaplığı ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="07b68-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="07b68-416">Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="07b68-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="07b68-417">Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="07b68-418">Uzantı yöntemleri, yapılandırılmış `HttpClient` örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="07b68-419">Polly uzantıları:</span><span class="sxs-lookup"><span data-stu-id="07b68-419">The Polly extensions:</span></span>

* <span data-ttu-id="07b68-420">İstemcilere Polly tabanlı işleyiciler eklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="07b68-421">, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="07b68-422">Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="07b68-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="07b68-423">Geçici hataları işle</span><span class="sxs-lookup"><span data-stu-id="07b68-423">Handle transient faults</span></span>

<span data-ttu-id="07b68-424">Yaygın hatalar, dış HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="07b68-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="07b68-425">Geçici hataları işlemek üzere bir `AddTransientHttpErrorPolicy` ilkenin tanımlanmasını sağlayan, çağrılan bir uygun genişletme yöntemi eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="07b68-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="07b68-426">Bu uzantı yöntemi tanıtıcısıyla `HttpRequestException`yapılandırılmış ilkeler, http 5xx YANıTLARı ve http 408 yanıtları.</span><span class="sxs-lookup"><span data-stu-id="07b68-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="07b68-427">`AddTransientHttpErrorPolicy` Uzantı içinde `Startup.ConfigureServices`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="07b68-428">Uzantı, olası bir geçici hatayı `PolicyBuilder` temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="07b68-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="07b68-429">Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="07b68-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="07b68-430">Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="07b68-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="07b68-431">Dinamik olarak ilke seçme</span><span class="sxs-lookup"><span data-stu-id="07b68-431">Dynamically select policies</span></span>

<span data-ttu-id="07b68-432">Polly tabanlı işleyiciler eklemek için kullanılabilecek ek uzantı yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="07b68-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="07b68-433">Bu tür bir uzantının `AddPolicyHandler`birden çok aşırı yüklemesi vardır.</span><span class="sxs-lookup"><span data-stu-id="07b68-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="07b68-434">Bir aşırı yükleme, hangi ilkenin uygulanacağını tanımlarken isteğin incelenebilirliğini sağlar:</span><span class="sxs-lookup"><span data-stu-id="07b68-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="07b68-435">Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="07b68-436">Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="07b68-437">Birden çok Polly işleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="07b68-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="07b68-438">Gelişmiş işlevsellik sağlamak için çok fazla ilke iç içe geçmiş bir yaygın hale gelir:</span><span class="sxs-lookup"><span data-stu-id="07b68-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="07b68-439">Yukarıdaki örnekte, iki işleyici eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="07b68-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="07b68-440">İlki, yeniden deneme `AddTransientHttpErrorPolicy` ilkesi eklemek için uzantıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="07b68-441">Başarısız istekler en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="07b68-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="07b68-442">İçin `AddTransientHttpErrorPolicy` ikinci çağrı, bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="07b68-443">Beş başarısız girişim sırayla gerçekleşiyorsa, daha fazla dış istek 30 saniye için engellenir.</span><span class="sxs-lookup"><span data-stu-id="07b68-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="07b68-444">Devre kesici ilkeleri durum bilgisi vardır.</span><span class="sxs-lookup"><span data-stu-id="07b68-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="07b68-445">Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="07b68-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="07b68-446">Polly kayıt defterinden ilke ekleme</span><span class="sxs-lookup"><span data-stu-id="07b68-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="07b68-447">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir `PolicyRegistry`ile kaydetmektir.</span><span class="sxs-lookup"><span data-stu-id="07b68-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="07b68-448">Kayıt defterinden bir ilke kullanılarak bir işleyicinin eklenmesine izin veren bir genişletme yöntemi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="07b68-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="07b68-449">Yukarıdaki kodda, öğesine eklendiğinde iki ilke kaydedilir `PolicyRegistry` `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="07b68-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="07b68-450">Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır ve uygulanacak ilke adı geçer.</span><span class="sxs-lookup"><span data-stu-id="07b68-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="07b68-451">Ve daha fazla `IHttpClientFactory` tümleştirme hakkında daha fazla bilgi, [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)' de bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="07b68-452">HttpClient ve ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="07b68-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="07b68-453">Her `HttpClient` `CreateClient` çağrıldığında yeni bir örnek döndürülür `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="07b68-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="07b68-454">Adlandırılmış istemci <xref:System.Net.Http.HttpMessageHandler> başına.</span><span class="sxs-lookup"><span data-stu-id="07b68-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="07b68-455">Fabrika, `HttpMessageHandler` örneklerin yaşam sürelerini yönetir.</span><span class="sxs-lookup"><span data-stu-id="07b68-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="07b68-456">`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="07b68-457">Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni `HttpClient` bir örnek oluştururken havuzdan yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="07b68-458">Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="07b68-459">Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="07b68-460">Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine yeniden davranmasını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="07b68-461">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="07b68-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="07b68-462">Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="07b68-463">Bunu geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> istemcisini oluştururken `IHttpClientBuilder` döndürülen öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="07b68-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="07b68-464">İstemcinin çıkarılması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="07b68-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="07b68-465">Çıkarma giden istekleri iptal eder ve çağırma `HttpClient` <xref:System.IDisposable.Dispose*>sonrasında verilen örneğin kullanılamaz olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="07b68-466">`IHttpClientFactory`örnekler tarafından `HttpClient` kullanılan kaynakları izler ve ortadan kaldırdık.</span><span class="sxs-lookup"><span data-stu-id="07b68-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="07b68-467">`HttpClient` Örnekler genellikle aktiften çıkarma gerektirmeyen .NET nesneleri olarak kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="07b68-468">Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde `IHttpClientFactory`kullanılmadan önce kullanılan ortak bir modeldir.</span><span class="sxs-lookup"><span data-stu-id="07b68-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="07b68-469">Bu model, ' a geçtikten sonra `IHttpClientFactory`gereksiz hale gelir.</span><span class="sxs-lookup"><span data-stu-id="07b68-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="07b68-470">Ihttpclientfactory alternatifleri</span><span class="sxs-lookup"><span data-stu-id="07b68-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="07b68-471">Dı `IHttpClientFactory` etkin bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="07b68-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="07b68-472">Havuz `HttpMessageHandler` örneklerine göre kaynak tükenmesi sorunları.</span><span class="sxs-lookup"><span data-stu-id="07b68-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="07b68-473">Düzenli aralıklarla `HttpMessageHandler` örnekleri GEÇIREREK eski DNS sorunları.</span><span class="sxs-lookup"><span data-stu-id="07b68-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="07b68-474">Uzun süreli <xref:System.Net.Http.SocketsHttpHandler> bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır.</span><span class="sxs-lookup"><span data-stu-id="07b68-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="07b68-475">Uygulamanın başladığı `SocketsHttpHandler` zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="07b68-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="07b68-476">DNS <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yenileme süreleri temelinde uygun bir değere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="07b68-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="07b68-477">Gerektiğinde `HttpClient` örnek `new HttpClient(handler, disposeHandler: false)` oluşturun.</span><span class="sxs-lookup"><span data-stu-id="07b68-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="07b68-478">Yukarıdaki yaklaşımlar, benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="07b68-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="07b68-479">, `SocketsHttpHandler` Örnekleri arasında `HttpClient` bağlantıları paylaşır.</span><span class="sxs-lookup"><span data-stu-id="07b68-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="07b68-480">Bu paylaşım, yuva azalmasına engel olur.</span><span class="sxs-lookup"><span data-stu-id="07b68-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="07b68-481">Bağlantıları `SocketsHttpHandler` , eski DNS sorunlarından `PooledConnectionLifetime` kaçınmak için öğesine göre döngüler.</span><span class="sxs-lookup"><span data-stu-id="07b68-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="07b68-482">Tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="07b68-482">Cookies</span></span>

<span data-ttu-id="07b68-483">Havuza alınmış `HttpMessageHandler` örnekler, paylaşılan `CookieContainer` nesneler ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="07b68-484">Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="07b68-485">Tanımlama bilgileri gerektiren uygulamalar için şunlardan birini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="07b68-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="07b68-486">Otomatik tanımlama bilgisi işlemeyi devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="07b68-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="07b68-487">Önlemenin`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="07b68-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="07b68-488">Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> tanımlama bilgisi işlemesini devre dışı bırakmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="07b68-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="07b68-489">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="07b68-489">Logging</span></span>

<span data-ttu-id="07b68-490">Tüm istekler için `IHttpClientFactory` kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="07b68-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="07b68-491">Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="07b68-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="07b68-492">İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="07b68-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="07b68-493">Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="07b68-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="07b68-494">Örneğin, *Mynamedclient*adlı bir istemci, bir kategorisine sahip iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="07b68-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="07b68-495">*Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="07b68-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="07b68-496">İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="07b68-497">Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="07b68-498">Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="07b68-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="07b68-499">*Mynamedclient* örneğinde, bu iletiler günlük kategorisine `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`göre günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="07b68-500">İstek için bu, tüm diğer işleyiciler çalıştıktan sonra ve istek ağda gönderilmeden hemen önce gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="07b68-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="07b68-501">Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="07b68-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="07b68-502">İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="07b68-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="07b68-503">Bu, örneğin veya yanıt durum kodunda istek başlıklarındaki değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="07b68-504">İstemci adı ' nı log kategorisinde da içermek, gerektiğinde belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.</span><span class="sxs-lookup"><span data-stu-id="07b68-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="07b68-505">HttpMessageHandler 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="07b68-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="07b68-506">İstemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmayı denetlemek gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="07b68-507">Adlandırılmış `IHttpClientBuilder` veya yazılan istemciler eklenirken bir döndürülür.</span><span class="sxs-lookup"><span data-stu-id="07b68-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="07b68-508"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="07b68-509">Temsilci, bu istemci tarafından kullanılan birincili `HttpMessageHandler` oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="07b68-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="07b68-510">Konsol uygulamasında ıhttpclientfactory kullanma</span><span class="sxs-lookup"><span data-stu-id="07b68-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="07b68-511">Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="07b68-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="07b68-512">Microsoft. Extensions. Hosting</span><span class="sxs-lookup"><span data-stu-id="07b68-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="07b68-513">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="07b68-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="07b68-514">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="07b68-514">In the following example:</span></span>

* <span data-ttu-id="07b68-515"><xref:System.Net.Http.IHttpClientFactory>, [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="07b68-516">`MyService`hizmetinden bir `HttpClient`istemci fabrikası örneği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="07b68-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="07b68-517">`HttpClient`, bir Web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="07b68-518">`Main`Hizmetin `GetPage` yöntemini yürütmek için bir kapsam oluşturur ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.</span><span class="sxs-lookup"><span data-stu-id="07b68-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="07b68-519">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="07b68-519">Additional resources</span></span>

* [<span data-ttu-id="07b68-520">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="07b68-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="07b68-521">HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın</span><span class="sxs-lookup"><span data-stu-id="07b68-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="07b68-522">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="07b68-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="07b68-523">, [Glenn CONDRON](https://github.com/glennc), [Ryan şimdi e](https://github.com/rynowak)ve [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="07b68-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="07b68-524">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak <xref:System.Net.Http.HttpClient> için kayıt yapılabilir ve kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="07b68-525">Aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="07b68-525">It offers the following benefits:</span></span>

* <span data-ttu-id="07b68-526">, Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="07b68-527">Örneğin, *GitHub istemcisi kayıtlı* ve [GitHub](https://github.com/)'a erişebilecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="07b68-528">Varsayılan istemci, diğer amaçlar için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="07b68-529">' De `HttpClient` işleyiciler için temsilci atama ile giden ara yazılım kavramı ve bundan faydalanmak Için, Polly tabanlı ara yazılım için uzantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="07b68-530">Yaşam sürelerini el ile yönetirken `HttpClientMessageHandler` `HttpClient` gerçekleşen yaygın DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir.</span><span class="sxs-lookup"><span data-stu-id="07b68-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="07b68-531">Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm `ILogger`istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).</span><span class="sxs-lookup"><span data-stu-id="07b68-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="07b68-532">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07b68-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="07b68-533">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="07b68-533">Prerequisites</span></span>

<span data-ttu-id="07b68-534">.NET Framework hedefleyen projeler [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet paketinin yüklenmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="07b68-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="07b68-535">.NET Core ile hedeflenen ve [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvuran projeler zaten `Microsoft.Extensions.Http` paketi içeriyor.</span><span class="sxs-lookup"><span data-stu-id="07b68-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="07b68-536">Tüketim desenleri</span><span class="sxs-lookup"><span data-stu-id="07b68-536">Consumption patterns</span></span>

<span data-ttu-id="07b68-537">Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="07b68-538">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="07b68-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="07b68-539">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="07b68-540">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="07b68-541">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="07b68-542">Hiçbiri diğerinden tamamen üst değildir.</span><span class="sxs-lookup"><span data-stu-id="07b68-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="07b68-543">En iyi yaklaşım, uygulamanın kısıtlamalarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="07b68-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="07b68-544">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="07b68-544">Basic usage</span></span>

<span data-ttu-id="07b68-545">`IHttpClientFactory` `AddHttpClient` , `Startup.ConfigureServices` Yöntemi içindeki içindeki genişletme yöntemi `IServiceCollection`çağırarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="07b68-546">Kaydedildikten sonra kod, `IHttpClientFactory` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile her yerden bir hizmeti kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="07b68-547">`IHttpClientFactory` Bir `HttpClient` örnek oluşturmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="07b68-548">Bu `IHttpClientFactory` biçimde kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="07b68-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="07b68-549">Kullanım şekli `HttpClient` üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="07b68-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="07b68-550">`HttpClient` Örneklerin Şu anda oluşturulduğu yerlerde, bu tekrarlamaları ' a çağrı ile değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="07b68-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="07b68-551">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-551">Named clients</span></span>

<span data-ttu-id="07b68-552">Bir uygulama `HttpClient`, her biri farklı bir yapılandırmaya sahip birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemciler**kullanılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="07b68-553">Adlandırılmış `HttpClient` için yapılandırma, içinde `Startup.ConfigureServices`kayıt sırasında belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="07b68-554">Yukarıdaki kodda, `AddHttpClient` *GitHub*adının sağlanması denir.</span><span class="sxs-lookup"><span data-stu-id="07b68-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="07b68-555">Bu istemci, GitHub API 'siyle birlikte&mdash;çalışmak için gerekli olan temel adres ve iki üst bilgi olan bazı varsayılan yapılandırma uygulanmış.</span><span class="sxs-lookup"><span data-stu-id="07b68-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="07b68-556">Her seferinde `CreateClient` her çağrıldığında yeni bir örneği `HttpClient` oluşturulur ve yapılandırma eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="07b68-557">Adlandırılmış bir istemciyi kullanmak için, bir dize parametresi iletilebilir `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="07b68-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="07b68-558">Oluşturulacak istemcinin adını belirtin:</span><span class="sxs-lookup"><span data-stu-id="07b68-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="07b68-559">Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="07b68-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="07b68-560">İstemci için yapılandırılan taban adresi kullanıldığından, bu yalnızca yolu geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="07b68-561">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-561">Typed clients</span></span>

<span data-ttu-id="07b68-562">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="07b68-562">Typed clients:</span></span>

* <span data-ttu-id="07b68-563">Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="07b68-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="07b68-564">İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="07b68-565">Yapılandırmak ve belirli `HttpClient`bir ile etkileşimde bulunmak için tek bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="07b68-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="07b68-566">Örneğin, tek bir arka uç uç noktası için tek bir adet yazılmış istemci kullanılabilir ve bu uç nokta ile ilgili tüm mantığı kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="07b68-567">DI ile birlikte çalışın ve uygulamanızda gerektiğinde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="07b68-568">Türü belirtilmiş istemci, oluşturucusunda `HttpClient` bir parametreyi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="07b68-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="07b68-569">Önceki kodda, yapılandırma yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="07b68-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="07b68-570">`HttpClient` Nesne bir ortak özellik olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="07b68-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="07b68-571">İşlevselliği kullanıma `HttpClient` sunan API 'ye özel yöntemler tanımlamak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="07b68-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="07b68-572">Yöntemi `GetAspNetDocsIssues` , GitHub deposundan en son açık sorunları sorgulamak ve ayrıştırmak için gereken kodu saklar.</span><span class="sxs-lookup"><span data-stu-id="07b68-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="07b68-573">Türü belirtilmiş bir istemciyi kaydettirmek için, genel <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genişletme yöntemi içinde `Startup.ConfigureServices`kullanılabilir istemci sınıfını belirterek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="07b68-574">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="07b68-575">Yazılan istemci doğrudan eklenebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="07b68-576">Tercih edilirse, yazılan istemcinin yapılandırması, türü belirlenmiş istemcinin Oluşturucusu yerine kayıt `Startup.ConfigureServices`sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="07b68-577">Türü belirtilmiş bir istemci `HttpClient` içinde tamamen kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="07b68-578">Bunu bir özellik olarak göstermek yerine, `HttpClient` örneği dahili olarak çağıran ortak Yöntemler sunulabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="07b68-579">Önceki kodda `HttpClient` , bir özel alan olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="07b68-580">Dış çağrıları yapmak için tüm erişim `GetRepos` yönteminden geçer.</span><span class="sxs-lookup"><span data-stu-id="07b68-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="07b68-581">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="07b68-581">Generated clients</span></span>

<span data-ttu-id="07b68-582">`IHttpClientFactory`, [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıklarıyla birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="07b68-583">Yeniden sığdırma, .NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="07b68-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="07b68-584">REST API 'Leri canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="07b68-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="07b68-585">Arabirim bir uygulama, dış HTTP çağrıları yapmak için kullanılarak `RestService` `HttpClient` tarafından dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="07b68-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="07b68-586">Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="07b68-586">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="07b68-587">Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="07b68-587">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="07b68-588">Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="07b68-589">Giden istek ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="07b68-589">Outgoing request middleware</span></span>

<span data-ttu-id="07b68-590">`HttpClient`, giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramı zaten var.</span><span class="sxs-lookup"><span data-stu-id="07b68-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="07b68-591">, `IHttpClientFactory` Her bir adlandırılmış istemci için uygulanacak işleyicileri tanımlamanızı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="07b68-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="07b68-592">Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydını ve zincirlemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="07b68-593">Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="07b68-594">Bu düzen, ASP.NET Core gelen ara yazılım ardışık düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="07b68-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="07b68-595">Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="07b68-596">Bir işleyici oluşturmak için, öğesinden türeten <xref:System.Net.Http.DelegatingHandler>bir sınıf tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="07b68-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="07b68-597">İsteği ardışık `SendAsync` düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="07b68-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="07b68-598">Yukarıdaki kod, temel bir işleyiciyi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="07b68-599">İsteğe bağlı bir `X-API-KEY` başlık olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="07b68-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="07b68-600">Üst bilgi eksikse, HTTP çağrısından kaçınabilir ve uygun bir yanıt döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="07b68-601">Kayıt sırasında, bir veya daha fazla işleyici bir `HttpClient`için yapılandırmasına eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="07b68-602">Bu görev, <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>üzerindeki genişletme yöntemleri aracılığıyla gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="07b68-603">Yukarıdaki kodda `ValidateHeaderHandler` ,, dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="07b68-604">İşleyicinin, bir geçici hizmet olarak dı 'ye kayıtlı olması **gerekir** , hiçbir koşulda kapsamı yoktur.</span><span class="sxs-lookup"><span data-stu-id="07b68-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="07b68-605">İşleyici kapsamlı bir hizmet olarak kayıtlıysa ve işleyicinin bağımlı olduğu tüm hizmetler atılabilir olur:</span><span class="sxs-lookup"><span data-stu-id="07b68-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="07b68-606">İşleyici kapsam dışına geçmeden önce işleyicinin Hizmetleri atılamaz.</span><span class="sxs-lookup"><span data-stu-id="07b68-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="07b68-607">Atılmış işleyici Hizmetleri işleyicinin başarısız olmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="07b68-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="07b68-608">Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyici türünü geçirerek çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="07b68-609">Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="07b68-610">Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarmalar:</span><span class="sxs-lookup"><span data-stu-id="07b68-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="07b68-611">İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="07b68-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="07b68-612">Kullanarak `HttpRequestMessage.Properties`işleyicide veri geçirin.</span><span class="sxs-lookup"><span data-stu-id="07b68-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="07b68-613">Geçerli `IHttpContextAccessor` isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="07b68-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="07b68-614">Verileri geçirmek için `AsyncLocal` özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="07b68-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="07b68-615">Polly tabanlı işleyiciler kullanın</span><span class="sxs-lookup"><span data-stu-id="07b68-615">Use Polly-based handlers</span></span>

<span data-ttu-id="07b68-616">`IHttpClientFactory`, [Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kitaplığı ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="07b68-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="07b68-617">Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="07b68-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="07b68-618">Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="07b68-619">Uzantı yöntemleri, yapılandırılmış `HttpClient` örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="07b68-620">Polly uzantıları:</span><span class="sxs-lookup"><span data-stu-id="07b68-620">The Polly extensions:</span></span>

* <span data-ttu-id="07b68-621">İstemcilere Polly tabanlı işleyiciler eklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="07b68-622">, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="07b68-623">Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="07b68-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="07b68-624">Geçici hataları işle</span><span class="sxs-lookup"><span data-stu-id="07b68-624">Handle transient faults</span></span>

<span data-ttu-id="07b68-625">Yaygın hatalar, dış HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="07b68-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="07b68-626">Geçici hataları işlemek üzere bir `AddTransientHttpErrorPolicy` ilkenin tanımlanmasını sağlayan, çağrılan bir uygun genişletme yöntemi eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="07b68-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="07b68-627">Bu uzantı yöntemi tanıtıcısıyla `HttpRequestException`yapılandırılmış ilkeler, http 5xx YANıTLARı ve http 408 yanıtları.</span><span class="sxs-lookup"><span data-stu-id="07b68-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="07b68-628">`AddTransientHttpErrorPolicy` Uzantı içinde `Startup.ConfigureServices`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="07b68-629">Uzantı, olası bir geçici hatayı `PolicyBuilder` temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="07b68-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="07b68-630">Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="07b68-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="07b68-631">Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="07b68-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="07b68-632">Dinamik olarak ilke seçme</span><span class="sxs-lookup"><span data-stu-id="07b68-632">Dynamically select policies</span></span>

<span data-ttu-id="07b68-633">Polly tabanlı işleyiciler eklemek için kullanılabilecek ek uzantı yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="07b68-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="07b68-634">Bu tür bir uzantının `AddPolicyHandler`birden çok aşırı yüklemesi vardır.</span><span class="sxs-lookup"><span data-stu-id="07b68-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="07b68-635">Bir aşırı yükleme, hangi ilkenin uygulanacağını tanımlarken isteğin incelenebilirliğini sağlar:</span><span class="sxs-lookup"><span data-stu-id="07b68-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="07b68-636">Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="07b68-637">Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="07b68-638">Birden çok Polly işleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="07b68-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="07b68-639">Gelişmiş işlevsellik sağlamak için çok fazla ilke iç içe geçmiş bir yaygın hale gelir:</span><span class="sxs-lookup"><span data-stu-id="07b68-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="07b68-640">Yukarıdaki örnekte, iki işleyici eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="07b68-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="07b68-641">İlki, yeniden deneme `AddTransientHttpErrorPolicy` ilkesi eklemek için uzantıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="07b68-642">Başarısız istekler en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="07b68-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="07b68-643">İçin `AddTransientHttpErrorPolicy` ikinci çağrı, bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="07b68-644">Beş başarısız girişim sırayla gerçekleşiyorsa, daha fazla dış istek 30 saniye için engellenir.</span><span class="sxs-lookup"><span data-stu-id="07b68-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="07b68-645">Devre kesici ilkeleri durum bilgisi vardır.</span><span class="sxs-lookup"><span data-stu-id="07b68-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="07b68-646">Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="07b68-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="07b68-647">Polly kayıt defterinden ilke ekleme</span><span class="sxs-lookup"><span data-stu-id="07b68-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="07b68-648">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir `PolicyRegistry`ile kaydetmektir.</span><span class="sxs-lookup"><span data-stu-id="07b68-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="07b68-649">Kayıt defterinden bir ilke kullanılarak bir işleyicinin eklenmesine izin veren bir genişletme yöntemi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="07b68-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="07b68-650">Yukarıdaki kodda, öğesine eklendiğinde iki ilke kaydedilir `PolicyRegistry` `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="07b68-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="07b68-651">Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır ve uygulanacak ilke adı geçer.</span><span class="sxs-lookup"><span data-stu-id="07b68-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="07b68-652">Ve daha fazla `IHttpClientFactory` tümleştirme hakkında daha fazla bilgi, [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)' de bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="07b68-653">HttpClient ve ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="07b68-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="07b68-654">Her `HttpClient` `CreateClient` çağrıldığında yeni bir örnek döndürülür `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="07b68-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="07b68-655">Adlandırılmış istemci <xref:System.Net.Http.HttpMessageHandler> başına.</span><span class="sxs-lookup"><span data-stu-id="07b68-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="07b68-656">Fabrika, `HttpMessageHandler` örneklerin yaşam sürelerini yönetir.</span><span class="sxs-lookup"><span data-stu-id="07b68-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="07b68-657">`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="07b68-658">Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni `HttpClient` bir örnek oluştururken havuzdan yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="07b68-659">Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="07b68-660">Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="07b68-661">Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine yeniden davranmasını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="07b68-662">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="07b68-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="07b68-663">Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="07b68-664">Bunu geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> istemcisini oluştururken `IHttpClientBuilder` döndürülen öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="07b68-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="07b68-665">İstemcinin çıkarılması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="07b68-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="07b68-666">Çıkarma giden istekleri iptal eder ve çağırma `HttpClient` <xref:System.IDisposable.Dispose*>sonrasında verilen örneğin kullanılamaz olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="07b68-667">`IHttpClientFactory`örnekler tarafından `HttpClient` kullanılan kaynakları izler ve ortadan kaldırdık.</span><span class="sxs-lookup"><span data-stu-id="07b68-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="07b68-668">`HttpClient` Örnekler genellikle aktiften çıkarma gerektirmeyen .NET nesneleri olarak kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="07b68-669">Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde `IHttpClientFactory`kullanılmadan önce kullanılan ortak bir modeldir.</span><span class="sxs-lookup"><span data-stu-id="07b68-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="07b68-670">Bu model, ' a geçtikten sonra `IHttpClientFactory`gereksiz hale gelir.</span><span class="sxs-lookup"><span data-stu-id="07b68-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="07b68-671">Ihttpclientfactory alternatifleri</span><span class="sxs-lookup"><span data-stu-id="07b68-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="07b68-672">Dı `IHttpClientFactory` etkin bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="07b68-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="07b68-673">Havuz `HttpMessageHandler` örneklerine göre kaynak tükenmesi sorunları.</span><span class="sxs-lookup"><span data-stu-id="07b68-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="07b68-674">Düzenli aralıklarla `HttpMessageHandler` örnekleri GEÇIREREK eski DNS sorunları.</span><span class="sxs-lookup"><span data-stu-id="07b68-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="07b68-675">Uzun süreli <xref:System.Net.Http.SocketsHttpHandler> bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır.</span><span class="sxs-lookup"><span data-stu-id="07b68-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="07b68-676">Uygulamanın başladığı `SocketsHttpHandler` zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="07b68-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="07b68-677">DNS <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yenileme süreleri temelinde uygun bir değere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="07b68-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="07b68-678">Gerektiğinde `HttpClient` örnek `new HttpClient(handler, disposeHandler: false)` oluşturun.</span><span class="sxs-lookup"><span data-stu-id="07b68-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="07b68-679">Yukarıdaki yaklaşımlar, benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="07b68-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="07b68-680">, `SocketsHttpHandler` Örnekleri arasında `HttpClient` bağlantıları paylaşır.</span><span class="sxs-lookup"><span data-stu-id="07b68-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="07b68-681">Bu paylaşım, yuva azalmasına engel olur.</span><span class="sxs-lookup"><span data-stu-id="07b68-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="07b68-682">Bağlantıları `SocketsHttpHandler` , eski DNS sorunlarından `PooledConnectionLifetime` kaçınmak için öğesine göre döngüler.</span><span class="sxs-lookup"><span data-stu-id="07b68-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="07b68-683">Tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="07b68-683">Cookies</span></span>

<span data-ttu-id="07b68-684">Havuza alınmış `HttpMessageHandler` örnekler, paylaşılan `CookieContainer` nesneler ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="07b68-685">Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="07b68-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="07b68-686">Tanımlama bilgileri gerektiren uygulamalar için şunlardan birini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="07b68-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="07b68-687">Otomatik tanımlama bilgisi işlemeyi devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="07b68-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="07b68-688">Önlemenin`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="07b68-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="07b68-689">Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> tanımlama bilgisi işlemesini devre dışı bırakmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="07b68-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="07b68-690">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="07b68-690">Logging</span></span>

<span data-ttu-id="07b68-691">Tüm istekler için `IHttpClientFactory` kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="07b68-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="07b68-692">Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="07b68-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="07b68-693">İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="07b68-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="07b68-694">Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="07b68-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="07b68-695">Örneğin, *Mynamedclient*adlı bir istemci, bir kategorisine sahip iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="07b68-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="07b68-696">*Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="07b68-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="07b68-697">İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="07b68-698">Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="07b68-699">Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="07b68-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="07b68-700">*Mynamedclient* örneğinde, bu iletiler günlük kategorisine `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`göre günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="07b68-701">İstek için bu, tüm diğer işleyiciler çalıştıktan sonra ve istek ağda gönderilmeden hemen önce gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="07b68-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="07b68-702">Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="07b68-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="07b68-703">İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="07b68-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="07b68-704">Bu, örneğin veya yanıt durum kodunda istek başlıklarındaki değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="07b68-705">İstemci adı ' nı log kategorisinde da içermek, gerektiğinde belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.</span><span class="sxs-lookup"><span data-stu-id="07b68-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="07b68-706">HttpMessageHandler 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="07b68-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="07b68-707">İstemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmayı denetlemek gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="07b68-708">Adlandırılmış `IHttpClientBuilder` veya yazılan istemciler eklenirken bir döndürülür.</span><span class="sxs-lookup"><span data-stu-id="07b68-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="07b68-709"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="07b68-710">Temsilci, bu istemci tarafından kullanılan birincili `HttpMessageHandler` oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="07b68-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="07b68-711">Konsol uygulamasında ıhttpclientfactory kullanma</span><span class="sxs-lookup"><span data-stu-id="07b68-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="07b68-712">Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="07b68-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="07b68-713">Microsoft. Extensions. Hosting</span><span class="sxs-lookup"><span data-stu-id="07b68-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="07b68-714">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="07b68-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="07b68-715">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="07b68-715">In the following example:</span></span>

* <span data-ttu-id="07b68-716"><xref:System.Net.Http.IHttpClientFactory>, [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="07b68-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="07b68-717">`MyService`hizmetinden bir `HttpClient`istemci fabrikası örneği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="07b68-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="07b68-718">`HttpClient`, bir Web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="07b68-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="07b68-719">`Main`Hizmetin `GetPage` yöntemini yürütmek için bir kapsam oluşturur ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.</span><span class="sxs-lookup"><span data-stu-id="07b68-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="07b68-720">Üst bilgi yayma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="07b68-720">Header propagation middleware</span></span>

<span data-ttu-id="07b68-721">Üst bilgi yayma, gelen istekten giden HTTP Istemci isteklerine HTTP üstbilgilerini yaymak için bir topluluk tarafından desteklenen bir ara yazılımlar.</span><span class="sxs-lookup"><span data-stu-id="07b68-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="07b68-722">Üst bilgi yaymayı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="07b68-722">To use header propagation:</span></span>

* <span data-ttu-id="07b68-723">Topluluğun, paket [Headeryayılmasının](https://www.nuget.org/packages/HeaderPropagation)desteklediği bağlantı noktasına başvurun.</span><span class="sxs-lookup"><span data-stu-id="07b68-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="07b68-724">ASP.NET Core 3,1 ve üzeri, [Microsoft. AspNetCore. Headeryayılmayı](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)destekler.</span><span class="sxs-lookup"><span data-stu-id="07b68-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="07b68-725">Ara yazılımı ve `HttpClient` içinde `Startup`yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="07b68-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="07b68-726">İstemci giden isteklerde yapılandırılan üst bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="07b68-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="07b68-727">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="07b68-727">Additional resources</span></span>

* [<span data-ttu-id="07b68-728">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="07b68-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="07b68-729">HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın</span><span class="sxs-lookup"><span data-stu-id="07b68-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="07b68-730">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="07b68-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
