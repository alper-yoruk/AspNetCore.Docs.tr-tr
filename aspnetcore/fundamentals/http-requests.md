---
title: ASP.NET Core'da IHttpClientFactory kullanarak HTTP isteklerini gerçekleştirin
author: stevejgordon
description: ASP.NET Core'da mantıksal HttpClient örneklerini yönetmek için IHttpClientFactory arabirimini kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
uid: fundamentals/http-requests
ms.openlocfilehash: 912be34ae0ee25837a94aab65443f15b17ab4556
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661688"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="6e731-103">ASP.NET Core'da IHttpClientFactory kullanarak HTTP isteklerini gerçekleştirin</span><span class="sxs-lookup"><span data-stu-id="6e731-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6e731-104">Glenn [Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), Rick [Anderson](https://twitter.com/RickAndMSFT), ve [Kirk Larkin](https://github.com/serpent5) tarafından</span><span class="sxs-lookup"><span data-stu-id="6e731-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="6e731-105">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve <xref:System.Net.Http.HttpClient> oluşturmak için kaydedilebilir ve kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="6e731-106">`IHttpClientFactory`aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="6e731-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="6e731-107">Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="6e731-108">Örneğin, *github* adlı bir istemci kaydedilebilir ve [GitHub'a](https://github.com/)erişmek için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="6e731-109">Varsayılan istemci genel erişim için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="6e731-110">Giden ara yazılım kavramını işleyicileri 'nde `HttpClient`atayarak kodlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="6e731-111">Polly tabanlı ara yazılım için uzantılar `HttpClient`sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="6e731-112">Temel `HttpClientMessageHandler` örneklerin havuzlama ve ömür boyu yönetir.</span><span class="sxs-lookup"><span data-stu-id="6e731-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="6e731-113">Otomatik yönetim, yaşam ömürlerini el ile yönetirken `HttpClient` oluşan yaygın DNS (Etki Alanı Adı Sistemi) sorunlarını önler.</span><span class="sxs-lookup"><span data-stu-id="6e731-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="6e731-114">Fabrika tarafından oluşturulan istemciler `ILogger`aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlük deneyimi (üzerinden) ekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="6e731-115">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirilir).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6e731-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6e731-116">Bu konu sürümündeki örnek <xref:System.Text.Json> kod, HTTP yanıtlarında döndürülen JSON içeriğini deserialize etmek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="6e731-117">Bu konuyu `Json.NET` kullanan `ReadAsAsync<T>`ve sürüm seçicisini kullanan örnekler için bu konunun 2.x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="6e731-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="6e731-118">Tüketim alışkanlıkları</span><span class="sxs-lookup"><span data-stu-id="6e731-118">Consumption patterns</span></span>

<span data-ttu-id="6e731-119">Bir uygulamada `IHttpClientFactory` kullanılabilen çeşitli yollar vardır:</span><span class="sxs-lookup"><span data-stu-id="6e731-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="6e731-120">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="6e731-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="6e731-121">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="6e731-122">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="6e731-123">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="6e731-124">En iyi yaklaşım uygulamanın gereksinimlerine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="6e731-125">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="6e731-125">Basic usage</span></span>

<span data-ttu-id="6e731-126">`IHttpClientFactory`arayarak `AddHttpClient`kaydedilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="6e731-127">Bağımlılık `IHttpClientFactory` [enjeksiyonu (DI)](xref:fundamentals/dependency-injection)kullanılarak istenebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6e731-128">Aşağıdaki kod `IHttpClientFactory` bir `HttpClient` örnek oluşturmak için kullanır:</span><span class="sxs-lookup"><span data-stu-id="6e731-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="6e731-129">Yukarıdaki `IHttpClientFactory` örnekte olduğu gibi kullanmak, varolan bir uygulamayı yeniden düzenlemenin iyi bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="6e731-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="6e731-130">Nasıl kullanılacağı üzerinde `HttpClient` hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="6e731-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="6e731-131">Varolan `HttpClient` bir uygulamada örneklerin oluşturulduğu yerlerde, bu oluşumları ' ya yapılan <xref:System.Net.Http.IHttpClientFactory.CreateClient*>çağrılarla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6e731-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="6e731-132">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-132">Named clients</span></span>

<span data-ttu-id="6e731-133">Adlandırılmış istemciler, şu zaman iyi bir seçimdir:</span><span class="sxs-lookup"><span data-stu-id="6e731-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="6e731-134">Uygulama birçok farklı kullanım `HttpClient`gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6e731-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="6e731-135">Birçok `HttpClient`s farklı yapılandırma var.</span><span class="sxs-lookup"><span data-stu-id="6e731-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="6e731-136">Bir adlandırılmış `HttpClient` için yapılandırma kayıt `Startup.ConfigureServices`sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="6e731-137">Önceki kodda istemci aşağıdakilerle yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="6e731-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="6e731-138">Temel adres. `https://api.github.com/`</span><span class="sxs-lookup"><span data-stu-id="6e731-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="6e731-139">GitHub API ile çalışmak için iki üstbilgi gerekir.</span><span class="sxs-lookup"><span data-stu-id="6e731-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="6e731-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="6e731-140">CreateClient</span></span>

<span data-ttu-id="6e731-141">Her <xref:System.Net.Http.IHttpClientFactory.CreateClient*> zaman denir:</span><span class="sxs-lookup"><span data-stu-id="6e731-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="6e731-142">Yeni `HttpClient` bir örnek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6e731-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="6e731-143">Yapılandırma eylemi denir.</span><span class="sxs-lookup"><span data-stu-id="6e731-143">The configuration action is called.</span></span>

<span data-ttu-id="6e731-144">Adlandırılmış bir istemci oluşturmak için, adını şu na `CreateClient`</span><span class="sxs-lookup"><span data-stu-id="6e731-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="6e731-145">Önceki kodda, istek bir ana bilgisayar adı belirtmek gerekmez.</span><span class="sxs-lookup"><span data-stu-id="6e731-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="6e731-146">İstemci için yapılandırılan temel adres kullanıldığından, kod yalnızca yolu geçebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="6e731-147">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-147">Typed clients</span></span>

<span data-ttu-id="6e731-148">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="6e731-148">Typed clients:</span></span>

* <span data-ttu-id="6e731-149">Dizeleri anahtar olarak kullanmaya gerek kalmadan adlandırılmış istemcilerle aynı yetenekleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="6e731-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="6e731-150">Müşterileri tüketirken IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="6e731-151">Belirli bir yeri yapılandırmak ve etkileşimde bulunabilmek `HttpClient`için tek bir konum sağlayın.</span><span class="sxs-lookup"><span data-stu-id="6e731-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="6e731-152">Örneğin, tek bir dakti-sa-yazılı istemci kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="6e731-153">Tek bir arka uç bitiş noktası için.</span><span class="sxs-lookup"><span data-stu-id="6e731-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="6e731-154">Bitiş noktası ile ilgili tüm mantık kapsüllemek için.</span><span class="sxs-lookup"><span data-stu-id="6e731-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="6e731-155">DI ile çalışın ve uygulamada gerektiğinde enjekte edilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="6e731-156">Yazılan istemci, oluşturucusu bir `HttpClient` parametre kabul eder:</span><span class="sxs-lookup"><span data-stu-id="6e731-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="6e731-157">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="6e731-157">In the preceding code:</span></span>

* <span data-ttu-id="6e731-158">Yapılandırma, yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="6e731-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="6e731-159">Nesne `HttpClient` bir kamu malı olarak ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="6e731-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="6e731-160">İşlevselliği ortaya çıkaran `HttpClient` API'ye özgü yöntemler oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="6e731-161">Örneğin, `GetAspNetDocsIssues` yöntem açık sorunları almak için kodu kapsüller.</span><span class="sxs-lookup"><span data-stu-id="6e731-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="6e731-162">Bir dakti-sa'lık istemci sınıfını kaydetmek <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> `Startup.ConfigureServices` için aşağıdaki kod çağırır:</span><span class="sxs-lookup"><span data-stu-id="6e731-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="6e731-163">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="6e731-164">Önceki kodda, `AddHttpClient` geçici `GitHubService` bir hizmet olarak kaydeder.</span><span class="sxs-lookup"><span data-stu-id="6e731-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="6e731-165">Bu kayıt, bir fabrika yöntemi kullanır:</span><span class="sxs-lookup"><span data-stu-id="6e731-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="6e731-166">`HttpClient` örneği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6e731-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="6e731-167">Bir örnek `GitHubService`oluşturun , onun `HttpClient` yapıcı için örneğinde geçen.</span><span class="sxs-lookup"><span data-stu-id="6e731-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="6e731-168">Yazılan istemci doğrudan enjekte edilebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="6e731-169">Daktio istemci için yapılandırma, yazılı `Startup.ConfigureServices`istemcinin oluşturucusu yerine, kayıt sırasında belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="6e731-170">Yazılmış `HttpClient` bir istemci içinde kapsüllenebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="6e731-171">Bir özellik olarak teşhir etmek yerine, `HttpClient` örneği dahili olarak çağıran bir yöntem tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="6e731-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="6e731-172">Önceki kodda, özel `HttpClient` bir alanda depolanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="6e731-173">Erişim ortak `HttpClient` `GetRepos` yöntemle.</span><span class="sxs-lookup"><span data-stu-id="6e731-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="6e731-174">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-174">Generated clients</span></span>

