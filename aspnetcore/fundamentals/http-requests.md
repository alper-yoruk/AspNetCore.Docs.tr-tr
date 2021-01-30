---
title: ASP.NET Core 'de ıhttpclientfactory kullanarak HTTP istekleri yapın
author: stevejgordon
description: ASP.NET Core içindeki mantıksal HttpClient örneklerini yönetmek için ıhttpclientfactory arabirimini kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 1/21/2021
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
uid: fundamentals/http-requests
ms.openlocfilehash: 1cf3029452f87a396847f969f0f3136a75874752
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057336"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="51ce7-103">ASP.NET Core 'de ıhttpclientfactory kullanarak HTTP istekleri yapın</span><span class="sxs-lookup"><span data-stu-id="51ce7-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51ce7-104">[Kirk Larkabağı](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn CONDRON](https://github.com/glennc)ve [Ryan şimdi ak](https://github.com/rynowak).</span><span class="sxs-lookup"><span data-stu-id="51ce7-104">By [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc), and [Ryan Nowak](https://github.com/rynowak).</span></span>

<span data-ttu-id="51ce7-105">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak için kayıt yapılabilir ve kullanılabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="51ce7-106">`IHttpClientFactory` aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="51ce7-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="51ce7-107">, Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-108">Örneğin,  *GitHub* adlı bir Istemci, [GitHub](https://github.com/)'a erişmek için kaydedilebilir ve yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="51ce7-109">Varsayılan istemci, genel erişim için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="51ce7-110">' De işleyiciler temsilci seçme yoluyla giden ara yazılım kavramını daha da artırır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="51ce7-111">' De işleyiciler temsilci atama avantajlarından faydalanmak için, Polya tabanlı bir ara yazılım için uzantılar sağlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="51ce7-112">Temel örneklerin biriktirmesini ve ömrünü yönetir `HttpClientMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="51ce7-113">Otomatik yönetim, yaşam sürelerini el ile yönetirken oluşan ortak DNS (etki alanı adı sistemi) sorunlarını önler `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="51ce7-114">`ILogger`Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).</span><span class="sxs-lookup"><span data-stu-id="51ce7-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="51ce7-115">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="51ce7-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="51ce7-116">Bu konu sürümündeki örnek kod, <xref:System.Text.Json> http yanıtlarında döndürülen JSON içeriğinin serisini kaldırmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="51ce7-117">Ve kullanan örnekler için `Json.NET` `ReadAsAsync<T>` , bu konunun 2. x sürümünü seçmek üzere sürüm seçiciyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="51ce7-118">Tüketim desenleri</span><span class="sxs-lookup"><span data-stu-id="51ce7-118">Consumption patterns</span></span>

<span data-ttu-id="51ce7-119">Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="51ce7-120">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="51ce7-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="51ce7-121">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="51ce7-122">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="51ce7-123">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="51ce7-124">En iyi yaklaşım, uygulamanın gereksinimlerine bağlı olarak değişir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="51ce7-125">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="51ce7-125">Basic usage</span></span>

<span data-ttu-id="51ce7-126">`IHttpClientFactory` , çağırarak kaydedilebilir `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="51ce7-127">`IHttpClientFactory` [Bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)kullanılarak bir istek yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="51ce7-128">Aşağıdaki kod `IHttpClientFactory` bir örnek oluşturmak için kullanır `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="51ce7-129">`IHttpClientFactory`Yukarıdaki örnekte olduğu gibi kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="51ce7-130">Kullanım hakkında hiçbir etkisi yoktur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="51ce7-131">`HttpClient`Var olan bir uygulamada örneklerin oluşturulduğu yerlerde, bu oluşumların ' i çağrılarıyla değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="51ce7-132">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-132">Named clients</span></span>

<span data-ttu-id="51ce7-133">Adlandırılmış istemciler şu durumlarda iyi bir seçimdir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="51ce7-134">Uygulama birçok farklı kullanımı gerektirir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="51ce7-135">Birçok `HttpClient` s farklı yapılandırmaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="51ce7-136">Adlandırılmış için yapılandırma `HttpClient` , içinde kayıt sırasında belirtilebilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="51ce7-137">İstemcinin yapılandırıldığı önceki kodda:</span><span class="sxs-lookup"><span data-stu-id="51ce7-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="51ce7-138">Temel adres `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="51ce7-139">GitHub API 'SI ile çalışmak için iki üst bilgi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="51ce7-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="51ce7-140">CreateClient</span></span>

<span data-ttu-id="51ce7-141">Her zaman <xref:System.Net.Http.IHttpClientFactory.CreateClient*> çağrılır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="51ce7-142">Yeni bir örneği `HttpClient` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="51ce7-143">Yapılandırma eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-143">The configuration action is called.</span></span>

<span data-ttu-id="51ce7-144">Adlandırılmış bir istemci oluşturmak için adını içine geçirin `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="51ce7-145">Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="51ce7-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="51ce7-146">İstemci için yapılandırılan taban adresi kullanıldığından, kod yalnızca yolu geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="51ce7-147">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-147">Typed clients</span></span>

<span data-ttu-id="51ce7-148">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="51ce7-148">Typed clients:</span></span>

* <span data-ttu-id="51ce7-149">Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="51ce7-150">İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="51ce7-151">Yapılandırmak ve belirli bir ile etkileşimde bulunmak için tek bir konum belirtin `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="51ce7-152">Örneğin, tek bir türü belirtilmiş istemci kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="51ce7-153">Tek bir arka uç uç noktası için.</span><span class="sxs-lookup"><span data-stu-id="51ce7-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="51ce7-154">Uç nokta ile ilgili tüm mantığı kapsüllemek için.</span><span class="sxs-lookup"><span data-stu-id="51ce7-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="51ce7-155">DI ile birlikte çalışın ve uygulamada gerektiğinde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="51ce7-156">Türü belirtilmiş istemci `HttpClient` , oluşturucusunda bir parametreyi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="51ce7-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="51ce7-157">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="51ce7-157">In the preceding code:</span></span>

* <span data-ttu-id="51ce7-158">Yapılandırma, yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="51ce7-159">`HttpClient`Nesne bir ortak özellik olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="51ce7-160">İşlevselliği kullanıma sunan, API 'ye özgü Yöntemler oluşturulabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="51ce7-161">Örneğin, `GetAspNetDocsIssues` yöntemi açık sorunları almak için kodu kapsüller.</span><span class="sxs-lookup"><span data-stu-id="51ce7-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="51ce7-162">Aşağıdaki kod, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> `Startup.ConfigureServices` türü belirtilmiş bir istemci sınıfını kaydetmek için ' de çağırır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="51ce7-163">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="51ce7-164">Yukarıdaki kodda `AddHttpClient` `GitHubService` geçici bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="51ce7-165">Bu kayıt, için bir fabrika yöntemi kullanır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="51ce7-166">`HttpClient` örneği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="51ce7-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="51ce7-167">Örneğini oluşturucusuna geçirerek bir örneğini oluşturun `GitHubService` `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="51ce7-168">Yazılan istemci doğrudan eklenebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="51ce7-169">Türü belirtilmiş bir istemcinin yapılandırması `Startup.ConfigureServices` , türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="51ce7-170">, `HttpClient` Türü belirlenmiş bir istemci içinde kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="51ce7-171">Bunu bir özellik olarak göstermek yerine, örneği dahili olarak çağıran bir yöntem tanımlayın `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="51ce7-172">Yukarıdaki kodda, `HttpClient` bir özel alanda depolanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="51ce7-173">Öğesine erişimi, `HttpClient` genel yöntemi tarafından yapılır `GetRepos` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="51ce7-174">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-174">Generated clients</span></span>

<span data-ttu-id="51ce7-175">`IHttpClientFactory` , [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi üçüncü taraf kitaplıklarla birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="51ce7-176">Yeniden sığdırma, .NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="51ce7-177">REST API 'Leri canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="51ce7-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="51ce7-178">Arabirim bir uygulama, `RestService` `HttpClient` dış http çağrıları yapmak için kullanılarak tarafından dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="51ce7-179">Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="51ce7-180">Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="51ce7-181">Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="51ce7-182">GÖNDERI, PUT ve DELETE isteklerini oluşturma</span><span class="sxs-lookup"><span data-stu-id="51ce7-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="51ce7-183">Yukarıdaki örneklerde, tüm HTTP istekleri GET HTTP fiilini kullanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="51ce7-184">`HttpClient` , aşağıdakiler de dahil olmak üzere diğer HTTP fiillerini de destekler:</span><span class="sxs-lookup"><span data-stu-id="51ce7-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="51ce7-185">POST</span><span class="sxs-lookup"><span data-stu-id="51ce7-185">POST</span></span>
* <span data-ttu-id="51ce7-186">PUT</span><span class="sxs-lookup"><span data-stu-id="51ce7-186">PUT</span></span>
* <span data-ttu-id="51ce7-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="51ce7-187">DELETE</span></span>
* <span data-ttu-id="51ce7-188">DÜZELTMESI</span><span class="sxs-lookup"><span data-stu-id="51ce7-188">PATCH</span></span>

<span data-ttu-id="51ce7-189">Desteklenen HTTP fiillerinin tüm listesi için bkz <xref:System.Net.Http.HttpMethod> ..</span><span class="sxs-lookup"><span data-stu-id="51ce7-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="51ce7-190">Aşağıdaki örnek, bir HTTP POST isteğinin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="51ce7-191">Yukarıdaki kodda, `CreateItemAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="51ce7-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="51ce7-192">`TodoItem`Parametresini kullanarak JSON için parametreyi seri hale getirir `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="51ce7-193">Bu <xref:System.Text.Json.JsonSerializerOptions> , serileştirme işlemini yapılandırmak için bir örneğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="51ce7-194"><xref:System.Net.Http.StringContent>Http isteğinin gövdesinde göndermek üzere seri hale GETIRILMIŞ JSON paketini paketlemek için bir örneği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="51ce7-195"><xref:System.Net.Http.HttpClient.PostAsync%2A>JSON içeriğini BELIRTILEN URL 'ye göndermek için çağrılar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="51ce7-196">Bu, [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress)'e eklenen GÖRELI bir URL 'dir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="51ce7-197"><xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A>Yanıt durum kodu başarıyı belirtmezse bir özel durum oluşturmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="51ce7-198">`HttpClient` , diğer içerik türlerini de destekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="51ce7-199">Örneğin, <xref:System.Net.Http.MultipartContent> ve <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="51ce7-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="51ce7-200">Desteklenen içeriğin tamamı listesi için bkz <xref:System.Net.Http.HttpContent> ..</span><span class="sxs-lookup"><span data-stu-id="51ce7-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="51ce7-201">Aşağıdaki örnekte bir HTTP PUT isteği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="51ce7-202">Yukarıdaki kod, POST örneğine çok benzer.</span><span class="sxs-lookup"><span data-stu-id="51ce7-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="51ce7-203">`SaveItemAsync`Yöntemi yerine çağırır <xref:System.Net.Http.HttpClient.PutAsync%2A> `PostAsync` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="51ce7-204">Aşağıdaki örnekte bir HTTP SILME isteği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="51ce7-205">Yukarıdaki kodda, `DeleteItemAsync` yöntemi çağırır <xref:System.Net.Http.HttpClient.DeleteAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="51ce7-206">HTTP DELETE istekleri genellikle gövde içermediğinden, `DeleteAsync` yöntemi bir örneğini kabul eden bir aşırı yükleme sağlamaz `HttpContent` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="51ce7-207">İle farklı HTTP fiillerini kullanma hakkında daha fazla bilgi için `HttpClient` bkz <xref:System.Net.Http.HttpClient> ..</span><span class="sxs-lookup"><span data-stu-id="51ce7-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="51ce7-208">Giden istek ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="51ce7-208">Outgoing request middleware</span></span>

<span data-ttu-id="51ce7-209">`HttpClient` , giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramıdır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="51ce7-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="51ce7-210">`IHttpClientFactory`:</span></span>

  * <span data-ttu-id="51ce7-211">Her bir adlandırılmış istemci için uygulanacak işleyiciler tanımlamayı basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-211">Simplifies defining the handlers to apply for each named client.</span></span>
  * <span data-ttu-id="51ce7-212">Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydedilmesini ve zincirleme kullanımını destekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-212">Supports registration and chaining of multiple handlers to build an outgoing request   middleware pipeline.</span></span> <span data-ttu-id="51ce7-213">Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-213">Each of these handlers is able to perform work before and after the   outgoing request.</span></span> <span data-ttu-id="51ce7-214">Bu model:</span><span class="sxs-lookup"><span data-stu-id="51ce7-214">This pattern:</span></span>
  
    * <span data-ttu-id="51ce7-215">ASP.NET Core gelen ara yazılım ardışık düzenine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
    * <span data-ttu-id="51ce7-216">, HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar, örneğin:</span><span class="sxs-lookup"><span data-stu-id="51ce7-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>
      * <span data-ttu-id="51ce7-217">önbelleği</span><span class="sxs-lookup"><span data-stu-id="51ce7-217">caching</span></span>
      * <span data-ttu-id="51ce7-218">hata işleme</span><span class="sxs-lookup"><span data-stu-id="51ce7-218">error handling</span></span>
      * <span data-ttu-id="51ce7-219">getir</span><span class="sxs-lookup"><span data-stu-id="51ce7-219">serialization</span></span>
      * <span data-ttu-id="51ce7-220">günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="51ce7-220">logging</span></span>

<span data-ttu-id="51ce7-221">Temsilci seçme işleyicisi oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="51ce7-221">To create a delegating handler:</span></span>

* <span data-ttu-id="51ce7-222">Türet <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="51ce7-223">Geçersiz kıl <xref:System.Net.Http.DelegatingHandler.SendAsync*> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="51ce7-224">İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütün:</span><span class="sxs-lookup"><span data-stu-id="51ce7-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="51ce7-225">Yukarıdaki kod, üstbilginin istekte olup olmadığını denetler `X-API-KEY` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="51ce7-226">`X-API-KEY`Eksikse, <xref:System.Net.HttpStatusCode.BadRequest> döndürülür.</span><span class="sxs-lookup"><span data-stu-id="51ce7-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="51ce7-227">İçin yapılandırmasına birden fazla işleyici eklenebilir `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="51ce7-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="51ce7-228">Yukarıdaki kodda,, `ValidateHeaderHandler` dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="51ce7-229">Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyicinin türü geçirerek, çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-229">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="51ce7-230">Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-230">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="51ce7-231">Her işleyici, son isteği çalıştırana kadar sonraki işleyiciyi sarmalar `HttpClientHandler` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-231">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a><span data-ttu-id="51ce7-232">Giden istek ara ortamında DI kullanma</span><span class="sxs-lookup"><span data-stu-id="51ce7-232">Use DI in outgoing request middleware</span></span>

<span data-ttu-id="51ce7-233">`IHttpClientFactory`Yeni bir temsilci seçme işleyicisi oluşturduğunda, işleyicinin Oluşturucu parametrelerini karşılamak IÇIN dı kullanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-233">When `IHttpClientFactory` creates a new delegating handler, it uses DI to fulfill the handler's constructor parameters.</span></span> <span data-ttu-id="51ce7-234">`IHttpClientFactory` Her işleyici için **ayrı** bir dı kapsamı oluşturur ve bu, bir işleyici *kapsamlı* bir hizmeti tükettiği zaman ortaya çıkmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-234">`IHttpClientFactory` creates a **separate** DI scope for each handler, which can lead to surprising behavior when a handler consumes a *scoped* service.</span></span>

<span data-ttu-id="51ce7-235">Örneğin, bir görevi tanımlayıcı ile bir işlem olarak temsil eden aşağıdaki arabirimi ve uygulamasını göz önünde bulundurun `OperationId` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-235">For example, consider the following interface and its implementation, which represents a task as an operation with an identifier, `OperationId`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

<span data-ttu-id="51ce7-236">Adından da anlaşılacağı gibi, `IOperationScoped` *kapsamlı* bir yaşam süresi kullanılarak dı ile kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-236">As its name suggests, `IOperationScoped` is registered with DI using a *scoped* lifetime:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

<span data-ttu-id="51ce7-237">Aşağıdaki temsilci işleyicisi, `IOperationScoped` `X-OPERATION-ID` giden istek için üst bilgiyi ayarlamak için kullanır ve kullanır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-237">The following delegating handler consumes and uses `IOperationScoped` to set the `X-OPERATION-ID` header for the outgoing request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

<span data-ttu-id="51ce7-238">[ `HttpRequestsSample` İndir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)] `/Operation` sayfasında, sayfasına gidin ve sayfayı yenileyin.</span><span class="sxs-lookup"><span data-stu-id="51ce7-238">In the [`HttpRequestsSample` download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], navigate to `/Operation` and refresh the page.</span></span> <span data-ttu-id="51ce7-239">İstek kapsamı değeri her istek için değişir, ancak işleyici kapsam değeri yalnızca her 5 saniyede bir değişir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-239">The request scope value changes for each request, but the handler scope value only changes every 5 seconds.</span></span>

<span data-ttu-id="51ce7-240">İşleyiciler herhangi bir kapsamın hizmetlerine bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-240">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="51ce7-241">İşleyicilerin bağımlı olduğu hizmetler, işleyicinin elden çıkarılmasıyla kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-241">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="51ce7-242">İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="51ce7-242">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="51ce7-243">[HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties)kullanarak işleyicide veri geçirin.</span><span class="sxs-lookup"><span data-stu-id="51ce7-243">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="51ce7-244"><xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>Geçerli isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-244">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="51ce7-245"><xref:System.Threading.AsyncLocal`1>Verileri geçirmek için özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="51ce7-245">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="51ce7-246">Polly tabanlı işleyiciler kullanın</span><span class="sxs-lookup"><span data-stu-id="51ce7-246">Use Polly-based handlers</span></span>

<span data-ttu-id="51ce7-247">`IHttpClientFactory` üçüncü taraf kitaplığı [Polly](https://github.com/App-vNext/Polly)ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-247">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="51ce7-248">Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-248">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="51ce7-249">Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-249">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="51ce7-250">Uzantı yöntemleri, yapılandırılmış örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-250">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-251">Polly uzantıları, istemcilere Polly tabanlı işleyiciler eklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-251">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="51ce7-252">Polly, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-252">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="51ce7-253">Geçici hataları işle</span><span class="sxs-lookup"><span data-stu-id="51ce7-253">Handle transient faults</span></span>

<span data-ttu-id="51ce7-254">Hatalar genellikle dış HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-254">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="51ce7-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> geçici hataları işlemek için bir ilkenin tanımlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="51ce7-256">`AddTransientHttpErrorPolicy`Aşağıdaki yanıtları işleyecek şekilde yapılandırılan ilkeler:</span><span class="sxs-lookup"><span data-stu-id="51ce7-256">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="51ce7-257">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="51ce7-257">HTTP 5xx</span></span>
* <span data-ttu-id="51ce7-258">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="51ce7-258">HTTP 408</span></span>

<span data-ttu-id="51ce7-259">`AddTransientHttpErrorPolicy``PolicyBuilder`olası bir geçici hatayı temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="51ce7-259">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="51ce7-260">Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-260">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="51ce7-261">Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-261">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="51ce7-262">Dinamik olarak ilke seçme</span><span class="sxs-lookup"><span data-stu-id="51ce7-262">Dynamically select policies</span></span>

<span data-ttu-id="51ce7-263">Uzantı yöntemleri, örneğin, Polly tabanlı işleyiciler eklemek için sağlanır <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-263">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="51ce7-264">Aşağıdaki `AddPolicyHandler` aşırı yükleme, hangi ilkenin uygulanacağını belirlemek için isteği inceler:</span><span class="sxs-lookup"><span data-stu-id="51ce7-264">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="51ce7-265">Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-265">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="51ce7-266">Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-266">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="51ce7-267">Birden çok Polly işleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="51ce7-267">Add multiple Polly handlers</span></span>

<span data-ttu-id="51ce7-268">Polly ilkeleri iç içe almak yaygın bir şekilde yapılır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-268">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="51ce7-269">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="51ce7-269">In the preceding example:</span></span>

* <span data-ttu-id="51ce7-270">İki işleyici eklenir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-270">Two handlers are added.</span></span>
* <span data-ttu-id="51ce7-271">İlk işleyici, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> yeniden deneme ilkesi eklemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-271">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="51ce7-272">Başarısız istekler en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-272">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="51ce7-273">İkinci `AddTransientHttpErrorPolicy` çağrı bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-273">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="51ce7-274">5 başarısız girişim sıralı olarak gerçekleşirse, daha fazla dış istek 30 saniye boyunca engellenir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-274">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="51ce7-275">Devre kesici ilkeleri durum bilgisi vardır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-275">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="51ce7-276">Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-276">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="51ce7-277">Polly kayıt defterinden ilke ekleme</span><span class="sxs-lookup"><span data-stu-id="51ce7-277">Add policies from the Polly registry</span></span>

<span data-ttu-id="51ce7-278">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir ile kaydetmektir `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-278">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="51ce7-279">Aşağıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="51ce7-279">In the following code:</span></span>

* <span data-ttu-id="51ce7-280">"Normal" ve "uzun" ilkeler eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-280">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="51ce7-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> kayıt defterinden "normal" ve "uzun" ilkeleri ekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="51ce7-282">Ve daha fazla tümleştirme hakkında daha fazla bilgi için `IHttpClientFactory` bkz. [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="51ce7-282">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="51ce7-283">HttpClient ve ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="51ce7-283">HttpClient and lifetime management</span></span>

<span data-ttu-id="51ce7-284">`HttpClient`Her çağrıldığında yeni bir örnek döndürülür `CreateClient` `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-284">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="51ce7-285"><xref:System.Net.Http.HttpMessageHandler>Adlandırılmış istemci başına oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-285">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="51ce7-286">Fabrika, örneklerin yaşam sürelerini yönetir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-286">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="51ce7-287">`IHttpClientFactory``HttpMessageHandler`kaynak tüketimini azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-287">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="51ce7-288">Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni bir örnek oluştururken havuzdan yeniden kullanılabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-288">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="51ce7-289">Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-289">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="51ce7-290">Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-290">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="51ce7-291">Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS (etki alanı adı sistemi) değişikliklerine yeniden davranmasını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-291">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="51ce7-292">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-292">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="51ce7-293">Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-293">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="51ce7-294">`HttpClient` örnekler genellikle aktiften **çıkarma gerektirmeyen .NET nesneleri olarak** kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-294">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="51ce7-295">Çıkarma giden istekleri iptal eder ve `HttpClient` çağırma sonrasında verilen örneğin kullanılamaz olmasını sağlar <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-295">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="51ce7-296">`IHttpClientFactory` örnekler tarafından kullanılan kaynakları izler ve ortadan kaldırdık `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-296">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="51ce7-297">Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde kullanılmadan önce kullanılan ortak bir modeldir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-297">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="51ce7-298">Bu model, ' a geçtikten sonra gereksiz hale gelir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-298">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="51ce7-299">Ihttpclientfactory alternatifleri</span><span class="sxs-lookup"><span data-stu-id="51ce7-299">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="51ce7-300">`IHttpClientFactory`Dı etkin bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="51ce7-300">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="51ce7-301">Havuz örneklerine göre kaynak tükenmesi sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-301">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="51ce7-302">Düzenli aralıklarla örnekleri geçirerek eski DNS sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-302">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="51ce7-303">Uzun süreli bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-303">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="51ce7-304">`SocketsHttpHandler`Uygulamanın başladığı zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-304">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="51ce7-305"><xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>DNS yenileme süreleri temelinde uygun bir değere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-305">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="51ce7-306">`HttpClient`Gerektiğinde örnek oluşturun `new HttpClient(handler, disposeHandler: false)` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-306">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="51ce7-307">Yukarıdaki yaklaşımlar, `IHttpClientFactory` benzer bir şekilde çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="51ce7-307">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="51ce7-308">, `SocketsHttpHandler` Örnekleri arasında bağlantıları paylaşır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-308">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-309">Bu paylaşım, yuva azalmasına engel olur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-309">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="51ce7-310">`SocketsHttpHandler`Bağlantıları, `PooledConnectionLifetime` eski DNS sorunlarından kaçınmak için öğesine göre döngüler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-310">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="51ce7-311">Cookiemalar</span><span class="sxs-lookup"><span data-stu-id="51ce7-311">Cookies</span></span>

<span data-ttu-id="51ce7-312">Havuza alınmış `HttpMessageHandler` örnekler, `CookieContainer` paylaşılan nesneler ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-312">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="51ce7-313">Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-313">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="51ce7-314">S gerektiren uygulamalar için cookie şunlardan birini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="51ce7-314">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="51ce7-315">Otomatik işlemeyi devre dışı bırakma cookie</span><span class="sxs-lookup"><span data-stu-id="51ce7-315">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="51ce7-316">Önlemenin `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="51ce7-316">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="51ce7-317"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Otomatik işlemeyi devre dışı bırakmak için çağırın cookie :</span><span class="sxs-lookup"><span data-stu-id="51ce7-317">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="51ce7-318">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="51ce7-318">Logging</span></span>

<span data-ttu-id="51ce7-319">`IHttpClientFactory`Tüm istekler için kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-319">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="51ce7-320">Varsayılan günlük iletilerini görmek için günlük yapılandırmasında uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="51ce7-320">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="51ce7-321">İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-321">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="51ce7-322">Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-322">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="51ce7-323">Örneğin, *Mynamedclient* adlı bir istemci, "System .net. http. HttpClient" kategorisine sahip iletileri günlüğe kaydeder. **Mynamedclient**. LogicalHandler ".</span><span class="sxs-lookup"><span data-stu-id="51ce7-323">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="51ce7-324">*Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-324">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="51ce7-325">İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-325">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="51ce7-326">Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-326">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="51ce7-327">Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-327">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="51ce7-328">*Mynamedclient* örneğinde, bu Iletiler "System .net. http. HttpClient" günlük kategorisiyle günlüğe kaydedilir. **Mynamedclient**. ClientHandler ".</span><span class="sxs-lookup"><span data-stu-id="51ce7-328">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="51ce7-329">İstek için bu, tüm diğer işleyiciler çalıştırıldıktan sonra ve istek gönderilmeden hemen önce gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-329">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="51ce7-330">Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-330">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="51ce7-331">İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-331">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="51ce7-332">Bu, istek üst bilgilerinde veya yanıt durum kodunda yapılan değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-332">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="51ce7-333">İstemcinin adını log kategorisinde da içermek, belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-333">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="51ce7-334">HttpMessageHandler 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="51ce7-334">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="51ce7-335">İstemci tarafından kullanılan iç yapılandırmayı denetlemek gerekli olabilir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-335">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="51ce7-336">`IHttpClientBuilder`Adlandırılmış veya yazılan istemciler eklenirken bir döndürülür.</span><span class="sxs-lookup"><span data-stu-id="51ce7-336">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="51ce7-337"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-337">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="51ce7-338">Temsilci, `HttpMessageHandler` Bu istemci tarafından kullanılan birincili oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-338">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="51ce7-339">Konsol uygulamasında ıhttpclientfactory kullanma</span><span class="sxs-lookup"><span data-stu-id="51ce7-339">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="51ce7-340">Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="51ce7-340">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="51ce7-341">Microsoft. Extensions. Hosting</span><span class="sxs-lookup"><span data-stu-id="51ce7-341">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="51ce7-342">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="51ce7-342">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="51ce7-343">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="51ce7-343">In the following example:</span></span>

* <span data-ttu-id="51ce7-344"><xref:System.Net.Http.IHttpClientFactory> , [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-344"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="51ce7-345">`MyService` hizmetinden bir istemci fabrikası örneği oluşturur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-345">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="51ce7-346">`HttpClient` , bir Web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-346">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="51ce7-347">`Main` hizmetin yöntemini yürütmek için bir kapsam oluşturur `GetPage` ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-347">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="51ce7-348">Üst bilgi yayma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="51ce7-348">Header propagation middleware</span></span>

<span data-ttu-id="51ce7-349">Üst bilgi yayma, gelen istekten giden HTTP Istemci isteklerine HTTP üstbilgilerini yaymaya yönelik bir ASP.NET Core ara istemcindedir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-349">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="51ce7-350">Üst bilgi yaymayı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="51ce7-350">To use header propagation:</span></span>

* <span data-ttu-id="51ce7-351">[Microsoft. AspNetCore. Headeryayma](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="51ce7-351">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="51ce7-352">Ara yazılımı ve `HttpClient` içinde yapılandırın `Startup` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-352">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="51ce7-353">İstemci giden isteklerde yapılandırılan üst bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-353">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="51ce7-354">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="51ce7-354">Additional resources</span></span>

* [<span data-ttu-id="51ce7-355">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="51ce7-355">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="51ce7-356">HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın</span><span class="sxs-lookup"><span data-stu-id="51ce7-356">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="51ce7-357">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="51ce7-357">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="51ce7-358">.NET 'te JSON serileştirme ve serisini kaldırma</span><span class="sxs-lookup"><span data-stu-id="51ce7-358">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="51ce7-359">, [Glenn CONDRON](https://github.com/glennc), [Ryan şimdi e](https://github.com/rynowak)ve [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="51ce7-359">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="51ce7-360">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak için kayıt yapılabilir ve kullanılabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-360">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="51ce7-361">Aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="51ce7-361">It offers the following benefits:</span></span>

* <span data-ttu-id="51ce7-362">, Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-362">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-363">Örneğin, *GitHub istemcisi kayıtlı* ve [GitHub](https://github.com/)'a erişebilecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-363">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="51ce7-364">Varsayılan istemci, diğer amaçlar için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-364">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="51ce7-365">' De işleyiciler için temsilci atama ile giden ara yazılım kavramı `HttpClient` ve bundan faydalanmak için, Polly tabanlı ara yazılım için uzantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-365">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="51ce7-366">`HttpClientMessageHandler`Yaşam sürelerini el ile yönetirken gerçekleşen YAYGıN DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-366">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="51ce7-367">`ILogger`Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).</span><span class="sxs-lookup"><span data-stu-id="51ce7-367">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="51ce7-368">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51ce7-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="51ce7-369">Tüketim desenleri</span><span class="sxs-lookup"><span data-stu-id="51ce7-369">Consumption patterns</span></span>

<span data-ttu-id="51ce7-370">Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-370">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="51ce7-371">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="51ce7-371">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="51ce7-372">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-372">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="51ce7-373">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-373">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="51ce7-374">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-374">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="51ce7-375">Hiçbiri diğerinden tamamen üst değildir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-375">None of them are strictly superior to another.</span></span> <span data-ttu-id="51ce7-376">En iyi yaklaşım, uygulamanın kısıtlamalarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-376">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="51ce7-377">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="51ce7-377">Basic usage</span></span>

<span data-ttu-id="51ce7-378">, `IHttpClientFactory` `AddHttpClient` Yöntemi içindeki içindeki genişletme yöntemi çağırarak kaydedilebilir `IServiceCollection` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-378">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="51ce7-379">Kaydedildikten sonra kod, `IHttpClientFactory` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile her yerden bir hizmeti kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-379">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="51ce7-380">`IHttpClientFactory`Bir örnek oluşturmak için kullanılabilir `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-380">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="51ce7-381">`IHttpClientFactory`Bu biçimde kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-381">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="51ce7-382">Kullanım şekli üzerinde hiçbir etkisi yoktur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-382">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="51ce7-383">`HttpClient`Örneklerin Şu anda oluşturulduğu yerlerde, bu tekrarlamaları ' a çağrı ile değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-383">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="51ce7-384">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-384">Named clients</span></span>

<span data-ttu-id="51ce7-385">Bir uygulama `HttpClient` , her biri farklı bir yapılandırmaya sahip birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemciler** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-385">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="51ce7-386">Adlandırılmış için yapılandırma `HttpClient` , içinde kayıt sırasında belirtilebilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-386">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="51ce7-387">Yukarıdaki kodda, `AddHttpClient` *GitHub* adının sağlanması denir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-387">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="51ce7-388">Bu istemci, &mdash; GITHUB API 'siyle birlikte çalışmak için gerekli olan temel adres ve iki üst bilgi olan bazı varsayılan yapılandırma uygulanmış.</span><span class="sxs-lookup"><span data-stu-id="51ce7-388">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="51ce7-389">Her seferinde her `CreateClient` çağrıldığında yeni bir örneği `HttpClient` oluşturulur ve yapılandırma eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-389">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="51ce7-390">Adlandırılmış bir istemciyi kullanmak için, bir dize parametresi iletilebilir `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-390">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="51ce7-391">Oluşturulacak istemcinin adını belirtin:</span><span class="sxs-lookup"><span data-stu-id="51ce7-391">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="51ce7-392">Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="51ce7-392">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="51ce7-393">İstemci için yapılandırılan taban adresi kullanıldığından, bu yalnızca yolu geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-393">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="51ce7-394">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-394">Typed clients</span></span>

<span data-ttu-id="51ce7-395">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="51ce7-395">Typed clients:</span></span>

* <span data-ttu-id="51ce7-396">Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-396">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="51ce7-397">İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-397">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="51ce7-398">Yapılandırmak ve belirli bir ile etkileşimde bulunmak için tek bir konum belirtin `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-398">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="51ce7-399">Örneğin, tek bir arka uç uç noktası için tek bir adet yazılmış istemci kullanılabilir ve bu uç nokta ile ilgili tüm mantığı kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-399">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="51ce7-400">DI ile birlikte çalışın ve uygulamanızda gerektiğinde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-400">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="51ce7-401">Türü belirtilmiş istemci `HttpClient` , oluşturucusunda bir parametreyi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="51ce7-401">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="51ce7-402">Önceki kodda, yapılandırma yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-402">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="51ce7-403">`HttpClient`Nesne bir ortak özellik olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-403">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="51ce7-404">İşlevselliği kullanıma sunan API 'ye özel yöntemler tanımlamak mümkündür `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-404">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="51ce7-405">`GetAspNetDocsIssues`Yöntemi, GitHub deposundan en son açık sorunları sorgulamak ve ayrıştırmak için gereken kodu saklar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-405">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="51ce7-406">Türü belirtilmiş bir istemciyi kaydettirmek için, genel <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genişletme yöntemi içinde kullanılabilir `Startup.ConfigureServices` istemci sınıfını belirterek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-406">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="51ce7-407">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-407">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="51ce7-408">Yazılan istemci doğrudan eklenebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-408">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="51ce7-409">Tercih edilirse, yazılan istemcinin yapılandırması, `Startup.ConfigureServices` türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-409">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="51ce7-410">`HttpClient`Türü belirtilmiş bir istemci içinde tamamen kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-410">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="51ce7-411">Bunu bir özellik olarak göstermek yerine, örneği dahili olarak çağıran ortak Yöntemler sunulabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-411">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="51ce7-412">Önceki kodda, `HttpClient` bir özel alan olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-412">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="51ce7-413">Dış çağrıları yapmak için tüm erişim `GetRepos` yönteminden geçer.</span><span class="sxs-lookup"><span data-stu-id="51ce7-413">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="51ce7-414">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-414">Generated clients</span></span>

<span data-ttu-id="51ce7-415">`IHttpClientFactory` , [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıklarıyla birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-415">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="51ce7-416">Yeniden sığdırma, .NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-416">Refit is a REST library for .NET.</span></span> <span data-ttu-id="51ce7-417">REST API 'Leri canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="51ce7-417">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="51ce7-418">Arabirim bir uygulama, `RestService` `HttpClient` dış http çağrıları yapmak için kullanılarak tarafından dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-418">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="51ce7-419">Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-419">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="51ce7-420">Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-420">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="51ce7-421">Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-421">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="51ce7-422">Giden istek ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="51ce7-422">Outgoing request middleware</span></span>

<span data-ttu-id="51ce7-423">`HttpClient` , giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramı zaten var.</span><span class="sxs-lookup"><span data-stu-id="51ce7-423">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="51ce7-424">, `IHttpClientFactory` Her bir adlandırılmış istemci için uygulanacak işleyicileri tanımlamanızı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-424">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="51ce7-425">Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydını ve zincirlemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-425">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="51ce7-426">Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-426">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="51ce7-427">Bu düzen, ASP.NET Core gelen ara yazılım ardışık düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="51ce7-427">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="51ce7-428">Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-428">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="51ce7-429">Bir işleyici oluşturmak için, öğesinden türeten bir sınıf tanımlayın <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-429">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="51ce7-430">`SendAsync`İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="51ce7-430">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="51ce7-431">Yukarıdaki kod, temel bir işleyiciyi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-431">The preceding code defines a basic handler.</span></span> <span data-ttu-id="51ce7-432">İsteğe bağlı bir başlık olup olmadığını denetler `X-API-KEY` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-432">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="51ce7-433">Üst bilgi eksikse, HTTP çağrısından kaçınabilir ve uygun bir yanıt döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-433">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="51ce7-434">Kayıt sırasında, bir veya daha fazla işleyici bir için yapılandırmasına eklenebilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-434">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="51ce7-435">Bu görev, üzerindeki genişletme yöntemleri aracılığıyla gerçekleştirilir <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-435">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="51ce7-436">Yukarıdaki kodda,, `ValidateHeaderHandler` dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-436">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="51ce7-437">`IHttpClientFactory`Her işleyici için ayrı bır dı kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-437">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="51ce7-438">İşleyiciler herhangi bir kapsamın hizmetlerine bağlı olarak ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-438">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="51ce7-439">İşleyicilerin bağımlı olduğu hizmetler, işleyicinin elden çıkarılmasıyla kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-439">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="51ce7-440">Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyicinin türü geçirerek, çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-440">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="51ce7-441">Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-441">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="51ce7-442">Her işleyici, son isteği çalıştırana kadar sonraki işleyiciyi sarmalar `HttpClientHandler` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-442">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="51ce7-443">İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="51ce7-443">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="51ce7-444">Kullanarak işleyicide veri geçirin `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-444">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="51ce7-445">`IHttpContextAccessor`Geçerli isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-445">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="51ce7-446">`AsyncLocal`Verileri geçirmek için özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="51ce7-446">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="51ce7-447">Polly tabanlı işleyiciler kullanın</span><span class="sxs-lookup"><span data-stu-id="51ce7-447">Use Polly-based handlers</span></span>

<span data-ttu-id="51ce7-448">`IHttpClientFactory` , [Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kitaplığı ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-448">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="51ce7-449">Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-449">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="51ce7-450">Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-450">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="51ce7-451">Uzantı yöntemleri, yapılandırılmış örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-451">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-452">Polly uzantıları:</span><span class="sxs-lookup"><span data-stu-id="51ce7-452">The Polly extensions:</span></span>

* <span data-ttu-id="51ce7-453">İstemcilere Polly tabanlı işleyiciler eklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-453">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="51ce7-454">, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-454">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="51ce7-455">Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-455">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="51ce7-456">Geçici hataları işle</span><span class="sxs-lookup"><span data-stu-id="51ce7-456">Handle transient faults</span></span>

<span data-ttu-id="51ce7-457">Yaygın hatalar, dış HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-457">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="51ce7-458">`AddTransientHttpErrorPolicy`Geçici hataları işlemek üzere bir ilkenin tanımlanmasını sağlayan, çağrılan bir uygun genişletme yöntemi eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-458">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="51ce7-459">Bu uzantı yöntemi tanıtıcısıyla yapılandırılmış ilkeler `HttpRequestException` , http 5xx yanıtları ve http 408 yanıtları.</span><span class="sxs-lookup"><span data-stu-id="51ce7-459">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="51ce7-460">`AddTransientHttpErrorPolicy`Uzantı içinde kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-460">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="51ce7-461">Uzantı, `PolicyBuilder` olası bir geçici hatayı temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="51ce7-461">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="51ce7-462">Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-462">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="51ce7-463">Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-463">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="51ce7-464">Dinamik olarak ilke seçme</span><span class="sxs-lookup"><span data-stu-id="51ce7-464">Dynamically select policies</span></span>

<span data-ttu-id="51ce7-465">Polly tabanlı işleyiciler eklemek için kullanılabilecek ek uzantı yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-465">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="51ce7-466">Bu tür bir uzantının `AddPolicyHandler` birden çok aşırı yüklemesi vardır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-466">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="51ce7-467">Bir aşırı yükleme, hangi ilkenin uygulanacağını tanımlarken isteğin incelenebilirliğini sağlar:</span><span class="sxs-lookup"><span data-stu-id="51ce7-467">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="51ce7-468">Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-468">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="51ce7-469">Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-469">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="51ce7-470">Birden çok Polly işleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="51ce7-470">Add multiple Polly handlers</span></span>

<span data-ttu-id="51ce7-471">Gelişmiş işlevsellik sağlamak için çok fazla ilke iç içe geçmiş bir yaygın hale gelir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-471">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="51ce7-472">Yukarıdaki örnekte, iki işleyici eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-472">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="51ce7-473">İlki, `AddTransientHttpErrorPolicy` yeniden deneme ilkesi eklemek için uzantıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-473">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="51ce7-474">Başarısız istekler en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-474">Failed requests are retried up to three times.</span></span> <span data-ttu-id="51ce7-475">İçin ikinci çağrı, `AddTransientHttpErrorPolicy` bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-475">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="51ce7-476">Beş başarısız girişim sırayla gerçekleşiyorsa, daha fazla dış istek 30 saniye için engellenir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-476">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="51ce7-477">Devre kesici ilkeleri durum bilgisi vardır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-477">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="51ce7-478">Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-478">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="51ce7-479">Polly kayıt defterinden ilke ekleme</span><span class="sxs-lookup"><span data-stu-id="51ce7-479">Add policies from the Polly registry</span></span>

<span data-ttu-id="51ce7-480">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir ile kaydetmektir `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-480">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="51ce7-481">Kayıt defterinden bir ilke kullanılarak bir işleyicinin eklenmesine izin veren bir genişletme yöntemi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-481">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="51ce7-482">Yukarıdaki kodda, öğesine eklendiğinde iki ilke kaydedilir `PolicyRegistry` `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-482">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="51ce7-483">Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır ve uygulanacak ilke adı geçer.</span><span class="sxs-lookup"><span data-stu-id="51ce7-483">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="51ce7-484">Ve daha fazla tümleştirme hakkında daha fazla bilgi `IHttpClientFactory` , [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)' de bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-484">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="51ce7-485">HttpClient ve ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="51ce7-485">HttpClient and lifetime management</span></span>

<span data-ttu-id="51ce7-486">`HttpClient`Her çağrıldığında yeni bir örnek döndürülür `CreateClient` `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-486">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="51ce7-487"><xref:System.Net.Http.HttpMessageHandler>Adlandırılmış istemci başına.</span><span class="sxs-lookup"><span data-stu-id="51ce7-487">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="51ce7-488">Fabrika, örneklerin yaşam sürelerini yönetir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-488">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="51ce7-489">`IHttpClientFactory``HttpMessageHandler`kaynak tüketimini azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-489">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="51ce7-490">Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni bir örnek oluştururken havuzdan yeniden kullanılabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-490">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="51ce7-491">Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-491">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="51ce7-492">Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-492">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="51ce7-493">Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine yeniden davranmasını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-493">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="51ce7-494">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-494">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="51ce7-495">Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-495">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="51ce7-496">Bunu geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` istemcisini oluştururken döndürülen öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="51ce7-496">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="51ce7-497">İstemcinin çıkarılması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-497">Disposal of the client isn't required.</span></span> <span data-ttu-id="51ce7-498">Çıkarma giden istekleri iptal eder ve `HttpClient` çağırma sonrasında verilen örneğin kullanılamaz olmasını sağlar <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-498">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="51ce7-499">`IHttpClientFactory` örnekler tarafından kullanılan kaynakları izler ve ortadan kaldırdık `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-499">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-500">`HttpClient`Örnekler genellikle aktiften çıkarma gerektirmeyen .NET nesneleri olarak kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-500">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="51ce7-501">Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde kullanılmadan önce kullanılan ortak bir modeldir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-501">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="51ce7-502">Bu model, ' a geçtikten sonra gereksiz hale gelir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-502">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="51ce7-503">Ihttpclientfactory alternatifleri</span><span class="sxs-lookup"><span data-stu-id="51ce7-503">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="51ce7-504">`IHttpClientFactory`Dı etkin bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="51ce7-504">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="51ce7-505">Havuz örneklerine göre kaynak tükenmesi sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-505">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="51ce7-506">Düzenli aralıklarla örnekleri geçirerek eski DNS sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-506">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="51ce7-507">Uzun süreli bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-507">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="51ce7-508">`SocketsHttpHandler`Uygulamanın başladığı zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-508">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="51ce7-509"><xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>DNS yenileme süreleri temelinde uygun bir değere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-509">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="51ce7-510">`HttpClient`Gerektiğinde örnek oluşturun `new HttpClient(handler, disposeHandler: false)` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-510">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="51ce7-511">Yukarıdaki yaklaşımlar, `IHttpClientFactory` benzer bir şekilde çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="51ce7-511">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="51ce7-512">, `SocketsHttpHandler` Örnekleri arasında bağlantıları paylaşır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-512">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-513">Bu paylaşım, yuva azalmasına engel olur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-513">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="51ce7-514">`SocketsHttpHandler`Bağlantıları, `PooledConnectionLifetime` eski DNS sorunlarından kaçınmak için öğesine göre döngüler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-514">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="51ce7-515">Cookiemalar</span><span class="sxs-lookup"><span data-stu-id="51ce7-515">Cookies</span></span>

<span data-ttu-id="51ce7-516">Havuza alınmış `HttpMessageHandler` örnekler, `CookieContainer` paylaşılan nesneler ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-516">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="51ce7-517">Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-517">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="51ce7-518">S gerektiren uygulamalar için cookie şunlardan birini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="51ce7-518">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="51ce7-519">Otomatik işlemeyi devre dışı bırakma cookie</span><span class="sxs-lookup"><span data-stu-id="51ce7-519">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="51ce7-520">Önlemenin `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="51ce7-520">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="51ce7-521"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Otomatik işlemeyi devre dışı bırakmak için çağırın cookie :</span><span class="sxs-lookup"><span data-stu-id="51ce7-521">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="51ce7-522">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="51ce7-522">Logging</span></span>

<span data-ttu-id="51ce7-523">`IHttpClientFactory`Tüm istekler için kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-523">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="51ce7-524">Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="51ce7-524">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="51ce7-525">İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-525">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="51ce7-526">Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-526">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="51ce7-527">Örneğin, *Mynamedclient* adlı bir istemci, bir kategorisine sahip iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-527">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="51ce7-528">*Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-528">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="51ce7-529">İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-529">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="51ce7-530">Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-530">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="51ce7-531">Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-531">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="51ce7-532">*Mynamedclient* örneğinde, bu iletiler günlük kategorisine göre günlüğe kaydedilir `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-532">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="51ce7-533">İstek için bu, tüm diğer işleyiciler çalıştıktan sonra ve istek ağda gönderilmeden hemen önce gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-533">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="51ce7-534">Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-534">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="51ce7-535">İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-535">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="51ce7-536">Bu, örneğin veya yanıt durum kodunda istek başlıklarındaki değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-536">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="51ce7-537">İstemci adı ' nı log kategorisinde da içermek, gerektiğinde belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-537">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="51ce7-538">HttpMessageHandler 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="51ce7-538">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="51ce7-539">İstemci tarafından kullanılan iç yapılandırmayı denetlemek gerekli olabilir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-539">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="51ce7-540">`IHttpClientBuilder`Adlandırılmış veya yazılan istemciler eklenirken bir döndürülür.</span><span class="sxs-lookup"><span data-stu-id="51ce7-540">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="51ce7-541"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-541">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="51ce7-542">Temsilci, `HttpMessageHandler` Bu istemci tarafından kullanılan birincili oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-542">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="51ce7-543">Konsol uygulamasında ıhttpclientfactory kullanma</span><span class="sxs-lookup"><span data-stu-id="51ce7-543">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="51ce7-544">Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="51ce7-544">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="51ce7-545">Microsoft. Extensions. Hosting</span><span class="sxs-lookup"><span data-stu-id="51ce7-545">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="51ce7-546">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="51ce7-546">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="51ce7-547">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="51ce7-547">In the following example:</span></span>

* <span data-ttu-id="51ce7-548"><xref:System.Net.Http.IHttpClientFactory> , [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-548"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="51ce7-549">`MyService` hizmetinden bir istemci fabrikası örneği oluşturur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-549">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="51ce7-550">`HttpClient` , bir Web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-550">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="51ce7-551">`Main` hizmetin yöntemini yürütmek için bir kapsam oluşturur `GetPage` ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-551">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="51ce7-552">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="51ce7-552">Additional resources</span></span>

* [<span data-ttu-id="51ce7-553">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="51ce7-553">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="51ce7-554">HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın</span><span class="sxs-lookup"><span data-stu-id="51ce7-554">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="51ce7-555">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="51ce7-555">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="51ce7-556">, [Glenn CONDRON](https://github.com/glennc), [Ryan şimdi e](https://github.com/rynowak)ve [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="51ce7-556">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="51ce7-557">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak için kayıt yapılabilir ve kullanılabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-557">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="51ce7-558">Aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="51ce7-558">It offers the following benefits:</span></span>

* <span data-ttu-id="51ce7-559">, Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-559">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-560">Örneğin, *GitHub istemcisi kayıtlı* ve [GitHub](https://github.com/)'a erişebilecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-560">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="51ce7-561">Varsayılan istemci, diğer amaçlar için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-561">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="51ce7-562">' De işleyiciler için temsilci atama ile giden ara yazılım kavramı `HttpClient` ve bundan faydalanmak için, Polly tabanlı ara yazılım için uzantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-562">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="51ce7-563">`HttpClientMessageHandler`Yaşam sürelerini el ile yönetirken gerçekleşen YAYGıN DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-563">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="51ce7-564">`ILogger`Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).</span><span class="sxs-lookup"><span data-stu-id="51ce7-564">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="51ce7-565">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51ce7-565">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="51ce7-566">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="51ce7-566">Prerequisites</span></span>

<span data-ttu-id="51ce7-567">.NET Framework hedefleyen projeler [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet paketinin yüklenmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-567">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="51ce7-568">.NET Core ile hedeflenen ve [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvuran projeler zaten paketi içeriyor `Microsoft.Extensions.Http` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-568">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="51ce7-569">Tüketim desenleri</span><span class="sxs-lookup"><span data-stu-id="51ce7-569">Consumption patterns</span></span>

<span data-ttu-id="51ce7-570">Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-570">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="51ce7-571">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="51ce7-571">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="51ce7-572">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-572">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="51ce7-573">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-573">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="51ce7-574">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-574">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="51ce7-575">Hiçbiri diğerinden tamamen üst değildir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-575">None of them are strictly superior to another.</span></span> <span data-ttu-id="51ce7-576">En iyi yaklaşım, uygulamanın kısıtlamalarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-576">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="51ce7-577">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="51ce7-577">Basic usage</span></span>

<span data-ttu-id="51ce7-578">, `IHttpClientFactory` `AddHttpClient` Yöntemi içindeki içindeki genişletme yöntemi çağırarak kaydedilebilir `IServiceCollection` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-578">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="51ce7-579">Kaydedildikten sonra kod, `IHttpClientFactory` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile her yerden bir hizmeti kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-579">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="51ce7-580">`IHttpClientFactory`Bir örnek oluşturmak için kullanılabilir `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-580">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="51ce7-581">`IHttpClientFactory`Bu biçimde kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-581">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="51ce7-582">Kullanım şekli üzerinde hiçbir etkisi yoktur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-582">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="51ce7-583">`HttpClient`Örneklerin Şu anda oluşturulduğu yerlerde, bu tekrarlamaları ' a çağrı ile değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-583">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="51ce7-584">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-584">Named clients</span></span>

<span data-ttu-id="51ce7-585">Bir uygulama `HttpClient` , her biri farklı bir yapılandırmaya sahip birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemciler** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-585">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="51ce7-586">Adlandırılmış için yapılandırma `HttpClient` , içinde kayıt sırasında belirtilebilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-586">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="51ce7-587">Yukarıdaki kodda, `AddHttpClient` *GitHub* adının sağlanması denir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-587">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="51ce7-588">Bu istemci, &mdash; GITHUB API 'siyle birlikte çalışmak için gerekli olan temel adres ve iki üst bilgi olan bazı varsayılan yapılandırma uygulanmış.</span><span class="sxs-lookup"><span data-stu-id="51ce7-588">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="51ce7-589">Her seferinde her `CreateClient` çağrıldığında yeni bir örneği `HttpClient` oluşturulur ve yapılandırma eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-589">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="51ce7-590">Adlandırılmış bir istemciyi kullanmak için, bir dize parametresi iletilebilir `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-590">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="51ce7-591">Oluşturulacak istemcinin adını belirtin:</span><span class="sxs-lookup"><span data-stu-id="51ce7-591">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="51ce7-592">Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="51ce7-592">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="51ce7-593">İstemci için yapılandırılan taban adresi kullanıldığından, bu yalnızca yolu geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-593">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="51ce7-594">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-594">Typed clients</span></span>

<span data-ttu-id="51ce7-595">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="51ce7-595">Typed clients:</span></span>

* <span data-ttu-id="51ce7-596">Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-596">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="51ce7-597">İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-597">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="51ce7-598">Yapılandırmak ve belirli bir ile etkileşimde bulunmak için tek bir konum belirtin `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-598">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="51ce7-599">Örneğin, tek bir arka uç uç noktası için tek bir adet yazılmış istemci kullanılabilir ve bu uç nokta ile ilgili tüm mantığı kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-599">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="51ce7-600">DI ile birlikte çalışın ve uygulamanızda gerektiğinde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-600">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="51ce7-601">Türü belirtilmiş istemci `HttpClient` , oluşturucusunda bir parametreyi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="51ce7-601">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="51ce7-602">Önceki kodda, yapılandırma yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-602">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="51ce7-603">`HttpClient`Nesne bir ortak özellik olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-603">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="51ce7-604">İşlevselliği kullanıma sunan API 'ye özel yöntemler tanımlamak mümkündür `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-604">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="51ce7-605">`GetAspNetDocsIssues`Yöntemi, GitHub deposundan en son açık sorunları sorgulamak ve ayrıştırmak için gereken kodu saklar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-605">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="51ce7-606">Türü belirtilmiş bir istemciyi kaydettirmek için, genel <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genişletme yöntemi içinde kullanılabilir `Startup.ConfigureServices` istemci sınıfını belirterek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-606">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="51ce7-607">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-607">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="51ce7-608">Yazılan istemci doğrudan eklenebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-608">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="51ce7-609">Tercih edilirse, yazılan istemcinin yapılandırması, `Startup.ConfigureServices` türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-609">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="51ce7-610">`HttpClient`Türü belirtilmiş bir istemci içinde tamamen kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-610">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="51ce7-611">Bunu bir özellik olarak göstermek yerine, örneği dahili olarak çağıran ortak Yöntemler sunulabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-611">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="51ce7-612">Önceki kodda, `HttpClient` bir özel alan olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-612">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="51ce7-613">Dış çağrıları yapmak için tüm erişim `GetRepos` yönteminden geçer.</span><span class="sxs-lookup"><span data-stu-id="51ce7-613">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="51ce7-614">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="51ce7-614">Generated clients</span></span>

<span data-ttu-id="51ce7-615">`IHttpClientFactory` , [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıklarıyla birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-615">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="51ce7-616">Yeniden sığdırma, .NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-616">Refit is a REST library for .NET.</span></span> <span data-ttu-id="51ce7-617">REST API 'Leri canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="51ce7-617">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="51ce7-618">Arabirim bir uygulama, `RestService` `HttpClient` dış http çağrıları yapmak için kullanılarak tarafından dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-618">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="51ce7-619">Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-619">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="51ce7-620">Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-620">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="51ce7-621">Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-621">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="51ce7-622">Giden istek ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="51ce7-622">Outgoing request middleware</span></span>

<span data-ttu-id="51ce7-623">`HttpClient` , giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramı zaten var.</span><span class="sxs-lookup"><span data-stu-id="51ce7-623">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="51ce7-624">, `IHttpClientFactory` Her bir adlandırılmış istemci için uygulanacak işleyicileri tanımlamanızı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-624">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="51ce7-625">Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydını ve zincirlemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-625">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="51ce7-626">Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-626">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="51ce7-627">Bu düzen, ASP.NET Core gelen ara yazılım ardışık düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="51ce7-627">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="51ce7-628">Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-628">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="51ce7-629">Bir işleyici oluşturmak için, öğesinden türeten bir sınıf tanımlayın <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-629">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="51ce7-630">`SendAsync`İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="51ce7-630">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="51ce7-631">Yukarıdaki kod, temel bir işleyiciyi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-631">The preceding code defines a basic handler.</span></span> <span data-ttu-id="51ce7-632">İsteğe bağlı bir başlık olup olmadığını denetler `X-API-KEY` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-632">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="51ce7-633">Üst bilgi eksikse, HTTP çağrısından kaçınabilir ve uygun bir yanıt döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-633">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="51ce7-634">Kayıt sırasında, bir veya daha fazla işleyici bir için yapılandırmasına eklenebilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-634">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="51ce7-635">Bu görev, üzerindeki genişletme yöntemleri aracılığıyla gerçekleştirilir <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-635">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="51ce7-636">Yukarıdaki kodda,, `ValidateHeaderHandler` dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-636">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="51ce7-637">İşleyicinin, bir geçici hizmet olarak dı 'ye kayıtlı olması **gerekir** , hiçbir koşulda kapsamı yoktur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-637">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="51ce7-638">İşleyici kapsamlı bir hizmet olarak kayıtlıysa ve işleyicinin bağımlı olduğu tüm hizmetler atılabilir olur:</span><span class="sxs-lookup"><span data-stu-id="51ce7-638">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="51ce7-639">İşleyici kapsam dışına geçmeden önce işleyicinin Hizmetleri atılamaz.</span><span class="sxs-lookup"><span data-stu-id="51ce7-639">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="51ce7-640">Atılmış işleyici Hizmetleri işleyicinin başarısız olmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-640">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="51ce7-641">Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyici türünü geçirerek çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-641">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="51ce7-642">Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-642">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="51ce7-643">Her işleyici, son isteği çalıştırana kadar sonraki işleyiciyi sarmalar `HttpClientHandler` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-643">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="51ce7-644">İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="51ce7-644">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="51ce7-645">Kullanarak işleyicide veri geçirin `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-645">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="51ce7-646">`IHttpContextAccessor`Geçerli isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-646">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="51ce7-647">`AsyncLocal`Verileri geçirmek için özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="51ce7-647">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="51ce7-648">Polly tabanlı işleyiciler kullanın</span><span class="sxs-lookup"><span data-stu-id="51ce7-648">Use Polly-based handlers</span></span>

<span data-ttu-id="51ce7-649">`IHttpClientFactory` , [Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kitaplığı ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-649">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="51ce7-650">Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-650">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="51ce7-651">Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-651">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="51ce7-652">Uzantı yöntemleri, yapılandırılmış örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-652">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-653">Polly uzantıları:</span><span class="sxs-lookup"><span data-stu-id="51ce7-653">The Polly extensions:</span></span>

* <span data-ttu-id="51ce7-654">İstemcilere Polly tabanlı işleyiciler eklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-654">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="51ce7-655">, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-655">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="51ce7-656">Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-656">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="51ce7-657">Geçici hataları işle</span><span class="sxs-lookup"><span data-stu-id="51ce7-657">Handle transient faults</span></span>

<span data-ttu-id="51ce7-658">Yaygın hatalar, dış HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-658">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="51ce7-659">`AddTransientHttpErrorPolicy`Geçici hataları işlemek üzere bir ilkenin tanımlanmasını sağlayan, çağrılan bir uygun genişletme yöntemi eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-659">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="51ce7-660">Bu uzantı yöntemi tanıtıcısıyla yapılandırılmış ilkeler `HttpRequestException` , http 5xx yanıtları ve http 408 yanıtları.</span><span class="sxs-lookup"><span data-stu-id="51ce7-660">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="51ce7-661">`AddTransientHttpErrorPolicy`Uzantı içinde kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-661">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="51ce7-662">Uzantı, `PolicyBuilder` olası bir geçici hatayı temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="51ce7-662">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="51ce7-663">Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-663">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="51ce7-664">Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-664">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="51ce7-665">Dinamik olarak ilke seçme</span><span class="sxs-lookup"><span data-stu-id="51ce7-665">Dynamically select policies</span></span>

<span data-ttu-id="51ce7-666">Polly tabanlı işleyiciler eklemek için kullanılabilecek ek uzantı yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-666">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="51ce7-667">Bu tür bir uzantının `AddPolicyHandler` birden çok aşırı yüklemesi vardır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-667">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="51ce7-668">Bir aşırı yükleme, hangi ilkenin uygulanacağını tanımlarken isteğin incelenebilirliğini sağlar:</span><span class="sxs-lookup"><span data-stu-id="51ce7-668">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="51ce7-669">Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-669">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="51ce7-670">Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-670">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="51ce7-671">Birden çok Polly işleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="51ce7-671">Add multiple Polly handlers</span></span>

<span data-ttu-id="51ce7-672">Gelişmiş işlevsellik sağlamak için çok fazla ilke iç içe geçmiş bir yaygın hale gelir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-672">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="51ce7-673">Yukarıdaki örnekte, iki işleyici eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-673">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="51ce7-674">İlki, `AddTransientHttpErrorPolicy` yeniden deneme ilkesi eklemek için uzantıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-674">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="51ce7-675">Başarısız istekler en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-675">Failed requests are retried up to three times.</span></span> <span data-ttu-id="51ce7-676">İçin ikinci çağrı, `AddTransientHttpErrorPolicy` bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-676">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="51ce7-677">Beş başarısız girişim sırayla gerçekleşiyorsa, daha fazla dış istek 30 saniye için engellenir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-677">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="51ce7-678">Devre kesici ilkeleri durum bilgisi vardır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-678">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="51ce7-679">Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-679">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="51ce7-680">Polly kayıt defterinden ilke ekleme</span><span class="sxs-lookup"><span data-stu-id="51ce7-680">Add policies from the Polly registry</span></span>

<span data-ttu-id="51ce7-681">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir ile kaydetmektir `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-681">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="51ce7-682">Kayıt defterinden bir ilke kullanılarak bir işleyicinin eklenmesine izin veren bir genişletme yöntemi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-682">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="51ce7-683">Yukarıdaki kodda, öğesine eklendiğinde iki ilke kaydedilir `PolicyRegistry` `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-683">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="51ce7-684">Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır ve uygulanacak ilke adı geçer.</span><span class="sxs-lookup"><span data-stu-id="51ce7-684">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="51ce7-685">Ve daha fazla tümleştirme hakkında daha fazla bilgi `IHttpClientFactory` , [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)' de bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-685">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="51ce7-686">HttpClient ve ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="51ce7-686">HttpClient and lifetime management</span></span>

<span data-ttu-id="51ce7-687">`HttpClient`Her çağrıldığında yeni bir örnek döndürülür `CreateClient` `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-687">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="51ce7-688"><xref:System.Net.Http.HttpMessageHandler>Adlandırılmış istemci başına.</span><span class="sxs-lookup"><span data-stu-id="51ce7-688">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="51ce7-689">Fabrika, örneklerin yaşam sürelerini yönetir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-689">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="51ce7-690">`IHttpClientFactory``HttpMessageHandler`kaynak tüketimini azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-690">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="51ce7-691">Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni bir örnek oluştururken havuzdan yeniden kullanılabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-691">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="51ce7-692">Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-692">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="51ce7-693">Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-693">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="51ce7-694">Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine yeniden davranmasını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-694">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="51ce7-695">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-695">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="51ce7-696">Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-696">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="51ce7-697">Bunu geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` istemcisini oluştururken döndürülen öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="51ce7-697">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="51ce7-698">İstemcinin çıkarılması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-698">Disposal of the client isn't required.</span></span> <span data-ttu-id="51ce7-699">Çıkarma giden istekleri iptal eder ve `HttpClient` çağırma sonrasında verilen örneğin kullanılamaz olmasını sağlar <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-699">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="51ce7-700">`IHttpClientFactory` örnekler tarafından kullanılan kaynakları izler ve ortadan kaldırdık `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-700">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-701">`HttpClient`Örnekler genellikle aktiften çıkarma gerektirmeyen .NET nesneleri olarak kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-701">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="51ce7-702">Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde kullanılmadan önce kullanılan ortak bir modeldir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-702">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="51ce7-703">Bu model, ' a geçtikten sonra gereksiz hale gelir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-703">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="51ce7-704">Ihttpclientfactory alternatifleri</span><span class="sxs-lookup"><span data-stu-id="51ce7-704">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="51ce7-705">`IHttpClientFactory`Dı etkin bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="51ce7-705">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="51ce7-706">Havuz örneklerine göre kaynak tükenmesi sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-706">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="51ce7-707">Düzenli aralıklarla örnekleri geçirerek eski DNS sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-707">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="51ce7-708">Uzun süreli bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="51ce7-708">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="51ce7-709">`SocketsHttpHandler`Uygulamanın başladığı zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-709">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="51ce7-710"><xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>DNS yenileme süreleri temelinde uygun bir değere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="51ce7-710">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="51ce7-711">`HttpClient`Gerektiğinde örnek oluşturun `new HttpClient(handler, disposeHandler: false)` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-711">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="51ce7-712">Yukarıdaki yaklaşımlar, `IHttpClientFactory` benzer bir şekilde çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="51ce7-712">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="51ce7-713">, `SocketsHttpHandler` Örnekleri arasında bağlantıları paylaşır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-713">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="51ce7-714">Bu paylaşım, yuva azalmasına engel olur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-714">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="51ce7-715">`SocketsHttpHandler`Bağlantıları, `PooledConnectionLifetime` eski DNS sorunlarından kaçınmak için öğesine göre döngüler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-715">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="51ce7-716">Cookiemalar</span><span class="sxs-lookup"><span data-stu-id="51ce7-716">Cookies</span></span>

<span data-ttu-id="51ce7-717">Havuza alınmış `HttpMessageHandler` örnekler, `CookieContainer` paylaşılan nesneler ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-717">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="51ce7-718">Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-718">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="51ce7-719">S gerektiren uygulamalar için cookie şunlardan birini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="51ce7-719">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="51ce7-720">Otomatik işlemeyi devre dışı bırakma cookie</span><span class="sxs-lookup"><span data-stu-id="51ce7-720">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="51ce7-721">Önlemenin `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="51ce7-721">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="51ce7-722"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Otomatik işlemeyi devre dışı bırakmak için çağırın cookie :</span><span class="sxs-lookup"><span data-stu-id="51ce7-722">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="51ce7-723">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="51ce7-723">Logging</span></span>

<span data-ttu-id="51ce7-724">`IHttpClientFactory`Tüm istekler için kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-724">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="51ce7-725">Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="51ce7-725">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="51ce7-726">İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-726">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="51ce7-727">Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-727">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="51ce7-728">Örneğin, *Mynamedclient* adlı bir istemci, bir kategorisine sahip iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-728">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="51ce7-729">*Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="51ce7-729">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="51ce7-730">İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-730">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="51ce7-731">Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-731">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="51ce7-732">Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-732">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="51ce7-733">*Mynamedclient* örneğinde, bu iletiler günlük kategorisine göre günlüğe kaydedilir `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-733">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="51ce7-734">İstek için bu, tüm diğer işleyiciler çalıştıktan sonra ve istek ağda gönderilmeden hemen önce gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-734">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="51ce7-735">Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-735">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="51ce7-736">İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-736">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="51ce7-737">Bu, örneğin veya yanıt durum kodunda istek başlıklarındaki değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-737">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="51ce7-738">İstemci adı ' nı log kategorisinde da içermek, gerektiğinde belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-738">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="51ce7-739">HttpMessageHandler 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="51ce7-739">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="51ce7-740">İstemci tarafından kullanılan iç yapılandırmayı denetlemek gerekli olabilir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-740">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="51ce7-741">`IHttpClientBuilder`Adlandırılmış veya yazılan istemciler eklenirken bir döndürülür.</span><span class="sxs-lookup"><span data-stu-id="51ce7-741">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="51ce7-742"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-742">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="51ce7-743">Temsilci, `HttpMessageHandler` Bu istemci tarafından kullanılan birincili oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="51ce7-743">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="51ce7-744">Konsol uygulamasında ıhttpclientfactory kullanma</span><span class="sxs-lookup"><span data-stu-id="51ce7-744">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="51ce7-745">Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="51ce7-745">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="51ce7-746">Microsoft. Extensions. Hosting</span><span class="sxs-lookup"><span data-stu-id="51ce7-746">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="51ce7-747">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="51ce7-747">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="51ce7-748">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="51ce7-748">In the following example:</span></span>

* <span data-ttu-id="51ce7-749"><xref:System.Net.Http.IHttpClientFactory> , [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="51ce7-749"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="51ce7-750">`MyService` hizmetinden bir istemci fabrikası örneği oluşturur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="51ce7-750">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="51ce7-751">`HttpClient` , bir Web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51ce7-751">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="51ce7-752">`Main` hizmetin yöntemini yürütmek için bir kapsam oluşturur `GetPage` ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-752">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="51ce7-753">Üst bilgi yayma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="51ce7-753">Header propagation middleware</span></span>

<span data-ttu-id="51ce7-754">Üst bilgi yayma, gelen istekten giden HTTP Istemci isteklerine HTTP üstbilgilerini yaymak için bir topluluk tarafından desteklenen bir ara yazılımlar.</span><span class="sxs-lookup"><span data-stu-id="51ce7-754">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="51ce7-755">Üst bilgi yaymayı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="51ce7-755">To use header propagation:</span></span>

* <span data-ttu-id="51ce7-756">Topluluğun, paket [Headeryayılmasının](https://www.nuget.org/packages/HeaderPropagation)desteklediği bağlantı noktasına başvurun.</span><span class="sxs-lookup"><span data-stu-id="51ce7-756">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="51ce7-757">ASP.NET Core 3,1 ve üzeri, [Microsoft. AspNetCore. Headeryayılmayı](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)destekler.</span><span class="sxs-lookup"><span data-stu-id="51ce7-757">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="51ce7-758">Ara yazılımı ve `HttpClient` içinde yapılandırın `Startup` :</span><span class="sxs-lookup"><span data-stu-id="51ce7-758">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="51ce7-759">İstemci giden isteklerde yapılandırılan üst bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="51ce7-759">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="51ce7-760">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="51ce7-760">Additional resources</span></span>

* [<span data-ttu-id="51ce7-761">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="51ce7-761">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="51ce7-762">HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın</span><span class="sxs-lookup"><span data-stu-id="51ce7-762">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="51ce7-763">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="51ce7-763">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
