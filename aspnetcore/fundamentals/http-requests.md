---
title: ASP.NET Core 'de ıhttpclientfactory kullanarak HTTP istekleri yapın
author: stevejgordon
description: ASP.NET Core içindeki mantıksal HttpClient örneklerini yönetmek için ıhttpclientfactory arabirimini kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/http-requests
ms.openlocfilehash: 34c35daac3da845bac9156fe96078df7902a4cd0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059500"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="5885b-103">ASP.NET Core 'de ıhttpclientfactory kullanarak HTTP istekleri yapın</span><span class="sxs-lookup"><span data-stu-id="5885b-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5885b-104">[Glenn CONDRON](https://github.com/glennc), [Ryan şimdi ak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Kirk larkabağı](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="5885b-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="5885b-105">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak için kayıt yapılabilir ve kullanılabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="5885b-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="5885b-106">`IHttpClientFactory` aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="5885b-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="5885b-107">, Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="5885b-108">Örneğin,  *GitHub* adlı bir Istemci, [GitHub](https://github.com/)'a erişmek için kaydedilebilir ve yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="5885b-109">Varsayılan istemci, genel erişim için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="5885b-110">' De işleyiciler temsilci seçme yoluyla giden ara yazılım kavramını daha da artırır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="5885b-111">' De işleyiciler temsilci atama avantajlarından faydalanmak için, Polya tabanlı bir ara yazılım için uzantılar sağlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="5885b-112">Temel örneklerin biriktirmesini ve ömrünü yönetir `HttpClientMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="5885b-113">Otomatik yönetim, yaşam sürelerini el ile yönetirken oluşan ortak DNS (etki alanı adı sistemi) sorunlarını önler `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="5885b-114">`ILogger`Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).</span><span class="sxs-lookup"><span data-stu-id="5885b-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="5885b-115">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="5885b-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="5885b-116">Bu konu sürümündeki örnek kod, <xref:System.Text.Json> http yanıtlarında döndürülen JSON içeriğinin serisini kaldırmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="5885b-117">Ve kullanan örnekler için `Json.NET` `ReadAsAsync<T>` , bu konunun 2. x sürümünü seçmek üzere sürüm seçiciyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="5885b-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="5885b-118">Tüketim desenleri</span><span class="sxs-lookup"><span data-stu-id="5885b-118">Consumption patterns</span></span>

<span data-ttu-id="5885b-119">Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="5885b-120">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="5885b-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="5885b-121">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="5885b-122">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="5885b-123">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="5885b-124">En iyi yaklaşım, uygulamanın gereksinimlerine bağlı olarak değişir.</span><span class="sxs-lookup"><span data-stu-id="5885b-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="5885b-125">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="5885b-125">Basic usage</span></span>

<span data-ttu-id="5885b-126">`IHttpClientFactory` , çağırarak kaydedilebilir `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="5885b-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="5885b-127">`IHttpClientFactory` [Bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)kullanılarak bir istek yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5885b-128">Aşağıdaki kod `IHttpClientFactory` bir örnek oluşturmak için kullanır `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="5885b-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="5885b-129">`IHttpClientFactory`Yukarıdaki örnekte olduğu gibi kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="5885b-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="5885b-130">Kullanım hakkında hiçbir etkisi yoktur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="5885b-131">`HttpClient`Var olan bir uygulamada örneklerin oluşturulduğu yerlerde, bu oluşumların ' i çağrılarıyla değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="5885b-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="5885b-132">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-132">Named clients</span></span>

<span data-ttu-id="5885b-133">Adlandırılmış istemciler şu durumlarda iyi bir seçimdir:</span><span class="sxs-lookup"><span data-stu-id="5885b-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="5885b-134">Uygulama birçok farklı kullanımı gerektirir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="5885b-135">Birçok `HttpClient` s farklı yapılandırmaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5885b-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="5885b-136">Adlandırılmış için yapılandırma `HttpClient` , içinde kayıt sırasında belirtilebilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5885b-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="5885b-137">İstemcinin yapılandırıldığı önceki kodda:</span><span class="sxs-lookup"><span data-stu-id="5885b-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="5885b-138">Temel adres `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="5885b-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="5885b-139">GitHub API 'SI ile çalışmak için iki üst bilgi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="5885b-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="5885b-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="5885b-140">CreateClient</span></span>

<span data-ttu-id="5885b-141">Her zaman <xref:System.Net.Http.IHttpClientFactory.CreateClient*> çağrılır:</span><span class="sxs-lookup"><span data-stu-id="5885b-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="5885b-142">Yeni bir örneği `HttpClient` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5885b-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="5885b-143">Yapılandırma eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-143">The configuration action is called.</span></span>

<span data-ttu-id="5885b-144">Adlandırılmış bir istemci oluşturmak için adını içine geçirin `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="5885b-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="5885b-145">Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5885b-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="5885b-146">İstemci için yapılandırılan taban adresi kullanıldığından, kod yalnızca yolu geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="5885b-147">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-147">Typed clients</span></span>

<span data-ttu-id="5885b-148">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="5885b-148">Typed clients:</span></span>

* <span data-ttu-id="5885b-149">Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="5885b-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="5885b-150">İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="5885b-151">Yapılandırmak ve belirli bir ile etkileşimde bulunmak için tek bir konum belirtin `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="5885b-152">Örneğin, tek bir türü belirtilmiş istemci kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="5885b-153">Tek bir arka uç uç noktası için.</span><span class="sxs-lookup"><span data-stu-id="5885b-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="5885b-154">Uç nokta ile ilgili tüm mantığı kapsüllemek için.</span><span class="sxs-lookup"><span data-stu-id="5885b-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="5885b-155">DI ile birlikte çalışın ve uygulamada gerektiğinde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="5885b-156">Türü belirtilmiş istemci `HttpClient` , oluşturucusunda bir parametreyi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="5885b-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="5885b-157">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="5885b-157">In the preceding code:</span></span>

* <span data-ttu-id="5885b-158">Yapılandırma, yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="5885b-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="5885b-159">`HttpClient`Nesne bir ortak özellik olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="5885b-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="5885b-160">İşlevselliği kullanıma sunan, API 'ye özgü Yöntemler oluşturulabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="5885b-161">Örneğin, `GetAspNetDocsIssues` yöntemi açık sorunları almak için kodu kapsüller.</span><span class="sxs-lookup"><span data-stu-id="5885b-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="5885b-162">Aşağıdaki kod, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> `Startup.ConfigureServices` türü belirtilmiş bir istemci sınıfını kaydetmek için ' de çağırır:</span><span class="sxs-lookup"><span data-stu-id="5885b-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="5885b-163">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="5885b-164">Yukarıdaki kodda `AddHttpClient` `GitHubService` geçici bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="5885b-165">Bu kayıt, için bir fabrika yöntemi kullanır:</span><span class="sxs-lookup"><span data-stu-id="5885b-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="5885b-166">`HttpClient` örneği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5885b-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="5885b-167">Örneğini oluşturucusuna geçirerek bir örneğini oluşturun `GitHubService` `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="5885b-168">Yazılan istemci doğrudan eklenebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="5885b-169">Türü belirtilmiş bir istemcinin yapılandırması `Startup.ConfigureServices` , türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="5885b-170">, `HttpClient` Türü belirlenmiş bir istemci içinde kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="5885b-171">Bunu bir özellik olarak göstermek yerine, örneği dahili olarak çağıran bir yöntem tanımlayın `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="5885b-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="5885b-172">Yukarıdaki kodda, `HttpClient` bir özel alanda depolanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="5885b-173">Öğesine erişimi, `HttpClient` genel yöntemi tarafından yapılır `GetRepos` .</span><span class="sxs-lookup"><span data-stu-id="5885b-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="5885b-174">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-174">Generated clients</span></span>

<span data-ttu-id="5885b-175">`IHttpClientFactory` , [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi üçüncü taraf kitaplıklarla birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="5885b-176">Yeniden sığdırma, .NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="5885b-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="5885b-177">REST API 'Leri canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="5885b-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="5885b-178">Arabirim bir uygulama, `RestService` `HttpClient` dış http çağrıları yapmak için kullanılarak tarafından dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5885b-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="5885b-179">Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="5885b-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="5885b-180">Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="5885b-181">Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="5885b-182">GÖNDERI, PUT ve DELETE isteklerini oluşturma</span><span class="sxs-lookup"><span data-stu-id="5885b-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="5885b-183">Yukarıdaki örneklerde, tüm HTTP istekleri GET HTTP fiilini kullanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="5885b-184">`HttpClient` , aşağıdakiler de dahil olmak üzere diğer HTTP fiillerini de destekler:</span><span class="sxs-lookup"><span data-stu-id="5885b-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="5885b-185">POST</span><span class="sxs-lookup"><span data-stu-id="5885b-185">POST</span></span>
* <span data-ttu-id="5885b-186">PUT</span><span class="sxs-lookup"><span data-stu-id="5885b-186">PUT</span></span>
* <span data-ttu-id="5885b-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="5885b-187">DELETE</span></span>
* <span data-ttu-id="5885b-188">DÜZELTMESI</span><span class="sxs-lookup"><span data-stu-id="5885b-188">PATCH</span></span>

<span data-ttu-id="5885b-189">Desteklenen HTTP fiillerinin tüm listesi için bkz <xref:System.Net.Http.HttpMethod> ..</span><span class="sxs-lookup"><span data-stu-id="5885b-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="5885b-190">Aşağıdaki örnek, bir HTTP POST isteğinin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="5885b-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="5885b-191">Yukarıdaki kodda, `CreateItemAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="5885b-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="5885b-192">`TodoItem`Parametresini kullanarak JSON için parametreyi seri hale getirir `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="5885b-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="5885b-193">Bu <xref:System.Text.Json.JsonSerializerOptions> , serileştirme işlemini yapılandırmak için bir örneğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="5885b-194"><xref:System.Net.Http.StringContent>Http isteğinin gövdesinde göndermek üzere seri hale GETIRILMIŞ JSON paketini paketlemek için bir örneği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5885b-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="5885b-195"><xref:System.Net.Http.HttpClient.PostAsync%2A>JSON içeriğini BELIRTILEN URL 'ye göndermek için çağrılar.</span><span class="sxs-lookup"><span data-stu-id="5885b-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="5885b-196">Bu, [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress)'e eklenen GÖRELI bir URL 'dir.</span><span class="sxs-lookup"><span data-stu-id="5885b-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="5885b-197"><xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A>Yanıt durum kodu başarıyı belirtmezse bir özel durum oluşturmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="5885b-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="5885b-198">`HttpClient` , diğer içerik türlerini de destekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="5885b-199">Örneğin, <xref:System.Net.Http.MultipartContent> ve <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="5885b-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="5885b-200">Desteklenen içeriğin tamamı listesi için bkz <xref:System.Net.Http.HttpContent> ..</span><span class="sxs-lookup"><span data-stu-id="5885b-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="5885b-201">Aşağıdaki örnekte bir HTTP PUT isteği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="5885b-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="5885b-202">Yukarıdaki kod, POST örneğine çok benzer.</span><span class="sxs-lookup"><span data-stu-id="5885b-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="5885b-203">`SaveItemAsync`Yöntemi yerine çağırır <xref:System.Net.Http.HttpClient.PutAsync%2A> `PostAsync` .</span><span class="sxs-lookup"><span data-stu-id="5885b-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="5885b-204">Aşağıdaki örnekte bir HTTP SILME isteği gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="5885b-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="5885b-205">Yukarıdaki kodda, `DeleteItemAsync` yöntemi çağırır <xref:System.Net.Http.HttpClient.DeleteAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5885b-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="5885b-206">HTTP DELETE istekleri genellikle gövde içermediğinden, `DeleteAsync` yöntemi bir örneğini kabul eden bir aşırı yükleme sağlamaz `HttpContent` .</span><span class="sxs-lookup"><span data-stu-id="5885b-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="5885b-207">İle farklı HTTP fiillerini kullanma hakkında daha fazla bilgi için `HttpClient` bkz <xref:System.Net.Http.HttpClient> ..</span><span class="sxs-lookup"><span data-stu-id="5885b-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="5885b-208">Giden istek ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="5885b-208">Outgoing request middleware</span></span>

<span data-ttu-id="5885b-209">`HttpClient` , giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramıdır.</span><span class="sxs-lookup"><span data-stu-id="5885b-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="5885b-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="5885b-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="5885b-211">Her bir adlandırılmış istemci için uygulanacak işleyiciler tanımlamayı basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="5885b-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="5885b-212">Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydedilmesini ve zincirleme kullanımını destekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="5885b-213">Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="5885b-214">Bu model:</span><span class="sxs-lookup"><span data-stu-id="5885b-214">This pattern:</span></span>

  * <span data-ttu-id="5885b-215">ASP.NET Core gelen ara yazılım ardışık düzenine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="5885b-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="5885b-216">, HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar, örneğin:</span><span class="sxs-lookup"><span data-stu-id="5885b-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="5885b-217">önbelleği</span><span class="sxs-lookup"><span data-stu-id="5885b-217">caching</span></span>
    * <span data-ttu-id="5885b-218">hata işleme</span><span class="sxs-lookup"><span data-stu-id="5885b-218">error handling</span></span>
    * <span data-ttu-id="5885b-219">getir</span><span class="sxs-lookup"><span data-stu-id="5885b-219">serialization</span></span>
    * <span data-ttu-id="5885b-220">günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="5885b-220">logging</span></span>

<span data-ttu-id="5885b-221">Temsilci seçme işleyicisi oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="5885b-221">To create a delegating handler:</span></span>

* <span data-ttu-id="5885b-222">Türet <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="5885b-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="5885b-223">Geçersiz kıl <xref:System.Net.Http.DelegatingHandler.SendAsync*> .</span><span class="sxs-lookup"><span data-stu-id="5885b-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="5885b-224">İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütün:</span><span class="sxs-lookup"><span data-stu-id="5885b-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="5885b-225">Yukarıdaki kod, üstbilginin istekte olup olmadığını denetler `X-API-KEY` .</span><span class="sxs-lookup"><span data-stu-id="5885b-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="5885b-226">`X-API-KEY`Eksikse, <xref:System.Net.HttpStatusCode.BadRequest> döndürülür.</span><span class="sxs-lookup"><span data-stu-id="5885b-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="5885b-227">İçin yapılandırmasına birden fazla işleyici eklenebilir `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="5885b-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="5885b-228">Yukarıdaki kodda,, `ValidateHeaderHandler` dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="5885b-229">`IHttpClientFactory`Her işleyici için ayrı bır dı kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5885b-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="5885b-230">İşleyiciler herhangi bir kapsamın hizmetlerine bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="5885b-231">İşleyicilerin bağımlı olduğu hizmetler, işleyicinin elden çıkarılmasıyla kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="5885b-232">Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyicinin türü geçirerek, çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="5885b-233">Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="5885b-234">Her işleyici, son isteği çalıştırana kadar sonraki işleyiciyi sarmalar `HttpClientHandler` :</span><span class="sxs-lookup"><span data-stu-id="5885b-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="5885b-235">İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="5885b-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="5885b-236">[HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties)kullanarak işleyicide veri geçirin.</span><span class="sxs-lookup"><span data-stu-id="5885b-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="5885b-237"><xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>Geçerli isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="5885b-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="5885b-238"><xref:System.Threading.AsyncLocal`1>Verileri geçirmek için özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5885b-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="5885b-239">Polly tabanlı işleyiciler kullanın</span><span class="sxs-lookup"><span data-stu-id="5885b-239">Use Polly-based handlers</span></span>

<span data-ttu-id="5885b-240">`IHttpClientFactory` üçüncü taraf kitaplığı [Polly](https://github.com/App-vNext/Polly)ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="5885b-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="5885b-241">Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="5885b-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="5885b-242">Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="5885b-243">Uzantı yöntemleri, yapılandırılmış örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="5885b-244">Polly uzantıları, istemcilere Polly tabanlı işleyiciler eklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="5885b-245">Polly, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="5885b-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="5885b-246">Geçici hataları işle</span><span class="sxs-lookup"><span data-stu-id="5885b-246">Handle transient faults</span></span>

<span data-ttu-id="5885b-247">Hatalar genellikle dış HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="5885b-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="5885b-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> geçici hataları işlemek için bir ilkenin tanımlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="5885b-249">`AddTransientHttpErrorPolicy`Aşağıdaki yanıtları işleyecek şekilde yapılandırılan ilkeler:</span><span class="sxs-lookup"><span data-stu-id="5885b-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="5885b-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="5885b-250">HTTP 5xx</span></span>
* <span data-ttu-id="5885b-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="5885b-251">HTTP 408</span></span>

<span data-ttu-id="5885b-252">`AddTransientHttpErrorPolicy``PolicyBuilder`olası bir geçici hatayı temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="5885b-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="5885b-253">Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5885b-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="5885b-254">Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="5885b-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="5885b-255">Dinamik olarak ilke seçme</span><span class="sxs-lookup"><span data-stu-id="5885b-255">Dynamically select policies</span></span>

<span data-ttu-id="5885b-256">Uzantı yöntemleri, örneğin, Polly tabanlı işleyiciler eklemek için sağlanır <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="5885b-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="5885b-257">Aşağıdaki `AddPolicyHandler` aşırı yükleme, hangi ilkenin uygulanacağını belirlemek için isteği inceler:</span><span class="sxs-lookup"><span data-stu-id="5885b-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="5885b-258">Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="5885b-259">Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="5885b-260">Birden çok Polly işleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="5885b-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="5885b-261">Polly ilkeleri iç içe almak yaygın bir şekilde yapılır:</span><span class="sxs-lookup"><span data-stu-id="5885b-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="5885b-262">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="5885b-262">In the preceding example:</span></span>

* <span data-ttu-id="5885b-263">İki işleyici eklenir.</span><span class="sxs-lookup"><span data-stu-id="5885b-263">Two handlers are added.</span></span>
* <span data-ttu-id="5885b-264">İlk işleyici, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> yeniden deneme ilkesi eklemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="5885b-265">Başarısız istekler en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="5885b-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="5885b-266">İkinci `AddTransientHttpErrorPolicy` çağrı bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="5885b-267">5 başarısız girişim sıralı olarak gerçekleşirse, daha fazla dış istek 30 saniye boyunca engellenir.</span><span class="sxs-lookup"><span data-stu-id="5885b-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="5885b-268">Devre kesici ilkeleri durum bilgisi vardır.</span><span class="sxs-lookup"><span data-stu-id="5885b-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="5885b-269">Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="5885b-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="5885b-270">Polly kayıt defterinden ilke ekleme</span><span class="sxs-lookup"><span data-stu-id="5885b-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="5885b-271">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir ile kaydetmektir `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="5885b-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="5885b-272">Aşağıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="5885b-272">In the following code:</span></span>

* <span data-ttu-id="5885b-273">"Normal" ve "uzun" ilkeler eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="5885b-273">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="5885b-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> kayıt defterinden "normal" ve "uzun" ilkeleri ekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="5885b-275">Ve daha fazla tümleştirme hakkında daha fazla bilgi için `IHttpClientFactory` bkz. [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="5885b-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="5885b-276">HttpClient ve ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="5885b-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="5885b-277">`HttpClient`Her çağrıldığında yeni bir örnek döndürülür `CreateClient` `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="5885b-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="5885b-278"><xref:System.Net.Http.HttpMessageHandler>Adlandırılmış istemci başına oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5885b-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="5885b-279">Fabrika, örneklerin yaşam sürelerini yönetir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="5885b-280">`IHttpClientFactory``HttpMessageHandler`kaynak tüketimini azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="5885b-281">Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni bir örnek oluştururken havuzdan yeniden kullanılabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="5885b-282">Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="5885b-283">Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="5885b-284">Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS (etki alanı adı sistemi) değişikliklerine yeniden davranmasını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="5885b-285">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="5885b-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="5885b-286">Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="5885b-287">`HttpClient` örnekler genellikle aktiften **çıkarma gerektirmeyen .NET nesneleri olarak** kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="5885b-288">Çıkarma giden istekleri iptal eder ve `HttpClient` çağırma sonrasında verilen örneğin kullanılamaz olmasını sağlar <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="5885b-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="5885b-289">`IHttpClientFactory` örnekler tarafından kullanılan kaynakları izler ve ortadan kaldırdık `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="5885b-290">Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde kullanılmadan önce kullanılan ortak bir modeldir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="5885b-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="5885b-291">Bu model, ' a geçtikten sonra gereksiz hale gelir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="5885b-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="5885b-292">Ihttpclientfactory alternatifleri</span><span class="sxs-lookup"><span data-stu-id="5885b-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="5885b-293">`IHttpClientFactory`Dı etkin bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="5885b-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="5885b-294">Havuz örneklerine göre kaynak tükenmesi sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="5885b-295">Düzenli aralıklarla örnekleri geçirerek eski DNS sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="5885b-296">Uzun süreli bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="5885b-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="5885b-297">`SocketsHttpHandler`Uygulamanın başladığı zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="5885b-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="5885b-298"><xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>DNS yenileme süreleri temelinde uygun bir değere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5885b-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="5885b-299">`HttpClient`Gerektiğinde örnek oluşturun `new HttpClient(handler, disposeHandler: false)` .</span><span class="sxs-lookup"><span data-stu-id="5885b-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="5885b-300">Yukarıdaki yaklaşımlar, `IHttpClientFactory` benzer bir şekilde çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="5885b-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="5885b-301">, `SocketsHttpHandler` Örnekleri arasında bağlantıları paylaşır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="5885b-302">Bu paylaşım, yuva azalmasına engel olur.</span><span class="sxs-lookup"><span data-stu-id="5885b-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="5885b-303">`SocketsHttpHandler`Bağlantıları, `PooledConnectionLifetime` eski DNS sorunlarından kaçınmak için öğesine göre döngüler.</span><span class="sxs-lookup"><span data-stu-id="5885b-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="5885b-304">:::no-loc(Cookie):::malar</span><span class="sxs-lookup"><span data-stu-id="5885b-304">:::no-loc(Cookie):::s</span></span>

<span data-ttu-id="5885b-305">Havuza alınmış `HttpMessageHandler` örnekler, `:::no-loc(Cookie):::Container` paylaşılan nesneler ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-305">The pooled `HttpMessageHandler` instances results in `:::no-loc(Cookie):::Container` objects being shared.</span></span> <span data-ttu-id="5885b-306">Beklenmeyen `:::no-loc(Cookie):::Container` nesne paylaşımı genellikle hatalı kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-306">Unanticipated `:::no-loc(Cookie):::Container` object sharing often results in incorrect code.</span></span> <span data-ttu-id="5885b-307">S gerektiren uygulamalar için :::no-loc(cookie)::: şunlardan birini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="5885b-307">For apps that require :::no-loc(cookie):::s, consider either:</span></span>

 - <span data-ttu-id="5885b-308">Otomatik işlemeyi devre dışı bırakma :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="5885b-308">Disabling automatic :::no-loc(cookie)::: handling</span></span>
 - <span data-ttu-id="5885b-309">Önlemenin `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="5885b-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="5885b-310"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Otomatik işlemeyi devre dışı bırakmak için çağırın :::no-loc(cookie)::: :</span><span class="sxs-lookup"><span data-stu-id="5885b-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic :::no-loc(cookie)::: handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="5885b-311">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="5885b-311">Logging</span></span>

<span data-ttu-id="5885b-312">`IHttpClientFactory`Tüm istekler için kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="5885b-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="5885b-313">Varsayılan günlük iletilerini görmek için günlük yapılandırmasında uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="5885b-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="5885b-314">İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="5885b-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="5885b-315">Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="5885b-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="5885b-316">Örneğin, *Mynamedclient* adlı bir istemci, "System .net. http. HttpClient" kategorisine sahip iletileri günlüğe kaydeder. **Mynamedclient** . LogicalHandler ".</span><span class="sxs-lookup"><span data-stu-id="5885b-316">A client named *MyNamedClient* , for example, logs messages with a category of "System.Net.Http.HttpClient. **MyNamedClient** .LogicalHandler".</span></span> <span data-ttu-id="5885b-317">*Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="5885b-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="5885b-318">İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="5885b-319">Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="5885b-320">Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="5885b-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="5885b-321">*Mynamedclient* örneğinde, bu Iletiler "System .net. http. HttpClient" günlük kategorisiyle günlüğe kaydedilir. **Mynamedclient** . ClientHandler ".</span><span class="sxs-lookup"><span data-stu-id="5885b-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient. **MyNamedClient** .ClientHandler".</span></span> <span data-ttu-id="5885b-322">İstek için bu, tüm diğer işleyiciler çalıştırıldıktan sonra ve istek gönderilmeden hemen önce gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="5885b-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="5885b-323">Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="5885b-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="5885b-324">İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="5885b-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="5885b-325">Bu, istek üst bilgilerinde veya yanıt durum kodunda yapılan değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="5885b-326">İstemcinin adını log kategorisinde da içermek, belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.</span><span class="sxs-lookup"><span data-stu-id="5885b-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="5885b-327">HttpMessageHandler 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5885b-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="5885b-328">İstemci tarafından kullanılan iç yapılandırmayı denetlemek gerekli olabilir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="5885b-329">`IHttpClientBuilder`Adlandırılmış veya yazılan istemciler eklenirken bir döndürülür.</span><span class="sxs-lookup"><span data-stu-id="5885b-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="5885b-330"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="5885b-331">Temsilci, `HttpMessageHandler` Bu istemci tarafından kullanılan birincili oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="5885b-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="5885b-332">Konsol uygulamasında ıhttpclientfactory kullanma</span><span class="sxs-lookup"><span data-stu-id="5885b-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="5885b-333">Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5885b-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="5885b-334">Microsoft. Extensions. Hosting</span><span class="sxs-lookup"><span data-stu-id="5885b-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="5885b-335">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="5885b-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="5885b-336">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="5885b-336">In the following example:</span></span>

* <span data-ttu-id="5885b-337"><xref:System.Net.Http.IHttpClientFactory> , [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="5885b-338">`MyService` hizmetinden bir istemci fabrikası örneği oluşturur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="5885b-339">`HttpClient` , bir Web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="5885b-340">`Main` hizmetin yöntemini yürütmek için bir kapsam oluşturur `GetPage` ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.</span><span class="sxs-lookup"><span data-stu-id="5885b-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="5885b-341">Üst bilgi yayma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="5885b-341">Header propagation middleware</span></span>

<span data-ttu-id="5885b-342">Üst bilgi yayma, gelen istekten giden HTTP Istemci isteklerine HTTP üstbilgilerini yaymaya yönelik bir ASP.NET Core ara istemcindedir.</span><span class="sxs-lookup"><span data-stu-id="5885b-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="5885b-343">Üst bilgi yaymayı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="5885b-343">To use header propagation:</span></span>

* <span data-ttu-id="5885b-344">[Microsoft. AspNetCore. Headeryayma](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="5885b-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="5885b-345">Ara yazılımı ve `HttpClient` içinde yapılandırın `Startup` :</span><span class="sxs-lookup"><span data-stu-id="5885b-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="5885b-346">İstemci giden isteklerde yapılandırılan üst bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="5885b-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="5885b-347">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5885b-347">Additional resources</span></span>

* [<span data-ttu-id="5885b-348">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="5885b-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="5885b-349">HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın</span><span class="sxs-lookup"><span data-stu-id="5885b-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="5885b-350">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="5885b-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="5885b-351">.NET 'te JSON serileştirme ve serisini kaldırma</span><span class="sxs-lookup"><span data-stu-id="5885b-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5885b-352">, [Glenn CONDRON](https://github.com/glennc), [Ryan şimdi e](https://github.com/rynowak)ve [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="5885b-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="5885b-353">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak için kayıt yapılabilir ve kullanılabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="5885b-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="5885b-354">Aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="5885b-354">It offers the following benefits:</span></span>

* <span data-ttu-id="5885b-355">, Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="5885b-356">Örneğin, *GitHub istemcisi kayıtlı* ve [GitHub](https://github.com/)'a erişebilecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="5885b-357">Varsayılan istemci, diğer amaçlar için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="5885b-358">' De işleyiciler için temsilci atama ile giden ara yazılım kavramı `HttpClient` ve bundan faydalanmak için, Polly tabanlı ara yazılım için uzantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="5885b-359">`HttpClientMessageHandler`Yaşam sürelerini el ile yönetirken gerçekleşen YAYGıN DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="5885b-360">`ILogger`Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).</span><span class="sxs-lookup"><span data-stu-id="5885b-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="5885b-361">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5885b-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="5885b-362">Tüketim desenleri</span><span class="sxs-lookup"><span data-stu-id="5885b-362">Consumption patterns</span></span>

<span data-ttu-id="5885b-363">Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="5885b-364">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="5885b-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="5885b-365">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="5885b-366">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="5885b-367">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="5885b-368">Hiçbiri diğerinden tamamen üst değildir.</span><span class="sxs-lookup"><span data-stu-id="5885b-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="5885b-369">En iyi yaklaşım, uygulamanın kısıtlamalarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="5885b-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="5885b-370">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="5885b-370">Basic usage</span></span>

<span data-ttu-id="5885b-371">, `IHttpClientFactory` `AddHttpClient` Yöntemi içindeki içindeki genişletme yöntemi çağırarak kaydedilebilir `IServiceCollection` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5885b-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="5885b-372">Kaydedildikten sonra kod, `IHttpClientFactory` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile her yerden bir hizmeti kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5885b-373">`IHttpClientFactory`Bir örnek oluşturmak için kullanılabilir `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="5885b-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="5885b-374">`IHttpClientFactory`Bu biçimde kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="5885b-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="5885b-375">Kullanım şekli üzerinde hiçbir etkisi yoktur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="5885b-376">`HttpClient`Örneklerin Şu anda oluşturulduğu yerlerde, bu tekrarlamaları ' a çağrı ile değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="5885b-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="5885b-377">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-377">Named clients</span></span>

<span data-ttu-id="5885b-378">Bir uygulama `HttpClient` , her biri farklı bir yapılandırmaya sahip birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemciler** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients** .</span></span> <span data-ttu-id="5885b-379">Adlandırılmış için yapılandırma `HttpClient` , içinde kayıt sırasında belirtilebilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5885b-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="5885b-380">Yukarıdaki kodda, `AddHttpClient` *GitHub* adının sağlanması denir.</span><span class="sxs-lookup"><span data-stu-id="5885b-380">In the preceding code, `AddHttpClient` is called, providing the name *github* .</span></span> <span data-ttu-id="5885b-381">Bu istemci, &mdash; GITHUB API 'siyle birlikte çalışmak için gerekli olan temel adres ve iki üst bilgi olan bazı varsayılan yapılandırma uygulanmış.</span><span class="sxs-lookup"><span data-stu-id="5885b-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="5885b-382">Her seferinde her `CreateClient` çağrıldığında yeni bir örneği `HttpClient` oluşturulur ve yapılandırma eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="5885b-383">Adlandırılmış bir istemciyi kullanmak için, bir dize parametresi iletilebilir `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="5885b-384">Oluşturulacak istemcinin adını belirtin:</span><span class="sxs-lookup"><span data-stu-id="5885b-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="5885b-385">Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5885b-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="5885b-386">İstemci için yapılandırılan taban adresi kullanıldığından, bu yalnızca yolu geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="5885b-387">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-387">Typed clients</span></span>

<span data-ttu-id="5885b-388">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="5885b-388">Typed clients:</span></span>

* <span data-ttu-id="5885b-389">Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="5885b-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="5885b-390">İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="5885b-391">Yapılandırmak ve belirli bir ile etkileşimde bulunmak için tek bir konum belirtin `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="5885b-392">Örneğin, tek bir arka uç uç noktası için tek bir adet yazılmış istemci kullanılabilir ve bu uç nokta ile ilgili tüm mantığı kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="5885b-393">DI ile birlikte çalışın ve uygulamanızda gerektiğinde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="5885b-394">Türü belirtilmiş istemci `HttpClient` , oluşturucusunda bir parametreyi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="5885b-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="5885b-395">Önceki kodda, yapılandırma yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="5885b-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="5885b-396">`HttpClient`Nesne bir ortak özellik olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="5885b-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="5885b-397">İşlevselliği kullanıma sunan API 'ye özel yöntemler tanımlamak mümkündür `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="5885b-398">`GetAspNetDocsIssues`Yöntemi, GitHub deposundan en son açık sorunları sorgulamak ve ayrıştırmak için gereken kodu saklar.</span><span class="sxs-lookup"><span data-stu-id="5885b-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="5885b-399">Türü belirtilmiş bir istemciyi kaydettirmek için, genel <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genişletme yöntemi içinde kullanılabilir `Startup.ConfigureServices` istemci sınıfını belirterek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="5885b-400">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="5885b-401">Yazılan istemci doğrudan eklenebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="5885b-402">Tercih edilirse, yazılan istemcinin yapılandırması, `Startup.ConfigureServices` türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="5885b-403">`HttpClient`Türü belirtilmiş bir istemci içinde tamamen kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="5885b-404">Bunu bir özellik olarak göstermek yerine, örneği dahili olarak çağıran ortak Yöntemler sunulabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="5885b-405">Önceki kodda, `HttpClient` bir özel alan olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="5885b-406">Dış çağrıları yapmak için tüm erişim `GetRepos` yönteminden geçer.</span><span class="sxs-lookup"><span data-stu-id="5885b-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="5885b-407">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-407">Generated clients</span></span>

<span data-ttu-id="5885b-408">`IHttpClientFactory` , [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıklarıyla birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="5885b-409">Yeniden sığdırma, .NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="5885b-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="5885b-410">REST API 'Leri canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="5885b-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="5885b-411">Arabirim bir uygulama, `RestService` `HttpClient` dış http çağrıları yapmak için kullanılarak tarafından dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5885b-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="5885b-412">Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="5885b-412">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="5885b-413">Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-413">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="5885b-414">Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="5885b-415">Giden istek ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="5885b-415">Outgoing request middleware</span></span>

<span data-ttu-id="5885b-416">`HttpClient` , giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramı zaten var.</span><span class="sxs-lookup"><span data-stu-id="5885b-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="5885b-417">, `IHttpClientFactory` Her bir adlandırılmış istemci için uygulanacak işleyicileri tanımlamanızı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="5885b-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="5885b-418">Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydını ve zincirlemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="5885b-419">Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="5885b-420">Bu düzen, ASP.NET Core gelen ara yazılım ardışık düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="5885b-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="5885b-421">Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="5885b-422">Bir işleyici oluşturmak için, öğesinden türeten bir sınıf tanımlayın <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="5885b-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="5885b-423">`SendAsync`İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="5885b-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="5885b-424">Yukarıdaki kod, temel bir işleyiciyi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="5885b-425">İsteğe bağlı bir başlık olup olmadığını denetler `X-API-KEY` .</span><span class="sxs-lookup"><span data-stu-id="5885b-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="5885b-426">Üst bilgi eksikse, HTTP çağrısından kaçınabilir ve uygun bir yanıt döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="5885b-427">Kayıt sırasında, bir veya daha fazla işleyici bir için yapılandırmasına eklenebilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="5885b-428">Bu görev, üzerindeki genişletme yöntemleri aracılığıyla gerçekleştirilir <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="5885b-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="5885b-429">Yukarıdaki kodda,, `ValidateHeaderHandler` dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="5885b-430">`IHttpClientFactory`Her işleyici için ayrı bır dı kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5885b-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="5885b-431">İşleyiciler herhangi bir kapsamın hizmetlerine bağlı olarak ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="5885b-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="5885b-432">İşleyicilerin bağımlı olduğu hizmetler, işleyicinin elden çıkarılmasıyla kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="5885b-433">Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyicinin türü geçirerek, çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="5885b-434">Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="5885b-435">Her işleyici, son isteği çalıştırana kadar sonraki işleyiciyi sarmalar `HttpClientHandler` :</span><span class="sxs-lookup"><span data-stu-id="5885b-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="5885b-436">İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="5885b-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="5885b-437">Kullanarak işleyicide veri geçirin `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="5885b-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="5885b-438">`IHttpContextAccessor`Geçerli isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="5885b-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="5885b-439">`AsyncLocal`Verileri geçirmek için özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5885b-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="5885b-440">Polly tabanlı işleyiciler kullanın</span><span class="sxs-lookup"><span data-stu-id="5885b-440">Use Polly-based handlers</span></span>

<span data-ttu-id="5885b-441">`IHttpClientFactory` , [Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kitaplığı ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="5885b-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="5885b-442">Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="5885b-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="5885b-443">Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="5885b-444">Uzantı yöntemleri, yapılandırılmış örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="5885b-445">Polly uzantıları:</span><span class="sxs-lookup"><span data-stu-id="5885b-445">The Polly extensions:</span></span>

* <span data-ttu-id="5885b-446">İstemcilere Polly tabanlı işleyiciler eklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="5885b-447">, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="5885b-448">Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="5885b-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="5885b-449">Geçici hataları işle</span><span class="sxs-lookup"><span data-stu-id="5885b-449">Handle transient faults</span></span>

<span data-ttu-id="5885b-450">Yaygın hatalar, dış HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="5885b-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="5885b-451">`AddTransientHttpErrorPolicy`Geçici hataları işlemek üzere bir ilkenin tanımlanmasını sağlayan, çağrılan bir uygun genişletme yöntemi eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="5885b-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="5885b-452">Bu uzantı yöntemi tanıtıcısıyla yapılandırılmış ilkeler `HttpRequestException` , http 5xx yanıtları ve http 408 yanıtları.</span><span class="sxs-lookup"><span data-stu-id="5885b-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="5885b-453">`AddTransientHttpErrorPolicy`Uzantı içinde kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5885b-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5885b-454">Uzantı, `PolicyBuilder` olası bir geçici hatayı temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="5885b-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="5885b-455">Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5885b-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="5885b-456">Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="5885b-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="5885b-457">Dinamik olarak ilke seçme</span><span class="sxs-lookup"><span data-stu-id="5885b-457">Dynamically select policies</span></span>

<span data-ttu-id="5885b-458">Polly tabanlı işleyiciler eklemek için kullanılabilecek ek uzantı yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="5885b-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="5885b-459">Bu tür bir uzantının `AddPolicyHandler` birden çok aşırı yüklemesi vardır.</span><span class="sxs-lookup"><span data-stu-id="5885b-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="5885b-460">Bir aşırı yükleme, hangi ilkenin uygulanacağını tanımlarken isteğin incelenebilirliğini sağlar:</span><span class="sxs-lookup"><span data-stu-id="5885b-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="5885b-461">Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="5885b-462">Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="5885b-463">Birden çok Polly işleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="5885b-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="5885b-464">Gelişmiş işlevsellik sağlamak için çok fazla ilke iç içe geçmiş bir yaygın hale gelir:</span><span class="sxs-lookup"><span data-stu-id="5885b-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="5885b-465">Yukarıdaki örnekte, iki işleyici eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="5885b-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="5885b-466">İlki, `AddTransientHttpErrorPolicy` yeniden deneme ilkesi eklemek için uzantıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="5885b-467">Başarısız istekler en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="5885b-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="5885b-468">İçin ikinci çağrı, `AddTransientHttpErrorPolicy` bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="5885b-469">Beş başarısız girişim sırayla gerçekleşiyorsa, daha fazla dış istek 30 saniye için engellenir.</span><span class="sxs-lookup"><span data-stu-id="5885b-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="5885b-470">Devre kesici ilkeleri durum bilgisi vardır.</span><span class="sxs-lookup"><span data-stu-id="5885b-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="5885b-471">Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="5885b-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="5885b-472">Polly kayıt defterinden ilke ekleme</span><span class="sxs-lookup"><span data-stu-id="5885b-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="5885b-473">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir ile kaydetmektir `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="5885b-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="5885b-474">Kayıt defterinden bir ilke kullanılarak bir işleyicinin eklenmesine izin veren bir genişletme yöntemi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="5885b-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="5885b-475">Yukarıdaki kodda, öğesine eklendiğinde iki ilke kaydedilir `PolicyRegistry` `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="5885b-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="5885b-476">Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır ve uygulanacak ilke adı geçer.</span><span class="sxs-lookup"><span data-stu-id="5885b-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="5885b-477">Ve daha fazla tümleştirme hakkında daha fazla bilgi `IHttpClientFactory` , [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)' de bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="5885b-478">HttpClient ve ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="5885b-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="5885b-479">`HttpClient`Her çağrıldığında yeni bir örnek döndürülür `CreateClient` `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="5885b-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="5885b-480"><xref:System.Net.Http.HttpMessageHandler>Adlandırılmış istemci başına.</span><span class="sxs-lookup"><span data-stu-id="5885b-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="5885b-481">Fabrika, örneklerin yaşam sürelerini yönetir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="5885b-482">`IHttpClientFactory``HttpMessageHandler`kaynak tüketimini azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="5885b-483">Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni bir örnek oluştururken havuzdan yeniden kullanılabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="5885b-484">Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="5885b-485">Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="5885b-486">Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine yeniden davranmasını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="5885b-487">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="5885b-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="5885b-488">Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="5885b-489">Bunu geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` istemcisini oluştururken döndürülen öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="5885b-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="5885b-490">İstemcinin çıkarılması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="5885b-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="5885b-491">Çıkarma giden istekleri iptal eder ve `HttpClient` çağırma sonrasında verilen örneğin kullanılamaz olmasını sağlar <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="5885b-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="5885b-492">`IHttpClientFactory` örnekler tarafından kullanılan kaynakları izler ve ortadan kaldırdık `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="5885b-493">`HttpClient`Örnekler genellikle aktiften çıkarma gerektirmeyen .NET nesneleri olarak kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="5885b-494">Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde kullanılmadan önce kullanılan ortak bir modeldir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="5885b-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="5885b-495">Bu model, ' a geçtikten sonra gereksiz hale gelir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="5885b-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="5885b-496">Ihttpclientfactory alternatifleri</span><span class="sxs-lookup"><span data-stu-id="5885b-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="5885b-497">`IHttpClientFactory`Dı etkin bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="5885b-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="5885b-498">Havuz örneklerine göre kaynak tükenmesi sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="5885b-499">Düzenli aralıklarla örnekleri geçirerek eski DNS sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="5885b-500">Uzun süreli bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="5885b-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="5885b-501">`SocketsHttpHandler`Uygulamanın başladığı zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="5885b-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="5885b-502"><xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>DNS yenileme süreleri temelinde uygun bir değere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5885b-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="5885b-503">`HttpClient`Gerektiğinde örnek oluşturun `new HttpClient(handler, disposeHandler: false)` .</span><span class="sxs-lookup"><span data-stu-id="5885b-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="5885b-504">Yukarıdaki yaklaşımlar, `IHttpClientFactory` benzer bir şekilde çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="5885b-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="5885b-505">, `SocketsHttpHandler` Örnekleri arasında bağlantıları paylaşır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="5885b-506">Bu paylaşım, yuva azalmasına engel olur.</span><span class="sxs-lookup"><span data-stu-id="5885b-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="5885b-507">`SocketsHttpHandler`Bağlantıları, `PooledConnectionLifetime` eski DNS sorunlarından kaçınmak için öğesine göre döngüler.</span><span class="sxs-lookup"><span data-stu-id="5885b-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="5885b-508">:::no-loc(Cookie):::malar</span><span class="sxs-lookup"><span data-stu-id="5885b-508">:::no-loc(Cookie):::s</span></span>

<span data-ttu-id="5885b-509">Havuza alınmış `HttpMessageHandler` örnekler, `:::no-loc(Cookie):::Container` paylaşılan nesneler ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-509">The pooled `HttpMessageHandler` instances results in `:::no-loc(Cookie):::Container` objects being shared.</span></span> <span data-ttu-id="5885b-510">Beklenmeyen `:::no-loc(Cookie):::Container` nesne paylaşımı genellikle hatalı kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-510">Unanticipated `:::no-loc(Cookie):::Container` object sharing often results in incorrect code.</span></span> <span data-ttu-id="5885b-511">S gerektiren uygulamalar için :::no-loc(cookie)::: şunlardan birini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="5885b-511">For apps that require :::no-loc(cookie):::s, consider either:</span></span>

 - <span data-ttu-id="5885b-512">Otomatik işlemeyi devre dışı bırakma :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="5885b-512">Disabling automatic :::no-loc(cookie)::: handling</span></span>
 - <span data-ttu-id="5885b-513">Önlemenin `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="5885b-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="5885b-514"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Otomatik işlemeyi devre dışı bırakmak için çağırın :::no-loc(cookie)::: :</span><span class="sxs-lookup"><span data-stu-id="5885b-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic :::no-loc(cookie)::: handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="5885b-515">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="5885b-515">Logging</span></span>

<span data-ttu-id="5885b-516">`IHttpClientFactory`Tüm istekler için kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="5885b-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="5885b-517">Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="5885b-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="5885b-518">İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="5885b-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="5885b-519">Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="5885b-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="5885b-520">Örneğin, *Mynamedclient* adlı bir istemci, bir kategorisine sahip iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-520">A client named *MyNamedClient* , for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="5885b-521">*Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="5885b-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="5885b-522">İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="5885b-523">Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="5885b-524">Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="5885b-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="5885b-525">*Mynamedclient* örneğinde, bu iletiler günlük kategorisine göre günlüğe kaydedilir `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="5885b-526">İstek için bu, tüm diğer işleyiciler çalıştıktan sonra ve istek ağda gönderilmeden hemen önce gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="5885b-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="5885b-527">Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="5885b-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="5885b-528">İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="5885b-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="5885b-529">Bu, örneğin veya yanıt durum kodunda istek başlıklarındaki değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="5885b-530">İstemci adı ' nı log kategorisinde da içermek, gerektiğinde belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.</span><span class="sxs-lookup"><span data-stu-id="5885b-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="5885b-531">HttpMessageHandler 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5885b-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="5885b-532">İstemci tarafından kullanılan iç yapılandırmayı denetlemek gerekli olabilir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="5885b-533">`IHttpClientBuilder`Adlandırılmış veya yazılan istemciler eklenirken bir döndürülür.</span><span class="sxs-lookup"><span data-stu-id="5885b-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="5885b-534"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="5885b-535">Temsilci, `HttpMessageHandler` Bu istemci tarafından kullanılan birincili oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="5885b-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="5885b-536">Konsol uygulamasında ıhttpclientfactory kullanma</span><span class="sxs-lookup"><span data-stu-id="5885b-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="5885b-537">Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5885b-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="5885b-538">Microsoft. Extensions. Hosting</span><span class="sxs-lookup"><span data-stu-id="5885b-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="5885b-539">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="5885b-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="5885b-540">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="5885b-540">In the following example:</span></span>

* <span data-ttu-id="5885b-541"><xref:System.Net.Http.IHttpClientFactory> , [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="5885b-542">`MyService` hizmetinden bir istemci fabrikası örneği oluşturur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="5885b-543">`HttpClient` , bir Web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="5885b-544">`Main` hizmetin yöntemini yürütmek için bir kapsam oluşturur `GetPage` ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.</span><span class="sxs-lookup"><span data-stu-id="5885b-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="5885b-545">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5885b-545">Additional resources</span></span>

* [<span data-ttu-id="5885b-546">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="5885b-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="5885b-547">HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın</span><span class="sxs-lookup"><span data-stu-id="5885b-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="5885b-548">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="5885b-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="5885b-549">, [Glenn CONDRON](https://github.com/glennc), [Ryan şimdi e](https://github.com/rynowak)ve [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="5885b-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="5885b-550">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak için kayıt yapılabilir ve kullanılabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="5885b-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="5885b-551">Aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="5885b-551">It offers the following benefits:</span></span>

* <span data-ttu-id="5885b-552">, Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="5885b-553">Örneğin, *GitHub istemcisi kayıtlı* ve [GitHub](https://github.com/)'a erişebilecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="5885b-554">Varsayılan istemci, diğer amaçlar için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="5885b-555">' De işleyiciler için temsilci atama ile giden ara yazılım kavramı `HttpClient` ve bundan faydalanmak için, Polly tabanlı ara yazılım için uzantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="5885b-556">`HttpClientMessageHandler`Yaşam sürelerini el ile yönetirken gerçekleşen YAYGıN DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="5885b-557">`ILogger`Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).</span><span class="sxs-lookup"><span data-stu-id="5885b-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="5885b-558">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5885b-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5885b-559">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="5885b-559">Prerequisites</span></span>

<span data-ttu-id="5885b-560">.NET Framework hedefleyen projeler [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet paketinin yüklenmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="5885b-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="5885b-561">.NET Core ile hedeflenen ve [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvuran projeler zaten paketi içeriyor `Microsoft.Extensions.Http` .</span><span class="sxs-lookup"><span data-stu-id="5885b-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="5885b-562">Tüketim desenleri</span><span class="sxs-lookup"><span data-stu-id="5885b-562">Consumption patterns</span></span>

<span data-ttu-id="5885b-563">Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="5885b-564">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="5885b-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="5885b-565">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="5885b-566">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="5885b-567">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="5885b-568">Hiçbiri diğerinden tamamen üst değildir.</span><span class="sxs-lookup"><span data-stu-id="5885b-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="5885b-569">En iyi yaklaşım, uygulamanın kısıtlamalarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="5885b-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="5885b-570">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="5885b-570">Basic usage</span></span>

<span data-ttu-id="5885b-571">, `IHttpClientFactory` `AddHttpClient` Yöntemi içindeki içindeki genişletme yöntemi çağırarak kaydedilebilir `IServiceCollection` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5885b-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="5885b-572">Kaydedildikten sonra kod, `IHttpClientFactory` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile her yerden bir hizmeti kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5885b-573">`IHttpClientFactory`Bir örnek oluşturmak için kullanılabilir `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="5885b-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="5885b-574">`IHttpClientFactory`Bu biçimde kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="5885b-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="5885b-575">Kullanım şekli üzerinde hiçbir etkisi yoktur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="5885b-576">`HttpClient`Örneklerin Şu anda oluşturulduğu yerlerde, bu tekrarlamaları ' a çağrı ile değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="5885b-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="5885b-577">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-577">Named clients</span></span>

<span data-ttu-id="5885b-578">Bir uygulama `HttpClient` , her biri farklı bir yapılandırmaya sahip birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemciler** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients** .</span></span> <span data-ttu-id="5885b-579">Adlandırılmış için yapılandırma `HttpClient` , içinde kayıt sırasında belirtilebilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5885b-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="5885b-580">Yukarıdaki kodda, `AddHttpClient` *GitHub* adının sağlanması denir.</span><span class="sxs-lookup"><span data-stu-id="5885b-580">In the preceding code, `AddHttpClient` is called, providing the name *github* .</span></span> <span data-ttu-id="5885b-581">Bu istemci, &mdash; GITHUB API 'siyle birlikte çalışmak için gerekli olan temel adres ve iki üst bilgi olan bazı varsayılan yapılandırma uygulanmış.</span><span class="sxs-lookup"><span data-stu-id="5885b-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="5885b-582">Her seferinde her `CreateClient` çağrıldığında yeni bir örneği `HttpClient` oluşturulur ve yapılandırma eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="5885b-583">Adlandırılmış bir istemciyi kullanmak için, bir dize parametresi iletilebilir `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="5885b-584">Oluşturulacak istemcinin adını belirtin:</span><span class="sxs-lookup"><span data-stu-id="5885b-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="5885b-585">Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5885b-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="5885b-586">İstemci için yapılandırılan taban adresi kullanıldığından, bu yalnızca yolu geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="5885b-587">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-587">Typed clients</span></span>

<span data-ttu-id="5885b-588">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="5885b-588">Typed clients:</span></span>

* <span data-ttu-id="5885b-589">Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="5885b-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="5885b-590">İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="5885b-591">Yapılandırmak ve belirli bir ile etkileşimde bulunmak için tek bir konum belirtin `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="5885b-592">Örneğin, tek bir arka uç uç noktası için tek bir adet yazılmış istemci kullanılabilir ve bu uç nokta ile ilgili tüm mantığı kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="5885b-593">DI ile birlikte çalışın ve uygulamanızda gerektiğinde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="5885b-594">Türü belirtilmiş istemci `HttpClient` , oluşturucusunda bir parametreyi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="5885b-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="5885b-595">Önceki kodda, yapılandırma yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="5885b-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="5885b-596">`HttpClient`Nesne bir ortak özellik olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="5885b-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="5885b-597">İşlevselliği kullanıma sunan API 'ye özel yöntemler tanımlamak mümkündür `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="5885b-598">`GetAspNetDocsIssues`Yöntemi, GitHub deposundan en son açık sorunları sorgulamak ve ayrıştırmak için gereken kodu saklar.</span><span class="sxs-lookup"><span data-stu-id="5885b-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="5885b-599">Türü belirtilmiş bir istemciyi kaydettirmek için, genel <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genişletme yöntemi içinde kullanılabilir `Startup.ConfigureServices` istemci sınıfını belirterek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="5885b-600">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="5885b-601">Yazılan istemci doğrudan eklenebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="5885b-602">Tercih edilirse, yazılan istemcinin yapılandırması, `Startup.ConfigureServices` türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="5885b-603">`HttpClient`Türü belirtilmiş bir istemci içinde tamamen kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="5885b-604">Bunu bir özellik olarak göstermek yerine, örneği dahili olarak çağıran ortak Yöntemler sunulabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="5885b-605">Önceki kodda, `HttpClient` bir özel alan olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="5885b-606">Dış çağrıları yapmak için tüm erişim `GetRepos` yönteminden geçer.</span><span class="sxs-lookup"><span data-stu-id="5885b-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="5885b-607">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="5885b-607">Generated clients</span></span>

<span data-ttu-id="5885b-608">`IHttpClientFactory` , [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıklarıyla birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="5885b-609">Yeniden sığdırma, .NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="5885b-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="5885b-610">REST API 'Leri canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="5885b-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="5885b-611">Arabirim bir uygulama, `RestService` `HttpClient` dış http çağrıları yapmak için kullanılarak tarafından dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5885b-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="5885b-612">Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="5885b-612">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="5885b-613">Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5885b-613">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="5885b-614">Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="5885b-615">Giden istek ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="5885b-615">Outgoing request middleware</span></span>

<span data-ttu-id="5885b-616">`HttpClient` , giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramı zaten var.</span><span class="sxs-lookup"><span data-stu-id="5885b-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="5885b-617">, `IHttpClientFactory` Her bir adlandırılmış istemci için uygulanacak işleyicileri tanımlamanızı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="5885b-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="5885b-618">Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydını ve zincirlemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="5885b-619">Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="5885b-620">Bu düzen, ASP.NET Core gelen ara yazılım ardışık düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="5885b-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="5885b-621">Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="5885b-622">Bir işleyici oluşturmak için, öğesinden türeten bir sınıf tanımlayın <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="5885b-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="5885b-623">`SendAsync`İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="5885b-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="5885b-624">Yukarıdaki kod, temel bir işleyiciyi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="5885b-625">İsteğe bağlı bir başlık olup olmadığını denetler `X-API-KEY` .</span><span class="sxs-lookup"><span data-stu-id="5885b-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="5885b-626">Üst bilgi eksikse, HTTP çağrısından kaçınabilir ve uygun bir yanıt döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="5885b-627">Kayıt sırasında, bir veya daha fazla işleyici bir için yapılandırmasına eklenebilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="5885b-628">Bu görev, üzerindeki genişletme yöntemleri aracılığıyla gerçekleştirilir <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="5885b-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="5885b-629">Yukarıdaki kodda,, `ValidateHeaderHandler` dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="5885b-630">İşleyicinin, bir geçici hizmet olarak dı 'ye kayıtlı olması **gerekir** , hiçbir koşulda kapsamı yoktur.</span><span class="sxs-lookup"><span data-stu-id="5885b-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="5885b-631">İşleyici kapsamlı bir hizmet olarak kayıtlıysa ve işleyicinin bağımlı olduğu tüm hizmetler atılabilir olur:</span><span class="sxs-lookup"><span data-stu-id="5885b-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="5885b-632">İşleyici kapsam dışına geçmeden önce işleyicinin Hizmetleri atılamaz.</span><span class="sxs-lookup"><span data-stu-id="5885b-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="5885b-633">Atılmış işleyici Hizmetleri işleyicinin başarısız olmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="5885b-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="5885b-634">Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyici türünü geçirerek çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="5885b-635">Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="5885b-636">Her işleyici, son isteği çalıştırana kadar sonraki işleyiciyi sarmalar `HttpClientHandler` :</span><span class="sxs-lookup"><span data-stu-id="5885b-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="5885b-637">İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="5885b-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="5885b-638">Kullanarak işleyicide veri geçirin `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="5885b-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="5885b-639">`IHttpContextAccessor`Geçerli isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="5885b-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="5885b-640">`AsyncLocal`Verileri geçirmek için özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5885b-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="5885b-641">Polly tabanlı işleyiciler kullanın</span><span class="sxs-lookup"><span data-stu-id="5885b-641">Use Polly-based handlers</span></span>

<span data-ttu-id="5885b-642">`IHttpClientFactory` , [Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kitaplığı ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="5885b-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="5885b-643">Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="5885b-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="5885b-644">Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="5885b-645">Uzantı yöntemleri, yapılandırılmış örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="5885b-646">Polly uzantıları:</span><span class="sxs-lookup"><span data-stu-id="5885b-646">The Polly extensions:</span></span>

* <span data-ttu-id="5885b-647">İstemcilere Polly tabanlı işleyiciler eklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="5885b-648">, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="5885b-649">Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="5885b-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="5885b-650">Geçici hataları işle</span><span class="sxs-lookup"><span data-stu-id="5885b-650">Handle transient faults</span></span>

<span data-ttu-id="5885b-651">Yaygın hatalar, dış HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="5885b-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="5885b-652">`AddTransientHttpErrorPolicy`Geçici hataları işlemek üzere bir ilkenin tanımlanmasını sağlayan, çağrılan bir uygun genişletme yöntemi eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="5885b-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="5885b-653">Bu uzantı yöntemi tanıtıcısıyla yapılandırılmış ilkeler `HttpRequestException` , http 5xx yanıtları ve http 408 yanıtları.</span><span class="sxs-lookup"><span data-stu-id="5885b-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="5885b-654">`AddTransientHttpErrorPolicy`Uzantı içinde kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5885b-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5885b-655">Uzantı, `PolicyBuilder` olası bir geçici hatayı temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="5885b-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="5885b-656">Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5885b-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="5885b-657">Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="5885b-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="5885b-658">Dinamik olarak ilke seçme</span><span class="sxs-lookup"><span data-stu-id="5885b-658">Dynamically select policies</span></span>

<span data-ttu-id="5885b-659">Polly tabanlı işleyiciler eklemek için kullanılabilecek ek uzantı yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="5885b-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="5885b-660">Bu tür bir uzantının `AddPolicyHandler` birden çok aşırı yüklemesi vardır.</span><span class="sxs-lookup"><span data-stu-id="5885b-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="5885b-661">Bir aşırı yükleme, hangi ilkenin uygulanacağını tanımlarken isteğin incelenebilirliğini sağlar:</span><span class="sxs-lookup"><span data-stu-id="5885b-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="5885b-662">Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="5885b-663">Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="5885b-664">Birden çok Polly işleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="5885b-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="5885b-665">Gelişmiş işlevsellik sağlamak için çok fazla ilke iç içe geçmiş bir yaygın hale gelir:</span><span class="sxs-lookup"><span data-stu-id="5885b-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="5885b-666">Yukarıdaki örnekte, iki işleyici eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="5885b-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="5885b-667">İlki, `AddTransientHttpErrorPolicy` yeniden deneme ilkesi eklemek için uzantıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="5885b-668">Başarısız istekler en fazla üç kez yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="5885b-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="5885b-669">İçin ikinci çağrı, `AddTransientHttpErrorPolicy` bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="5885b-670">Beş başarısız girişim sırayla gerçekleşiyorsa, daha fazla dış istek 30 saniye için engellenir.</span><span class="sxs-lookup"><span data-stu-id="5885b-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="5885b-671">Devre kesici ilkeleri durum bilgisi vardır.</span><span class="sxs-lookup"><span data-stu-id="5885b-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="5885b-672">Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="5885b-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="5885b-673">Polly kayıt defterinden ilke ekleme</span><span class="sxs-lookup"><span data-stu-id="5885b-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="5885b-674">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir ile kaydetmektir `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="5885b-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="5885b-675">Kayıt defterinden bir ilke kullanılarak bir işleyicinin eklenmesine izin veren bir genişletme yöntemi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="5885b-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="5885b-676">Yukarıdaki kodda, öğesine eklendiğinde iki ilke kaydedilir `PolicyRegistry` `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="5885b-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="5885b-677">Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır ve uygulanacak ilke adı geçer.</span><span class="sxs-lookup"><span data-stu-id="5885b-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="5885b-678">Ve daha fazla tümleştirme hakkında daha fazla bilgi `IHttpClientFactory` , [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)' de bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="5885b-679">HttpClient ve ömür yönetimi</span><span class="sxs-lookup"><span data-stu-id="5885b-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="5885b-680">`HttpClient`Her çağrıldığında yeni bir örnek döndürülür `CreateClient` `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="5885b-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="5885b-681"><xref:System.Net.Http.HttpMessageHandler>Adlandırılmış istemci başına.</span><span class="sxs-lookup"><span data-stu-id="5885b-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="5885b-682">Fabrika, örneklerin yaşam sürelerini yönetir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="5885b-683">`IHttpClientFactory``HttpMessageHandler`kaynak tüketimini azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="5885b-684">Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni bir örnek oluştururken havuzdan yeniden kullanılabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="5885b-685">Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="5885b-686">Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="5885b-687">Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine yeniden davranmasını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="5885b-688">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="5885b-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="5885b-689">Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="5885b-690">Bunu geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` istemcisini oluştururken döndürülen öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="5885b-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="5885b-691">İstemcinin çıkarılması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="5885b-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="5885b-692">Çıkarma giden istekleri iptal eder ve `HttpClient` çağırma sonrasında verilen örneğin kullanılamaz olmasını sağlar <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="5885b-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="5885b-693">`IHttpClientFactory` örnekler tarafından kullanılan kaynakları izler ve ortadan kaldırdık `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="5885b-694">`HttpClient`Örnekler genellikle aktiften çıkarma gerektirmeyen .NET nesneleri olarak kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="5885b-695">Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde kullanılmadan önce kullanılan ortak bir modeldir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="5885b-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="5885b-696">Bu model, ' a geçtikten sonra gereksiz hale gelir `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="5885b-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="5885b-697">Ihttpclientfactory alternatifleri</span><span class="sxs-lookup"><span data-stu-id="5885b-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="5885b-698">`IHttpClientFactory`Dı etkin bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="5885b-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="5885b-699">Havuz örneklerine göre kaynak tükenmesi sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="5885b-700">Düzenli aralıklarla örnekleri geçirerek eski DNS sorunları `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="5885b-701">Uzun süreli bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="5885b-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="5885b-702">`SocketsHttpHandler`Uygulamanın başladığı zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="5885b-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="5885b-703"><xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>DNS yenileme süreleri temelinde uygun bir değere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5885b-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="5885b-704">`HttpClient`Gerektiğinde örnek oluşturun `new HttpClient(handler, disposeHandler: false)` .</span><span class="sxs-lookup"><span data-stu-id="5885b-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="5885b-705">Yukarıdaki yaklaşımlar, `IHttpClientFactory` benzer bir şekilde çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="5885b-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="5885b-706">, `SocketsHttpHandler` Örnekleri arasında bağlantıları paylaşır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="5885b-707">Bu paylaşım, yuva azalmasına engel olur.</span><span class="sxs-lookup"><span data-stu-id="5885b-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="5885b-708">`SocketsHttpHandler`Bağlantıları, `PooledConnectionLifetime` eski DNS sorunlarından kaçınmak için öğesine göre döngüler.</span><span class="sxs-lookup"><span data-stu-id="5885b-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="5885b-709">:::no-loc(Cookie):::malar</span><span class="sxs-lookup"><span data-stu-id="5885b-709">:::no-loc(Cookie):::s</span></span>

<span data-ttu-id="5885b-710">Havuza alınmış `HttpMessageHandler` örnekler, `:::no-loc(Cookie):::Container` paylaşılan nesneler ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-710">The pooled `HttpMessageHandler` instances results in `:::no-loc(Cookie):::Container` objects being shared.</span></span> <span data-ttu-id="5885b-711">Beklenmeyen `:::no-loc(Cookie):::Container` nesne paylaşımı genellikle hatalı kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="5885b-711">Unanticipated `:::no-loc(Cookie):::Container` object sharing often results in incorrect code.</span></span> <span data-ttu-id="5885b-712">S gerektiren uygulamalar için :::no-loc(cookie)::: şunlardan birini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="5885b-712">For apps that require :::no-loc(cookie):::s, consider either:</span></span>

 - <span data-ttu-id="5885b-713">Otomatik işlemeyi devre dışı bırakma :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="5885b-713">Disabling automatic :::no-loc(cookie)::: handling</span></span>
 - <span data-ttu-id="5885b-714">Önlemenin `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="5885b-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="5885b-715"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Otomatik işlemeyi devre dışı bırakmak için çağırın :::no-loc(cookie)::: :</span><span class="sxs-lookup"><span data-stu-id="5885b-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic :::no-loc(cookie)::: handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="5885b-716">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="5885b-716">Logging</span></span>

<span data-ttu-id="5885b-717">`IHttpClientFactory`Tüm istekler için kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="5885b-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="5885b-718">Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="5885b-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="5885b-719">İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="5885b-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="5885b-720">Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="5885b-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="5885b-721">Örneğin, *Mynamedclient* adlı bir istemci, bir kategorisine sahip iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-721">A client named *MyNamedClient* , for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="5885b-722">*Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="5885b-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="5885b-723">İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="5885b-724">Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="5885b-725">Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="5885b-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="5885b-726">*Mynamedclient* örneğinde, bu iletiler günlük kategorisine göre günlüğe kaydedilir `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="5885b-727">İstek için bu, tüm diğer işleyiciler çalıştıktan sonra ve istek ağda gönderilmeden hemen önce gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="5885b-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="5885b-728">Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="5885b-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="5885b-729">İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="5885b-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="5885b-730">Bu, örneğin veya yanıt durum kodunda istek başlıklarındaki değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="5885b-731">İstemci adı ' nı log kategorisinde da içermek, gerektiğinde belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.</span><span class="sxs-lookup"><span data-stu-id="5885b-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="5885b-732">HttpMessageHandler 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5885b-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="5885b-733">İstemci tarafından kullanılan iç yapılandırmayı denetlemek gerekli olabilir `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="5885b-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="5885b-734">`IHttpClientBuilder`Adlandırılmış veya yazılan istemciler eklenirken bir döndürülür.</span><span class="sxs-lookup"><span data-stu-id="5885b-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="5885b-735"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="5885b-736">Temsilci, `HttpMessageHandler` Bu istemci tarafından kullanılan birincili oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="5885b-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="5885b-737">Konsol uygulamasında ıhttpclientfactory kullanma</span><span class="sxs-lookup"><span data-stu-id="5885b-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="5885b-738">Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5885b-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="5885b-739">Microsoft. Extensions. Hosting</span><span class="sxs-lookup"><span data-stu-id="5885b-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="5885b-740">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="5885b-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="5885b-741">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="5885b-741">In the following example:</span></span>

* <span data-ttu-id="5885b-742"><xref:System.Net.Http.IHttpClientFactory> , [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5885b-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="5885b-743">`MyService` hizmetinden bir istemci fabrikası örneği oluşturur `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="5885b-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="5885b-744">`HttpClient` , bir Web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5885b-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="5885b-745">`Main` hizmetin yöntemini yürütmek için bir kapsam oluşturur `GetPage` ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.</span><span class="sxs-lookup"><span data-stu-id="5885b-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="5885b-746">Üst bilgi yayma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="5885b-746">Header propagation middleware</span></span>

<span data-ttu-id="5885b-747">Üst bilgi yayma, gelen istekten giden HTTP Istemci isteklerine HTTP üstbilgilerini yaymak için bir topluluk tarafından desteklenen bir ara yazılımlar.</span><span class="sxs-lookup"><span data-stu-id="5885b-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="5885b-748">Üst bilgi yaymayı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="5885b-748">To use header propagation:</span></span>

* <span data-ttu-id="5885b-749">Topluluğun, paket [Headeryayılmasının](https://www.nuget.org/packages/HeaderPropagation)desteklediği bağlantı noktasına başvurun.</span><span class="sxs-lookup"><span data-stu-id="5885b-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="5885b-750">ASP.NET Core 3,1 ve üzeri, [Microsoft. AspNetCore. Headeryayılmayı](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)destekler.</span><span class="sxs-lookup"><span data-stu-id="5885b-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="5885b-751">Ara yazılımı ve `HttpClient` içinde yapılandırın `Startup` :</span><span class="sxs-lookup"><span data-stu-id="5885b-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="5885b-752">İstemci giden isteklerde yapılandırılan üst bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="5885b-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="5885b-753">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5885b-753">Additional resources</span></span>

* [<span data-ttu-id="5885b-754">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="5885b-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="5885b-755">HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın</span><span class="sxs-lookup"><span data-stu-id="5885b-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="5885b-756">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="5885b-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
