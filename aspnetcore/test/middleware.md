---
title: Test ASP.NET Core ara yazılımı
author: tratcher
description: ASP.NET Core ara yazılımını TestServer ile test etme hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
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
uid: test/middleware
ms.openlocfilehash: ed0925259bf3d4fee6c903ff55cdf1dae2355af7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631907"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="5ad02-103">Test ASP.NET Core ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="5ad02-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="5ad02-104">[Chris](https://github.com/Tratcher) 'e göre</span><span class="sxs-lookup"><span data-stu-id="5ad02-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="5ad02-105">Ara yazılım ile yalıtımta test edilebilir <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="5ad02-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="5ad02-106">Şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="5ad02-106">It allows you to:</span></span>

* <span data-ttu-id="5ad02-107">Yalnızca test etmeniz gereken bileşenleri içeren bir uygulama işlem hattı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5ad02-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="5ad02-108">Ara yazılım davranışını doğrulamak için özel istekler gönderin.</span><span class="sxs-lookup"><span data-stu-id="5ad02-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="5ad02-109">Artılar:</span><span class="sxs-lookup"><span data-stu-id="5ad02-109">Advantages:</span></span>

* <span data-ttu-id="5ad02-110">İstekler ağ üzerinden serileştirilmesi yerine bellek içinde gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5ad02-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="5ad02-111">Bu, bağlantı noktası yönetimi ve HTTPS sertifikaları gibi ek sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="5ad02-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="5ad02-112">Ara yazılım içindeki özel durumlar doğrudan çağıran teste akabilir.</span><span class="sxs-lookup"><span data-stu-id="5ad02-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="5ad02-113">Doğrudan testte gibi sunucu veri yapılarını özelleştirmek mümkündür <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="5ad02-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="5ad02-114">TestServer 'ı ayarlama</span><span class="sxs-lookup"><span data-stu-id="5ad02-114">Set up the TestServer</span></span>

<span data-ttu-id="5ad02-115">Test projesinde bir test oluşturun:</span><span class="sxs-lookup"><span data-stu-id="5ad02-115">In the test project, create a test:</span></span>

* <span data-ttu-id="5ad02-116">Tarafından kullanılan bir konak oluşturun ve başlatın <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="5ad02-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="5ad02-117">Ara yazılım tarafından kullanılan gerekli hizmetleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5ad02-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="5ad02-118">Projeye [Microsoft. AspNetCore. TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet paketini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5ad02-118">Add the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet package to the project:</span></span>
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* <span data-ttu-id="5ad02-119">Test için ara yazılımı kullanmak üzere işleme işlem hattını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5ad02-119">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="5ad02-120">HttpClient ile istek gönderme</span><span class="sxs-lookup"><span data-stu-id="5ad02-120">Send requests with HttpClient</span></span>
<span data-ttu-id="5ad02-121">Şunu kullanarak bir istek gönder <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="5ad02-121">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="5ad02-122">Sonucu onaylama.</span><span class="sxs-lookup"><span data-stu-id="5ad02-122">Assert the result.</span></span> <span data-ttu-id="5ad02-123">İlk olarak, bir onaylama işlemi beklediğinizi sonucun tersini yapın.</span><span class="sxs-lookup"><span data-stu-id="5ad02-123">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="5ad02-124">Hatalı pozitif bir onaylama işlemi olan ilk çalıştırma, ara yazılım doğru çalışırken testin başarısız olduğunu onaylar.</span><span class="sxs-lookup"><span data-stu-id="5ad02-124">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="5ad02-125">Testi çalıştırın ve testin başarısız olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="5ad02-125">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="5ad02-126">Aşağıdaki örnekte, kök uç noktası istendiğinde, ara yazılım 404 durum kodu döndürmelidir (*bulunamadı*).</span><span class="sxs-lookup"><span data-stu-id="5ad02-126">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="5ad02-127">Başarısız olması gereken ilk testi çalıştırın `Assert.NotEqual( ... );` :</span><span class="sxs-lookup"><span data-stu-id="5ad02-127">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="5ad02-128">Normal çalışma koşullarında ara yazılımı test etmek için onayı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5ad02-128">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="5ad02-129">Son test kullanır `Assert.Equal( ... );` .</span><span class="sxs-lookup"><span data-stu-id="5ad02-129">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="5ad02-130">Başarılı olduğunu onaylamak için testi yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5ad02-130">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="5ad02-131">HttpContext ile istek gönderme</span><span class="sxs-lookup"><span data-stu-id="5ad02-131">Send requests with HttpContext</span></span>

<span data-ttu-id="5ad02-132">Bir test uygulaması, [Sendadsync (Action \<HttpContext> , CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A)kullanarak da bir istek gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="5ad02-132">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="5ad02-133">Aşağıdaki örnekte, `https://example.com/A/Path/?and=query` Ara yazılım tarafından işlendiğinde birkaç denetim yapılır:</span><span class="sxs-lookup"><span data-stu-id="5ad02-133">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="5ad02-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A><xref:Microsoft.AspNetCore.Http.HttpContext>soyutlamalar kullanmak yerine bir nesnenin doğrudan yapılandırılmasına izin verir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="5ad02-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="5ad02-135"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>Yalnızca sunucuda kullanılabilir olan, [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) veya [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features)gibi yapıları işlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="5ad02-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="5ad02-136">Önceki örnekte olduğu gibi, *404-olmayan* bir yanıt için test edilmiş, önceki testteki her bir deyimin tersini denetleyin `Assert` .</span><span class="sxs-lookup"><span data-stu-id="5ad02-136">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="5ad02-137">Denetim, ara yazılım normal olarak çalışırken testin doğru şekilde başarısız olduğunu onaylar.</span><span class="sxs-lookup"><span data-stu-id="5ad02-137">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="5ad02-138">Yanlış pozitif testin çalışıp çalışmadığını onayladıktan sonra, `Assert` beklenen koşulların ve test değerlerinin son deyimlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5ad02-138">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="5ad02-139">Testin başarılı olduğunu onaylamak için yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5ad02-139">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="5ad02-140">TestServer sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="5ad02-140">TestServer limitations</span></span>

<span data-ttu-id="5ad02-141">TestServer:</span><span class="sxs-lookup"><span data-stu-id="5ad02-141">TestServer:</span></span>

* <span data-ttu-id="5ad02-142">, Sunucu davranışlarını test eden yazılım için çoğaltmak üzere oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="5ad02-142">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="5ad02-143">Tüm davranışları ***çoğaltmayı denemez*** <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="5ad02-143">Does ***not*** try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
* <span data-ttu-id="5ad02-144">İstemci erişimine mümkün olduğunca çok denetim sağlamak ve sunucu üzerinde mümkün olduğunca fazla görünürlük sağlamak için denemeler yapmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5ad02-144">Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="5ad02-145">Örneğin, `HttpClient` sunucu durumunu doğrudan iletmek için normalde tarafından oluşturulan özel durumlar oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="5ad02-145">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="5ad02-146">Genellikle ara yazılım ile ilgili olmadığından, bazı aktarıma özgü üstbilgiler varsayılan olarak ayarlanamaz.</span><span class="sxs-lookup"><span data-stu-id="5ad02-146">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="5ad02-147">Daha fazla bilgi için sonraki bölüme bakın.</span><span class="sxs-lookup"><span data-stu-id="5ad02-147">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="5ad02-148">Content-Length ve Transfer-Encoding üstbilgileri</span><span class="sxs-lookup"><span data-stu-id="5ad02-148">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="5ad02-149">TestServer, taşıma ile ilgili istek ya da [Içerik uzunluğu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) veya [aktarım kodlaması](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding)gibi yanıt üst ***bilgileri ayarlanmamış.***</span><span class="sxs-lookup"><span data-stu-id="5ad02-149">TestServer does ***not*** set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="5ad02-150">Kullanımları istemci, senaryo ve protokole göre değiştiğinden uygulamalar bu üstbilgilere bağlı olarak kaçınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5ad02-150">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="5ad02-151">`Content-Length`Ve `Transfer-Encoding` belirli bir senaryoyu test etmek için gerekliyse, veya oluştururken test içinde belirtilenebilir <xref:System.Net.Http.HttpRequestMessage> <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="5ad02-151">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="5ad02-152">Daha fazla bilgi için bkz. aşağıdaki GitHub sorunları:</span><span class="sxs-lookup"><span data-stu-id="5ad02-152">For more information, see the following GitHub issues:</span></span>

* [<span data-ttu-id="5ad02-153">DotNet/aspnetcore # 21677</span><span class="sxs-lookup"><span data-stu-id="5ad02-153">dotnet/aspnetcore#21677</span></span>](https://github.com/dotnet/aspnetcore/issues/21677)
* [<span data-ttu-id="5ad02-154">DotNet/aspnetcore # 18463</span><span class="sxs-lookup"><span data-stu-id="5ad02-154">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="5ad02-155">DotNet/aspnetcore # 13273</span><span class="sxs-lookup"><span data-stu-id="5ad02-155">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