<span data-ttu-id="6e731-175">`IHttpClientFactory`[Refit](https://github.com/paulcbetts/refit)gibi üçüncü taraf kitaplıkları ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="6e731-176">Refit ,NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="6e731-177">REST API'lerini canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="6e731-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="6e731-178">Arabirimin bir uygulaması, harici HTTP `RestService`aramaları `HttpClient` yapmak için kullanılarak dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6e731-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="6e731-179">Dış API'yi ve yanıtını temsil edecek bir arabirim ve yanıt tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="6e731-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="6e731-180">Uygulamayı oluşturmak için Refit kullanılarak yazılan bir istemci eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="6e731-181">Tanımlanan arabirim, DI ve Refit tarafından sağlanan uygulama ile gerektiğinde tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="6e731-182">Giden istek middleware</span><span class="sxs-lookup"><span data-stu-id="6e731-182">Outgoing request middleware</span></span>

<span data-ttu-id="6e731-183">`HttpClient`giden HTTP istekleri için birbirine bağlanabilir işleyicileri delegating kavramı vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="6e731-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="6e731-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="6e731-185">Her adlandırılmış istemci için uygulamak için işleyicileri tanımlama kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="6e731-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="6e731-186">Giden istek ara yazılım ardışık oluşturmak için birden çok işleyicinin kaydedilmesi ve zincirlemesi destekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="6e731-187">Bu işleyicilerin her biri, giden istekten önce ve sonra iş yapabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="6e731-188">Bu desen:</span><span class="sxs-lookup"><span data-stu-id="6e731-188">This pattern:</span></span>

  * <span data-ttu-id="6e731-189">ASP.NET Core gelen middleware boru hattı benzer.</span><span class="sxs-lookup"><span data-stu-id="6e731-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="6e731-190">HTTP istekleri yle ilgili çapraz kesme endişelerini yönetmek için bir mekanizma sağlar:</span><span class="sxs-lookup"><span data-stu-id="6e731-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="6e731-191">Önbelleğe alma</span><span class="sxs-lookup"><span data-stu-id="6e731-191">caching</span></span>
    * <span data-ttu-id="6e731-192">hata işleme</span><span class="sxs-lookup"><span data-stu-id="6e731-192">error handling</span></span>
    * <span data-ttu-id="6e731-193">Seri -leştirme</span><span class="sxs-lookup"><span data-stu-id="6e731-193">serialization</span></span>
    * <span data-ttu-id="6e731-194">günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="6e731-194">logging</span></span>

<span data-ttu-id="6e731-195">Devratıcı bir işleyici oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="6e731-195">To create a delegating handler:</span></span>

* <span data-ttu-id="6e731-196">Türetin <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="6e731-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="6e731-197">Geçersiz <xref:System.Net.Http.DelegatingHandler.SendAsync*>kılma.</span><span class="sxs-lookup"><span data-stu-id="6e731-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="6e731-198">İsteği ardışık alandaki bir sonraki işleyiciye geçirmeden önce kodu yürüt:</span><span class="sxs-lookup"><span data-stu-id="6e731-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="6e731-199">`X-API-KEY` Üstbilgi istekte olup olmadığını önceki kod denetler.</span><span class="sxs-lookup"><span data-stu-id="6e731-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="6e731-200">`X-API-KEY` Eksikse, <xref:System.Net.HttpStatusCode.BadRequest> döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6e731-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="6e731-201">Bir yapılandırmaya `HttpClient` birden fazla işleyici <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="6e731-202">Önceki kodda, DI `ValidateHeaderHandler` kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="6e731-203">Her `IHttpClientFactory` işleyici için ayrı bir DI kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6e731-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="6e731-204">İşleyiciler herhangi bir kapsamdaki hizmetlere güvenebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="6e731-205">İşleyicinin bağlı olduğu hizmetler, işleyici imha edildiğinde elden çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="6e731-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="6e731-206">Bir kez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> kaydedildikten sonra, işleyici için tür geçen, çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="6e731-207">Birden çok işleyici, yürütmeleri gerektiği sırada kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="6e731-208">Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarar:</span><span class="sxs-lookup"><span data-stu-id="6e731-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="6e731-209">İsteme başına durumu ileti işleyicileriyle paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="6e731-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="6e731-210">[HttpRequestMessage.Properties'i](xref:System.Net.Http.HttpRequestMessage.Properties)kullanarak verileri işleyiciye aktarın.</span><span class="sxs-lookup"><span data-stu-id="6e731-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="6e731-211">Geçerli <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6e731-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="6e731-212">Verileri aktarmak <xref:System.Threading.AsyncLocal`1> için özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6e731-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="6e731-213">Polly tabanlı işleyicileri kullanma</span><span class="sxs-lookup"><span data-stu-id="6e731-213">Use Polly-based handlers</span></span>

<span data-ttu-id="6e731-214">`IHttpClientFactory`üçüncü taraf kütüphane [Polly](https://github.com/App-vNext/Polly)ile entegre .</span><span class="sxs-lookup"><span data-stu-id="6e731-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="6e731-215">Polly, .NET için kapsamlı bir esneklik ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="6e731-216">Geliştiricilerin Retry, Circuit Breaker, Timeout, Bulkhead Isolation ve Fallback gibi ilkeleri akıcı ve iş parçacığı güvenli bir şekilde ifade etmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="6e731-217">Polly ilkelerinin yapılandırılmış `HttpClient` örneklerle kullanımını etkinleştirmek için uzantı yöntemleri sağlanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="6e731-218">Polly uzantıları, istemcilere Polly tabanlı işleyiciler eklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="6e731-219">Polly [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6e731-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="6e731-220">Geçici hataları işleme</span><span class="sxs-lookup"><span data-stu-id="6e731-220">Handle transient faults</span></span>

<span data-ttu-id="6e731-221">Hatalar genellikle harici HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="6e731-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>geçici hataları işlemek için bir ilke tanımlanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="6e731-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="6e731-223">Aşağıdaki yanıtları `AddTransientHttpErrorPolicy` işlemek ile yapılandırılan ilkeler:</span><span class="sxs-lookup"><span data-stu-id="6e731-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="6e731-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="6e731-224">HTTP 5xx</span></span>
* <span data-ttu-id="6e731-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="6e731-225">HTTP 408</span></span>

<span data-ttu-id="6e731-226">`AddTransientHttpErrorPolicy`olası geçici `PolicyBuilder` bir hatayı temsil eden hataları işlemek için yapılandırılan bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="6e731-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="6e731-227">Önceki kodda bir `WaitAndRetryAsync` ilke tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="6e731-228">Başarısız istekler, denemeler arasında 600 ms'lik bir gecikmeyle üç defaya kadar yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="6e731-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="6e731-229">Dinamik olarak ilkeleri seçin</span><span class="sxs-lookup"><span data-stu-id="6e731-229">Dynamically select policies</span></span>

<span data-ttu-id="6e731-230">Uzantı yöntemleri Polly tabanlı işleyicileri eklemek için <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>sağlanır, örneğin.</span><span class="sxs-lookup"><span data-stu-id="6e731-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="6e731-231">Aşağıdaki `AddPolicyHandler` aşırı yükleme, hangi politikanın uygulanacağına karar verme isteğini denetler:</span><span class="sxs-lookup"><span data-stu-id="6e731-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="6e731-232">Önceki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman ayarı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="6e731-233">Başka bir HTTP yöntemi için 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e731-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="6e731-234">Birden çok Polly işleyicisi ekleme</span><span class="sxs-lookup"><span data-stu-id="6e731-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="6e731-235">Polly politikalarını yuvalamak yaygındır:</span><span class="sxs-lookup"><span data-stu-id="6e731-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="6e731-236">Önceki örnekte:</span><span class="sxs-lookup"><span data-stu-id="6e731-236">In the preceding example:</span></span>

* <span data-ttu-id="6e731-237">İki işleyici eklenir.</span><span class="sxs-lookup"><span data-stu-id="6e731-237">Two handlers are added.</span></span>
* <span data-ttu-id="6e731-238">İlk işleyici <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> yeniden deneme ilkesi eklemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="6e731-239">Başarısız istekler en fazla üç kez yeniden denendir.</span><span class="sxs-lookup"><span data-stu-id="6e731-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="6e731-240">İkinci `AddTransientHttpErrorPolicy` çağrı bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="6e731-241">5 başarısız deneme sırayla gerçekleşirse, diğer dış istekler 30 saniye boyunca engellenir.</span><span class="sxs-lookup"><span data-stu-id="6e731-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="6e731-242">Devre kesici ilkeleri durumludur.</span><span class="sxs-lookup"><span data-stu-id="6e731-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="6e731-243">Bu istemci den gelen tüm aramalar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="6e731-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="6e731-244">Polly kayıt defterinden ilkeler ekleme</span><span class="sxs-lookup"><span data-stu-id="6e731-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="6e731-245">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları `PolicyRegistry`bir kez tanımlamak ve bir .</span><span class="sxs-lookup"><span data-stu-id="6e731-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="6e731-246">Aşağıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="6e731-246">In the following code:</span></span>

* <span data-ttu-id="6e731-247">"Düzenli" ve "uzun" polisler eklenir.</span><span class="sxs-lookup"><span data-stu-id="6e731-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="6e731-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>kayıt defterinden "düzenli" ve "uzun" ilkeleri ekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="6e731-249">Polly entegrasyonları `IHttpClientFactory` hakkında daha fazla bilgi için [Polly wiki sayfasına](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)bakın.</span><span class="sxs-lookup"><span data-stu-id="6e731-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="6e731-250">Httpİste ve yaşam boyu yönetim</span><span class="sxs-lookup"><span data-stu-id="6e731-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="6e731-251">Yeni `HttpClient` bir örnek her `CreateClient` zaman `IHttpClientFactory`döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6e731-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="6e731-252">Adlandırılmış istemci başına bir <xref:System.Net.Http.HttpMessageHandler> oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6e731-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="6e731-253">`HttpMessageHandler` Fabrika, örneklerin yaşam ömürlerini yönetir.</span><span class="sxs-lookup"><span data-stu-id="6e731-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="6e731-254">`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri bir araya sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="6e731-255">Bir `HttpMessageHandler` örnek, kullanım ömrü dolmamışsa `HttpClient` yeni bir örnek oluştururken havuzdan yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="6e731-256">Her işleyici genellikle kendi temel HTTP bağlantılarını yönetir gibi işleyicileri havuzlama arzu edilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="6e731-257">Gerekenden daha fazla işleyici oluşturmak bağlantı gecikmelerine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="6e731-258">Bazı işleyiciler de bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS (Etki Alanı Adı Sistemi) değişikliklerine tepki sini engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="6e731-259">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="6e731-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="6e731-260">Varsayılan değer, adlandırılmış istemci bazında geçersiz kılınabilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="6e731-261">`HttpClient`örnekleri genellikle imha **gerektirmeyen** .NET nesneleri olarak kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="6e731-262">Elden çıkarma giden istekleri iptal eder `HttpClient` ve verilen örneğin aramadan <xref:System.IDisposable.Dispose*>sonra kullanılamayacağından garanti eder.</span><span class="sxs-lookup"><span data-stu-id="6e731-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="6e731-263">`IHttpClientFactory`örnekleri tarafından `HttpClient` kullanılan kaynakları izler ve elden çıkar.</span><span class="sxs-lookup"><span data-stu-id="6e731-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="6e731-264">Tek `HttpClient` bir örneği uzun süre canlı tutmak, kuruluşundan önce `IHttpClientFactory`kullanılan yaygın bir desendir.</span><span class="sxs-lookup"><span data-stu-id="6e731-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="6e731-265">Bu desen' e geçtikten `IHttpClientFactory`sonra gereksiz hale gelir.</span><span class="sxs-lookup"><span data-stu-id="6e731-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="6e731-266">IHttpClientFactory için alternatifler</span><span class="sxs-lookup"><span data-stu-id="6e731-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="6e731-267">DI `IHttpClientFactory` özellikli bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="6e731-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="6e731-268">Örnekleri birleştirerek `HttpMessageHandler` kaynak tükenmesi sorunları.</span><span class="sxs-lookup"><span data-stu-id="6e731-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="6e731-269">Düzenli aralıklarla bisiklet `HttpMessageHandler` örnekleri tarafından Bayat DNS sorunları.</span><span class="sxs-lookup"><span data-stu-id="6e731-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="6e731-270">Uzun ömürlü bir örneği kullanarak önceki sorunları çözmek <xref:System.Net.Http.SocketsHttpHandler> için alternatif yollar vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="6e731-271">Uygulamanın ne `SocketsHttpHandler` zaman başladığına bir örnek oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="6e731-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="6e731-272">DNS yenileme sürelerine göre uygun bir değere göre <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="6e731-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="6e731-273">Gerektiğinde `HttpClient` kullanarak `new HttpClient(handler, disposeHandler: false)` örnekler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6e731-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="6e731-274">Önceki yaklaşımlar benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="6e731-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="6e731-275">Örnekler `SocketsHttpHandler` arasında `HttpClient` bağlantıları paylaşır.</span><span class="sxs-lookup"><span data-stu-id="6e731-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="6e731-276">Bu paylaşım soket yorgunluğunu önler.</span><span class="sxs-lookup"><span data-stu-id="6e731-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="6e731-277">Eski `SocketsHttpHandler` DNS sorunlarını `PooledConnectionLifetime` önlemek için döngüleri bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="6e731-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="6e731-278">Tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="6e731-278">Cookies</span></span>

<span data-ttu-id="6e731-279">Birleştirilmiş `HttpMessageHandler` örnekler, nesnelerin `CookieContainer` paylaşılmasına neden oluyor.</span><span class="sxs-lookup"><span data-stu-id="6e731-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="6e731-280">Beklenmeyen `CookieContainer` nesne paylaşımı genellikle yanlış kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="6e731-281">Tanımlama bilgisi gerektiren uygulamalar için aşağıdakileri göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="6e731-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="6e731-282">Otomatik çerez işlemeyi devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="6e731-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="6e731-283">Kaçın -arak`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="6e731-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="6e731-284">Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> çerez kullanımını devre dışı kılabilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="6e731-285">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="6e731-285">Logging</span></span>

<span data-ttu-id="6e731-286">Tüm istekler için kayıt günlüğü iletileri aracılığıyla `IHttpClientFactory` oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="6e731-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="6e731-287">Varsayılan günlük iletilerini görmek için günlüğe kaydetme yapılandırmasında uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="6e731-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="6e731-288">İstek üstbilgilerinin günlüğe kaydedilmesi gibi ek günlüğe kaydetme yalnızca izleme düzeyinde dahildir.</span><span class="sxs-lookup"><span data-stu-id="6e731-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="6e731-289">Her istemci için kullanılan günlük kategorisi istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="6e731-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="6e731-290">Örneğin *MyNamedClient*adlı bir istemci iletileri "System.Net.Http.HttpClient" kategorisiyle günlüğe kaydeder. **MyNamedClient**. MantıksalHandler".</span><span class="sxs-lookup"><span data-stu-id="6e731-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="6e731-291">*LogicalHandler* ile sabitlenmiş iletiler istek işleyicisi ardışık alanıdışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="6e731-292">İstek üzerine, iletiler ardışık ardışık işlemden önce günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="6e731-293">Yanıtta, iletiler yanıtı aldıktan sonra günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="6e731-294">Günlüğe kaydetme, istek işleyicisi ardışık hattının içinde de oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="6e731-295">*MyNamedClient* örneğinde, bu iletiler "System.Net.HttpClient" günlük kategorisi ile günlüğe kaydedilir. **MyNamedClient**. MüşteriHandler".</span><span class="sxs-lookup"><span data-stu-id="6e731-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="6e731-296">İstek için, bu, diğer tüm işleyiciler çalıştırdıktan sonra ve istek gönderilmeden hemen önce oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="6e731-297">Yanıtta, bu günlüğe kaydetme, işleyici ardışık işlemden geçmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="6e731-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="6e731-298">Boru hattının dışında ve içinde günlüğe kaydetmeyi etkinleştirmek, diğer boru hattı işleyicileri tarafından yapılan değişikliklerin incelenmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="6e731-299">Bu, istek üstbilgisi veya yanıt durum kodu değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="6e731-300">Günlük kategorisinde istemcinin adını niçin de dahil olmak üzere, belirli adlandırılmış istemciler için günlük filtreleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="6e731-301">HttpMessageHandler'ı yapılandırın</span><span class="sxs-lookup"><span data-stu-id="6e731-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="6e731-302">Bir istemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmasını denetlemek için gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="6e731-303">Adlandırılmış veya yazılan istemciler eklendiğinde bir `IHttpClientBuilder` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6e731-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="6e731-304">Uzantı <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> yöntemi bir temsilci tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="6e731-305">Temsilci, bu istemci tarafından kullanılan `HttpMessageHandler` birincil oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="6e731-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="6e731-306">iHttpClientFactory konsol uygulamasında kullanma</span><span class="sxs-lookup"><span data-stu-id="6e731-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="6e731-307">Konsol uygulamasında, projeye aşağıdaki paket başvurularını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6e731-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="6e731-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="6e731-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="6e731-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="6e731-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="6e731-310">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="6e731-310">In the following example:</span></span>

* <span data-ttu-id="6e731-311"><xref:System.Net.Http.IHttpClientFactory>Genel Ana [Bilgisayar hizmet](xref:fundamentals/host/generic-host) konteynerine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="6e731-312">`MyService`bir `HttpClient`. oluşturmak için kullanılan hizmetten bir istemci fabrika örneği oluşturur</span><span class="sxs-lookup"><span data-stu-id="6e731-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="6e731-313">`HttpClient`bir web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e731-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="6e731-314">`Main`hizmetin `GetPage` yöntemini yürütmek ve web sayfası içeriğinin ilk 500 karakterini konsola yazmak için bir kapsam oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6e731-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="6e731-315">Üstbilgi yayılımı ara ware</span><span class="sxs-lookup"><span data-stu-id="6e731-315">Header propagation middleware</span></span>

<span data-ttu-id="6e731-316">Üstbilgi yayılımı, gelen istekten giden HTTP İstemci isteklerine HTTP üstbilgilerini yayaymak için ASP.NET bir Çekirdek ara yazılımıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="6e731-317">Üstbilgi yayılmasını kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="6e731-317">To use header propagation:</span></span>

* <span data-ttu-id="6e731-318">[Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="6e731-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="6e731-319">Ara yazılımı yapılandırın ve `HttpClient` şu `Startup`şekilde:</span><span class="sxs-lookup"><span data-stu-id="6e731-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="6e731-320">İstemci, giden isteklerde yapılandırılan üstbilgiiçerir:</span><span class="sxs-lookup"><span data-stu-id="6e731-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="6e731-321">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6e731-321">Additional resources</span></span>

* [<span data-ttu-id="6e731-322">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="6e731-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="6e731-323">HttpClientFactory ve Polly politikaları ile üstel geri dönüş ile HTTP çağrı yeniden uygulama</span><span class="sxs-lookup"><span data-stu-id="6e731-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="6e731-324">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="6e731-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="6e731-325">JSON'u .NET'te serihale ve deserialize etme</span><span class="sxs-lookup"><span data-stu-id="6e731-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6e731-326">Glenn [Condron](https://github.com/glennc)tarafından , [Ryan Nowak](https://github.com/rynowak), ve [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="6e731-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="6e731-327">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve <xref:System.Net.Http.HttpClient> oluşturmak için kaydedilebilir ve kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="6e731-328">Aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="6e731-328">It offers the following benefits:</span></span>

* <span data-ttu-id="6e731-329">Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="6e731-330">Örneğin, bir *github* istemcisi kaydedilebilir ve [GitHub'a](https://github.com/)erişmek için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="6e731-331">Varsayılan istemci başka amaçlarla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="6e731-332">Giden ara yazılım kavramını işleyicileri atayarak kodlar `HttpClient` ve Polly tabanlı ara yazılımiçin bundan yararlanmak için uzantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="6e731-333">Yaşam ömürlerini el ile `HttpClientMessageHandler` yönetirken `HttpClient` ortaya çıkan yaygın DNS sorunlarını önlemek için temel örneklerin biraraya getiriyi ve kullanım ömrünü yönetir.</span><span class="sxs-lookup"><span data-stu-id="6e731-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="6e731-334">Fabrika tarafından oluşturulan istemciler `ILogger`aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlük deneyimi (üzerinden) ekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="6e731-335">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6e731-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="6e731-336">Tüketim alışkanlıkları</span><span class="sxs-lookup"><span data-stu-id="6e731-336">Consumption patterns</span></span>

<span data-ttu-id="6e731-337">Bir uygulamada `IHttpClientFactory` kullanılabilen çeşitli yollar vardır:</span><span class="sxs-lookup"><span data-stu-id="6e731-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="6e731-338">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="6e731-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="6e731-339">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="6e731-340">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="6e731-341">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="6e731-342">Hiçbiri diğerinden kesinlikle üstün değil.</span><span class="sxs-lookup"><span data-stu-id="6e731-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="6e731-343">En iyi yaklaşım uygulamanın kısıtlamalarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="6e731-344">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="6e731-344">Basic usage</span></span>

<span data-ttu-id="6e731-345">Yöntemin `IHttpClientFactory` `AddHttpClient` `IServiceCollection`içinde, `Startup.ConfigureServices` uzantı yöntemi ni arayarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="6e731-346">Bir kez kayıtlı, `IHttpClientFactory` kod bağımlılık [enjeksiyon (DI)](xref:fundamentals/dependency-injection)ile enjekte edilebilir her yerde hizmetleri kabul edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e731-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6e731-347">Bir `IHttpClientFactory` `HttpClient` örnek oluşturmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="6e731-348">Bu `IHttpClientFactory` şekilde kullanmak, varolan bir uygulamayı yeniden düzenlemenin iyi bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="6e731-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="6e731-349">Bu şekilde `HttpClient` kullanılan hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="6e731-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="6e731-350">Örneklerin `HttpClient` şu anda oluşturulduğu yerlerde, bu oluşumları bir çağrıyla değiştirin. <xref:System.Net.Http.IHttpClientFactory.CreateClient*></span><span class="sxs-lookup"><span data-stu-id="6e731-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="6e731-351">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-351">Named clients</span></span>

<span data-ttu-id="6e731-352">Bir `HttpClient`uygulama, her biri farklı bir yapılandırmaya sahip olan birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemcileri**kullanma seçeneği vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="6e731-353">Bir adlandırılmış `HttpClient` için yapılandırma kayıt `Startup.ConfigureServices`sırasında belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="6e731-354">Önceki kodda, `AddHttpClient` adı *github*sağlayan denir.</span><span class="sxs-lookup"><span data-stu-id="6e731-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="6e731-355">Bu istemci, temel&mdash;adres ve GitHub API ile çalışmak için gerekli iki üstbilgi uygulanan bazı varsayılan yapılandırma vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="6e731-356">Her `CreateClient` zaman çağrılır, `HttpClient` yeni bir örnek oluşturulur ve yapılandırma eylem denir.</span><span class="sxs-lookup"><span data-stu-id="6e731-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="6e731-357">Adlandırılmış bir istemciyi tüketmek için `CreateClient`dize parametresi .</span><span class="sxs-lookup"><span data-stu-id="6e731-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="6e731-358">Oluşturulacak istemcinin adını belirtin:</span><span class="sxs-lookup"><span data-stu-id="6e731-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="6e731-359">Önceki kodda, istek bir ana bilgisayar adı belirtmek gerekmez.</span><span class="sxs-lookup"><span data-stu-id="6e731-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="6e731-360">İstemci için yapılandırılan temel adres kullanıldığından, yalnızca yolu geçebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="6e731-361">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-361">Typed clients</span></span>

<span data-ttu-id="6e731-362">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="6e731-362">Typed clients:</span></span>

* <span data-ttu-id="6e731-363">Dizeleri anahtar olarak kullanmaya gerek kalmadan adlandırılmış istemcilerle aynı yetenekleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="6e731-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="6e731-364">Müşterileri tüketirken IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="6e731-365">Belirli bir yeri yapılandırmak ve etkileşimde bulunabilmek `HttpClient`için tek bir konum sağlayın.</span><span class="sxs-lookup"><span data-stu-id="6e731-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="6e731-366">Örneğin, tek bir daktilo istemcisi tek bir arka uç bitiş noktası için kullanılabilir ve bu uç noktaile ilgili tüm mantığı kapsülleyebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="6e731-367">DI ile çalışın ve uygulamanızda gerektiğinde enjekte edilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="6e731-368">Yazılan istemci, oluşturucusu bir `HttpClient` parametre kabul eder:</span><span class="sxs-lookup"><span data-stu-id="6e731-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6e731-369">Önceki kodda, yapılandırma yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="6e731-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="6e731-370">Nesne `HttpClient` bir kamu malı olarak ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="6e731-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="6e731-371">İşlevselliği ortaya `HttpClient` çıkaran API'ye özgü yöntemleri tanımlamak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="6e731-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="6e731-372">Yöntem, `GetAspNetDocsIssues` github deposundan en son açık sorunları sorgulamak ve ayrışdırmak için gereken kodu kapsüller.</span><span class="sxs-lookup"><span data-stu-id="6e731-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="6e731-373">Bir daktilan istemci kaydetmek <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> için, genel `Startup.ConfigureServices`uzantı yöntemi içinde kullanılabilir , daktilan istemci sınıfı belirterek:</span><span class="sxs-lookup"><span data-stu-id="6e731-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="6e731-374">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="6e731-375">Yazılan istemci doğrudan enjekte edilebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="6e731-376">Tercih edilirse, yazılan istemcinin oluşturucusu yerine, `Startup.ConfigureServices`kayıt sırasında yazılı istemcinin yapılandırması belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="6e731-377">Yazılan istemciyi `HttpClient` tamamen kapsüllemek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="6e731-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="6e731-378">Bir özellik olarak teşhir etmek yerine, `HttpClient` örneği dahili olarak adlandıran genel yöntemler sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="6e731-379">Önceki kodda, özel `HttpClient` alan olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="6e731-380">Harici arama yapmak için tüm `GetRepos` erişim yöntemi geçer.</span><span class="sxs-lookup"><span data-stu-id="6e731-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="6e731-381">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-381">Generated clients</span></span>

<span data-ttu-id="6e731-382">`IHttpClientFactory`[Refit](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıkları ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="6e731-383">Refit ,NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="6e731-384">REST API'lerini canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="6e731-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="6e731-385">Arabirimin bir uygulaması, harici HTTP `RestService`aramaları `HttpClient` yapmak için kullanılarak dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6e731-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="6e731-386">Dış API'yi ve yanıtını temsil edecek bir arabirim ve yanıt tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="6e731-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="6e731-387">Uygulamayı oluşturmak için Refit kullanılarak yazılan bir istemci eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="6e731-388">Tanımlanan arabirim, DI ve Refit tarafından sağlanan uygulama ile gerektiğinde tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="6e731-389">Giden istek middleware</span><span class="sxs-lookup"><span data-stu-id="6e731-389">Outgoing request middleware</span></span>

<span data-ttu-id="6e731-390">`HttpClient`zaten giden HTTP istekleri için birbirine bağlanabilir işleyicileri delegating kavramı vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="6e731-391">Bu, `IHttpClientFactory` her adlandırılmış istemci için uygulamak için işleyicileri tanımlamak kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="6e731-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="6e731-392">Giden bir istek ara yazılım boru hattı oluşturmak için birden çok işleyicinin kaydedilmesi ve zincirlemesi destekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="6e731-393">Bu işleyicilerin her biri, giden istekten önce ve sonra iş yapabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="6e731-394">Bu desen, ASP.NET Core'daki gelen ara yazılım düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="6e731-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="6e731-395">Desen, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme gibi HTTP istekleri etrafında çapraz kesme yle ilgili endişeleri yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="6e731-396">İşleyici oluşturmak için, 'den <xref:System.Net.Http.DelegatingHandler>kaynaklanan bir sınıf tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="6e731-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="6e731-397">İsteği `SendAsync` ardışık sistemdeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kıl:</span><span class="sxs-lookup"><span data-stu-id="6e731-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="6e731-398">Önceki kod temel bir işleyici tanımlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="6e731-399">`X-API-KEY` Üstbilginin isteğe ekilip eklenmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="6e731-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="6e731-400">Üstbilgi eksikse, HTTP aramasını önleyebilir ve uygun bir yanıt döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="6e731-401">Kayıt sırasında, bir veya daha fazla işleyicisi `HttpClient`için yapılandırmaya eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="6e731-402">Bu görev, uzantı yöntemleri <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>ile gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="6e731-403">Önceki kodda, DI `ValidateHeaderHandler` kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="6e731-404">Her `IHttpClientFactory` işleyici için ayrı bir DI kapsamı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6e731-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="6e731-405">İşleyiciler herhangi bir kapsamdaki hizmetlere bağımlı olmakta serbesttir.</span><span class="sxs-lookup"><span data-stu-id="6e731-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="6e731-406">İşleyicinin bağlı olduğu hizmetler, işleyici imha edildiğinde elden çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="6e731-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="6e731-407">Bir kez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> kaydedildikten sonra, işleyici için tür geçen, çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="6e731-408">Birden çok işleyici, yürütmeleri gerektiği sırada kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="6e731-409">Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarar:</span><span class="sxs-lookup"><span data-stu-id="6e731-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="6e731-410">İsteme başına durumu ileti işleyicileriyle paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="6e731-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="6e731-411">Kullanarak verileri işleyiciye `HttpRequestMessage.Properties`aktarın.</span><span class="sxs-lookup"><span data-stu-id="6e731-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="6e731-412">Geçerli `IHttpContextAccessor` isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6e731-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="6e731-413">Verileri aktarmak `AsyncLocal` için özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6e731-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="6e731-414">Polly tabanlı işleyicileri kullanma</span><span class="sxs-lookup"><span data-stu-id="6e731-414">Use Polly-based handlers</span></span>

<span data-ttu-id="6e731-415">`IHttpClientFactory`[Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kütüphanesi ile entegre .</span><span class="sxs-lookup"><span data-stu-id="6e731-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="6e731-416">Polly, .NET için kapsamlı bir esneklik ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="6e731-417">Geliştiricilerin Retry, Circuit Breaker, Timeout, Bulkhead Isolation ve Fallback gibi ilkeleri akıcı ve iş parçacığı güvenli bir şekilde ifade etmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="6e731-418">Polly ilkelerinin yapılandırılmış `HttpClient` örneklerle kullanımını etkinleştirmek için uzantı yöntemleri sağlanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="6e731-419">Polly uzantıları:</span><span class="sxs-lookup"><span data-stu-id="6e731-419">The Polly extensions:</span></span>

* <span data-ttu-id="6e731-420">İstemcilere Polly tabanlı işleyiciler eklemeyi destekleyin.</span><span class="sxs-lookup"><span data-stu-id="6e731-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="6e731-421">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="6e731-422">Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="6e731-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="6e731-423">Geçici hataları işleme</span><span class="sxs-lookup"><span data-stu-id="6e731-423">Handle transient faults</span></span>

<span data-ttu-id="6e731-424">En sık kullanılan hatalar, harici HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="6e731-425">Geçici hataları işlemek `AddTransientHttpErrorPolicy` için bir ilke tanımlanmasına olanak sağlayan uygun bir uzatma yöntemi denir.</span><span class="sxs-lookup"><span data-stu-id="6e731-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="6e731-426">Bu uzantı yöntemi ile `HttpRequestException`yapılandırılan ilkeler, HTTP 5xx yanıtları ve HTTP 408 yanıtları.</span><span class="sxs-lookup"><span data-stu-id="6e731-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="6e731-427">Uzantısı `AddTransientHttpErrorPolicy` içinde `Startup.ConfigureServices`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e731-428">Uzantı, olası `PolicyBuilder` geçici bir hatayı temsil eden hataları işlemek için yapılandırılan bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="6e731-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="6e731-429">Önceki kodda bir `WaitAndRetryAsync` ilke tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="6e731-430">Başarısız istekler, denemeler arasında 600 ms'lik bir gecikmeyle üç defaya kadar yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="6e731-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="6e731-431">Dinamik olarak ilkeleri seçin</span><span class="sxs-lookup"><span data-stu-id="6e731-431">Dynamically select policies</span></span>

<span data-ttu-id="6e731-432">Polly tabanlı işleyicileri eklemek için kullanılabilecek ek uzatma yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="6e731-433">Böyle bir `AddPolicyHandler`uzantısı , birden fazla aşırı yükleri vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="6e731-434">Bir aşırı yükleme, hangi politikanın uygulanacağı tanımlanırken isteğin denetlenmesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="6e731-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="6e731-435">Önceki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman ayarı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="6e731-436">Başka bir HTTP yöntemi için 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e731-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="6e731-437">Birden çok Polly işleyicisi ekleme</span><span class="sxs-lookup"><span data-stu-id="6e731-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="6e731-438">Gelişmiş işlevsellik sağlamak için Polly ilkelerini iç içe yerleştirmek yaygındır:</span><span class="sxs-lookup"><span data-stu-id="6e731-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="6e731-439">Önceki örnekte, iki işleyicieklenir.</span><span class="sxs-lookup"><span data-stu-id="6e731-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="6e731-440">İlk bir `AddTransientHttpErrorPolicy` yeniden deneme ilkesi eklemek için uzantısı kullanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="6e731-441">Başarısız istekler en fazla üç kez yeniden denendir.</span><span class="sxs-lookup"><span data-stu-id="6e731-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="6e731-442">İkinci çağrı `AddTransientHttpErrorPolicy` bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="6e731-443">Beş başarısız deneme sırayla gerçekleşirse, diğer dış istekler 30 saniye boyunca engellenir.</span><span class="sxs-lookup"><span data-stu-id="6e731-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="6e731-444">Devre kesici ilkeleri durumludur.</span><span class="sxs-lookup"><span data-stu-id="6e731-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="6e731-445">Bu istemci den gelen tüm aramalar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="6e731-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="6e731-446">Polly kayıt defterinden ilkeler ekleme</span><span class="sxs-lookup"><span data-stu-id="6e731-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="6e731-447">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları `PolicyRegistry`bir kez tanımlamak ve bir .</span><span class="sxs-lookup"><span data-stu-id="6e731-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="6e731-448">Kayıt defterinden bir ilke kullanılarak işleyicinin eklenmesine olanak tanıyan bir uzantı yöntemi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="6e731-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="6e731-449">Önceki kodda, `PolicyRegistry` `ServiceCollection`iki ilke kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="6e731-450">Kayıt defterinden bir ilke `AddPolicyHandlerFromRegistry` kullanmak için, yöntem uygulanacak ilkenin adını geçerek kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e731-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="6e731-451">Polly `IHttpClientFactory` [wiki'de polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)hakkında daha fazla bilgi bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e731-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="6e731-452">Httpİste ve yaşam boyu yönetim</span><span class="sxs-lookup"><span data-stu-id="6e731-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="6e731-453">Yeni `HttpClient` bir örnek her `CreateClient` zaman `IHttpClientFactory`döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6e731-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="6e731-454">Her bir <xref:System.Net.Http.HttpMessageHandler> müşteri yeası var.</span><span class="sxs-lookup"><span data-stu-id="6e731-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="6e731-455">`HttpMessageHandler` Fabrika, örneklerin yaşam ömürlerini yönetir.</span><span class="sxs-lookup"><span data-stu-id="6e731-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="6e731-456">`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri bir araya sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="6e731-457">Bir `HttpMessageHandler` örnek, kullanım ömrü dolmamışsa `HttpClient` yeni bir örnek oluştururken havuzdan yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="6e731-458">Her işleyici genellikle kendi temel HTTP bağlantılarını yönetir gibi işleyicileri havuzlama arzu edilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="6e731-459">Gerekenden daha fazla işleyici oluşturmak bağlantı gecikmelerine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="6e731-460">Bazı işleyiciler de bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine tepki sini engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="6e731-461">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="6e731-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="6e731-462">Varsayılan değer, adlandırılmış istemci bazında geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="6e731-463">Geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> istemci `IHttpClientBuilder` oluştururken döndürülenleri arayın:</span><span class="sxs-lookup"><span data-stu-id="6e731-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="6e731-464">İstemcinin atılması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="6e731-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="6e731-465">Elden çıkarma giden istekleri iptal eder `HttpClient` ve verilen örneğin aramadan <xref:System.IDisposable.Dispose*>sonra kullanılamayacağından garanti eder.</span><span class="sxs-lookup"><span data-stu-id="6e731-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="6e731-466">`IHttpClientFactory`örnekleri tarafından `HttpClient` kullanılan kaynakları izler ve elden çıkar.</span><span class="sxs-lookup"><span data-stu-id="6e731-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="6e731-467">Örnekler `HttpClient` genellikle imha gerektirmeyen .NET nesneleri olarak kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="6e731-468">Tek `HttpClient` bir örneği uzun süre canlı tutmak, kuruluşundan önce `IHttpClientFactory`kullanılan yaygın bir desendir.</span><span class="sxs-lookup"><span data-stu-id="6e731-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="6e731-469">Bu desen' e geçtikten `IHttpClientFactory`sonra gereksiz hale gelir.</span><span class="sxs-lookup"><span data-stu-id="6e731-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="6e731-470">IHttpClientFactory için alternatifler</span><span class="sxs-lookup"><span data-stu-id="6e731-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="6e731-471">DI `IHttpClientFactory` özellikli bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="6e731-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="6e731-472">Örnekleri birleştirerek `HttpMessageHandler` kaynak tükenmesi sorunları.</span><span class="sxs-lookup"><span data-stu-id="6e731-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="6e731-473">Düzenli aralıklarla bisiklet `HttpMessageHandler` örnekleri tarafından Bayat DNS sorunları.</span><span class="sxs-lookup"><span data-stu-id="6e731-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="6e731-474">Uzun ömürlü bir örneği kullanarak önceki sorunları çözmek <xref:System.Net.Http.SocketsHttpHandler> için alternatif yollar vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="6e731-475">Uygulamanın ne `SocketsHttpHandler` zaman başladığına bir örnek oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="6e731-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="6e731-476">DNS yenileme sürelerine göre uygun bir değere göre <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="6e731-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="6e731-477">Gerektiğinde `HttpClient` kullanarak `new HttpClient(handler, disposeHandler: false)` örnekler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6e731-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="6e731-478">Önceki yaklaşımlar benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="6e731-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="6e731-479">Örnekler `SocketsHttpHandler` arasında `HttpClient` bağlantıları paylaşır.</span><span class="sxs-lookup"><span data-stu-id="6e731-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="6e731-480">Bu paylaşım soket yorgunluğunu önler.</span><span class="sxs-lookup"><span data-stu-id="6e731-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="6e731-481">Eski `SocketsHttpHandler` DNS sorunlarını `PooledConnectionLifetime` önlemek için döngüleri bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="6e731-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="6e731-482">Tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="6e731-482">Cookies</span></span>

<span data-ttu-id="6e731-483">Birleştirilmiş `HttpMessageHandler` örnekler, nesnelerin `CookieContainer` paylaşılmasına neden oluyor.</span><span class="sxs-lookup"><span data-stu-id="6e731-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="6e731-484">Beklenmeyen `CookieContainer` nesne paylaşımı genellikle yanlış kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="6e731-485">Tanımlama bilgisi gerektiren uygulamalar için aşağıdakileri göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="6e731-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="6e731-486">Otomatik çerez işlemeyi devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="6e731-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="6e731-487">Kaçın -arak`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="6e731-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="6e731-488">Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> çerez kullanımını devre dışı kılabilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="6e731-489">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="6e731-489">Logging</span></span>

<span data-ttu-id="6e731-490">Tüm istekler için kayıt günlüğü iletileri aracılığıyla `IHttpClientFactory` oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="6e731-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="6e731-491">Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="6e731-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="6e731-492">İstek üstbilgilerinin günlüğe kaydedilmesi gibi ek günlüğe kaydetme yalnızca izleme düzeyinde dahildir.</span><span class="sxs-lookup"><span data-stu-id="6e731-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="6e731-493">Her istemci için kullanılan günlük kategorisi istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="6e731-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="6e731-494">*Örneğin MyNamedClient*adlı bir istemci, iletileri `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="6e731-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="6e731-495">*LogicalHandler* ile sabitlenmiş iletiler istek işleyicisi ardışık alanıdışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="6e731-496">İstek üzerine, iletiler ardışık ardışık işlemden önce günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="6e731-497">Yanıtta, iletiler yanıtı aldıktan sonra günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="6e731-498">Günlüğe kaydetme, istek işleyicisi ardışık hattının içinde de oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="6e731-499">*MyNamedClient* örneğinde, bu iletiler günlük kategorisine `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`göre günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="6e731-500">İstek için, bu işlem, diğer tüm işleyiciler çalıştırdıktan sonra ve istek ağa gönderilmeden hemen önce oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="6e731-501">Yanıtta, bu günlüğe kaydetme, işleyici ardışık işlemden geçmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="6e731-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="6e731-502">Boru hattının dışında ve içinde günlüğe kaydetmeyi etkinleştirmek, diğer boru hattı işleyicileri tarafından yapılan değişikliklerin incelenmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="6e731-503">Bu, örneğin istek üstbilgisi veya yanıt durum kodunda yapılan değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="6e731-504">Günlük kategorisine istemcinin adını niçin eklenmesi gerektiğinde belirli adlandırılmış istemciler için günlük filtreleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="6e731-505">HttpMessageHandler'ı yapılandırın</span><span class="sxs-lookup"><span data-stu-id="6e731-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="6e731-506">Bir istemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmasını denetlemek için gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="6e731-507">Adlandırılmış veya yazılan istemciler eklendiğinde bir `IHttpClientBuilder` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6e731-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="6e731-508">Uzantı <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> yöntemi bir temsilci tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="6e731-509">Temsilci, bu istemci tarafından kullanılan `HttpMessageHandler` birincil oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="6e731-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="6e731-510">iHttpClientFactory konsol uygulamasında kullanma</span><span class="sxs-lookup"><span data-stu-id="6e731-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="6e731-511">Konsol uygulamasında, projeye aşağıdaki paket başvurularını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6e731-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="6e731-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="6e731-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="6e731-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="6e731-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="6e731-514">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="6e731-514">In the following example:</span></span>

* <span data-ttu-id="6e731-515"><xref:System.Net.Http.IHttpClientFactory>Genel Ana [Bilgisayar hizmet](xref:fundamentals/host/generic-host) konteynerine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="6e731-516">`MyService`bir `HttpClient`. oluşturmak için kullanılan hizmetten bir istemci fabrika örneği oluşturur</span><span class="sxs-lookup"><span data-stu-id="6e731-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="6e731-517">`HttpClient`bir web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e731-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="6e731-518">`Main`hizmetin `GetPage` yöntemini yürütmek ve web sayfası içeriğinin ilk 500 karakterini konsola yazmak için bir kapsam oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6e731-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="6e731-519">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6e731-519">Additional resources</span></span>

* [<span data-ttu-id="6e731-520">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="6e731-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="6e731-521">HttpClientFactory ve Polly politikaları ile üstel geri dönüş ile HTTP çağrı yeniden uygulama</span><span class="sxs-lookup"><span data-stu-id="6e731-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="6e731-522">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="6e731-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="6e731-523">Glenn [Condron](https://github.com/glennc)tarafından , [Ryan Nowak](https://github.com/rynowak), ve [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="6e731-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="6e731-524">Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve <xref:System.Net.Http.HttpClient> oluşturmak için kaydedilebilir ve kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="6e731-525">Aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="6e731-525">It offers the following benefits:</span></span>

* <span data-ttu-id="6e731-526">Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="6e731-527">Örneğin, bir *github* istemcisi kaydedilebilir ve [GitHub'a](https://github.com/)erişmek için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="6e731-528">Varsayılan istemci başka amaçlarla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="6e731-529">Giden ara yazılım kavramını işleyicileri atayarak kodlar `HttpClient` ve Polly tabanlı ara yazılımiçin bundan yararlanmak için uzantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="6e731-530">Yaşam ömürlerini el ile `HttpClientMessageHandler` yönetirken `HttpClient` ortaya çıkan yaygın DNS sorunlarını önlemek için temel örneklerin biraraya getiriyi ve kullanım ömrünü yönetir.</span><span class="sxs-lookup"><span data-stu-id="6e731-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="6e731-531">Fabrika tarafından oluşturulan istemciler `ILogger`aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlük deneyimi (üzerinden) ekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="6e731-532">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6e731-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6e731-533">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="6e731-533">Prerequisites</span></span>

<span data-ttu-id="6e731-534">.NET Framework'ü hedefleyen projeler [microsoft.extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet paketinin yüklenmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6e731-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="6e731-535">.NET Core'u hedefleyen ve [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app) başvuran projeler zaten `Microsoft.Extensions.Http` paketi içerir.</span><span class="sxs-lookup"><span data-stu-id="6e731-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="6e731-536">Tüketim alışkanlıkları</span><span class="sxs-lookup"><span data-stu-id="6e731-536">Consumption patterns</span></span>

<span data-ttu-id="6e731-537">Bir uygulamada `IHttpClientFactory` kullanılabilen çeşitli yollar vardır:</span><span class="sxs-lookup"><span data-stu-id="6e731-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="6e731-538">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="6e731-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="6e731-539">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="6e731-540">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="6e731-541">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="6e731-542">Hiçbiri diğerinden kesinlikle üstün değil.</span><span class="sxs-lookup"><span data-stu-id="6e731-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="6e731-543">En iyi yaklaşım uygulamanın kısıtlamalarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="6e731-544">Temel kullanım</span><span class="sxs-lookup"><span data-stu-id="6e731-544">Basic usage</span></span>

<span data-ttu-id="6e731-545">Yöntemin `IHttpClientFactory` `AddHttpClient` `IServiceCollection`içinde, `Startup.ConfigureServices` uzantı yöntemi ni arayarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="6e731-546">Bir kez kayıtlı, `IHttpClientFactory` kod bağımlılık [enjeksiyon (DI)](xref:fundamentals/dependency-injection)ile enjekte edilebilir her yerde hizmetleri kabul edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e731-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6e731-547">Bir `IHttpClientFactory` `HttpClient` örnek oluşturmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="6e731-548">Bu `IHttpClientFactory` şekilde kullanmak, varolan bir uygulamayı yeniden düzenlemenin iyi bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="6e731-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="6e731-549">Bu şekilde `HttpClient` kullanılan hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="6e731-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="6e731-550">Örneklerin `HttpClient` şu anda oluşturulduğu yerlerde, bu oluşumları bir çağrıyla değiştirin. <xref:System.Net.Http.IHttpClientFactory.CreateClient*></span><span class="sxs-lookup"><span data-stu-id="6e731-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="6e731-551">Adlandırılmış istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-551">Named clients</span></span>

<span data-ttu-id="6e731-552">Bir `HttpClient`uygulama, her biri farklı bir yapılandırmaya sahip olan birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemcileri**kullanma seçeneği vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="6e731-553">Bir adlandırılmış `HttpClient` için yapılandırma kayıt `Startup.ConfigureServices`sırasında belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="6e731-554">Önceki kodda, `AddHttpClient` adı *github*sağlayan denir.</span><span class="sxs-lookup"><span data-stu-id="6e731-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="6e731-555">Bu istemci, temel&mdash;adres ve GitHub API ile çalışmak için gerekli iki üstbilgi uygulanan bazı varsayılan yapılandırma vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="6e731-556">Her `CreateClient` zaman çağrılır, `HttpClient` yeni bir örnek oluşturulur ve yapılandırma eylem denir.</span><span class="sxs-lookup"><span data-stu-id="6e731-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="6e731-557">Adlandırılmış bir istemciyi tüketmek için `CreateClient`dize parametresi .</span><span class="sxs-lookup"><span data-stu-id="6e731-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="6e731-558">Oluşturulacak istemcinin adını belirtin:</span><span class="sxs-lookup"><span data-stu-id="6e731-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="6e731-559">Önceki kodda, istek bir ana bilgisayar adı belirtmek gerekmez.</span><span class="sxs-lookup"><span data-stu-id="6e731-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="6e731-560">İstemci için yapılandırılan temel adres kullanıldığından, yalnızca yolu geçebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="6e731-561">Yazılan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-561">Typed clients</span></span>

<span data-ttu-id="6e731-562">Yazılan istemciler:</span><span class="sxs-lookup"><span data-stu-id="6e731-562">Typed clients:</span></span>

* <span data-ttu-id="6e731-563">Dizeleri anahtar olarak kullanmaya gerek kalmadan adlandırılmış istemcilerle aynı yetenekleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="6e731-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="6e731-564">Müşterileri tüketirken IntelliSense ve derleyici yardımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="6e731-565">Belirli bir yeri yapılandırmak ve etkileşimde bulunabilmek `HttpClient`için tek bir konum sağlayın.</span><span class="sxs-lookup"><span data-stu-id="6e731-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="6e731-566">Örneğin, tek bir daktilo istemcisi tek bir arka uç bitiş noktası için kullanılabilir ve bu uç noktaile ilgili tüm mantığı kapsülleyebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="6e731-567">DI ile çalışın ve uygulamanızda gerektiğinde enjekte edilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="6e731-568">Yazılan istemci, oluşturucusu bir `HttpClient` parametre kabul eder:</span><span class="sxs-lookup"><span data-stu-id="6e731-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6e731-569">Önceki kodda, yapılandırma yazılan istemciye taşınır.</span><span class="sxs-lookup"><span data-stu-id="6e731-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="6e731-570">Nesne `HttpClient` bir kamu malı olarak ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="6e731-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="6e731-571">İşlevselliği ortaya `HttpClient` çıkaran API'ye özgü yöntemleri tanımlamak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="6e731-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="6e731-572">Yöntem, `GetAspNetDocsIssues` github deposundan en son açık sorunları sorgulamak ve ayrışdırmak için gereken kodu kapsüller.</span><span class="sxs-lookup"><span data-stu-id="6e731-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="6e731-573">Bir daktilan istemci kaydetmek <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> için, genel `Startup.ConfigureServices`uzantı yöntemi içinde kullanılabilir , daktilan istemci sınıfı belirterek:</span><span class="sxs-lookup"><span data-stu-id="6e731-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="6e731-574">Yazılan istemci, DI ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="6e731-575">Yazılan istemci doğrudan enjekte edilebilir ve tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="6e731-576">Tercih edilirse, yazılan istemcinin oluşturucusu yerine, `Startup.ConfigureServices`kayıt sırasında yazılı istemcinin yapılandırması belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="6e731-577">Yazılan istemciyi `HttpClient` tamamen kapsüllemek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="6e731-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="6e731-578">Bir özellik olarak teşhir etmek yerine, `HttpClient` örneği dahili olarak adlandıran genel yöntemler sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="6e731-579">Önceki kodda, özel `HttpClient` alan olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="6e731-580">Harici arama yapmak için tüm `GetRepos` erişim yöntemi geçer.</span><span class="sxs-lookup"><span data-stu-id="6e731-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="6e731-581">Oluşturulan istemciler</span><span class="sxs-lookup"><span data-stu-id="6e731-581">Generated clients</span></span>

<span data-ttu-id="6e731-582">`IHttpClientFactory`[Refit](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıkları ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="6e731-583">Refit ,NET için bir REST kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="6e731-584">REST API'lerini canlı arabirimlere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="6e731-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="6e731-585">Arabirimin bir uygulaması, harici HTTP `RestService`aramaları `HttpClient` yapmak için kullanılarak dinamik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6e731-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="6e731-586">Dış API'yi ve yanıtını temsil edecek bir arabirim ve yanıt tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="6e731-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="6e731-587">Uygulamayı oluşturmak için Refit kullanılarak yazılan bir istemci eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="6e731-588">Tanımlanan arabirim, DI ve Refit tarafından sağlanan uygulama ile gerektiğinde tüketilebilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="6e731-589">Giden istek middleware</span><span class="sxs-lookup"><span data-stu-id="6e731-589">Outgoing request middleware</span></span>

<span data-ttu-id="6e731-590">`HttpClient`zaten giden HTTP istekleri için birbirine bağlanabilir işleyicileri delegating kavramı vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="6e731-591">Bu, `IHttpClientFactory` her adlandırılmış istemci için uygulamak için işleyicileri tanımlamak kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="6e731-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="6e731-592">Giden bir istek ara yazılım boru hattı oluşturmak için birden çok işleyicinin kaydedilmesi ve zincirlemesi destekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="6e731-593">Bu işleyicilerin her biri, giden istekten önce ve sonra iş yapabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="6e731-594">Bu desen, ASP.NET Core'daki gelen ara yazılım düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="6e731-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="6e731-595">Desen, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme gibi HTTP istekleri etrafında çapraz kesme yle ilgili endişeleri yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="6e731-596">İşleyici oluşturmak için, 'den <xref:System.Net.Http.DelegatingHandler>kaynaklanan bir sınıf tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="6e731-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="6e731-597">İsteği `SendAsync` ardışık sistemdeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kıl:</span><span class="sxs-lookup"><span data-stu-id="6e731-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="6e731-598">Önceki kod temel bir işleyici tanımlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="6e731-599">`X-API-KEY` Üstbilginin isteğe ekilip eklenmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="6e731-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="6e731-600">Üstbilgi eksikse, HTTP aramasını önleyebilir ve uygun bir yanıt döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="6e731-601">Kayıt sırasında, bir veya daha fazla işleyicisi `HttpClient`için yapılandırmaya eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="6e731-602">Bu görev, uzantı yöntemleri <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>ile gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="6e731-603">Önceki kodda, DI `ValidateHeaderHandler` kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="6e731-604">İşleyici, di'ye geçici bir hizmet olarak **kaydedilmeli,** hiçbir zaman kapsama alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="6e731-605">İşleyici kapsamlı bir hizmet olarak kayıtlıysa ve işleyicinin bağlı olduğu hizmetler tek kullanımlıksa:</span><span class="sxs-lookup"><span data-stu-id="6e731-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="6e731-606">Işleyicinin hizmetleri kapsam dışına çıkmadan önce elden çıkarılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="6e731-607">Elden çıkarılan işleyici hizmetleri işleyicinin başarısız olması için neden olur.</span><span class="sxs-lookup"><span data-stu-id="6e731-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="6e731-608">Bir kez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> kaydedildikten sonra, işleyici türünde geçerek çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="6e731-609">Birden çok işleyici, yürütmeleri gerektiği sırada kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="6e731-610">Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarar:</span><span class="sxs-lookup"><span data-stu-id="6e731-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="6e731-611">İsteme başına durumu ileti işleyicileriyle paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="6e731-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="6e731-612">Kullanarak verileri işleyiciye `HttpRequestMessage.Properties`aktarın.</span><span class="sxs-lookup"><span data-stu-id="6e731-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="6e731-613">Geçerli `IHttpContextAccessor` isteğe erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6e731-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="6e731-614">Verileri aktarmak `AsyncLocal` için özel bir depolama nesnesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6e731-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="6e731-615">Polly tabanlı işleyicileri kullanma</span><span class="sxs-lookup"><span data-stu-id="6e731-615">Use Polly-based handlers</span></span>

<span data-ttu-id="6e731-616">`IHttpClientFactory`[Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kütüphanesi ile entegre .</span><span class="sxs-lookup"><span data-stu-id="6e731-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="6e731-617">Polly, .NET için kapsamlı bir esneklik ve geçici hata işleme kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="6e731-618">Geliştiricilerin Retry, Circuit Breaker, Timeout, Bulkhead Isolation ve Fallback gibi ilkeleri akıcı ve iş parçacığı güvenli bir şekilde ifade etmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="6e731-619">Polly ilkelerinin yapılandırılmış `HttpClient` örneklerle kullanımını etkinleştirmek için uzantı yöntemleri sağlanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="6e731-620">Polly uzantıları:</span><span class="sxs-lookup"><span data-stu-id="6e731-620">The Polly extensions:</span></span>

* <span data-ttu-id="6e731-621">İstemcilere Polly tabanlı işleyiciler eklemeyi destekleyin.</span><span class="sxs-lookup"><span data-stu-id="6e731-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="6e731-622">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="6e731-623">Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="6e731-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="6e731-624">Geçici hataları işleme</span><span class="sxs-lookup"><span data-stu-id="6e731-624">Handle transient faults</span></span>

<span data-ttu-id="6e731-625">En sık kullanılan hatalar, harici HTTP çağrıları geçici olduğunda oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="6e731-626">Geçici hataları işlemek `AddTransientHttpErrorPolicy` için bir ilke tanımlanmasına olanak sağlayan uygun bir uzatma yöntemi denir.</span><span class="sxs-lookup"><span data-stu-id="6e731-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="6e731-627">Bu uzantı yöntemi ile `HttpRequestException`yapılandırılan ilkeler, HTTP 5xx yanıtları ve HTTP 408 yanıtları.</span><span class="sxs-lookup"><span data-stu-id="6e731-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="6e731-628">Uzantısı `AddTransientHttpErrorPolicy` içinde `Startup.ConfigureServices`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e731-629">Uzantı, olası `PolicyBuilder` geçici bir hatayı temsil eden hataları işlemek için yapılandırılan bir nesneye erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="6e731-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="6e731-630">Önceki kodda bir `WaitAndRetryAsync` ilke tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="6e731-631">Başarısız istekler, denemeler arasında 600 ms'lik bir gecikmeyle üç defaya kadar yeniden denenir.</span><span class="sxs-lookup"><span data-stu-id="6e731-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="6e731-632">Dinamik olarak ilkeleri seçin</span><span class="sxs-lookup"><span data-stu-id="6e731-632">Dynamically select policies</span></span>

<span data-ttu-id="6e731-633">Polly tabanlı işleyicileri eklemek için kullanılabilecek ek uzatma yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="6e731-634">Böyle bir `AddPolicyHandler`uzantısı , birden fazla aşırı yükleri vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="6e731-635">Bir aşırı yükleme, hangi politikanın uygulanacağı tanımlanırken isteğin denetlenmesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="6e731-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="6e731-636">Önceki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman ayarı uygulanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="6e731-637">Başka bir HTTP yöntemi için 30 saniyelik bir zaman aşımı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e731-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="6e731-638">Birden çok Polly işleyicisi ekleme</span><span class="sxs-lookup"><span data-stu-id="6e731-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="6e731-639">Gelişmiş işlevsellik sağlamak için Polly ilkelerini iç içe yerleştirmek yaygındır:</span><span class="sxs-lookup"><span data-stu-id="6e731-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="6e731-640">Önceki örnekte, iki işleyicieklenir.</span><span class="sxs-lookup"><span data-stu-id="6e731-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="6e731-641">İlk bir `AddTransientHttpErrorPolicy` yeniden deneme ilkesi eklemek için uzantısı kullanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="6e731-642">Başarısız istekler en fazla üç kez yeniden denendir.</span><span class="sxs-lookup"><span data-stu-id="6e731-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="6e731-643">İkinci çağrı `AddTransientHttpErrorPolicy` bir devre kesici ilkesi ekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="6e731-644">Beş başarısız deneme sırayla gerçekleşirse, diğer dış istekler 30 saniye boyunca engellenir.</span><span class="sxs-lookup"><span data-stu-id="6e731-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="6e731-645">Devre kesici ilkeleri durumludur.</span><span class="sxs-lookup"><span data-stu-id="6e731-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="6e731-646">Bu istemci den gelen tüm aramalar aynı devre durumunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="6e731-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="6e731-647">Polly kayıt defterinden ilkeler ekleme</span><span class="sxs-lookup"><span data-stu-id="6e731-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="6e731-648">Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları `PolicyRegistry`bir kez tanımlamak ve bir .</span><span class="sxs-lookup"><span data-stu-id="6e731-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="6e731-649">Kayıt defterinden bir ilke kullanılarak işleyicinin eklenmesine olanak tanıyan bir uzantı yöntemi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="6e731-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="6e731-650">Önceki kodda, `PolicyRegistry` `ServiceCollection`iki ilke kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="6e731-651">Kayıt defterinden bir ilke `AddPolicyHandlerFromRegistry` kullanmak için, yöntem uygulanacak ilkenin adını geçerek kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e731-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="6e731-652">Polly `IHttpClientFactory` [wiki'de polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)hakkında daha fazla bilgi bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e731-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="6e731-653">Httpİste ve yaşam boyu yönetim</span><span class="sxs-lookup"><span data-stu-id="6e731-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="6e731-654">Yeni `HttpClient` bir örnek her `CreateClient` zaman `IHttpClientFactory`döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6e731-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="6e731-655">Her bir <xref:System.Net.Http.HttpMessageHandler> müşteri yeası var.</span><span class="sxs-lookup"><span data-stu-id="6e731-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="6e731-656">`HttpMessageHandler` Fabrika, örneklerin yaşam ömürlerini yönetir.</span><span class="sxs-lookup"><span data-stu-id="6e731-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="6e731-657">`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri bir araya sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="6e731-658">Bir `HttpMessageHandler` örnek, kullanım ömrü dolmamışsa `HttpClient` yeni bir örnek oluştururken havuzdan yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="6e731-659">Her işleyici genellikle kendi temel HTTP bağlantılarını yönetir gibi işleyicileri havuzlama arzu edilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="6e731-660">Gerekenden daha fazla işleyici oluşturmak bağlantı gecikmelerine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="6e731-661">Bazı işleyiciler de bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine tepki sini engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="6e731-662">Varsayılan işleyici ömrü iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="6e731-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="6e731-663">Varsayılan değer, adlandırılmış istemci bazında geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="6e731-664">Geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> istemci `IHttpClientBuilder` oluştururken döndürülenleri arayın:</span><span class="sxs-lookup"><span data-stu-id="6e731-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="6e731-665">İstemcinin atılması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="6e731-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="6e731-666">Elden çıkarma giden istekleri iptal eder `HttpClient` ve verilen örneğin aramadan <xref:System.IDisposable.Dispose*>sonra kullanılamayacağından garanti eder.</span><span class="sxs-lookup"><span data-stu-id="6e731-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="6e731-667">`IHttpClientFactory`örnekleri tarafından `HttpClient` kullanılan kaynakları izler ve elden çıkar.</span><span class="sxs-lookup"><span data-stu-id="6e731-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="6e731-668">Örnekler `HttpClient` genellikle imha gerektirmeyen .NET nesneleri olarak kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="6e731-669">Tek `HttpClient` bir örneği uzun süre canlı tutmak, kuruluşundan önce `IHttpClientFactory`kullanılan yaygın bir desendir.</span><span class="sxs-lookup"><span data-stu-id="6e731-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="6e731-670">Bu desen' e geçtikten `IHttpClientFactory`sonra gereksiz hale gelir.</span><span class="sxs-lookup"><span data-stu-id="6e731-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="6e731-671">IHttpClientFactory için alternatifler</span><span class="sxs-lookup"><span data-stu-id="6e731-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="6e731-672">DI `IHttpClientFactory` özellikli bir uygulamada kullanmak şunları önler:</span><span class="sxs-lookup"><span data-stu-id="6e731-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="6e731-673">Örnekleri birleştirerek `HttpMessageHandler` kaynak tükenmesi sorunları.</span><span class="sxs-lookup"><span data-stu-id="6e731-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="6e731-674">Düzenli aralıklarla bisiklet `HttpMessageHandler` örnekleri tarafından Bayat DNS sorunları.</span><span class="sxs-lookup"><span data-stu-id="6e731-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="6e731-675">Uzun ömürlü bir örneği kullanarak önceki sorunları çözmek <xref:System.Net.Http.SocketsHttpHandler> için alternatif yollar vardır.</span><span class="sxs-lookup"><span data-stu-id="6e731-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="6e731-676">Uygulamanın ne `SocketsHttpHandler` zaman başladığına bir örnek oluşturun ve uygulamanın ömrü boyunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="6e731-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="6e731-677">DNS yenileme sürelerine göre uygun bir değere göre <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="6e731-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="6e731-678">Gerektiğinde `HttpClient` kullanarak `new HttpClient(handler, disposeHandler: false)` örnekler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6e731-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="6e731-679">Önceki yaklaşımlar benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.</span><span class="sxs-lookup"><span data-stu-id="6e731-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="6e731-680">Örnekler `SocketsHttpHandler` arasında `HttpClient` bağlantıları paylaşır.</span><span class="sxs-lookup"><span data-stu-id="6e731-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="6e731-681">Bu paylaşım soket yorgunluğunu önler.</span><span class="sxs-lookup"><span data-stu-id="6e731-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="6e731-682">Eski `SocketsHttpHandler` DNS sorunlarını `PooledConnectionLifetime` önlemek için döngüleri bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="6e731-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="6e731-683">Tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="6e731-683">Cookies</span></span>

<span data-ttu-id="6e731-684">Birleştirilmiş `HttpMessageHandler` örnekler, nesnelerin `CookieContainer` paylaşılmasına neden oluyor.</span><span class="sxs-lookup"><span data-stu-id="6e731-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="6e731-685">Beklenmeyen `CookieContainer` nesne paylaşımı genellikle yanlış kodla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="6e731-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="6e731-686">Tanımlama bilgisi gerektiren uygulamalar için aşağıdakileri göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="6e731-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="6e731-687">Otomatik çerez işlemeyi devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="6e731-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="6e731-688">Kaçın -arak`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="6e731-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="6e731-689">Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> çerez kullanımını devre dışı kılabilir:</span><span class="sxs-lookup"><span data-stu-id="6e731-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="6e731-690">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="6e731-690">Logging</span></span>

<span data-ttu-id="6e731-691">Tüm istekler için kayıt günlüğü iletileri aracılığıyla `IHttpClientFactory` oluşturulan istemciler.</span><span class="sxs-lookup"><span data-stu-id="6e731-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="6e731-692">Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="6e731-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="6e731-693">İstek üstbilgilerinin günlüğe kaydedilmesi gibi ek günlüğe kaydetme yalnızca izleme düzeyinde dahildir.</span><span class="sxs-lookup"><span data-stu-id="6e731-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="6e731-694">Her istemci için kullanılan günlük kategorisi istemcinin adını içerir.</span><span class="sxs-lookup"><span data-stu-id="6e731-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="6e731-695">*Örneğin MyNamedClient*adlı bir istemci, iletileri `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="6e731-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="6e731-696">*LogicalHandler* ile sabitlenmiş iletiler istek işleyicisi ardışık alanıdışında oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="6e731-697">İstek üzerine, iletiler ardışık ardışık işlemden önce günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="6e731-698">Yanıtta, iletiler yanıtı aldıktan sonra günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="6e731-699">Günlüğe kaydetme, istek işleyicisi ardışık hattının içinde de oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="6e731-700">*MyNamedClient* örneğinde, bu iletiler günlük kategorisine `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`göre günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="6e731-701">İstek için, bu işlem, diğer tüm işleyiciler çalıştırdıktan sonra ve istek ağa gönderilmeden hemen önce oluşur.</span><span class="sxs-lookup"><span data-stu-id="6e731-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="6e731-702">Yanıtta, bu günlüğe kaydetme, işleyici ardışık işlemden geçmeden önce yanıtın durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="6e731-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="6e731-703">Boru hattının dışında ve içinde günlüğe kaydetmeyi etkinleştirmek, diğer boru hattı işleyicileri tarafından yapılan değişikliklerin incelenmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="6e731-704">Bu, örneğin istek üstbilgisi veya yanıt durum kodunda yapılan değişiklikleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="6e731-705">Günlük kategorisine istemcinin adını niçin eklenmesi gerektiğinde belirli adlandırılmış istemciler için günlük filtreleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="6e731-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="6e731-706">HttpMessageHandler'ı yapılandırın</span><span class="sxs-lookup"><span data-stu-id="6e731-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="6e731-707">Bir istemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmasını denetlemek için gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="6e731-708">Adlandırılmış veya yazılan istemciler eklendiğinde bir `IHttpClientBuilder` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6e731-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="6e731-709">Uzantı <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> yöntemi bir temsilci tanımlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="6e731-710">Temsilci, bu istemci tarafından kullanılan `HttpMessageHandler` birincil oluşturmak ve yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="6e731-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="6e731-711">iHttpClientFactory konsol uygulamasında kullanma</span><span class="sxs-lookup"><span data-stu-id="6e731-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="6e731-712">Konsol uygulamasında, projeye aşağıdaki paket başvurularını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6e731-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="6e731-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="6e731-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="6e731-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="6e731-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="6e731-715">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="6e731-715">In the following example:</span></span>

* <span data-ttu-id="6e731-716"><xref:System.Net.Http.IHttpClientFactory>Genel Ana [Bilgisayar hizmet](xref:fundamentals/host/generic-host) konteynerine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6e731-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="6e731-717">`MyService`bir `HttpClient`. oluşturmak için kullanılan hizmetten bir istemci fabrika örneği oluşturur</span><span class="sxs-lookup"><span data-stu-id="6e731-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="6e731-718">`HttpClient`bir web sayfasını almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6e731-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="6e731-719">`Main`hizmetin `GetPage` yöntemini yürütmek ve web sayfası içeriğinin ilk 500 karakterini konsola yazmak için bir kapsam oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6e731-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="6e731-720">Üstbilgi yayılımı ara ware</span><span class="sxs-lookup"><span data-stu-id="6e731-720">Header propagation middleware</span></span>

<span data-ttu-id="6e731-721">Üstbilgi yayılımı, gelen istekten giden HTTP İstemci isteklerine HTTP üstbilgilerini yaymak için desteklenen bir topluluk orta yazılımıdır.</span><span class="sxs-lookup"><span data-stu-id="6e731-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="6e731-722">Üstbilgi yayılmasını kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="6e731-722">To use header propagation:</span></span>

* <span data-ttu-id="6e731-723">Başvuru topluluk paketinin bağlantı noktası desteklenen [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="6e731-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="6e731-724">ASP.NET Core 3.1 ve daha sonra [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)destekler.</span><span class="sxs-lookup"><span data-stu-id="6e731-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="6e731-725">Ara yazılımı yapılandırın ve `HttpClient` şu `Startup`şekilde:</span><span class="sxs-lookup"><span data-stu-id="6e731-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="6e731-726">İstemci, giden isteklerde yapılandırılan üstbilgiiçerir:</span><span class="sxs-lookup"><span data-stu-id="6e731-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="6e731-727">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6e731-727">Additional resources</span></span>

* [<span data-ttu-id="6e731-728">Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma</span><span class="sxs-lookup"><span data-stu-id="6e731-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="6e731-729">HttpClientFactory ve Polly politikaları ile üstel geri dönüş ile HTTP çağrı yeniden uygulama</span><span class="sxs-lookup"><span data-stu-id="6e731-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="6e731-730">Devre Kesici desenini uygulama</span><span class="sxs-lookup"><span data-stu-id="6e731-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
